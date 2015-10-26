电话控制
##############

发起呼叫
=============

.. http:post:: /api/user/(string: telnum)/makecall

  手机号码为 `telnum` 的用户通知服务器，将要发起呼叫

  :<json string caller: 作为主叫号码专号
  :<json string callee: 被叫号码
  :>json string callid: 呼叫ID

.. note::
  调用用该API之后，服务器将记录 `telnum` 的呼叫请求。

  在 **2分钟** 之内，服务器如果收到 `telnum` 向其专号 `caller` 的呼叫，
  就会自动接通，然后以 `caller` 为主叫号码，向 `callee` 发起呼叫，并桥接两者之间的通话。

.. attention::
  对于同一个手机号码，服务器只保存其最近一次呼叫请求。
  如果同一个手机号码连续调用该接口，服务器将以最后一次请求为准。

.. attention::
  如果没有调用该接口，或者调用后又取消（ :http:post:`/api/user/(string: telnum)/cancelcall` ），
  服务器不会接受 `telnum` 对 `caller` 的电话呼叫。

呼叫过程顺序图
---------------

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


取消呼叫
=============

.. http:post:: /api/user/(string: telnum)/cancelcall

  取消上次的呼叫请求
