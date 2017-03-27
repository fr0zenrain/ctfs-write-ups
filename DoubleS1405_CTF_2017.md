## Doubles1405 ctf 2017

Android 

-------------

the classes.dex file is corruption,most of the Java discompiler will miss one method which is the truly decrypt function .in IDA you can view the smali code.
​    
```shell
CODE:000005E0                 sget-byte                       v0, System_out # Size of bytecode (in 16-bit units): 0x64
CODE:000005E4                 const/16                        v0, 0x10
CODE:000005E8                 .prologue_end
CODE:000005E8                 .line 88
CODE:000005E8                 new-array                       v1, v0, <t: byte[]>
CODE:000005EC                 fill-array-data                 v1, word_67C
CODE:000005F2                 .line 90
CODE:000005F2                 new-array                       v2, v0, <t: byte[]>
CODE:000005F6                 fill-array-data                 v2, word_694
CODE:000005FC                 .line 92
CODE:000005FC                 new-instance                    v3, <t: StringBuilder>
CODE:00000600                 invoke-direct                   {v3}, <void StringBuilder.<init>() imp. @ _def_StringBuilder__init_@V>
CODE:00000606                 .line 94
CODE:00000606                 const/4                         v0, 0
CODE:00000608
CODE:00000608 loc_608:                                # CODE XREF: CODE:00000624j
CODE:00000608                 array-length                    v4, v1
CODE:0000060A                 if-ge                           v0, v4, loc_626
CODE:0000060E                 .line 96
CODE:0000060E                 aget-byte                       v4, v1, v0
CODE:00000612                 aget-byte                       v5, v2, v0
CODE:00000616                 xor-int/2addr                   v4, v5
CODE:00000618                 .line 97
CODE:00000618                 int-to-char                     v4, v4
CODE:0000061A                 invoke-virtual                  {v3, v4}, <ref StringBuilder.append(char) imp. @ _def_StringBuilder_append@LC>
CODE:00000620                 .line 94
CODE:00000620                 add-int/lit8                    v0, v0, 1
CODE:00000624                 goto                            loc_608
CODE:00000626 # ---------------------------------------------------------------------------
CODE:00000626
CODE:00000626 loc_626:                                # CODE XREF: CODE:0000060Aj
CODE:00000626                 .line 100
CODE:00000626                 new-instance                    v0, <t: SecretKeySpec>
CODE:0000062A                 new-instance                    v1, <t: String>
CODE:0000062E                 invoke-direct                   {v1, v3}, <void String.<init>(ref) imp. @ _def_String__init_@VL>
CODE:00000634                 invoke-virtual                  {v1}, <ref String.getBytes() imp. @ _def_String_getBytes@L>
CODE:0000063A                 move-result-object              v1
CODE:0000063C                 const-string                    v2, aAes # "AES"
CODE:00000640                 invoke-direct                   {v0, v1, v2}, <void SecretKeySpec.<init>(ref, ref) imp. @ _def_SecretKeySpec__init_@VLL>
CODE:00000646                 .line 101
CODE:00000646                 const-string                    v1, aAesEcbPkcs5pad # "AES/ECB/PKCS5Padding"
CODE:0000064A                 invoke-static                   {v1}, <ref Cipher.getInstance(ref) imp. @ _def_Cipher_getInstance@LL>
CODE:00000650                 move-result-object              v1
CODE:00000652                 .line 102
CODE:00000652                 const-string                    v2, a8bl8gduxqjbtqr # "8Bl8GDuXQJbTQrjT6LfQdw=="
CODE:00000656                 .line 104
CODE:00000656                 const/4                         v3, 2
CODE:00000658                 invoke-virtual                  {v1, v3, v0}, <void Cipher.init(int, ref) imp. @ _def_Cipher_init@VIL>
CODE:0000065E                 .line 105
CODE:0000065E                 invoke-static                   {v2}, <ref DatatypeConverter.parseBase64Binary(ref) imp. @ _def_DatatypeConverter_parseBase64Binary@LL>
CODE:00000664                 move-result-object              v0
CODE:00000666                 invoke-virtual                  {v1, v0}, <ref Cipher.doFinal(ref) imp. @ _def_Cipher_doFinal@LL>
CODE:0000066C                 move-result-object              v0
CODE:0000066E                 .line 106
CODE:0000066E                 new-instance                    v1, <t: String>
CODE:00000672                 invoke-direct                   {v1, v0}, <void String.<init>(ref) imp. @ _def_String__init_@VL_0>
CODE:00000678                 .line 107
CODE:00000678                 return-void
```


the python implement as follows:

```python
from Crypto.Cipher import AES
import base64

iv="\x00"*16
x1=[0x00, 0x03, 0x01, 0x00, 0x10, 0x00, 0x00, 0x00,0x61, 0x30, 0x31, 0x62, 0x32,0x63, 0x33, 0x64, 0x34, 0x65,0x35, 0x66, 0x36, 0x67, 0x37, 0x68]
x2=[0x00, 0x03, 0x01, 0x00,0x10, 0x00, 0x00, 0x00, 0x12, 0x5B, 0x42, 0x0F,0x41,  0x11,0x5B, 0x01, 0x46, 0x0B, 0x46, 0x03, 0x59, 0x15, 0x5C, 0x09]

for i in range(0,24):
    x1[i] = x1[i] ^ x2[i]
key =''.join(map(chr,x1))
encrypted = base64.b64decode('8Bl8GDuXQJbTQrjT6LfQdw==')
cipher = AES.new(key[8:],AES.MODE_CBC,iv)
print cipher.decrypt(encrypted)
```