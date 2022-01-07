# ende_cryption
 利用複合式金鑰系統建立一個環境，進行傳輸

# 準備 #
1.直接在目錄中使用 endeCryption 即可，訊息也可直接建立
2.資料夾內的原文名稱需是:message_"name"，name根據使用者名稱變更   
3.等待時間太久會跳掉    
![image](https://user-images.githubusercontent.com/56072039/146652185-34760a45-876b-4d7b-a49d-1da1d44e38aa.png)

4.錯誤太多會跳掉   

# 使用 #
在目錄上使用 endeCryption   
東西太多就用clearFile刪掉 

# 環境 #
- 兩個可執行程式 ( *clearFile, endeCryption*)   
- 存放 user的目錄 ( 裡面有使用者的資料夾)   
- 使用者的資料夾放著訊息原文(可更改）   
![image](https://user-images.githubusercontent.com/56072039/146648545-84413ce5-5c75-426a-8ba1-58299e1c1a69.png)


# 使用順序 #
兩個視窗環境直接使用即可，詳細可以看 複合式金鑰第二版.mkv
```
./endeCryption
```

# endeCryption #
1. 輸入使用者名稱 (接收方)    
2. 屬於傳送者方還是接收者方    
3. 想連接的對象（傳送方）：等待超過30秒會終止    
4. 加密方式（傳送方）    
> 使用對稱式－加密原文（輸入密碼）
> ```
> openssl rc4 -e -in $messageName -out $trans/Message.rc4 -k $Symmetric_key &> /dev/null
> ```   
> <br>  
> 
> 使用非對稱加密（使用接收方的公鑰）－對稱密碼（RSA） 
> ```
> openssl rsautl -encrypt -pubin -inkey $userFolder/${targetName}.PubKey -in $newFolder/sym_key 
> ```    
> <br>   
> 
> 使用非對稱加密(使用傳送方的私鑰簽章)－原文雜湊(MD5)   
> ```
> openssl rsautl -sign -inkey $userPriKey -in $newFolder/HashValue_MD5 -out $trans/HashValue_MD5.rsa
> ```
<br>

5. 解密方式 （接收方）     
> 使用非對稱解密（接收方的私鑰）－獲得對稱密碼     
> ```
> openssl rsautl -decrypt -inkey $userPriKey -in $newFolder/sym_key.rsa -out $ans/sym_key.ans
> ```
> <br>
> 
> 使用對稱密碼解密－獲得原文     
> ```
> openssl rc4 -d -in $newFolder/Message.rc4 -out $ans/Message.ans -k $sym_key 2> /dev/null
> ```
> <br>
> 
> 使用非對稱解密（傳送方的公鑰）－獲得雜湊（MD5）    
> ```
> openssl rsautl -verify -pubin -inkey $userFolder/${targetName}.PubKey -in $newFolder/HashValue_MD5.rsa -out $ans/HashValue_MD5.ans
> ```
<br>

6. 最後確認   
> 接收方將雜湊（sha256）加密（使用接收方的私鑰簽章）   
> ```
> openssl rsautl -sign -inkey $userPriKey -in $newFolder/HashValue_sha256 -out ${targetFolder}/${nowTime}.dir/HashValue_sha256.rsa   
> ```
> <br>
> 
> 傳送方使用非對稱解密（使用接收方的公鑰解簽章）－雜湊(sha256)   
> ```
> openssl rsautl -verify -pubin -inkey $userFolder/${targetName}.PubKey -in $newFolder/HashValue_sha256.rsa -out $newFolder/HashValue_sha256.ans
> ```
<br>
