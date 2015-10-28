验证与授权
##########

登录
======

.. http:post:: /api/user/(string: telnum)/login

  手机号码为 `telnum` 的用户登录

  :<json string password: 密码的MD5散列，用十六进制字符串（其中的小写字母转要换成大写字母）表示
  :>json string token: 令牌
    用于该用户所有后续请求的验证，见 :http:any:`/api/user/(string: telnum)/*`

  .. attention::
    `token` 具有时效性。服务程序在一段时间之后，会抛弃原来的令牌。
    此时，客户端需要调用登录接口，以获取新的令牌。
    一点调用该接口，原来的令牌就会失效。

注销
======

.. http:post:: /api/user/(string: telnum)/logout

  注销手机号码为 `telnum` 的用户
