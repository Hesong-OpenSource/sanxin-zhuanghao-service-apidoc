用户管理
##############

用户管理API用于管理用户的账户信息和基本资料。

.. attention:: 在本系统中，用户使用手机号码登录，且专号与手机号码绑定，因此，手机号码被视作用户ID。

获取用户信息
================

.. http:get:: /api/user/(string: telenum)

  返回手机号码为 `telenum` 的用户的基本信息

  :>json string telenum: 用户的手机号码
  :>json string name: 用户的名称
  :>json string createtime: 用户建立的时间（ISO格式）
  :>json string avatar: 用户的头像（BASE64格式）

注册新用户
================

.. http:post:: /api/user

  :<json string telenum: 用户的手机号码 (*必填*)
  :<json string name: 用户的名称 (*必填*)
  :<json string avatar: 用户的头像（BASE64格式） (*非必填*)

修改用户信息
================

.. http:put:: /api/user/(string: telenum)

  修改手机号码为 `telenum` 的用户的基本信息

  :<json string name: 用户的名称
  :<json string avatar: 用户的头像（BASE64格式）

  .. note::
    JSON 中的属性都不是必填项，属性值为 `null` 的或者没有提供属性的，不会被修改。

删除用户
================

.. http:delete:: /api/user/(string: telenum)

  删除手机号码为 `telenum` 的用户
