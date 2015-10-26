专号管理
###########

专号管理API用于管理用户所属的专号

获取专号列表
===============

.. http:get:: /api/user/(string: telenum)/vtelnum

  返回手机号码为 `telenum` 的用户所拥有的专号列表

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Totle-Pages: 总页数
  :>header X-Pagination-Totle-Entries: 总条目数
  :>json string vtelnum: 专号

例子
--------

此例子中，返回手机号码 (`telenum`) 为 `123` 的用户，所拥有的5个专号中的第3~4个

**Request**

.. sourcecode:: http

  GET /api/user/123/vtelnum?page=2&perPage=2 HTTP/1.1
  Host: example.com

**Response**

.. sourcecode:: http

  HTTP/1.1 200 OK
  Content-Type: application/json
  X-Pagination-Current-Page: 2
  X-Pagination-Per-Page: 2
  X-Pagination-Totle-Pages: 3
  X-Pagination-Totle-Entries: 5

  [{"vtelnum": "10003"}, {"vtelnum": "10004"}]


获取可选专号列表
=================

.. http:get:: /api/user/(string: telenum)/availablevtelnum

  返回手机号码为 `telenum` 的用户可以选取的可用专号的列表

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Totle-Pages: 总页数
  :>header X-Pagination-Totle-Entries: 总条目数
  :>json string vtelnum: 专号

绑定新专号
=============

.. http:post:: /api/user/(string: telenum)/vtelnum

  手机号码为 `telenum` 的用户为该手机号码绑定一个新的专号

  :<json string vtelnum: 要绑定的新专号

取消专号绑定
=============

.. http:delete:: /api/user/(string: telenum)/vtelnum/(string: vtelnum)

  手机号码为 `telenum` 的用户取消该手机号码绑定的的一个专号

更换专号
=============

.. http:post:: /api/user/(string: telenum)/vtelnum/(string: vtelnum)/replace

    手机号码为 `telenum` 的用户取消该手机号码绑定的专号替换成另外一个

  :<json string vtelnum: 要更换的新专号
