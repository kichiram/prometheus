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
* ホスト名の確認方法は[EC2インスタンスへの接続方法](https://github.com/kichiram/aws/tree/main/connect_ec2_instance/README.md)の「2.2」をご確認ください。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
#### 3. 常駐プロセス化
daemon（常駐プロセス）にして管理しやすいようにします。
##### 3.1. ファイル整理
ファイルを/etc/prometheusディレクトリを作成し、その配下に移動します。
```
$ sudo mv ~/prometheus-2.30.3.linux-amd64 /etc/prometheus
$ ls /etc/prometheus
console_libraries  consoles  data  LICENSE  NOTICE  prometheus  prometheus.yml  promtool
```
##### 3.2. daemonの設定ファイル作成
```
$ sudo vi /usr/lib/systemd/system/prometheus.service
[Unit]
Description=Prometheus - Monitoring system and time series database
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
Type=simple
ExecStart=/etc/prometheus/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/etc/prometheus/data

[Install]
WantedBy=multi-user.target
```
##### 3.3. daemonの自動起動設定と起動
```
$ sudo systemctl enable prometheus.service
Created symlink from /etc/systemd/system/multi-user.target.wants/prometheus.service to /usr/lib/systemd/system/pro
metheus.service.
$ sudo systemctl start prometheus.service
```
##### 3.4. 起動確認
```
$ sudo systemctl status prometheus.service
● prometheus.service - Prometheus - Monitoring system and time series database
   Loaded: loaded (/usr/lib/systemd/system/prometheus.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2021-10-22 02:48:12 UTC; 3min 12s ago
     Docs: https://prometheus.io/docs/introduction/overview/
 Main PID: 30411 (prometheus)
   CGroup: /system.slice/prometheus.service
           └─30411 /etc/prometheus/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/etc/prometheus/data

Oct 22 02:48:12 ip-172-31-39-113.ap-northeast-1.compute.internal prometheus[30411]: level=info ts=2021-10-22T02...
--- 省略 ---
```
active (running)と表示されていれば成功です。念のためブラウザでアクセスできるか確認しましょう。
```
http://<ホスト名>:9090
```
