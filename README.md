framework-js
============

**Структура папок**

    modules/
        ModuleName/
            config/
            controllers/
            models/
            views/
            tests/

    config/
    cache/
    log/
    web/
        js/
        img/
        css/
        index.html

По умолчанию все модули (наши и сторонние) будут в папке modules, но в конфиге можно будет указать где ещё могут лежать модули.

Например конфиг по-умолчанию
```coffeescript
modules:
  dir: 'modules'
```
т.е. даже дефолтную папку можно переименовать, например на bundles.
Пример расширенный
```coffeescript
modules:
  dir: ['modules','vendor']
```
что будет означать, что модуль нужно искать в папке modules и vendor

Хотя возможно второй какрас и нужно сделать дефолтным.

