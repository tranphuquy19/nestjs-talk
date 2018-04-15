---
# Advanced Nest
.column-count-2[
* Websockets
* Microservices
* Nest CLI
* Dynamic modules
* Execution Context
* Global Usage
* Service Locator
* SQL (TypeORM)
* GraphQL
* OpenAPI (Swagger)
* CQRS
]
---
# Gateways
```ts
@WebSocketGateway()
export class EventsGateway {
  @WebSocketServer() server;

  @SubscribeMessage('events')
  onEvent(client, data): Observable<WsResponse<number>> {
    const event = 'events';
    const response = [1, 2, 3];

    return Observable.from(response).map(res => ({ event, data: res }));
  }
}
```
???
Вебсокеты или Гейтвэй. Гейтвэй - это класс с декоратором `@WebSocketGateway()`, по факту это инкапсулированный socket.io сервер.  
---
# Exception Filters

```ts
throw new WsException('Invalid credentials.');
```
```ts
import { Catch, WsExceptionFilter } from '@nestjs/common';
import { WsException } from '@nestjs/websockets';

@Catch(WsException)
export class ExceptionFilter implements WsExceptionFilter {
  catch(exception: WsException, client) {
    client.emit('exception', {
      status: 'error',
      message: `It's a message from the exception filter`,
    });
  }
}
```
???
У вебсокетов есть эксепшн фильтры, они ничем не отличаются от обычный эксепшн фильтров, кроме одного - вместо HttpException-ов надо выбрасывать `WsException`.
---
## Pipes / Guards / Interceptors
Instead of throwing `HttpException`, you should use the `WsException`.

* Guards: `canActivate(data, context)`
* Interceptors: `intercept(data, context, stream$)`

The websockets guards takes the `data` passed from the client instead of expressjs `request` object as an argument.
???
Пайпы, Гварды, и интерцепторы - работают так же как и в веб-приложении, только опять надо выбрасывать `WsException` вместо `HttpException`.  
И еще одно отличие в гвардах и интерцепторах первый аргумент будет не `http-request`, а какие-то данные от клиента при `socket.emit`.
---
# WebSocket Adapter
```ts
export class WsAdapter implements WebSocketAdapter {
  
  create(port: number) {
    return new WebSocket.Server({ port });
  }

  bindClientConnect(server, callback: (...args: any[]) => void) {
    server.on('connection', callback);
  }

  bindMessageHandlers(client: WebSocket, handlers: MessageMappingProperties[], process: (data) => Observable<any>) {
    Observable.fromEvent(client, 'message')
      .switchMap((buffer) => this.bindMessageHandler(buffer, handlers, process))
      .filter((result) => !!result)
      .subscribe((response) => client.send(JSON.stringify(response)));
  }

  private bindMessageHandler(buffer, handlers: MessageMappingProperties[], process: (data) => Observable<any>): Observable<any> {
    const data = JSON.parse(buffer.data);
    const messageHandler = handlers.find((handler) => handler.message === data.type);
    if (!messageHandler) {
      return Observable.empty();
    }
    const { callback } = messageHandler;
    return process(callback(data));
  }
}
```
???
Если вы не хотите использовать socket.io, а хотите, к примеру, нативные веб-сокеты.  
Все что надо - это написать свой класс который реализовывает интерфейс `WebSocketAdapter`, с 3-мя методами.  
Выглядит он примерно так.
---
# Microservices
![](https://docs.nestjs.com/assets/Microservices_1.png)
???
По факту, это не совсем микросервис, это небольшое приложение, которое использует различные транспортные уровни (не http).
Nest поддерживает 2 типа транспорта: TCP и Redis pub/sub, но ничто не мешает написать реализацию для другого протокола.
Т.е. надо создать класс, которые реализовывает интерфейс `CustomTransportStrategy`.
---
# Basics
```ts
import { NestFactory } from '@nestjs/core';
import { ApplicationModule } from './modules/app.module';
import { Transport } from '@nestjs/microservices';

async function bootstrap() {
  const app = await NestFactory.createMicroservice(ApplicationModule, {
    transport: Transport.TCP,
  });
  app.listen(() => console.log('Microservice is listening'));
}
bootstrap();
```
???
Чтобы создать микросервис надо вызвать `NestFactory.createMicroservice` 1-ый параметр модуль, 2-ой настройки микросервиса (транспорт, порт и т.д.)
---
# Patterns
```ts
import { Controller } from '@nestjs/common';
import { MessagePattern } from '@nestjs/microservices';

@Controller()
export class MathController {
  @MessagePattern({ cmd: 'sum' })
  sum(data: number[]): number {
    return (data || []).reduce((a, b) => a + b);
  }
}
```
???
Микросервис разпознает сообщения с помощью патернов, это может быть строка или объект.
Т.е. Чтобы метод контроллера, стал обработчиком этих сообщений, надо добавить декоратор `@MessagePattern()`
---
# Client
```
import { Controller, Get } from '@nestjs/common';
import { Client, ClientProxy, Transport } from '@nestjs/microservices';

@Controller()
export class ClientController {
    
    @Client({ transport: Transport.TCP, port: 5667 })
    client: ClientProxy;

    @Get('client')
    sendMessage(req, res) {
        const pattern = { command: 'add' };
        const data = [ 1, 2, 3, 4, 5 ];

        this.client.send(pattern, data)
            .catch((err) => Observable.empty())
            .subscribe((result) => res.status(200).json({ result }));
    }
}
```
???
Чтобы чтобы отправить в этот обработчик сообщение, нужен клиент. Создать его можно с помощью декоратора `@Client` с настройками сервера.
Метод `send` у объекта `client` - отправляет данные и возвращает rxjs observable.
---
# Custom Transport
```ts
interface CustomTransportStrategy {
    listen(callback: () => void): any;
    close(): any;
}
```
```ts
export class RabbitMQServer extends Server implements CustomTransportStrategy {

    constructor(private host: string, private queue: string) {
        super();
    }

    // ...
}
```
```ts
const app = await NestFactory.createMicroservice(ApplicationModule, {
    strategy: new RabbitMQServer('amqp://localhost', 'channel'),
});
```
???
Во фреймворк встроена поддержка только TCP и Redis, но коммуникацию по другой схеме можно написать самому.
Надо реализовать интерфейс `CustomTransportStrategy`.
---
# Custom Client
```ts
abstract class ClientProxy {
    protected abstract sendSingleMessage(msg: any, callback: (err, result, disposed?: boolean) => void): any;
    send<T>(pattern: any, data: any): Observable<T>;
    protected createObserver<T>(observer: Observer<T>): (err, result, disposed?: boolean) => void;
}
```
```ts
export class RabbitMQClient extends ClientProxy {
  
  constructor(private host: string, private queue: string) {
      super();
  }
}
```
```ts
this.client = new RabbitMQClient('amqp://localhost', 'example');
```
???
И для этого кастомного транспорта нужен соответствующий клиент, который можно сделать отнаследовавшись от класса `ClientProxy`.
---