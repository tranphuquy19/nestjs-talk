class: center, middle, inverse
background-image: url(https://nestjs.com/img/cat-header.png)
# Nest.js Overview
.footnote[May 2018]
---
# Content
.column-count-2[
* Philosophy
* Features
* Core Concepts
* Module
* Controller
* Component
* Middlewares
* Pipes
* Exception Filters
* Interceptors
* Guards
* Websockets
* Microservices
* Unit Testing
]
???
Сегодня будет обзор фрейморка nest, посмотрим его идею и основные концепции.
---
# About Project Author
.float[
![](https://avatars0.githubusercontent.com/u/23244943?s=300&v=4)
]

.float[
* Kamil Myśliwiec
* [kamilmysliwiec.com](https://kamilmysliwiec.com/)
* Poland
]
???
Несколько слов аб авторе фреймворка. Зовут его Камиль Мышлевиц. Он из Польши.  
---
# Introduction
Nest is a powerful web framework for Node.js,
which helps you effortlessly build efficient, scalable applications.
It uses modern JavaScript, is built with TypeScript and combines best concepts of both OOP (Object Oriented Progamming) and FP (Functional Programming).
???
Nest.js это современный backend ф., написан на TypeScript и активно использует декораторы.  
Писать приложения можно TypeScript, можно на JavaScript, но рекомендуется TypeScript (потому что сам фреймворк на нем написан).  
---
# Philosophy
The core concept of Nest is to provide an **architecture**.  
Which helps developers to accomplish maximum separation of layers and increase abstraction in their applications.
???
Философия nest.js - это архитектура.  
Nest стремится предоставить архитектуру приложения из коробки, что позволяет разработчику делать максимальное разделение слоев
и увеличивать абстракицию в приложениях, и разрабатывать слабо связанные компоненты.
---
# Features
* Based on express.js and socket.io
* Dependency Injection
* Abstract Layers
  * Controllers
  * Exception Layer
  * Guards
  * Interceptors
  * Pipes
* Testing Utils
* Written in TypeScript
* Websockets
* Microservices
???
Какие фичи фреймворк предоставляет:  
Основан на express, поэтому вся экосистема express в вашем распоряжении.  
Dependency Injection, несколько слоев (Exception Layer, Guards, Interceptors, Pipes)  
Инструменты для тестирования, вебсокеты и микросервисы из коробки.
---
# Core Concepts
* **Modules** - logical part of the application which lives within the same domain boundary
* **Controlers** - is responsible for handling incoming requests, and return a response to the client
* **Components** - almost everything is a component – service, repository, provider etc.
???
Строительные блоки для приложения.
Модули - это какая инкапсулированная часть приложения, например, пользователи.  
КонтрОллеры ответственны за обработку запроса, и выдачи ответа клиенту.  
Компоненты - это все остальное, т.е. любые сервисы.
---
# Modules
```ts
import { Module } from '@nestjs/common';

@Module({})
export class ApplicationModule { }
```
???
Модуль это класс с декоратором `@Module`.
Любое приложение, должно иметь хотя бы один модуль, модули могут импортировать другие модули.
---
# Modules
![](https://docs.nestjs.com/assets/Modules_1.png)
???
Такм образом, структура приложения получается как дерево модулей.
---
# Setup Application
```typescript
import { Module } from '@nestjs/common';

@Module({})
export class ApplicationModule { }
```
```typescript
import { NestFactory } from '@nestjs/core';
import { ApplicationModule } from './app.module';

const app = await NestFactory.create(ApplicationModule);
app.listen(3000, () => console.log('Application is listening on port 3000'));
```
???
Итак, чтобы запустить nest.js приложение, нужно создать как минимум один модуль передать его в `NestFactory.create()`
Вторым параметром, иожно передать инстанс экспресса.
Приложение запустится, но оно пустое, оно не будет делать ничего полезного, т.к. нет контрОллеров.
---
# Controller
```typescript
import { Controller, Get, Post } from '@nestjs/common';

@Controller()
export class UsersController {
    
    @Get('users')
    getAllUsers(req, res, next) { }

    @Get('users/:id')
    getUser(req, res, next) { }

    @Post('users')
    addUser(req, res, next) { }
}
```
???
Добавим следующий контроллер UsersController.  
Обычный класс, на методы ставятся декораторы get, post, delete и другие, в параметрах декоратора можно указать путь.
Аргументы которые будут приходить в метод, это express-овские объекты request, response и next.  
---
# Endpoints
```
GET: users
GET: users/:id 
POST: users
```
???
Мы получим следующие эндпоинты.
--
```typescript
import { Controller, Get, Post } from '@nestjs/common';

@Controller('users')
export class UsersController {
    
    @Get()
    getAllUsers(req, res, next) { }

    @Get(':id')
    getUser(req, res, next) { }

    @Post()
    addUser(req, res, next) { }
}
```
???
Пути можно сократить на users и записать следующим образом.  
Т.е. префикс users мы его перенесли в параметр декоратора для контроллера и удалили из декораторов метода.
---
# Adding to Module
```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';

@Module({
    controllers: [UsersController],
})
export class ApplicationModule { }
```
???
Мы создали контрОллер, но модуль и приложение о нем ничего не знает.  
Его надо добавить в модуль.
---
# Response Object
```typescript
import { Controller, Get, Res } from '@nestjs/common';

@Controller('users')
export class UsersController {
	
	@Get()
	getAllUsers(@Res() res) {
		res.json([]);
	}
}
```
???
В большинстве случаев нам не нужны все 3 объекта от express: request, response, next в аргументах.
Иногда достаточно только response. И в nestjs есть набор декораторов для аргументов, которые позволяют получить нужный объект из express.
---
# Request Object

.fullwidth[
| Nest Decorators            | Express Object                     |
|:---------------------------|:-----------------------------------|
| `@Request()`               | `req`                              |
| `@Response()`              | `res`                              |
| `@Next()`                  | `next`                             |
| `@Session()`               | `req.session`                      |
| `@Param(param?: string)`   | `req.params / req.params[param]`   |
| `@Body(param?: string)`    | `req.body / req.body[param]`       |
| `@Query(param?: string)`   | `req.query / req.query[param]`     |
| `@Headers(param?: string)` | `req.headers / req.headers[param]` |
]
???
Вот этот список декораторов-оберток над объектами express.
Т.е. мы можем достать разные свойства из request body, headers и других свойств.
Если этого недостаточно...
---
# Custom Decorators
```typescript
import { createRouteParamDecorator } from '@nestjs/common';

export const User = createRouteParamDecorator((data, req) => {
	return req.user;
});
```
#### Using in Controller
```typescript
@Get()
async findOne(@User() user: UserEntity) {
	console.log(user);
}
```
???
То можно создать свой, и достать из объекта request нужные данные.
---
# Component
```typescript
import { Component } from '@nestjs/common';

@Component()
export class UsersService {
    
    private users = [
        { id: 1, name: 'John Doe' },
        { id: 2, name: 'Alice Caeiro' },
        { id: 3, name: 'Who Knows' },
    ];

    getAllUsers() {
        return Promise.resolve(this.users);
    }
    
    addUser(user) {
        this.users.push(user);
        return Promise.resolve();
    }
}
```
???
Далее сделаем компонент - сервис пользователей, класс с декоратором `@Component()`, который умеет получать пользователей, здесь данные жестко закодированы, но на практике, здесь был бы какой-нибудь UserRepository компонент, который ходил бы хранилище (в базу данных) и получал данные.
(Компоненты - это все остальное, т.е. сервисы, репозитории, фабрики, хелперы и т.п. Эти компоненты могут быть инжектированы в контрОллеры и другие компоненты)
---
# Let's use it
```typescript
@Controller('users')
export class UsersController {
    
    constructor(private usersService: UsersService) { }

    @Get()
    async getAllUsers() {
        const result = await this.usersService.getAllUsers();
        return result;
    }
    
    @Post()
    async addUser(req, res) {
        const result = await this.usersService.addUser(req.body.user);
        res.status(201).json(result);
    }
```
???
И теперь мы может заинжектить его в контрОллер.  
Из метода можно вернуть объект или массив, и результат будет автоматически преобразован в json, и будут выставлены соответствующие заголовки.  
Это второй и рекомендуемый способ выдачи ответа клиента. (На слайде [15](#p15) нерекомендуемый способ)   
Приложение все еще не будет работать, как надо...
---
# `req.body.user`
We are trying to extract request body (`req.body.user`) without `body-parser`

```ts
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import * as bodyParser from 'body-parser';

const express = express();
express.use(bodyParser.json({ strict: false }));

const app = await NestFactory.create(AppModule, express);
await app.listen();
```
???
... потому что мы обращаемся к `request.body.user`, но `body-parser` не подключен.  
`NestFactory.create` принимает вторым параметром инстанс express-a, в который предварительно можно подключить модуль body-parser.
---
# Adding to Module
```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
    controllers: [UsersController],
    components: [UsersService],
})
export class ApplicationModule { }
```
???
И как и в остальных случаях, сервис надо добавить в модуль.  
---
# UsersModule
By default, modules **encapsulate** each dependency. It means, that it is not possible to use its components / controllers outside module.
```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
    controllers: [UsersController],
    components: [UsersService],
    exports: [UsersService],
})
export class UsersModule { }
```
```ts
@Module({
    imports: [UsersModule],
})
export class ApplicationModule { }
```
???
Модуль - это обычный класс с декоратором Module.  
Все компоненты этого модуля доступны только внутри этого модуля и недоступны снаружи, до тех пор пока явно это не укажем в свойстве `exports`.
---
# Middlewares
![](https://docs.nestjs.com/assets/Middlewares_1.png)
Middleware is a function, which is called before route handler. Middleware functions can perform the following tasks:
* Execute any code
* Make changes to the request and the response objects
* End the request-response cycle
* Call the next middleware function in the stack
* If the current middleware function does not end the request-response cycle, it must call next() to pass control to the next middleware function. Otherwise, the request will be left hanging
???
Мидлвар это функция которая вызывается перед роутингом. Эта функция имеет доступ к объектам `request` и `response`.  
Также может выступать в качестве барьера - если не вызовется `next()`, то метод в контрОллере никогда не вызовется.
---
# LoggerMiddleware
```typescript
import { Middleware, NestMiddleware, ExpressMiddleware } from '@nestjs/common';

@Middleware()
export class LoggerMiddleware implements NestMiddleware {
  
    resolve(...args: any[]): ExpressMiddleware {
        return (req, res, next) => {
            console.log('Request...');
            next();
        };
    }

}
```
???
Мидлвар это класс с соответствующим декоратором, который должен имплементировать интерфейс NestMiddleware с одним методом resolve,
и этот метод должен вернуть функцию, сигнатура которой соответствует миддлвару в express.  
(Это можно записать в сокращенном виде, как функцию)
---
# Where to put middlewares?

```typescript
import { Module, NestModule, RequestMethod } from '@nestjs/common';

@Module({
    // ...
})
export class ApplicationModule implements NestModule { 

    configure(consumer: MiddlewaresConsumer): void {
        consumer.apply(LoggerMiddleware).forRoutes(
            { path: '/users', method: RequestMethod.GET },
        );
        // Or...
        consumer.apply(LoggerMiddleware).forRoutes(UsersController);
    }

}
```
???
Как его применить? Мы должны в нашем модуле добавить функцию `configure`, которая принимает какой-то контекст. И мы говорим, примени этот логгер мидлвар для роута,
которые начинаются со /users (слэш юзерс).  
Можно указать, только ПОСТ. Или можно в методе forRoutes указать класс - тогда для всех методов в этом классе, будет применяться этот мидлвар.  
В мидлвары можно внедрять зависимости - компоненты, также как и в контрОллер.
---
## Pipes
A pipe transforms the input data to the desired output.
Also, it could overtake the validation responsibility.
<img src="https://docs.nestjs.com/assets/Pipe_1.png" />
???
Следующая фича - пайпы.  
Похожи на ангуляровские пайпы, их задача преобразовать один формат в другой.  
Также пайпы могут использоваться для валидации входных данных.  
---
# Pipes
```typescript
@Post()
public async addUser(@Res() res: Response, @Body() createUser: CreateUserDto) {
    const result = await this.usersService.addUser(createUser);
    res.status(201).json(result);
}
```
```typescript
class CreateUserDto {
    name: string;
}
```
???
Например, у нас есть эндпоинт - создание пользователя, метод принимает модель CreateUserDto.
Было бы неплохо всю эту модель проверить. Т.е. метод ждет валидную модель, и мы можем эту логику делегировать пайпам.
---
# Pipes
```typescript
import * as Joi from 'joi';

export const userSchema = Joi.object().keys({
    name: Joi.string().alphanum().min(3).max(30).required(),
}).required();
```
???
https://youtu.be/Z9KkMRd8Blc?t=1106
Есть такая библиотека joi, которая умеет проверять объекты на соответствие схеме.
Здесь мы говорим что у нас должен быть объект, в котором есть поле, name, и это должна быть строка, в которой содержатся только буквы и цифры,
длиной минимум 3 и максимум 30, это поле обязательно, и сам объект обязателен.
---
# Pipes
```typescript
import { PipeTransform, Pipe, HttpStatus } from '@nestjs/common';
import { HttpException } from '@nestjs/common';
import * as Joi from 'joi';

@Pipe()
export class JoiValidatorPipe implements PipeTransform {
    
    constructor(private schema: Joi.ObjectSchema) { }

    public transform(value, metadata) {
        const { error } = Joi.validate(value, this.schema);
        if (error) {
            throw new HttpException(error, HttpStatus.BAD_REQUEST);
        }
        return value;
    }
}
```
???
Пайп, это класс с декоратором Pipe() и который реализовывает интерфейс `PipeTransform`.
Это и. с одним методом transform(), который принимает value это что нам пришло, и метод должен вернуть новое значение.
В этом примере пайп работает как валидатор, мы проверяем значение с помощью библиотеки joi, если есть ошибки выбрасываем эксепшн, ошибку 409,
если нет ошибок - ничего не делаем.
---
# Pipes usage
```typescript
@Post()
@UsePipes(new JoiValidatorPipe(schema))
public async addUser( @Res() res: Response, @Body() user: CreateUserDto) {
    const result = await this.usersService.addUser(user)
    res.status(201).json(result);
}
```
???
Чтобы применить этот пайп, надо использовать декоратор UsePipes, и передать туда инстансы пайпов.
Один или несколько.
---
# Pipes usage

```typescript
@UsePipes(new ValidationPipe(...))
class UsersController {
    // ...
}
```
```typescript
async addUser(@Body(new ValidationPipe(...)) user: CreateUserDto) {
    this.usersService.addUser(user);
}
```
???
Пайп декоратор можно повесить на метод, а можно декорировать весь класс контрОллера, тогда пайп будет применяться на всех методах.  
Также можно передать пайп в декоратор Body() как параметр.
---
# Exception Filters
In Nest there's an exceptions layer, which responsibility is to catch the unhandled exceptions and return the appropriate response to the end-user.
???
Следующий слой - фильтры (exception filters).  
Их задача, это ловить ловить исключения, и выдавать клиенту измененный ответ.
---
# HttpException

```typescript
import { Controller, Get, HttpException } from '@nestjs/common';

@Controller()
export class UsersController {

    // ...
    
    @Get('users/:id')
    async findById(@Param('id') userId: number): User {
        const user = await this.usersService.findById(userId);
        if (!user) {
            throw new HttpException('User not found', HttpStatus.NOT_FOUND);
        }
        return user;
    }
}
```
???
Пример. По запросу нам надо найти какого-то юзера по ид, и такой не нашелся, в этом случае мы должны вернуть 404-ую ошибку.
---
# Exception Filter
```typescript
import { ExceptionFilter, Catch, HttpException } from '@nestjs/common';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
    
    catch(exception: HttpException, response) {
        const status = exception.getStatus();

        response
          .status(status)
          .json({
               statusCode: status,
               message: `It's a message from the exception filter`,
          });
    }
}
```
???
Как в этом случает будет выглядеть exception filter.  
Это класс, который реализовывает интерфейс ExceptionFilter с одним методом catch(), в методе доступен объект response.
В декоратор `@Catch` передаются референсы классов, которые этот фильтр будет ловить.
---
# Filter usage
```typescript
@Controller()
@UseFilters(new HttpExceptionFilter())
export class UsersController {
    // ...
}
```
???
Подключить фильтры можно декоратором `@UseFilters`.  
Можно целиком на класс или на конкретный метод (как и в случае c пайпами).
---
#### Interceptors
.image90[
![](https://docs.nestjs.com/assets/Interceptors_1.png)
]
* bind extra logic before / after method execution
* transform the result returned from the function
* transform the exception thrown from the function
* completely override the function depending on the chosen conditions (e.g. caching purposes)
???
Следующая абстракция - это интерсепторы. Их задача привязать дополнительную логику до или после вызова метода контрОллера.
Преобразовать исключение или результат возвращаемый из метода.  
А можно полность переписать результат выполнения метода контрОллера.
---
# Interceptor Example
```typescript
import { Interceptor, NestInterceptor, ExecutionContext } from '@nestjs/common';
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/do';

@Interceptor()
export class LoggingInterceptor implements NestInterceptor {
  intercept(dataOrRequest, context: ExecutionContext, stream$: Observable<any>): Observable<any> {
    console.log('Before...');
    const now = Date.now();

    return stream$.do(
      () => console.log(`After... ${Date.now() - now}ms`),
    );
  }
}
```
???
Пример интерсептора. Это класс с декоратором `@Interceptor()` который реализует интерфейс NestInterceptor.  
1-ый аргумент, в случае веб-приложения, это HttpRequest (express), если это микросервис или веб-сокет, то это какие-то данные.  
context - это объект с двумя свойствами: parent (ссылка контроллер) и handler (ссылка метод).  
И последний stream$ - это observable из библиотеки RxJS.
---
# Guards
![](https://docs.nestjs.com/assets/Guards_1.png)
* Guards have a single responsibility
* They determine whether request should be handled by route handler or not
???
Гварды.
Единственная задача гвардов - это ответить на вопрос,
может ли метод контрОллера обработать текущий HttpRequest.
---
# RolesGuard
```typescript
import { Guard, CanActivate, ExecutionContext } from '@nestjs/common';
import { Observable } from 'rxjs/Observable';

@Guard()
export class RolesGuard implements CanActivate {
  canActivate(dataOrRequest, context: ExecutionContext)
          : boolean | Promise<boolean> | Observable<boolean> {
    // Validate user logic...
    return true;
  }
}
```
???
Guard это класс с соответствующим декоратором и реализует CanActivate интерфейс с одним методом `canActivate`.  
Этот метод выполняется после мидлваров, но до пайпов.
Если возвращается `false` (синхронно или асинхронно), то клиенту вернется вернется 403 ошибка.  
Аргументы в методе, такие же как в интерсепторе - HttpRequest или данные, и объект со ссылками на метод и контрОллер.
---
# Guards Usage
```typescript
@Controller('users')
@UseGuards(RolesGuard)
export class UsersController { }
```
```typescript
const app = await NestFactory.create(ApplicationModule);
app.useGlobalGuards(new RolesGuard());
```
???
Использование гвардов возможно на уровне отдельных методов контроллера, на всем классе контроллера или глобально.
---
# NestJS CLI
`@nestjs/schematics` Nestjs project and architecture element generation based on `@angular-devkit/schematics` engine.
* Application
* Controller
* Exception
* Guard
* Interceptor
* Middleware
* Module
* Pipe
* Service
???
У nest.js есть консольный инструмент, с помощью него можно генерировать компоненты для приложения: Controller, Interceptor и т.д.
---
# Socket Gateway
```typescript
import { WebSocketGateway, SubscribeMessage, WsResponse, WebSocketServer, WsException } from '@nestjs/websockets';
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/observable/from';
import 'rxjs/add/operator/map';

@WebSocketGateway()
export class EventsGateway {
  
  @WebSocketServer() server;

  @SubscribeMessage('events')
  onEvent(sender, data): Observable<WsResponse<number>> {
    const event = 'events';
    const response = [1, 2, 3];

    return Observable.from(response).map(res => ({ event, data: res }));
  }
} 
```
???
С вебсокетами работать очень просто, у нас есть декоратор `WebSocketGateway`, `WebSocketServer` (фактически это инкапсулированный socket.io сервер).
Декоратор `SubscribeMessage` - подписка на событие. Здесь мы подписываемся на событие `events`.
Можно написать свой адаптер, и работать с нативными веб-сокетами или с чем-то другим.
---
# Microservices
```typescript
import { Controller, Get, UseInterceptors } from '@nestjs/common';
import { ClientProxy, Client, Transport, MessagePattern } from '@nestjs/microservices';
import { Observable } from 'rxjs/Observable';

@Controller()
export class MathController {
  
  @Client({ transport: Transport.TCP, port: 43210 })
  client: ClientProxy;

  @Get()
  call(): Observable<number> {
    const pattern = { cmd: 'sum' };
    const data = [1, 2, 3, 4, 5];
    return this.client.send<number>(pattern, data);
  }

  // In microservice
  @MessagePattern({ cmd: 'sum' })
  sum(data: number[]): number {
    return (data || []).reduce((a, b) => a + b);
  }
}
```
???
Как выглядит микросервисы. К примеру, есть какой-то сервис который общается по TCP, порт 43210.
Мы берем контрОллер, настраиваем декоратор `@Client`.
Отправляем сервису команду `sum` с данными, в сервисе будет вызван метод, который декорирован `@MessagePattern` с нашей командой `sum`.
---
# Unit Testing
```typescript
import { Test } from '@nestjs/testing';
import { UserController } from './user.controller';
import { UserService } from './user.service';

const module = await Test.createTestingModule({
    controllers: [UserController],
    components: [
        { provide: UserService, useValue: mockUserService },
    ],
}).compile();

const userController = module.get<UserController>(UserController);
```
???
В nestjs есть специальные инструменты для тестирования.  
Можно взять наш модуль и подменить в нем конкретные реализации компонентов своими.  
С помощью метода `get` можно получить любой инстанс и протестировать его.
---
# End of Overview

* https://nestjs.com/
* https://unlight.github.io/nestjs-talk/presentation.html
* https://videoportal.epam.com/video/PoAXPZa8
* https://kamilmysliwiec.com/nest-release-canditate-is-here-introduction-modern-node-js-framework
* https://github.com/juliandavidmr/awesome-nest
* https://angularcamp.tech/workshops/scalable-nodejs-with-nest/
