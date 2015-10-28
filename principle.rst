原则
########

1. 基于 `HTTP 1.1 <http://www.w3.org/Protocols/rfc2616/rfc2616.html>`_ （ `HTTP 2.0 <https://en.wikipedia.org/wiki/HTTP/2>`_ 就算了吧 -_-）的 WebAPI
2. 客户端 -> 服务器 单方向访问
3. `Restfule <https://en.wikipedia.org/wiki/Representational_state_transfer>`_ API 形态
4. 使用 `UTF8 <https://en.wikipedia.org/wiki/UTF-8>`_ 编码的 `JSON <http://www.json.org/>`_ 记录结构化数据
5. 使用 `HTTPS <https://en.wikipedia.org/wiki/HTTPS>`_

HTTP Content
=============

`POST` 或者 `PUT` 请求采用 `JSON` 格式的数据，
此时 `Content-Type` 头域的值应是 ``application/json``

回复的 `Content` 也必须是 `JSON` 格式，
其 `Content-Type` 头域的值应是 ``application/json`` 。

如果不需要返回数据，为了保证 `Content` 可以被 `JSON` 解码，应使用 ``null`` 作为回复的内容，例如：

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json

  null

HTTP Status Code
==================

200 执行成功
-----------------

如果API调用成功，服务器应返回状态码 `200 OK` 。

回复中，头域 `Content-Type` **一定** 是 `application/json` ，内容 **一定** 是 `JSON` 格式。

500 执行失败
-----------------

如果服务器在响应API调用期间出现错误，应返回 `500 Internal Server Error`。

如果服务器可以提供具体的错误编码以及错误信息，应采用 `JSON` 格式在内容中记录这些数据：
``code`` 属性记录错误编码， ``text`` 属性记录错误文本信息。

如：

.. code-block:: http

  HTTP/1.1 500 Internal Server Error
  Content-Type: application/json

  {"code": 10013, "text": "calee not allowed"}

.. attention::
  服务器无法在所有情况在都提供 `JSON` 格式错误信息，客户端应注意判断。


401 授权错误
-----------------

登录失败， `API` 请求签名验证错误，均返回该 `Status Code`

其它
-------

其它 `Status Code` 均遵照 `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 的定义
