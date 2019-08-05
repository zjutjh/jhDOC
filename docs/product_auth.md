## 项目说明

为对接以后业务或者第三方系统，建立一套OAuth2.0的用户认证系统，便于其他系统对接用户中心，并管理官方应用以及第三方应用的授权和提供部分数据存储服务及API代理服务。

## 系统架构

### 数据库架构

#### 用户信息

!!! summary "ver 0.1.0"

    用户最基本的信息

    ```python
    model('User')
    @unique
    uid: CharField(max_length=64)  # 唯一标识名，一般指精弘通行证账号
    password: CharField(max_length=128) # 密码，采用AES加密存储
    nickname: CharField(max_length=64)  # 昵称，1-32个任意字符
    email: CharField(max_length=64)  # 邮箱
    create_time: DateTimeField()  # 创建时间
    user_type: PostiveSmallIntegerField(default=0, choices=
        (0, 'nomral'),(1, 'admin'),(2, 'superadmin'))  # 用户类型
    ```


#### 用户活动信息

!!! summary "ver 0.1.0"


    ```python
    model('UserActivity')
    @unique
    user: ForeignKey(User, related_name='activities', on_delete=models.CASCADE)  # 活动指向的用户
    software: CharField(max_length=32)  # 对应的软件名称，默认为'index'
    access_ip: CharField(max_length=32)  # 最后活动的ip地址
    device_type: CharField(max_length=32)  # 设备类型的名称，默认支持'mobile','pc','web','pad'四个平台的设备
    access_time: DateTimeField()  # 最后活动的时间
    code: CharField(max_length=32)  # 第三方登录验证需要的code
    state: CharField(max_length)  # 状态，一般分为'success','fail','noinitialize','freeze'几种状态。
    ```


    !!! info "token"
        该表并没有存储token信息，实际上，发给用户的信息将会转译成token

        token生成方式：生成字符串，格式为`type::useruid::software::device_type::access_time`，例如，然后使用AES方式加密该字符串。

        除了'test'和'index'外，其他支持的软件需要进行后台注册。

        ```
        login::123143123123ac123::community::mobile::1231231231
        login::123143123123ac123::community::pc::1231231231
        ```

#### 用户绑定信息

!!! summary "ver0.1.0"

     该表用于记录用户的绑定信息，用于提供服务和使用第三方信息登录该软件。

    !!! tip "唯一绑定"
        每个第三方用户绑定是唯一的，即只能绑定在一个用户上，若要解绑，需要验证其他信息。

    ```python
    model('UserBinding')
    @unique
    user: ForeignKey(User, related_name='bindings', on_delete=models.CASCADE)  # 外键，对应持有该绑定信息的用户
    name: CharField(max_length=128)  # 绑定平台的名称，绑定的平台需要访问配置信息。
    uid: CharField(max_length=128)  # 唯一标识符，注意，此字段是唯一绑定的关键信息
    password: CharField(max_length=128)  # 绑定的密码
    state: PositiveSmallIntegerField(default=0, choices=
        (0, 'fail'), (1, 'success'))  # 绑定状态，'success','fail'.
    ```

#### 用户日志

!!! summary "ver 0.1.0"

    用户的日志信息，每次登录/自动登录或者请求都可能往日志里写一条记录。

    ```python
    model('UserLog')
    @unique
    user: ForeignKey(User, related_name='logs')  # 外键，对应持有该日志的用户
    action_type: CharField(max_length=64)  # 用户日志类别，所有兼容的类别都会在设置内写出
    time: DateTimeField()  # 打log的时间
    content: TextField()  # 用户日志内容
    ```

    预设的日志信息类别、格式和说明
    
    | action_type | format | description |
    | :---: | :---: | :--- :
    | login | state=enumof(success/fail)::software=str::access_ip=str::access_time=str | 用户登录活动 |

#### 全局设置表

!!! summary "ver 0.1.0"

    ```python
    model('Settings')
    @unique
    name: CharField(max_length=64)  # 设置的名称
    write: CharField(max_length=64)  # 写权限的级别
    content: TextField()  # 内容
    group: CharField(max_length=32)  # 所在的组
    description: TextField()  # 设置的描述
    declare: TextField()  # 关于字段类型和范围的描述
    ```

#### 应用架构

!!! summary "ver 0.1.0"

    ```python
    model('AppScheme')
    @unique
    name: CharField(max_length=64)  # 应用的名称，只能包含数字，大小写英文，下划线。（长度限制为2~32个字符）
    owner: ForeignKey(User, related_name='apps', on_delete=models.CASCADE)  # 软件所有者的名称
    admins: MultiForeginKey(User, related_name='admin_apps', on_delete=models.CASCADE)  # 软件管理者名单
    level: PositiveSmallIntegerField(default=0, choices=
        (0, 'authority'), (1, 'third'))  # 标识软件是不是第三方
    description: TextField()  # 应用的描述
    tags: CharField(max_length=256)  # 应用的标签
    appKey: CharField(max_length=128)  # 应用的密钥，只有开发者知道
    state: PostiveSmallIntegerField(default=0, choices=
        (0, 'closed'), (1, 'open'), (2, 'banned'), (3, 'noauth'))  # 标识应用的状态，'banned'标识应用被封禁，'noauth'标识还没有完成审批。
    allowed_auths: MultiForeignKey(AppAuth, related_name='apps', on_delete=models.CASCADE)  # 允许使用的权限
    ```

#### 应用权限管理

!!! summary "0.1.0"

    ```python
    model('AppAuth')
    @unique
    name: CharField(max_length=64)  # 权限的名称
    owner: ForeignKey(AppScheme, related_name='auths')  # 权限的拥有者
    ```

#### API代理

!!! summary "0.1.0"

    ```python
    model('AppProxy')
    @unique
    name: CharField(max_length=64)  # 需要代理的名称，例如(zfedu)等等。
    target: TextField(max_length=64)  # 匹配后目标路径的名称    
    ```

### 模块设计

#### 用户模块

包含用户的登录，自动登录，第三方登录，用户激活，忘记密码重新激活。

#### 应用模块

应用注册，应用权限修改，应用设置，开发者应用设置管理

