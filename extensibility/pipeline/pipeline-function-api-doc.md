# Pipeline 函数开发指南

{% hint style="success" %}
Pipeline 为一组函数，和普通 Hooks 的区别在于，Pipeline 整个流程中的函数数据可以相互传递，实现工业流水线一样的效果。这种设计模式，可以使得开发者的自定义函数更加模块化，便于管理。
{% endhint %}

## Pipeline 函数类型 <a id="pipeline-type"></a>

目前 Authing 支持三种类型的 Pipeline 函数：

| 名称 | 说明 |
| :--- | :--- |
| Pre-Register  Pipeline | 注册前 Pipeline，会在每次用户正式进入注册逻辑前触发，开发者可用此实现注册邮箱白名单、注册 IP 白名单等功能。 |
| Post-Register Pipeline | 注册后Pipeline， 会在每次用户完成注册逻辑 **（但还未保存至数据库）** 之后触发，开发者可用此实现往数据库写入自定义 metadata 、新用户注册 webhook 通知等功能。  |
| Post-Authentication Pipeline | 认证后 Pipeline 会在每次用户完成认证之后触发，开发者可用此实现往 token 加入自定义字段等功能。 |
| Pre-OIDCTokenIssued  Pipeline | OIDC 应用 code 换 token 之前触发，开发者可用此实现往 idToken 中写入自定义字段等功能。OIDC 认证流程的 code 换 token 部分详情请见：[使用 OIDC 授权](../../advanced/oidc/oidc-authorization.md#04-shi-yong-code-huan-qu-token)。 |

{% hint style="info" %}
开发者创建 Pipeline 函数时必须选择一种  Pipeline 类型。
{% endhint %}

{% hint style="warning" %}
注意：Post-Register Pipeline 发生在保持至数据库之前，所以此时无法还通过 API 获取到该用户，也就意味着此时无法将该用户加入到某个群组，因为此时用户不存在。 
{% endhint %}

## 函数定义 <a id="definition"></a>

Pre-Register  Pipeline 函数定义：

```javascript
async function pipe(context, callback)
```

Post-Authentication Pipeline 和 Post-Authentication Pipeline 函数定义：

```javascript
async function pipe(user, context, callback)
```

{% hint style="success" %}
Pre-Register  Pipeline 少了一个 user 参数，因为注册前无法确认此用户是谁。
{% endhint %}

{% hint style="success" %}
pipe 函数支持 async / await 语法！
{% endhint %}

{% hint style="danger" %}
请勿重命名 pipe 函数！
{% endhint %}

参数说明：

| 参数 | 类型 | 说明 |
| :--- | :--- | :--- |
| user | object | 当前请求用户。详细字段请见 [user 对象](user-object.md)。 |
| context | object | 请求认证上下文。详细字段请见 [context 对象](context-object.md)。 |
| callback | function | 回调函数，使用文档见下文。 |

### callback 函数 <a id="callback"></a>

 定义：

```javascript
function callback(error, user, context)
```

或：

```javascript
function callback(error, context)
```

callback 函数的第一个参数表示是开发者希望传给终端用户的  error，**如果不为 null，整个认证流程将会中断，直接返回错误给前端**。同时请务必将最新的 user 和 context 传给 callback 函数，否则之后的 pipeline 函数将可能无法正常工作。

## Pipeline 函数示例 <a id="example"></a>

这里我们实现一个注册邮箱白名单的 **Pre-Register  Pipeline**。

```javascript
async function pipe(context, callback) {
  const email = context.data.userInfo.email;
  // 非邮箱注册方式, 跳过此 pipe 函数
  if (!email) {
    return callback(null, context)
  }
  
  // 如果域名邮箱不是 example.com, 返回 Access denied. 错误给终端。
  if (!email.endsWith("@example.com")) {
    return callback(new Error('Access denied.'));
  }
  return callback(null, context);
}
```

简要解释一下代码：

* 2-6 行判断请求参数中是否包含 email, 如果有的话说明是邮箱注册方式。如果没有，直接跳过此 pipe 函数，调用 callback 的参数分别为 null 和 context（**请勿忘记此参数！**）。当然，如果你只是希望邮箱方式注册，这一步如果没有邮箱返回错误也是可以的 ～
* 8-10 行判断邮箱域名是否为`example.com`，如果不是调用 callback 函数，第一个参数为 `new Error('Access Denied.')`。
* 11 行，调用 `return callback(null, context)`，接着进入下一个 pipe 函数，如果有的话。


