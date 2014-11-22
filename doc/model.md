Модель
======

- Основной класс Model
  - Create
  - Read
  - Update
  - Delete
  - Обновление и удаление без создания модели
- Конфигурационный файл
- Генерация base файлов классов

## Основной класс Model

Все модели должны наследоваться от класса Model, который содержит в себе CRUD методы для работы с БД. 
Заточка на две базы данных MySQL и Mongo. Не нужно создавать тяжёлую ORM, достаточно сделать методы для типичных задач, а сложные запросы делать напрямую с валидируемыми параметрами, что бы избежать иньекций.

Все CRUD методы возвращают промис объект.

### Create

Добавлять модели в базу можно через метод `insert()`. Почему не `save()`? По имени метода должно быть очевидно, какой запрос пойдёт в базу.

```javascript
var user = new User();
user
  .setName('Max')
  .insert()
;
```

Для того что бы пачкой сохранить несколько моделей можно воспользоваться методом `insert()` самого класса
```javascript
var user1 = new User();
var user2 = new User();
User.insert([user1, user2]);
```

### Read

Все методы выборки данных:

`Model.query(string, [params])`
```javascript
User.query("select * from user where name like '%{name}%'", {name: 'value'})
// можно начинать сразу с join или where
User.query("where id = {id}", {id: '1;select *'})
```
- `findAll([whereParams], [options])`
- `findFirst([whereParams], [options])`
- `count([whereParams], [options])`

А также методы для поиска по ключу

- getByPK(pk1, pk2, ...)

Пример поиска постов по id юзера
```coffeescript
posts = Post.find({user_id: 1})
```
Или по id (первичного ключа) самого поста
```coffeescript
post = Post.getByPK(100)
# что бы не писать вот так
post = Post.findOne({id: 100})
```

### Update

Обновить модель можно методом update()
```coffeescript
post = Post.getByPK(100)
post.setTitle('JS')
post.update()
```

### Delete

Удалить модель можно методом remove()
```coffeescript
post = Post.getByPK(100)
post.remove()
```

### Обновление и удаление без создания модели

Обновление и удаление данных в БД без создания модели, можно сделать через методы update() и remove() самого класса модели, который практически полстью копируют методы коллекции в MongoDB

- [update()](http://docs.mongodb.org/manual/reference/method/db.collection.update/)
- [remove()](http://docs.mongodb.org/manual/reference/method/db.collection.remove/)

Разница состоит только в том, если вы используете реляционную БД (MySQL, PostgreSQL), то вторым параметром метода update() может быть только объект без операторов таких как $set, параметр multi всегда равен true, параметр upsert вообще отсутствует.
Если вы используете MongoDB, то оба метода работают абсолютно идентично родным методам монго.

Пример в котором мы меняем статус всем постам на "published", которые принадлежать пользователю 10
```coffeescript
Post.update({user_id: 10}, {status: 'published'})
```
Пример в котором мы удаляем все посты имеющие статус "archive"
```coffeescript
Post.remove({status: 'archive'})
```

## Конфигурационный файл

Модели для каждого модуля будут описываться в конфиг файле.
На основе этого файла будут генерироваться классы.

Пример конфига
```coffeescript
@models =
    user:
        id:   {type: Number, index: 'primary'}
        name: {type: String}

    post:
        id:     {type: Number, index: 'primary'}
        title:  {type: String}
        status: {type: String}
        user_id:
            type: Number
            relation:
                model: 'user'
                field: 'id'
```

## Генерация base файлов классов

С помощью можно будет сгенерировать base файлы моделей.
В отличии от языков с наследованием классов, в JS просто генерируется класс с основными методами, а все кастомные методы потом добавляются в прототип.

Пример файла **models/base/User.coffee**
```coffeescript
class User extends Model
    getId:    -> @get('id')
    getName:  -> @get('name')
    getPosts: -> Post.find({user_id: @getId()})
```
Пример файла **models/base/Post.coffee**
```coffeescript
class Post extends Model
    getId:     -> @get('id')
    getTitle:  -> @get('title')
    getStatus: -> @get('status')
    getUserId: -> @get('user_id')
    getUser:   -> User.findOne({id: @getUserId()})
```
Пример файла **models/Post.coffee**
```coffeescript
Post::getUserPosts = ->
    Post.find({user_id: @getUserId()})
```
