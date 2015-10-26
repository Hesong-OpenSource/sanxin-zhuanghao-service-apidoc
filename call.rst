电话控制
##############

发起呼叫
=============

.. http:post:: /api/user/(int: user_id)/makecall

  用户 (`user_id`) 使用专号 (`vtelnum`) 发起呼叫

  :<json string vtelnum: 专号
  :>json string callid: 呼叫ID

.. warning:: 调用用该API之后，必须在 **2分钟** 之内，向该专号 (`vtelnum`) 发起呼叫。

呼叫过程
----------

.. seqdiag::

  seqdiag call {
    default_fontsize = 18;

    UserPhone; App; Server; TargetPhone;

    App => Server [label = "POST /api/user/xxx/makecall"];
    UserPhone -> Server [label = "call"];
    Server -> TargetPhone [label = "forward"];
    Server <-- TargetPhone [label = "answer"];
    UserPhone <-- Server [label = 'answer'];
  }
