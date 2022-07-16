# オレオレ認証局

### オレオレ認証局作成？
```console
openssl genrsa -aes256 -out ./private/privkey.pem 2048
openssl req -new -key ./private/privkey.pem -config openssl.cnf -out ./cert/cacert.csr
openssl x509 -req -in ./cert/cacert.csr -signkey ./private/privkey.pem -days 1000 -out ./cert/cacert.pem
```

### オレオレ認証局の証明書をJDK Keystoreに格納？
```console
keytool -import -cacerts -alias lowbrain_ca -trustcacerts -file 証明書のファイルパス
```