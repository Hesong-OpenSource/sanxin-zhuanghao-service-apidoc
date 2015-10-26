安全
#########

SSL 验证
=============

TODO: Client Side Certificate Auth ....

Token 验证
==============

当APP向发起HTTP请求时， **必须** 附带若干URL参数，作为验证依据。

后台服务在收到请求时，会进行验证，并拒未通过绝验证的请求。

Token 算法说明
-----------------------

.. http:any:: /api/*

  除了登录（:http:post:`/api/login`） ，所有的请求都需要使用附加的URL参数进行验证

  :query string timestamp:
    时间戳，形如： ``1407812629434``

    .. note:: `Java <http://java.com/>`_ 中的获取这种时间戳方法 ``Date().getTime()``

  :query string signature:
    签名，由 ``userid``, ``password`` , ``token`` （见 :http:post:`/api/login`） , ``timestamp`` 计算得出

    APP应将 ``signature`` 参数填写为 SHA1 哈希值的16进制字符串表达式，其算法是：

    .. math::

        signature = sha1(userid + password + token + timestamp)

    以 Python 语言举例::

      >>> from hashlib import sha1
      >>> userid = b'1001'
      >>> token = b'123456abcdef'
      >>> password = b'password'
      >>> timestamp = b'1407812629434'
      >>> signature = sha1(userid + password + token + timestamp).hexdigest()
      >>> print(signature)
      4c609e5d5d234a406d446ea42898efad50e4541c

    此时，url形如::

        http://api/your/path/?timestamp=1407812629434&signature=4c609e5d5d234a406d446ea42898efad50e4541c
