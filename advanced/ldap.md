---
description: LDAP 是一个树型的用来存储用户和组织信息的数据库，常被用来做单点登录和组织架构管理。
---

# 配置 LDAP 服务

### LDAP 是什么？ <a id="ldap-&#x662F;&#x4EC0;&#x4E48;&#xFF1F;"></a>

LDAP 是轻量目录访问协议，英文全称是 Lightweight Directory Access Protocol，一般都简称为 LDAP。你可以把它理解为一个树型的用来存储用户和组织信息的数据库，常被用来做单点登录（ SSO ），更多介绍请[点击这里](https://baike.baidu.com/item/LDAP/2875565)。

使用 LDAP 服务请先[创建一个 OAuth 应用](https://docs.authing.cn/#/oauthProvider/new)或[创建一个 OIDC 应用](https://docs.authing.cn/#/OIDCProvider/authorize?id=%E5%9C%A8-a-%E5%BA%94%E7%94%A8%E7%AC%AC%E4%B8%89%E6%96%B9%E7%99%BB%E5%BD%95%E9%80%89%E9%A1%B9%E5%8D%A1%E4%B8%8B%E5%88%9B%E5%BB%BA-oidc-%E5%BA%94%E7%94%A8)。

创建完成后，LDAP 服务对全部 OAuth 或 OIDC 应用都可用。

我们准备了一个[LDAP 测试应用](https://ldap-test.authing.cn)可让你体验使用 LDAP 登录的全流程（测试账户：tesla，测试密码：password）。

![](https://usercontents.authing.cn/docs/oauth/ldap/ldap_test.png)

如果你想接入 LDAP 服务，请按照以下步骤完成 LDAP 配置。

### 添加 LDAP 服务 <a id="&#x6DFB;&#x52A0;-ldap-&#x670D;&#x52A1;"></a>

![](../.gitbook/assets/image%20%28200%29.png)

### 填写相关信息 <a id="&#x586B;&#x5199;&#x76F8;&#x5173;&#x4FE1;&#x606F;"></a>

在弹出的对话框中填写相关信息，如果你不知道如何填写可以点击右上角的「点击这里」填充测试配置信息。

![](../.gitbook/assets/image%20%28107%29.png)

#### 参数解释 <a id="&#x53C2;&#x6570;&#x89E3;&#x91CA;"></a>

1. `LDAP 别名`，必填，自定义的 LDAP 服务名称
2. `LDAP 链接`，必填，LDAP 服务器的地址，如：ldap://dc.fabrikam.com
3. `Base DN`，必填，用于连接 LDAP 的用户名，此用户名将用于测试连接结果和搜索用户或用户组
4. `密码`，必填，用于连接 LDAP 的密码，该密码将会被加密存储到数据库中
5. `Search DN`，必填，定义从哪个目录开始搜索，如：dc=fabrikam,dc=local
6. `查询条件`，必填，如果这里是 mail 表示查询用户信息是通过邮箱信息来查询的。注意，该字段信息与 LDAP 数据库存储数据的字段相对应，如果如果存储用户邮箱信息的字段是 email, 这里就需要修改成 email。同时此处支持自定义 filter 表达式，基本形式为：&\(objectClass=user\)\(cn=%s\), 其中 %s 会被用户在登录时填写的用户名替换
7. `LDAP 描述`，选填，描述下这个 LDAP 服务是做什么用的

### 连通性测试 <a id="&#x8FDE;&#x901A;&#x6027;&#x6D4B;&#x8BD5;"></a>

连通性测试可以帮助你检查参数是否填写正确了，如果你填入了「测试配置」，那么点击页面上的「连通性测试」应该可以看到如下结果：

![](../.gitbook/assets/image%20%28549%29.png)

你可以用这个测试来测试不同的 `Base DN` 和`密码`。

### 验证用户 <a id="&#x9A8C;&#x8BC1;&#x7528;&#x6237;"></a>

验证用户可以帮助你检查查询条件是否正确，如果查询条件不正确，会返回无法找到用户的错误。

我们给出的测试用户名是 `tesla`，密码是 `password`，查询条件是 `uid`，点击「验证用户」后应该看到如下结果：

![](../.gitbook/assets/image%20%2880%29.png)

如果将查询条件从 `uid` 改为 `uidd`，那么此时应该返回如下结果：

![](../.gitbook/assets/image%20%28433%29.png)

用户可用此方式，通过修改 `Search DN` 和 `查询条件` 完成对 LDAP 的调试。

配置完成后，访问任意 OAuth 应用或 OIDC 应用即可使用 LDAP 账号登录。

{% hint style="info" %}
我们准备了一个[LDAP 测试应用](https://ldap-test.authing.cn)可让你体验使用 LDAP 登录的全流程：

测试账户：tesla

测试密码：password
{% endhint %}





