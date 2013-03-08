## Контроллер

Контроллеры будут представляться в виде файлов в которых будет описан [модуль CommonJS](http://habrahabr.ru/post/130035/).

Пример
```javascript
this.getUsers = function(){
    return User.getAll();
};

this.getUser = function(id){
    return User.getByPk(id);
};
```
