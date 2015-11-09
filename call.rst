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
  如果没有调用该接口，或者调用后又取消(:http:post:`/api/user/(string: telnum)/cancelcall`)，
  服务器不会接受 `telnum` 对 `caller` 的电话呼叫。

例子
--------

此例中，电话号码为 `1001` 的用户，专号是 `2001` ，他使用该专号呼叫目标号码 `3001`

**Request**

.. code-block:: http

  POST /api/user/1001/makecall HTTP/1.1
  Host: example.com
  Content-Type: application/json

  {"caller": "2001", "callee": "3001"}

**Response**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json

  {"callid": "12870498123"}

顺序图
---------------

下面使用顺序图描述一个呼叫场景。
该场景中：电话号码为 `1001` 的用户，专号是 `2001` ，他使用该专号呼叫目标号码 `3001`

.. sidebar:: 说明

  红色表示WebAPI访问，蓝色表示电话线路通信

.. seqdiag::

  seqdiag call {
    default_fontsize = 12;

    UserPhone; WebServer; CtiServer; TargetPhone;

    UserPhone => WebServer [
      label='POST /api/user/1001/makecall\n{"caller":"2001","callee":"3001"}',
      return='200 OK\n {"callid":"27"}',
      color=red];
    UserPhone ->> CtiServer [label="CALL: 2001", color=blue];
    CtiServer => WebServer [
      label='POST /api/cti/callin\n{"from":"1001","to":"3001"}',
      return='200 OK\n{"action":"bridge","caller":"2001","callee":"3001"}',
      color=red];
    CtiServer ->> TargetPhone [label="CALL: from=2001,to=3001", color=blue];
    CtiServer <<-- TargetPhone [label="RINGING", color=blue];
    UserPhone <<-- CtiServer [label="RINGING", color=blue];
    TargetPhone ->> CtiServer  [label="ANSWER", color=blue];
    CtiServer ->> UserPhone [label='ANSWER', color=blue];
  }

取消呼叫
=============

.. http:post:: /api/user/(string: telnum)/cancelcall

  取消上次的呼叫请求
