Роутинг
=======

Роутинг описывается в файле который будет импортироваться как CommonJS модуль.

```coffeescript
controller = require('../controller/default')
view = require('../view/json')

@route = [
    rule:   /\/posts$/
    action: controller.getPosts
,
    method: 'GET'
    rule:   /\/user\/(?P<user_id>\d+)\/post\/(\d+)$/
    action: controller.getPost
    convert: [
        params:  ['user_id']
        handler: User.getByPK
    ,
        params:  [1]
        handler: Post.getByPK
    ]
,
    rule:   /\/posts\/(\d+)\/comments$/
    action: controller.getPostComments
    view:   view.toJson
]
```