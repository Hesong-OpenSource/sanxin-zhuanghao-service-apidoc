安全
#########

Token 验证
==============

当APP向发起HTTP请求时， **必须** 附带若干URL参数，作为验证依据。

后台服务在收到请求时，会进行验证，并拒未通过绝验证的请求。

.. http:any:: /api/user/(string: telnum)/*

  该系统的服务器需要为每一个接入程序（以下简称 `APP` ）分配相应的 `ACCESS-ID` 和 `ACCESS-KEY`。

  `APP` 在调用服务器接口时，绝大多数情况下，都需要使用 `ACCESS-ID` 和 `ACCESS-KEY` 进行签名。
  服务器会验证签名的正确性，并拒绝验证不通过的请求。

  除了登录 ，所有的请求都需要使用附加的URL参数进行验证

  :query string accessid:
    客户端访问ID，由系统管理员分配

  :query string timestamp:
    Unix 时间戳，形如： `1445851008`

    .. attention:: 服务器将验证时间戳，如果与服务器时间差超过正负48小时，服务器将认为验证失败

  :query string signature:
    签名，由 `url path`, `telnum`, `password` , `token` （见 :http:post:`/api/user/(string: telnum)/login`） ,
    `timestamp` ， `accessid`， `accesskey` 7个参数计算得出

  APP应将 ``signature`` 参数填写为 SHA1 哈希值的16进制字符串表达式，其算法是：

  1. 将 `url path` (裁剪结尾的 `/` ), `telnum`, `password` (大写MD5散列) , `token`, `timestamp`, `accessid`, `accesskey` (大写MD5散列)
     这7个参数按照字符串顺序进行从小到大的排序。
  2. 将排序完毕的各个参数字符串头尾相连，连接成新的字符串。
  3. 计算上一个步骤中，连接好的字符串的 SHA-1 散列值，散列值用经过大写字母转换(upper case)的十六进制字符串表达式。

  .. attention::
    调用登录接口时（:http:post:`/api/user/(string: telnum)/login`）， `token` 参数应使用空字符串


签名算法例子代码
---------------------

在下面的例子中，手机号码为 `13887654321` 的用户已经登录；
其登录密码是 `This_Is#My&p@ssw0rd`；
该APP的访问ID与KEY分别是 `developer-001` 与 `xm90uojWSd34E8y3` ；
其 `token=4C609E5D5D234A406D446EA42898EFAD50E4541C` ；
当前时间戳是 `1407812629434` ；
要访问的URL路径是 `/api/user/13887654321/path/of/the/api` ；
那么，最终计算得出的签名将是 `DCE009D2AF85050E249A6511D1C0F0F180EDFA64`

下面是几种语言的算法实现片段:

Python3
^^^^^^^^^^^

.. code-block:: py

  >>> from hashlib import sha1, md5
  >>> accessid = b'developer-001'
  >>> accesskey = bytes(md5(b'xm90uojWSd34E8y3').hexdigest().upper(), 'ascii')
  >>> url_path = b'/api/user/13887654321/path/of/the/api'
  >>> telnum = b'13887654321'
  >>> token = b'4C609E5D5D234A406D446EA42898EFAD50E4541C'
  >>> password = bytes(md5(b'This_Is#My&p@ssw0rd').hexdigest().upper(), 'ascii')
  >>> timestamp = b'1407812629434'
  >>> _param_list = [accessid, accesskey, url_path, telnum, token, password, timestamp]
  >>> _param_list.sort()
  >>> signature = sha1(b''.join(_param_list)).hexdigest().upper()
  >>> print(signature)
  DCE009D2AF85050E249A6511D1C0F0F180EDFA64

此时，url形如::

    http://api/user/13887654321/path/of/the/api?accessid=developer-001&timestamp=1407812629434&signature=DCE009D2AF85050E249A6511D1C0F0F180EDFA64

获取 Unix 时间戳的例子代码
----------------------------

以下是几种常见语言获取 Unix 时间戳的方法:

C
^^^

.. code-block:: c

  include <time.h>

  void your_func() {
    int ts = time(NULL);
  }

C#
^^^^^^^

.. code-block:: csharp

  Int32 tx = (Int32)(DateTime.UtcNow.Subtract(new DateTime(1970, 1, 1))).TotalSeconds;

Java
^^^^^^^

.. code-block:: java

  long ts = System.currentTimeMillis()/1000L;

Javascript
^^^^^^^^^^^^^^^^^^

.. code-block:: js

  var ts = Math.floor(Date.now()/1000);

Php
^^^^^

.. code-block:: php

  <?php
  $ts = time();

Python
^^^^^^^^^

.. code-block:: py

  import time
  ts = int(time.time())
