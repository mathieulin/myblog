---
title: "TodoList"
date: 2022-06-01T00:00:00+08:00
draft: false
author: "Mathieu"
---

# TodoList

## TodoList Vue前端项目





## TodoList Django后端项目

### 1. 建立数据表

数据库的配置文件在主目录下

djangoProject\settings.py中

数据库可视化工具直接建表

+ users表存储用户id，密码

+ todolist表，由于mysql数据库无法存储json格式的文件，因此我们将用户的todo放到多行数据中显示，每一行包括todo的id（主键），用户id（外键），是否完成， 是否可删除， 标题， 开始时间，结束时间

### 2. 将数据表导出为模型

我这里用的是conda环境，所以需要在命令行中执行

首页，激活环境

```plain
conda activate django
```

然后我们建立一个app，我们就叫做TodoList了

```plain
django-admin.py startapp TodoList
# 将数据库的表的模型用inspectdb命令转换
python manage.py inspectdb > TodoList/models.py

```

然后我们在settings.py中注册这个TodoList

![](1.jpg)

```plain
python manage.py makemigrations
python manage.py migrate
```





## 前后端

#### 一大恶心之处就是跨域访问，我是这么解决的

前端项目中的main.js

```js
// main.js
import axios from 'axios'
Vue.prototype.$axios = axios
axios.defaults.baseURL = 'http://127.0.0.1:8000/api/todo';
axios.defaults.headers.post['Content-Type'] = 'application/x-www-fromurlencodeed'
axios.defaults.headers['Content-Type']='application/x-www-form-urlencoded';
```

后端项目中

```python
allowed_origins = ['http://127.0.0.1:8000']
origin = request.headers['Origin']

response = JsonResponse({'errno': 1001, 'msg': "请求方式错误"})
response["Access-Control-Allow-Origin"] = origin
return response
```

挺麻烦的，整了好久。好像有工具包可以来跨域访问，但我pip安装出问题了



#### 交互

我这里一般是前端传json格式

```js
this.$axios({
      method: 'POST',              /* 指明请求方式，可以是 get 或 post */
      url: '/loadTodo',      /* 指明后端 api 路径，由于在 main.js 已指定根路径，因此在此处只需写相对路由 */
      data: qs.stringify({        /* 需要向后端传输的数据，此处使用 qs.stringify 将 json 数据序列化以发送后端 */
          uid:this.$store.state.user.username
      })
    })
    .then(result => {                  /* res 是 response 的缩写 */
      if (result.data.errno === 0)       /* res.data 是后端返回的数据对象 */
        console.log(result.data.res);
      else
        alert("获取失败！");
    })
    .catch(err => {                 /* 请求若出现路由找不到等其它异常，则在终端输出错误信息 */
      console.log(err);
    })
```

然后后端

```python
@csrf_exempt
def loadTodo(request):
    allowed_origins = ['http://127.0.0.1:8000']
    origin = request.headers['Origin']
    if request.method == 'POST':
        print("i am here")
        uid = request.POST.get('uid')
        print(uid)
        # print(request.session['uid'])
        todos = Todolist.objects.all()
        res = []
        for todo in todos:
            if todo.uid == uid:
                todoJson = {"uid": todo.uid, "id": todo.id, "title": todo.title,
                            "isDone": todo.isdone, "operated": todo.operated,
                            "date_begin": todo.date_begin, "date_end": todo.date_end}
                res.append(todoJson)
        res = {"res": res}
        response = JsonResponse({'errno': 0, 'data': res})
        response["Access-Control-Allow-Origin"] = origin
        return response
    else:
        response = JsonResponse({'errno': 1001, 'msg': "请求方式错误"})
        response["Access-Control-Allow-Origin"] = origin
        return response
```



使用request.POST.get来获取值，然后将response传回前端

前端result.data.data可以接收传回来的信息