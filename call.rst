电话控制
##############

发起呼叫
=============

.. http:post:: /api/user/(string: telenum)/makecall

  手机号码为 `telenum` 的用户通知服务器，将要发起呼叫

  :<json string caller: 作为主叫号码专号
  :<json string callee: 被叫号码
  :>json string callid: 呼叫ID

.. warning:: 调用用该API之后，必须在 **2分钟** 之内，向该专号 (`vtelnum`) 发起呼叫。

呼叫过程
----------

.. seqdiag::

  seqdiag call {
    default_fontsize = 18;

    UserPhone; App; Server; TargetPhone;

    App => Server [label = "POST /api/user/xxx/makecall\n\ncaller='123',callee='456'", color=red];
    UserPhone -> Server [label = "call '123'", color=blue];
    Server -> TargetPhone [label = "forward to '456' as '123'", color=blue];
    Server <-- TargetPhone [label = "answer", color=blue];
    UserPhone <-- Server [label = 'answer', color=blue];
  }
