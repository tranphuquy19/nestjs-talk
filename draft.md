# Road to version 5
* https://github.com/nestjs/nest/pull/489
* https://docs.nestjs.com/v5/
* Switch from `@Component()`, `@Middleware()`, `@Interceptor()`, and `@Guard()` to `@Injectable()`
* HTTP server independence (could be used with such libraries like fastify, polka, or connect)
* Development experience HMR webpack (even 300% faster rebuilds)
* Mcroservices - new strategies - NATS, MQTT, and GRPC
???

---

# Controller
The controllers layer is responsible for handling incoming HTTP requests.
```typescript
import { Controller, Get } from '@nestjs/common';

@Controller()
export class UsersController {
	
	@Get('users')
	getAllUsers() {
		return [];
	}
}
```
???
Контроллер это класс, с декоратором Controller.  
КонтрОлеры принимают http request и их обрабатывают.  
На методы можно добавить декораторы get, post, delete и т.п.
---
# Component

```typescript
import { Component } from '@nestjs/common';

@Component()
export class UsersService {

	getAllUsers() {
		return [];
	}
}
```
???
Классы с декоратором @Component это будут сервисы, репозитории, фабрики, хелперы и т.п.  
Эти компоненты могут быть инжектированы в контрОллеры и другие компоненты. 
---

# Global Filters
```typescript
const app = await NestFactory.create(ApplicationModule);
app.useGlobalFilters(new LoggerExceptionFilter());
```
???
Еще есть глобальные фильтры, которые применяются ко всему приложению.
---

# Global Interceptors
```typescript
const app = await NestFactory.create(ApplicationModule);
app.useGlobalInterceptors(new EventsInterceptor());
```
???
И интерсепторы тоже можно использовать глобально на всем приложении.
---

# Controller
```typescript
import { Controller, Get } from '@nestjs/common';

@Controller()
export class UsersController {
	
	@Get('users')
	getAllUsers() {
		return [];
	}
}
```

# Controller
The controllers layer is responsible for handling incoming HTTP requests.
```
import { Controller, Get } from '@nestjs/common';

@Controller()
export class UsersController {
    
    @Get('users')
    getAllUsers(req, res, next) {
    	return [];
    }

}
```
???
Контроллер это класс, с декоратором Controller.  
КонтрОлеры принимают http request и их обрабатывают.  
На методы можно добавить декораторы get, post, delete и т.п.
Аргументы которые приходят в метод, express-овские объекты request, response и next.


---

---

???
Module - это класс с декоратором Module пример, все суб-компоненты этого м. доступны только внутри и недоступны снаруже, чтобы были надо указать экспортс
Modules: The @Module() decorator provides metadata, which Nest uses to organize the application structure.
Controllers: The controllers layer is responsible for handling incoming requests, and return a response to the client.
Components: Almost everything is a component – Service, Repository, Factory, Helper […] and they can be injected into controllers or into other components through constructor.
---
