小号管理
###########

.. http:get:: /api/user/(int:user_id)/telnum

  返回用户的小号列表，形如::

    [{"telnum": "123"}, {"telnum": "456"}]

    

  :>jsonarr string telnum: 小号的号码
