# オレオレ認証局

### オレオレ認証局作成？（オレオレ認証局になるサーバで操作）
```console
openssl genrsa -aes256 -out ./demoCA/private/cakey.pem 2048
openssl req -new -key ./demoCA/private/cakey.pem -out ./demoCA/cacert.csr -config openssl.cnf
openssl x509 -days 3650 -in ./demoCA/cacert.csr -req -signkey ./demoCA/private/cakey.pem -out ./demoCA/cacert.pem -config openssl.cnf
touch ./demoCA/index.txt
echo 00 > ./demoCA/serial
```
- とりあえずということで秘密鍵のパスフレーズは`oreoreca`で設定している

### サーバ証明書発行要求作成？（サーバ証明書格納先サーバで操作）
```console
openssl genrsa -aes256 -out ./http/privkey.pem 2048
openssl req -new -key ./http/privkey.pem -out ./http/server.csr
openssl ca -in ./http/server.csr -out ./http/server.crt -days 825 -extfile ./demoCA/ext
```
- とりあえずということで秘密鍵のパスフレーズは`oreore`で設定している
- 発行要求の際に入力するコモンネームにはとりあえずサーバのホスト名などを記入

### サーバ証明書発行？（オレオレ認証局で操作）
```console
openssl ca -in ./http/server.csr -out ./http/server.crt -days 825 -extfile ./demoCA/ext -config openssl.cnf
```
- 引数で指定する`./demoCA/ext`の中身としてはURLと一致するFQDNもしくはIPアドレスを指定すること

### サーバ証明書導入？（サーバ証明書格納先サーバで操作）
```console
cp ./http/server.crt /workspace/pki/http/server.crt
openssl rsa -in /workspace/pki/http/privkey.pem -out /workspace/pki/http/privkey-nopass.pem
```
- 1行目のコマンドで認証局で作成した証明書を所定の場所に配置
- 2行目のコマンドで`privkey.pem`に設定されているパスフレーズを解除（解除しないとHTTPサーバ起動時にエラーになった）
- 最後に`server.crt`と`privkey.pem`をHTTPサーバの設定ファイルに設定し，SSL有効化の上サーバを起動

---

### 参考：オレオレ認証局の証明書をJDK Keystoreに格納？
```console
keytool -import -cacerts -alias lowbrain_ca -trustcacerts -file 証明書のファイルパス
```