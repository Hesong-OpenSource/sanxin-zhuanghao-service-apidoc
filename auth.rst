验证与授权
##########

登录
======

.. http:post:: /api/login

  使用三新账户密码登录到系统

  :<json string id: 三新账户
  :<json string password: 三新密码
  :>json string token: 令牌。
    用于该用户所有后续请求的验证，见 :http:any:`/api/*`

    .. warning::
      ``token`` 具有时效性，后台服务在一段时间之后，会抛弃原来的令牌，此时，客户端需要调用登录接口，以获取新的令牌。

注销
======

.. http:post:: /api/logout

  注销
