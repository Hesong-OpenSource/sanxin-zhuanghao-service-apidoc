验证与授权
##########

登录
======

.. http:post:: /api/login

  使用手机户登录

  :<json string telnum: 手机号码
  :<json string password: 三新密码
  :>json string token: 令牌。
    用于该用户所有后续请求的验证，见 :http:any:`/api/*`

  .. note::
    该手机号码必须是在三新系统中登记过的，并且使用三新的密码登录。

    .. warning::
      ``token`` 具有时效性，后台服务在一段时间之后，会抛弃原来的令牌，此时，客户端需要调用登录接口，以获取新的令牌。

注销
======

.. http:post:: /api/logout

  注销
