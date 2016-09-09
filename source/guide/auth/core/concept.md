title: Wilddog用户
---

## 用户属性

终端用户有一组用户的基本属性：Wilddog ID,主邮箱地址,名称,照片地址，这些属性存储在该项目的用户数据库中,并且可以被用户更新。

终端用户直接添加其他属性，当时可以通过Sync中存储附加信息。

一个终端用户初次注册那个应用时,系统使用可用信息填充该用户的个人资料数据：
* 若该终端用户使用的是电子邮件地址和密码注册,则控制填充电子邮件地址属性。

* 若该终端用户是使用Oauth方式注册,则会使用Oauth提供的账户信息填充终端用户信息。

## 登录提供程序

野狗用户可以使用几种将用户登录到野狗应用:电子邮件地址与密码，Oauth方式登录，以及自定义身份认证。
同时我们也支持将多种登录方式与一个用户关联:例如,终端用户可以使用电子邮件地址与密码方式和QQ登录方式关联到同一个账户上,那么终端用户可以使用该账户下任意一种登录方式进行登录。

## 当前用户

当一个用户注册或登录时，该用户则变为身份认证实例的当前用户。野狗的身份认证实例保留该用户的状态，所以刷新页面（在浏览器中）或重启应用不会导致该用户的信息丢失。

当用户注销时，身份认证实例停止保存对用户对象的引用，不再保留其状态，所以没有当前用户。

## 用户生命周期

目前野狗使用Auth监听器监听当前用户状态。
Auth监听器会在以下情况收到通知:
* 用户进行登录
* 用户退出
* 当前的Wilddog ID Token已刷新

其中需要注意：有些操作需要最近获取的Wilddog ID Token,这些操作包括删除账户,设置主电子邮件地址和修改密码。当遇到这种情况下,除了用户重新登录外,还提供一致方式:即从该用户获取新的凭据并通过凭据进行用户对象重新认证。

## 身份认证令牌
执行野狗身份认证时,我们会出现三种身份认证令牌


| Wilddog ID Token | 当用户登录野狗应用时由野狗创建,这个Token 签署了JWT,可在 Wilddog 应用中安全地识别用户。其中Wilddog ID Token包含了用户的基本个人信息,包括 Wilddog ID，其中 Wilddog Id 在对应应用内是唯一的。 |
| -- | ----- |
| OAuth的accessToken | 由OAuth提供商创建的一种Token,这些Token可以有不同的格式,但常用的是OAuth的accessToken,creditwilddog应用使用这些 Token来确认用户已成功通过OAuth身份认证,然后将它转换成可供 Wilddog服务使用的credentials|
| 野狗的 CustomToken | 由自定义身份认证系统创建,用于允许用户可以自己实现身份系统登录野狗的应用。CustomToken是使用服务账户超级秘钥的JWT签名格式的 Token |

注意：Wilddog ID Token 现已升级为新版。新旧版 Wilddog ID Token 对比如下：

旧版 Wilddog ID Token 格式：

```
{
  "v": 0,
   "iat": 1473131840,
   "exp": 1473131840,
   "d": {
    "uid": "5fbb360743d26a40420cbb636b2e",
    "claim":{}
  }
}
```

新版 Wilddog ID Token 格式：

```
{
    "v":1,
    "iss":"[https://%3Cappid/]https://<appid>.wilddogio.com"
    "iat":132323223,
    "exp":132323223,
    "user_id":"3128736827jdmsdsd",
    "provider_id": "anonymous", //只有匿名的时候有
    "email":"xxx@wilddog.com",
    "email_verified":false,
    "name":"xxx",
    "picture":"http://picture.com/xxxxxx",
    "wilddog":{
        "identities": {
            "google.com": [
                "23232323243534543"
                ],
            "password":[
                "juzhen@wilddog.com"
                ]
            },
        "sign_in_provider": "password"
    },
    <claim1> : {},
    <claim2> : {},
    <option> : {}
}
```
旧版的应用不能用新版的 Wilddog ID Token，同时新版的应用不能用旧版的 Wilddog ID Token。如需要升级，请参考文档[ SDK 2.0升级指南](https://z.wilddog.com/upgrade/webupgrade)

## 主邮箱使用
本次auth升级 定义了主邮箱的定义
主邮箱的使用场景如下

| 重置账号密码 | 若终端用户忘记密码后,可以通过重置账号密码的方式,主邮箱会收到重置密码邮件 |
| -- | ----- |
| **邮箱修改提醒** | **当终端用户的账户修改了主邮箱时,原邮箱会收到一封提示邮件,提示 主邮箱已经修改。若邮箱修改不是本人操作,那么可以通过邮件中的还原邮箱操作进行主邮箱还原。** |
| **账号邮箱确认** | **用户可以对终端用户发送一封邮箱确认邮件,终端用户可以通过点击邮件中的链接,对个人账户进行确认操作。** |

