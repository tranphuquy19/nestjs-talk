* Я могу из контроллера вернуть observable?
> Да. Промис, observable, объект, массив.

* Поддерживается ли в pipe-ах асинхронный transform()?
> Да.

* Скорость по сравнение с express?
> 79% - https://stackoverflow.com/questions/47733390/nestjs-vs-plain-express-performance/48226084

* Guard Observable какой вернет последний?
> надо посмотреть что-там во что конвертируется, скорее всего obs в промис toPromise = Obserbable().last()