# node_exporterのインストール方法
EC2のインスタンス上で実施します。
#### 1. ダウンロード
##### 1.1. homeディレクトリにwgetを利用してダウンロードします。
```
$ cd 
$ wget https://github.com/prometheus/node_exporter/releases/download/v1.2.2/node_exporter-1.2.2.linux-amd64.tar.gz
--- 省略 ---
HTTP request sent, awaiting response... 200 OK
Length: 8898481 (8.5M) [application/octet-stream]
Saving to: ‘node_exporter-1.2.2.linux-amd64.tar.gz’

100%[========================================================================>] 8,898,481   --.-K/s   in 0.08s   

2021-10-22 08:34:35 (101 MB/s) - ‘node_exporter-1.2.2.linux-amd64.tar.gz’ saved [8898481/8898481]
```
##### 1.2. ダウンロードされたことを確認して解凍します。
```
$ ls node_exporter-1.2.2.linux-amd64.tar.gz
node_exporter-1.2.2.linux-amd64.tar.gz
$ tar zxvf node_exporter-1.2.2.linux-amd64.tar.gz
node_exporter-1.2.2.linux-amd64/
node_exporter-1.2.2.linux-amd64/LICENSE
node_exporter-1.2.2.linux-amd64/NOTICE
node_exporter-1.2.2.linux-amd64/node_exporter
```
#### 2. 動作確認
##### 2.1. 解凍したディレクトリに移動して実行します。
```
$ cd node_exporter-1.2.2.linux-amd64
$ ./node_exporter
level=info ts=2021-10-22T08:38:19.006Z caller=node_exporter.go:182 msg="Starting node_exporter" version="(version=
1.2.2, branch=HEAD, revision=26645363b486e12be40af7ce4fc91e731a33104e)"
level=info ts=2021-10-22T08:38:19.006Z caller=node_exporter.go:183 msg="Build context" build_context="(go=go1.16.7
, user=root@b9cb4aa2eb17, date=20210806-13:44:18)"
--- 省略 ---
```
##### 2.2. ブラウザでアクセスできるか確認します。
```
http://<ホスト名>:9100
```
下記のように表示されれば成功です。
![image](https://user-images.githubusercontent.com/91726058/138423171-c603e53e-929b-4137-80d4-680c60bf1302.png)
IPアドレス、ホスト名の確認方法は[EC2インスタンスへの接続方法](../../aws/connect_ec2_instance/README.md)の「2.2」をご確認ください。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
#### 3. 常駐プロセス化
daemon（常駐プロセス）にして管理しやすいようにします。
##### 3.1. ファイル整理
ファイルを/etc/prometheus/node_exporterディレクトリを作成し、その配下に移動します。
```
$ sudo mv ~/node_exporter-1.2.2.linux-amd64 /etc/prometheus/node_exporter
$ ls /etc/prometheus/node_exporter
LICENSE  node_exporter  NOTICE
```
##### 3.2. daemonの設定ファイル作成
```
$ sudo vi /usr/lib/systemd/system/node_exporter.service
[Unit]
Description=The Prometheus Node Exporter exposes a wide variety of hardware- and kernel-related metrics.
Documentation=https://github.com/prometheus/node_exporter
After=network-online.target

[Service]
Type=simple
ExecStart=/etc/prometheus/node_exporter/node_exporter

[Install]
WantedBy=multi-user.target
```
##### 3.3. daemonの自動起動設定と起動
```
$ sudo systemctl enable node_exporter.service
Created symlink from /etc/systemd/system/multi-user.target.wants/node_exporter.service to /usr/lib/systemd/system/
node_exporter.service.
$ sudo systemctl start node_exporter.service
```
##### 3.4. 起動確認
```
$ $ sudo systemctl status node_exporter.service
● node_exporter.service - The Prometheus Node Exporter exposes a wide variety of hardware- and kernel-related metr
ics.
   Loaded: loaded (/usr/lib/systemd/system/node_exporter.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2021-10-22 08:53:40 UTC; 35s ago
     Docs: https://github.com/prometheus/node_exporter
 Main PID: 18797 (node_exporter)
   CGroup: /system.slice/node_exporter.service
           └─18797 /etc/prometheus/node_exporter/node_exporter

Oct 22 08:53:40 ip-172-31-39-113.ap-northeast-1.compute.internal node_exporter[18797]: level=info ts=2021-10-22...
--- 省略 ---
```
active (running)と表示されていれば成功です。念のためブラウザでアクセスできるか確認しましょう。
```
http://<ホスト名>:9100
```
