# USTC-Software 2019 iGEM 后端测试题

**DDL: 2019 年 7 月 10 日**

## 任务

你需要：

1. Fork 此仓库至你的账号。
2. 使用 Django，完成如下所述的功能。
3. 在本文件末尾为前端组的同学撰写一份说明文档，使他们了解你的应用的接口等信息。
4. 发起 Pull Request。（请在 PR 时写上你的姓名、学号）

### 约定

为了节约大家的时间，请尽量使用默认配置的组件：

1. 使用 Python 3.6+ 与 Django 2.2。如果你使用了 Python 3.7 的特性，请在报告中注明。
2. 使用默认的数据库 (SQLite)、默认的 HTTP 服务器和默认的端口。
3. 不需要使用 template，不需要编写 HTML 文件。所有接口的返回内容为 JSON。

## 目标功能

### 必须完成的功能

- 登录账户
- 注册账户
- 退出 (log out) 账户
- 显示与修改账户的个人信息

### 可以选做的功能

- 「给未来的自己发送消息」，分为两个接口：
  - 接口 1: 用户可以输入一条消息与这条消息隐藏的时间。例如，某条消息隐藏时间为 5 分钟，则在用户提交 5 分钟之后，这条消息才能在接口 2 中显示。
  - 接口 2: 列出当前用户的所有信息和状态（时间是否达到）。如果某条信息的隐藏时间已经达到，可以显示信息内容，否则不应该显示信息内容。
  - 可以在此基础上扩充更多功能。

### 加分项

这些加分项都是值得提倡的良好的开发习惯，因此我们将它们作为加分项。

- 良好的代码风格（如变量命名）会有额外加分
- 良好的注释和文档会有额外加分
- 良好的 Git 提交记录（每次提交有明确的信息）会有额外加分
- 良好的安全性、鲁棒性和可扩展性会有额外加分
- 良好的单元测试有额外加分

## 其它注意事项和提醒

- 对于以上提到的各项功能，最基础的要求是：用户输入正确的请求时，程序可以给出正确的回复。同时，对于用户发起的错误的请求，程序不会受到灾难性的破坏，不会影响其它用户的正常使用（例如，在注册账户时把已有的账户「覆盖」掉是不容许的）。

  你可以选择将这些功能做得更完善和更安全，具体内容和方式请自行决定，例如检查输入并返回有意义的错误信息等。
  
- 你可以参考[去年的后端测试题](https://github.com/volltin/USTC-Software-2018-BE-Test)，但请注意，**不要抄袭其他人的代码**，如果某段代码对你编写有帮助，请在注释中写明来源。

- 允许使用任意 PyPI 中的模块，即可以使用 `pip` 命令安装的模块。

- 我们最终的代码会在 Linux 下执行，所以如果你在使用其它操作系统开发，请谨慎使用依赖于特定操作系统的特性。（但这里应该不会出现这种情况）

## 报告（需要完成）

请将你为前端组的同学撰写的报告放在这里。

# 总览

此程序，可以进行用户注册、登录、退出
发送一条消息给未来，并查看所有可见消息、所有消息、所有公共消息
并可以修改消息、查看某一条消息的详细信息、删除某一条消息

## Request

```get```方法，用于获取服务器中的数据。如获取用户信息，获取消息

```post```方法，用于在服务器中新建资源。如创建用户，将用户登录到系统中

```put```方法，用于在服务器中更新资源。如更新用户信息，更新消息

```delete```方法，用于在服务器中删除资源。如删除某一条消息

#### 说明

所有的```post```和```put```方法，提交的均为json数据

所有的```put```方法，必须在header中指定```content_type="application/json"```，否则将会返回430错误

正常情况下，所有的http status将被置为200，而在非程序错误导致的服务器5XX异常或者url错误导致的404错误将会以http status的方式返回，请捕获异常

## Response

所有的返回数据均为json对象（object），包含两个键值对

| 键名 | 键值含义                         |
| ---- | -------------------------------- |
| code | 返回状态                         |
| msg  | 错误原因或者从服务器中取得的内容 |

```code```对应的键值类型为int

```msg```对应的键名类型均为list（in Python）或者说array（in JSON）

如果```code```为200，则为正常返回，在```msg```中将以list的形式提供所需要的数据

如果```code```不为200，说明遇到错误。在```msg```中将以list的形式给出错误的解释

> 在登录、注册、修改密码时，往往会遇到多个错误，则需要从```msg```中取出所有错误；在其他的请求时，将只会返回1个错误。所有的错误原因以字符串的形式给出。可能会有多个错误的将会在具体的API中加以说明

# 用户账户

## 用户登录

```
/accounts/login
```

### POST

用于将用户登录到系统中

#### 请求参数

| 参数名称 | 类型 | 是否必须 | 描述   |
| -------- | ---- | -------- | ------ |
| username | str  | 是       | 用户名 |
| password | str  | 是       | 密码   |

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |


其中code参数的含义如下

| 返回值 | 含义             |
| ------ | ---------------- |
| 200    | 登录成功         |
| 400    | 用户名或密码错误 |
| 410    | 用户名不符合要求 |

用户名不符合要求，可能原因：
- 用户没有提交username字段或该字段为None
- 用户的用户名包含除字母、数字和下划线以外的字符

具体的原因已通过msg字段返回

> msg中可能会有多个错误

## 用户注册

```
/accounts/register
```

用于注册用户

### POST

#### 请求参数

| 参数名称 | 类型 | 是否必须 | 描述   |
| -------- | ---- | -------- | ------ |
| username | str  | 是       | 用户名 |
| password | str  | 是       | 密码   |

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义             |
| ------ | ---------------- |
| 200    | 注册成功         |
| 410    | 用户名不符合要求 |
| 420    | 密码不符合要求   |

用户名不符合要求，可能原因：
- 用户没有提交username字段或该字段为None
- 用户的用户名包含除字母、数字和下划线以外的字符
- 用户的用户名已经被注册过了

密码不符合要求，可能原因：
- 密码过短
- 密码过于简单

具体的原因已通过msg字段返回

> msg中可能会有多个错误

## 修改密码

```
/accounts/change_password
```


### POST

用于修改用户的密码

### 请求参数

| 参数名称     | 类型 | 是否必须 | 描述     |
| ------------ | ---- | -------- | -------- |
| old_password | str  | 是       | 旧的密码 |
| new_password | str  | 是       | 新的密码 |

### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义               |
| ------ | ------------------ |
| 200    | 修改密码成功       |
| 400    | 旧的密码错误       |
| 401    | 未登录             |
| 420    | 新的密码不符合要求 |

密码不符合要求，可能原因：
- 密码过短
- 密码过于简单

具体的原因已通过msg字段返回

> msg中可能会有多个错误

## 用户退出

```
/accounts/logout
```

### POST

用于将用户从系统中退出

#### 请求参数

无

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义     |
| ------ | -------- |
| 200    | 退出成功 |
| 401    | 未登录   |


## 用户信息

```
/accounts/profile
```

### GET

用于获取用户自身信息，如用户昵称、手机号

#### 响应数据

| 参数名称     | 类型 | 描述         |
| ------------ | ---- | ------------ |
| code         | int  | 返回状态     |
| nickname     | str  | 用户的昵称   |
| phone_number | str  | 用户的手机号 |

其中code参数的含义如下

| 返回值 | 含义                   |
| ------ | ---------------------- |
| 200    | 正常返回用户的个人信息 |
| 401    | 未登录                 |

> 返回的nickname和phone_number可能为None

### PUT

用于修改用户信息

| 参数名称     | 类型 | 是否必须 | 描述             |
| ------------ | ---- | -------- | ---------------- |
| nickname     | str  | 否       | 修改后的用户昵称 |
| phone_number | str  | 否       | 用户的手机号     |

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义                                          |
| ------ | --------------------------------------------- |
| 200    | 修改成功                                      |
| 401    | 未登录                                        |
| 402    | 手机号无效                                    |
| 430    | 无法解析请求数据为json                        |
| 431    | Header没有指定content_type="application/json" |

# 消息功能

## 创建消息

```
/msg/create
```

### POST

用于创建新的消息

#### 请求参数

| 参数名称   | 类型 | 是否必须 | 描述                            |
| ---------- | ---- | -------- | ------------------------------- |
| content    | str  | 是       | 消息内容                        |
| public     | bool | 否       | 是否公开，默认：否              |
| delay_time | str  | 否       | 延迟发布时间，格式：DD:HH:MM:SS |

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义             |
| ------ | ---------------- |
| 200    | 创建成功         |
| 401    | 未登录           |
| 410    | 延迟时间格式有误 |
| 411    | 延迟时间不能为负 |

## 显示我的消息

```
/msg/my
```

### GET

用于显示我的消息，**不包含**未到展示时间的消息

#### 响应数据

| 参数名称 | 类型 | 描述               |
| -------- | ---- | ------------------ |
| code     | int  | 返回状态           |
| msg      | list | 消息内容或错误原因 |

其中code参数的含义如下

| 返回值 | 含义     |
| ------ | -------- |
| 200    | 获取成功 |
| 401    | 未登录   |

若code为200，其中msg参数每一个元素对应一条消息的dict，内容如下

| 参数名称  | 类型 | 描述           |
| --------- | ---- | -------------- |
| content   | str  | 消息的内容     |
| edit_time | str  | 修改时间       |
| user      | str  | 作者           |
| uuid      | str  | 当前消息的uuid |

> 返回的时间时UTC时间，格式为ISO标准时间格式，使用isoformat进行格式化

## 显示我的所有消息

```
/msg/my/all
```

### GET

用于显示我的消息，**包含**未到展示时间的消息

#### 响应数据

| 参数名称 | 类型 | 描述               |
| -------- | ---- | ------------------ |
| code     | int  | 返回状态           |
| msg      | list | 消息内容或错误原因 |

其中code参数的含义如下

| 返回值 | 含义     |
| ------ | -------- |
| 200    | 获取成功 |
| 401    | 未登录   |

若code为200，其中msg参数每一个元素对应一条消息的dict，内容如下

| 参数名称  | 类型 | 描述           |
| --------- | ---- | -------------- |
| content   | str  | 消息的内容     |
| edit_time | str  | 修改时间       |
| show_time | str  | 展示时间       |
| user      | str  | 作者           |
| uuid      | str  | 当前消息的uuid |

> 返回的时间时UTC时间，格式为ISO标准时间格式，使用isoformat进行格式化

## 显示所有用户公开的消息

```
/msg/all
```

### GET

用于获取所有用户的公开的消息，**不包含**未到展示时间的消息

#### 响应数据

| 参数名称 | 类型 | 描述                              |
| -------- | ---- | --------------------------------- |
| code     | int  | 返回状态，只会返回200，即代表成功 |
| msg      | list | 消息内容                          |

若code为200，其中msg参数每一个元素对应一条消息的dict，内容如下

| 参数名称  | 类型 | 描述           |
| --------- | ---- | -------------- |
| content   | str  | 消息的内容     |
| edit_time | str  | 修改时间       |
| user      | str  | 作者           |
| uuid      | str  | 当前消息的uuid |

## 显示某一条消息的详细信息

```
/msg/<uuid>
```

### GET

可以用于获取某一条消息的作者、内容、创建时间、最后的修改时间、可以被展示的时间、是否公开、uuid

#### 响应数据

| 参数名称 | 类型 | 描述               |
| -------- | ---- | ------------------ |
| code     | int  | 返回状态           |
| msg      | dict | 消息内容或错误原因 |

其中code参数的含义如下

| 返回值 | 含义                                     |
| ------ | ---------------------------------------- |
| 200    | 获取成功                                 |
| 401    | 未登录                                   |
| 403    | 不是这条消息的作者，并且这条消息并未公开 |
| 404    | 输入的uuid有误                           |

若code为200，其中msg参数的内容如下

| 参数名称    | 类型 | 描述           |
| ----------- | ---- | -------------- |
| content     | str  | 消息的内容     |
| create_time | str  | 创建时间       |
| edit_time   | str  | 修改时间       |
| show_time   | str  | 展示时间       |
| user        | str  | 作者           |
| public      | bool | 是否公开       |
| uuid        | str  | 这条消息的uuid |

> 返回的时间时UTC时间，格式为ISO标准时间格式，使用isoformat进行格式化

### PUT

修改某条消息

#### 请求参数

| 参数名称   | 类型 | 是否必须 | 描述                            |
| ---------- | ---- | -------- | ------------------------------- |
| content    | str  | 是       | 消息内容                        |
| public     | bool | 否       | 是否公开，默认：否              |
| delay_time | str  | 否       | 延迟发布时间，格式：DD:HH:MM:SS |

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义                   |
| ------ | ---------------------- |
| 200    | 修改成功               |
| 401    | 未登录                 |
| 403    | 不是这条消息的作者     |
| 404    | 输入的uuid有误         |
| 410    | 延迟时间格式有误       |
| 411    | 延迟时间不能为负       |
| 430    | 无法解析请求数据为json |

### DELETE

删除某条消息

#### 响应数据

| 参数名称 | 类型 | 描述                   |
| -------- | ---- | ---------------------- |
| code     | int  | 返回状态               |
| msg      | list | 展示的文本或者错误原因 |

其中code参数的含义如下

| 返回值 | 含义               |
| ------ | ------------------ |
| 200    | 删除成功           |
| 401    | 未登录             |
| 403    | 不是这条消息的作者 |
| 404    | 输入的uuid有误     |