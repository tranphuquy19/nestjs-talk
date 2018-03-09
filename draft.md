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


# Nest v5 roadmap
???
https://github.com/nestjs/nest/pull/489