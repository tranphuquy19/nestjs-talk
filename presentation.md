class: center, middle, inverse
background-image: url(https://nestjs.com/img/cat-header.png)
# Nest.js Overview
.footnote[March 2018]
???
Обзор Nest.js  
Ф. = фреймворк;
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
Писать приложения можно TypeScript, можно на JavaScript, но рекомендуется TypeScript (потому что сам ф. на нем написан).  
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
Какие фичи ф. предоставляет:  
Основан на express, поэтому вся экосистема express в вашем распоряжении.  
Dependency Injection, несколько слоев (Exception Layer, Guards, Interceptors, Pipes)  
Инструменты для тестирования  
Вебсокеты и Микросервисы
---

# Core Concepts
* **Modules** - logical part of the application which lives within the same domain boundary
* **Controlers** - is responsible for handling incoming requests, and return a response to the client
* **Components** - almost everything is a component – service, repository, provider etc.
???
Строительные блоки для приложения.
Модули - это какая инкапсулированная часть приложения, например, пользователи.  
КонтрОллеры ответственны за обработку запросов от клиенты, и возврата ответов.  
Компоненты - это все остальное, т.е. сервисы, репозитории, фабрики, хелперы и т.п. Эти компоненты могут быть инжектированы в контрОллеры и другие компоненты.
---

# Module
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
???
Модуль - это класс с декоратором Module.  
Все компоненты этого модуля доступны только внутри этого модуля и недоступны снаружи, до тех пор пока явно это не укажем в свойстве exports.
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
Компоненты - это все остальное, т.е. сервисы, репозитории, фабрики, хелперы и т.п.  
Эти компоненты могут быть инжектированы в контрОллеры и другие компоненты.  
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

const app = NestFactory.create(ApplicationModule);
app.listen(3000, () => console.log('Application is listening on port 3000'));
```
???
Итак, чтобы запустить nest.js приложение создать как минимум один модуль, я его назвал `ApplicationModule`, и он пустой.  
И передать его в `NestFactory.create()`
---

# First Controller
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
Аргументы которые приходят в метод, express-овские объекты request, response и next.  
---

# Endpoints
```
GET: users
GET: users/:id 
POST: users
```
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
Мы получим следующие эндпоинты.
Пути можно сократить на users и записать следующим образом.
Т.е. префикс для эндпоинта мы переместили в параметр декоратора.
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
import { Controller, Get, Req } from '@nestjs/common';

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
Иногда достаточно только response. И в nest.js есть набор декораторов для аргументов, которые позволяют получить нужный объект из express.
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
Это список декораторов-оберток над объектами экспресса.
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
То можно создать свой, и выдернуть из объекта request нужные данные.
---

# Component
```typescript
import { Component } from '@nestjs/common';

@Component()
export class UsersService {
    
    private users = [
        { id: 1, name: "John Doe" },
        { id: 2, name: "Alice Caeiro" },
        { id: 3, name: "Who Knows" },
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
Компоненты - это все остальное, т.е. сервисы, репозитории, фабрики, хелперы и т.п. Эти компоненты могут быть инжектированы в контрОллеры и другие компоненты.  
Вот мы сделали сервис пользователей, который умеет получать пользователей, здесь данные жетско закодированы, но на практике, здесь был бы какой-нибудь UserRepository компонент, который ходил бы хранилище (в базу данных) и получал данные.
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
        const msg = await this.usersService.getUser(req.body.user);
        res.status(201).json(msg);
    }
```
???
И следующим образом можно использовать его в контрОллере.
Из метода можно вернуть объект или массив, и результат будет автоматически преобразован в json, и будут выставлены соответствующие заголовки.
Это второй и рекомендуемы способ выдачи ответа клиента.
(На слайде 15 нерекомендуемый способ)
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
---

# Middlewares
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
Мидлвар это класс с соответствующим декоратором @Middleware(), который должен имплементировать один метод resolve,
и этот метод должен вернуть функцию.
---

# Middlewares

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
Далее мы должны в нашем модуле добавить функцию configure, которая принимает какой-то контекст. И мы говорим контексту примени этот логгер мидлвар для роута которые начинаются со /users (слэш юзерс).
(В мидлвары можно внедрять зависимости - компоненты)
---

# The pipes