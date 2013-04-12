Структура папок
===============

    modules/
        ModuleName/
            config/
            model/
            controller/
            view/
            test/
    config/
    cache/
    log/
    web/
        js/
        img/
        css/
        index.html

## Папка модулей

По умолчанию все модули (наши и сторонние) будут в папке modules, но в [главном конфиг файле](doc/config.md) можно будет указать где ещё могут лежать модули.

Например конфиг по-умолчанию
```coffeescript
modules:
  dir: 'modules'
```
т.е. даже дефолтную папку можно переименовать, например на bundles.
Чтобы разделить модули на свои и сторонние их имена можно указатьв массиве
```coffeescript
modules:
  dir: ['modules','vendor']
```
что будет означать, что модуль нужно загружать из папок modules и vendor.

