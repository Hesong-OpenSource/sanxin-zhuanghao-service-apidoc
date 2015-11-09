安全
#########

User API 安全
================

当APP向发起HTTP请求时， **必须** 附带若干URL参数，作为验证依据。

后台服务在收到请求时，会进行验证，并拒未通过绝验证的请求。

.. http:any:: /api/user/(string: telnum)/*

  该系统的服务器需要为每一个接入程序（以下简称 `APP` ）分配相应的 `ACCESS-ID` 和 `ACCESS-KEY`。

  `APP` 在调用服务器接口时，需要使用 `ACCESS-ID` 和 `ACCESS-KEY` 进行签名。
  服务器会验证签名的正确性，并拒绝验证不通过的请求。

  使用URL参数承载签名数据。

  :query string accessid: 客户端访问ID，由系统管理员分配

  :query string timestamp: Unix 时间戳，形如： `1445851008`

    .. attention:: 服务器将验证时间戳，如果与服务器时间差超过正负48小时，服务器将认为验证失败

  :query string signature:
    签名，由 `url path`, `telnum`, `password` , `token` （见 :http:post:`/api/user/(string: telnum)/login`） ,
    `timestamp` ， `accessid`， `accesskey` 7个参数计算得出

  APP应将 ``signature`` 参数填写为 SHA1 哈希值的16进制字符串表达式，其算法是：

  1. 将 `url path` (裁剪结尾的 `/` ), `telnum`, `password` (大写MD5散列HEX表达式) , `token`, `timestamp`, `accessid`, `accesskey` (大写MD5散列HEX表达式)
     这7个参数按照字符串顺序进行从小到大的排序。
  2. 将排序完毕的各个参数字符串头尾相连，连接成新的字符串。
  3. 计算上一个步骤中，连接好的字符串的 SHA-1 散列值字符串，这个字符串是经过大写字母转换(upper case)的十六进制表达式。

  .. attention::
    调用登录接口时（:http:post:`/api/user/(string: telnum)/login`）， `token` 参数应使用空字符串

签名算法例子代码
---------------------

在下面的例子中：

手机号码为 `13887654321` 的用户已经登录；

其登录密码是 `This_Is#My&p@ssw0rd`；

该APP的访问ID与KEY分别是 `developer-001` 与 `xm90uojWSd34E8y3` ；

其 `token=4C609E5D5D234A406D446EA42898EFAD50E4541C` ；

当前时间戳是 `1407812629434` ；

要访问的URL路径是 `/api/user/13887654321/path/of/the/api` ；

那么，最终计算得出的签名将是 `DCE009D2AF85050E249A6511D1C0F0F180EDFA64`

加上验证和签名参数的完整url是::

    http://api/user/13887654321/path/of/the/api?accessid=developer-001&timestamp=1407812629434&signature=DCE009D2AF85050E249A6511D1C0F0F180EDFA64

下面是几种语言的算法实现片段:

Java
^^^^^^^^^

.. code-block:: java

  import java.security.MessageDigest;
  import java.security.NoSuchAlgorithmException;
  import java.util.ArrayList;
  import java.util.Collections;

  public class Signature {

  	public static String byteArrayToHex(byte[] byteArray) {
  		char[] hexDigits = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9',
  				'A', 'B', 'C', 'D', 'E', 'F' };
  		char[] resultCharArray = new char[byteArray.length * 2];
  		int index = 0;
  		for (byte b : byteArray) {
  			resultCharArray[index++] = hexDigits[b >>> 4 & 0xf];
  			resultCharArray[index++] = hexDigits[b & 0xf];
  		}
  		return new String(resultCharArray);
  	}

  	public static String hashStr(String input, String digest)
  			throws NoSuchAlgorithmException {
  		MessageDigest messageDigest = MessageDigest.getInstance(digest);
  		byte[] inputByteArray = input.getBytes();
  		messageDigest.update(inputByteArray);
  		byte[] resultByteArray = messageDigest.digest();
  		return byteArrayToHex(resultByteArray);
  	}

  	public static String calcSignature() throws NoSuchAlgorithmException {
  		String accessid = "developer-001";
  		String accesskey = hashStr("xm90uojWSd34E8y3", "MD5");
  		String urlpath = "/api/user/13887654321/path/of/the/api";
  		String telnum = "13887654321";
  		String token = "4C609E5D5D234A406D446EA42898EFAD50E4541C";
  		String password = hashStr("This_Is#My&p@ssw0rd", "MD5");
  		String timestamp = "1407812629434";

  		ArrayList<String> tmpList = new ArrayList<String>();
  		tmpList.add(accessid);
  		tmpList.add(accesskey);
  		tmpList.add(urlpath);
  		tmpList.add(telnum);
  		tmpList.add(token);
  		tmpList.add(password);
  		tmpList.add(timestamp);
  		Collections.sort(tmpList);

  		String result = hashStr(String.join("", tmpList), "SHA1");
  		return result;
  	}

  	public static void main(String[] args)  {
  		try {
  			String sigstr = calcSignature();
  			System.out.format("Signature = %s", sigstr);
  		} catch (NoSuchAlgorithmException e) {
  			e.printStackTrace();
  		}
  	}

  }

NodeJs
^^^^^^^^^^^

.. code-block:: js

  var crypto = require('crypto');

  (function (){
    let hashStr = function (input, algorithm) {
      let hasher = crypto.createHash(algorithm);
      hasher.update(input);
      return hasher.digest('hex').toUpperCase();
    }

    let accessid = "developer-001";
    let accesskey = hashStr("xm90uojWSd34E8y3", "md5");
    let urlpath = "/api/user/13887654321/path/of/the/api";
    let telnum = "13887654321";
    let token = "4C609E5D5D234A406D446EA42898EFAD50E4541C";
    let password = hashStr("This_Is#My&p@ssw0rd", "md5");
    let timestamp = "1407812629434";

    let tmpList = [accessid,accesskey, urlpath, telnum, token, password, timestamp];
    tmpList.sort();
    let signature = hashStr(tmpList.join(''), 'sha1');

    console.log("signature = " + signature);
  })();

Php
^^^^^^^^^^^

.. code-block:: php

  <?php
  $accessid = 'developer-001';
  $accesskey = strtoupper(md5('xm90uojWSd34E8y3'));
  $url_path = '/api/user/13887654321/path/of/the/api';
  $telnum = '13887654321';
  $token = '4C609E5D5D234A406D446EA42898EFAD50E4541C';
  $password = strtoupper(md5('This_Is#My&p@ssw0rd'));
  $timestamp = '1407812629434';
  $tmp_arr = array($accessid, $accesskey, $url_path, $telnum, $token, $password, $timestamp);
  sort($tmp_arr, SORT_STRING);
  $signature = strtoupper(sha1(implode($tmp_arr)));
  echo(signature);

Python
^^^^^^^^^^^

仅适用于 Python 3.0+

.. code-block:: py

  from hashlib import sha1, md5
  accessid = b'developer-001'
  accesskey = bytes(md5(b'xm90uojWSd34E8y3').hexdigest().upper(), 'ascii')
  url_path = b'/api/user/13887654321/path/of/the/api'
  telnum = b'13887654321'
  token = b'4C609E5D5D234A406D446EA42898EFAD50E4541C'
  password = bytes(md5(b'This_Is#My&p@ssw0rd').hexdigest().upper(), 'ascii')
  timestamp = b'1407812629434'
  signature = sha1(b''.join(sorted([accessid, accesskey, url_path, telnum, token, password, timestamp]))).hexdigest().upper()
  print(signature)

获取 Unix 时间戳的例子代码
----------------------------

以下是几种常见语言获取 Unix 时间戳的方法:

C
^^^

.. code-block:: c

  #include <time.h>       /* time_t, struct tm, time ... */

  /// ... ...
  time_t val = time(NULL);
  int ts = (int) val;

  /// ... ...

C#
^^^^^^^

.. code-block:: c#

  int tx = (Int32)(DateTime.UtcNow.Subtract(new DateTime(1970, 1, 1))).TotalSeconds;

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

CTI API 安全
================

.. http:any:: /api/cti/*

  CTI服务器与Web服务器之间的通信与APP的不同，它们之间的API调用是内部的、固定的、无身份的。

  Web服务器 **同时** 使用下面两种方法保障CTI调用的安全：

  1. SSL 客户端证书：

      Web服务器要为CTI服务器颁客户端SSL证书，只有通过SSL验证的HTTP请求才被允许。
      建议使用 `nginx <http://nginx.org/>`_ 进行SSL证书验证，见 http://nginx.org/en/docs/http/ngx_http_ssl_module.html

  2. `HTTP 基本认证 <https://zh.wikipedia.org/wiki/HTTP%E5%9F%BA%E6%9C%AC%E8%AE%A4%E8%AF%81>`_

      Web服务器要针对CTI的HTTP亲求进行HTTP基本认证
