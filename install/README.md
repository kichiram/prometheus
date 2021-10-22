# prometheusのインストール方法
EC2のインスタンス上で実施します。
#### 1. ダウンロード
##### 1.1. homeディレクトリにwgetを利用してダウンロードします。
```
$ cd 
$ wget https://github.com/prometheus/prometheus/releases/download/v2.30.3/prometheus-2.30.3.linux-amd64.tar.gz
--- 省略 ---
HTTP request sent, awaiting response... 200 OK
Length: 72638078 (69M) [application/octet-stream]
Saving to: ‘prometheus-2.30.3.linux-amd64.tar.gz’

100%[========================================================================>] 72,638,078  25.0MB/s   in 2.8s   

2021-10-21 23:55:16 (25.0 MB/s) - ‘prometheus-2.30.3.linux-amd64.tar.gz’ saved [72638078/72638078]
```
##### 1.2. ダウンロードされたことを確認して解凍します。
```
$ ls prometheus-2.30.3.linux-amd64.tar.gz
prometheus-2.30.3.linux-amd64.tar.gz
$ tar zxvf prometheus-2.30.3.linux-amd64.tar.gz
prometheus-2.30.3.linux-amd64/
prometheus-2.30.3.linux-amd64/consoles/
prometheus-2.30.3.linux-amd64/consoles/index.html.example
--- 省略 ---
prometheus-2.30.3.linux-amd64/prometheus.yml
prometheus-2.30.3.linux-amd64/LICENSE
prometheus-2.30.3.linux-amd64/NOTICE
prometheus-2.30.3.linux-amd64/prometheus
prometheus-2.30.3.linux-amd64/promtool
```
#### 2. 動作確認
##### 2.1. 解凍したディレクトリに移動して実行します。
```
$ cd prometheus-2.30.3.linux-amd64
$ ./prometheus --config.file prometheus.yml
level=info ts=2021-10-22T00:15:39.221Z caller=main.go:400 msg="No time or size retention was set so using the defa
ult time retention" duration=15d
level=info ts=2021-10-22T00:15:39.221Z caller=main.go:438 msg="Starting Prometheus" version="(version=2.30.3, bran
ch=HEAD, revision=f29caccc42557f6a8ec30ea9b3c8c089391bd5df)"
level=info ts=2021-10-22T00:15:39.221Z caller=main.go:443 build_context="(go=go1.17.1, user=root@5cff4265f0e3, dat
e=20211005-16:10:52)"
--- 省略 ---
```
###### もし下記のようなerrorが出力されて失敗する場合は既に起動したプロセスが残っている可能性があります。
```
$ ./prometheus --config.file prometheus.yml
--- 省略 ---
level=error ts=2021-10-22T00:28:49.513Z caller=main.go:651 msg="Unable to start web listener" err="listen tcp 0.0.
0.0:9090: bind: address already in use"
```
###### その場合は下記コマンドで実行されているプロセスを削除した後、再実行してください。
```
$ pgrep prometheus | xargs sudo kill
```
##### 2.2. ブラウザでアクセスできるか確認します。
```
http://<ホスト名>:9090
```
下記のように表示されれば成功です。
![image](https://user-images.githubusercontent.com/91726058/138374406-2be8a58c-7ff7-47a3-85a7-0128ef7422bc.png)
IPアドレス、ホスト名の確認方法は[EC2インスタンスへの接続方法](../../aws/connect_ec2_instance/README.md)の「2.2」をご確認ください。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
