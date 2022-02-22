



## jwt是什么?

JWTs是JSON对象的编码表示。JSON对象由零或多个名称/值对组成，其中名称为字符串，值为任意JSON值。

JWT有助于在clear(例如在URL中)发送这样的信息，可以被信任为不可读(即加密的)、不可修改的(即签名)和URL - safe(即Base64编码的)。

JSON Web Token（JWT）是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。

让我们来假想一下一个场景。在A用户关注了B用户的时候，系统发邮件给B用户，并且附有一个链接“点此关注A用户”。链接的地址可以是这样的

```
https://your.awesome-app.com/make-friend/?from_user=B&target_user=A
```

上面的URL主要通过URL来描述这个当然这样做有一个弊端，那就是要求用户B用户是一定要先登录的。可不可以简化这个流程，让B用户不用登录就可以完成这个操作。JWT就允许我们做到这点。

## jwt的组成

一个JWT实际上就是一个字符串，它由三部分组成，**头部**、**载荷**与**签名**。

- Header: 标题包含了令牌的元数据，并且在最小包含签名和/或加密算法的类型
- Claims: Claims包含您想要签署的任何信息
- JSON Web Signature (JWS): 在header中指定的使用该算法的数字签名和声明

例如：

```json
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Claims:
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}

Signature:
base64UrlEncode(Header) + "." + base64UrlEncode(Claims),
```

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111110627.webp)



### 载荷（Payload）

我们先将上面的添加好友的操作描述成一个JSON对象。其中添加了一些其他的信息，帮助今后收到这个JWT的服务器理解这个JWT。

```json
{
    "iss": "John Wu JWT",
    "sub": "jrocket@example.com",
    "aud": "www.example.com",
    "exp": 1441594722,
    "iat": 1441593502,
    "from_user": "B",
    "target_user": "A"
}
```

这里面的前五个字段都是由JWT的标准所定义的。

- iss: 该JWT的签发者
- sub: 该JWT所面向的用户
- aud: 接收该JWT的一方
- exp(expires): 什么时候过期，这里是一个Unix时间戳
- iat(issued at): 在什么时候签发的
- **nbf**: 定义在什么时间之前，该jwt都是不可用的.
- **jti**: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。

这些定义都可以在标准中找到。

将上面的JSON对象进行[base64编码]可以得到下面的字符串。这个字符串我们将它称作JWT的**Payload**（载荷）。

```
eyJpc3MiOiJKb2huIFd1IEpXVCIsImlhdCI6MTQ0MTU5MzUwMiwiZXhwIjoxNDQxNTk0NzIyLCJhdW
```

*小知识：Base64是一种编码，也就是说，它是可以被翻译回原来的样子来的。它并不是一种加密过程。*

```js
// Node.js的包base64url来得到这个字符串。
var base64url = require('base64url')
var header = {
    "from_user": "B",
    "target_user": "A"
}
console.log(base64url(JSON.stringify(header)))
// 输出：eyJpc3MiOiJKb2huIFd1IEpXVCIsImlhdCI6MTQ0MTU5MzUwMiwiZXhwIjoxNDQxNTk0NzIyLCJhdWQiOiJ3d3cuZXhhbXBsZS5jb20iLCJzdWIiOiJqcm9ja2V0QGV4YW1wbGUuY29tIiwiZnJvbV91c2VyIjoiQiIsInRhcmdldF91c2VyIjoiQSJ9
```

### 头部（Header）

JWT还需要一个头部，头部用于描述关于该JWT的最基本的信息，例如其类型以及签名所用的算法等。这也可以被表示成一个JSON对象。

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

在这里，我们说明了这是一个JWT，并且我们所用的签名算法（后面会提到）是HS256算法。

对它也要进行Base64编码，之后的字符串就成了JWT的**Header**（头部）。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

### 签名（签名）

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

- header (base64后的)
- payload (base64后的)
- secret

这个部分需要base64加密后的header和base64加密后的payload使用`.`连接组成的字符串，然后通过header中声明的加密方式进行加盐`secret`组合加密，然后就构成了jwt的第三部分。

```js
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);
//base64UrlEncode(header)   eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
//base64UrlEncode(payload)  eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9
var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

将这三部分用`.`连接成一个完整的字符串,构成了最终的jwt:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

这一部分又叫做**签名**。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111147.webp)

最后将这一部分签名也拼接在被签名的字符串后面，我们就得到了完整的JWT

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcm9tX3VzZXIiOiJCIiwidGFyZ2V0X3VzZXIiOiJBIn
```

于是，我们就可以将邮件中的URL改成

```
https://your.awesome-app.com/make-friend/?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJm
```

这样就可以安全地完成添加好友的操作了！

且慢，我们一定会有一些问题：

1. 签名的目的是什么？
2. Base64是一种编码，是可逆的，那么我的信息不就被暴露了吗？

让我逐一为你说明。



服务端会验证token，如果验证通过就会返回相应的资源。整个流程就是这样的:

![image-20220111115714715](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111115714.png)

## 签名的目的

最后一步签名的过程，实际上是对头部以及载荷内容进行签名。一般而言，加密算法对于不同的输入产生的输出总是不一样的。对于两个不同的输入，产生同样的输出的概率极其地小（有可能比我成世界首富的概率还小）。所以，我们就把“不一样的输入产生不一样的输出”当做必然事件来看待吧。

所以，如果有人对头部以及载荷的内容解码之后进行修改，再进行编码的话，那么新的头部和载荷的签名和之前的签名就将是不一样的。而且，如果不知道服务器加密的时候用的密钥的话，得出来的签名也一定会是不一样的。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111300.webp)

服务器应用在接受到JWT后，会首先对头部和载荷的内容用同一算法再次签名。那么服务器应用是怎么知道我们用的是哪一种算法呢？别忘了，我们在JWT的头部中已经用alg字段指明了我们的加密算法了。

如果服务器应用对头部和载荷再次以同样方法签名之后发现，自己计算出来的签名和接受到的签名不一样，那么就说明这个Token的内容被别人动过的，我们应该拒绝这个Token，返回一个HTTP 401 Unauthorized响应。

## 信息会暴露？

是的。

所以，在JWT中，不应该在载荷里面加入任何敏感的数据。在上面的例子中，我们传输的是用户的User ID。这个值实际上不是什么敏感内容，一般情况下被知道也是安全的。

但是像密码这样的内容就不能被放在JWT中了。如果将用户的密码放在了JWT中，那么怀有恶意的第三方通过Base64解码就能很快地知道你的密码了。

## JWT的适用场景

我们可以看到，JWT适合用于向Web应用传递一些非敏感信息。例如在上面提到的完成加好友的操作，还有诸如下订单的操作等等。

## 用户认证八步走

所谓用户认证（Authentication），就是让用户登录，并且在接下来的一段时间内让用户访问网站时可以使用其账户，而不需要再次登录的机制。

*小知识：可别把用户认证和用户授权（Authorization）搞混了。用户授权指的是规定并允许用户使用自己的权限，例如发布帖子、管理站点等。*

首先，服务器应用（下面简称“应用”）让用户通过Web表单将自己的用户名和密码发送到服务器的接口。这一过程一般是一个HTTP POST请求。建议的方式是通过SSL加密的传输（https协议），从而避免敏感信息被嗅探。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111336.webp)

接下来，应用和数据库核对用户名和密码。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111344.webp)

核对用户名和密码成功后，应用将用户的id（图中的user_id）作为JWT Payload的一个属性，将其与头部分别进行Base64编码拼接后签名，形成一个JWT。这里的JWT就是一个形同lll.zzz.xxx的字符串。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111357.webp)

应用将JWT字符串作为该请求Cookie的一部分返回给用户。注意，在这里必须使用HttpOnly属性来防止Cookie被JavaScript读取，从而避免跨站脚本攻击（XSS攻击）。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111408.webp)

在Cookie失效或者被删除前，用户每次访问应用，应用都会接受到含有jwt的Cookie。从而应用就可以将JWT从请求中提取出来。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111418.webp)

应用通过一系列任务检查JWT的有效性。例如，检查签名是否正确；检查Token是否过期；检查Token的接收方是否是自己（可选）。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111427.webp)

应用在确认JWT有效之后，JWT进行Base64解码（可能在上一步中已经完成），然后在Payload中读取用户的id值，也就是user_id属性。这里用户的id为1025。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111438.webp)

应用从数据库取到id为1025的用户的信息，加载到内存中，进行ORM之类的一系列底层逻辑初始化。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111449.webp)

应用根据用户请求进行响应。

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111456.webp)

## 和Session方式存储id的差异

Session方式存储用户id的最大弊病在于要占用大量服务器内存，对于较大型应用而言可能还要保存许多的状态。一般而言，大型应用还需要借助一些KV数据库和一系列缓存机制来实现Session的存储。

而JWT方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。除了用户id之外，还可以存储其他的和用户相关的信息，例如该用户是否是管理员、用户所在的分桶（见[《你所应该知道的A/B测试基础》一文]( /2015/08/27/introduction-to-ab-testing/）等。

虽说JWT方式让服务器有一些计算压力（例如加密、编码和解码），但是这些压力相比磁盘I/O而言或许是半斤八两。具体是否采用，需要在不同场景下用数据说话。

## 单点登录

Session方式来存储用户id，一开始用户的Session只会存储在一台服务器上。对于有多个子域名的站点，每个子域名至少会对应一台不同的服务器，例如：

- www.taobao.com
- nv.taobao.com
- nz.taobao.com
- login.taobao.com

所以如果要实现在login.taobao.com登录后，在其他的子域名下依然可以取到Session，这要求我们在多台服务器上同步Session。

使用JWT的方式则没有这个问题的存在，因为用户的状态已经被传送到了客户端。因此，我们只需要将含有JWT的Cookie的domain设置为顶级域名即可，例如

```
Set-Cookie: jwt=lll.zzz.xxx; HttpOnly; max-age=980000; domain=.taobao.com
```

注意domain必须设置为一个点加顶级域名，即.taobao.com。这样，taobao.com和*.taobao.com就都可以接受到这个Cookie，并获取JWT了。

## jwt的框架：JJWT

JJWT是一个提供端到端的JWT创建和验证的Java库。永远免费和开源(Apache License，版本2.0)，JJWT很容易使用和理解。它被设计成一个以建筑为中心的流畅界面，隐藏了它的大部分复杂性。

- JJWT的目标是最容易使用和理解用于在JVM上创建和验证JSON Web令牌(JWTs)的库。
- JJWT是基于JWT、JWS、JWE、JWK和JWA RFC规范的Java实现。
- JJWT还添加了一些不属于规范的便利扩展，比如JWT压缩和索赔强制。

#### 规范兼容:

- 创建和解析明文压缩JWTs
- 创建、解析和验证所有标准JWS算法的数字签名紧凑JWTs(又称JWSs):
- HS256: HMAC using SHA-256
- HS384: HMAC using SHA-384
- HS512: HMAC using SHA-512
- RS256: RSASSA-PKCS-v1_5 using SHA-256
- RS384: RSASSA-PKCS-v1_5 using SHA-384
- RS512: RSASSA-PKCS-v1_5 using SHA-512
- PS256: RSASSA-PSS using SHA-256 and MGF1 with SHA-256
- PS384: RSASSA-PSS using SHA-384 and MGF1 with SHA-384
- PS512: RSASSA-PSS using SHA-512 and MGF1 with SHA-512
- ES256: ECDSA using P-256 and SHA-256
- ES384: ECDSA using P-384 and SHA-384
- ES512: ECDSA using P-521 and SHA-512

这里以github上的demo演示，理解原理，集成到自己项目中即可。Spring Boot 基础就不介绍了，推荐下这个实战教程：https://www.javastack.cn/categories/Spring-Boot/

##### 应用采用 spring boot + angular + jwt

![Image](https://cdn.jsdelivr.net/gh/youensChen/picgo/img2/20220111111815.webp)