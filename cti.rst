电话服务
#########

电话网服务器(CTI)通过调用Web服务器的电话服务接口，控制其电话线路的接续、转接等行为。

.. attention::

  这一系列的接口需要进行 `HTTP 基本认证 <https://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81>`_

呼入事件
=============

.. http:post:: /api/cti/callin

  电话网服务器(CTI)在接到电话呼入后，调用这个API。
  Web服务器应在API的返回中，告知CTI服务器后续动作。

  :<json string from: 主叫号码
  :<json string to: 被叫号码

  :>json string action: CTI后续动作，有：

    * `bridge` : 桥接
    * `refuse` : 拒接

  :>json string caller: 进行桥接时的主叫号码

    .. note:: 仅当 `action` 为 `"bridge"` 时有效

  :>json string callee: 进行桥接时的被叫号码

    .. note:: 仅当 `action` 为 `"bridge"` 时有效

例子
--------

此例中，电话号码为 `1001` 的用户，专号是 `2001` ，他使用该专号呼叫目标号码 `3001`。

当APP的呼叫发起请求 (:http:post:`/api/user/(string: telnum)/makecall`) 提交成功后，
APP控制手机呼叫 `2001` 。
此时，CTI服务器询问Web服务器后续动作，而Web服务器告知CTI应以 `2001` 的名义呼叫 `3001` ，
并桥接 `1001` 与 `3001` ：

**Request**

.. code-block:: http

  POST /api/cti/callin HTTP/1.1
  Host: example.com
  Content-Type: application/json

  {"from": "1001", "to": "2001"}

**Response**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json

  {"action": "bridge", "caller": "2001", "callee": "3001"}
