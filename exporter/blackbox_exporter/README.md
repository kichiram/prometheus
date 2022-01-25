# blackbox_exporterのインストール方法
EC2のインスタンス上で実施します。
#### 1. ダウンロード
##### 1.1. homeディレクトリにwgetを利用してダウンロードします。
```
$ cd 
$ wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.19.0/blackbox_exporter-0.19.0.linux-amd64.tar.gz
--- 省略 ---
HTTP request sent, awaiting response... 200 OK
Length: 8881739 (8.5M) [application/octet-stream]
Saving to: ‘blackbox_exporter-0.19.0.linux-amd64.tar.gz’

100%[========================================================================>] 8,881,739   23.3MB/s   in 0.4s   

2021-10-22 09:19:35 (23.3 MB/s) - ‘blackbox_exporter-0.19.0.linux-amd64.tar.gz’ saved [8881739/8881739]
```
##### 1.2. ダウンロードされたことを確認して解凍します。
```
$ ls blackbox_exporter-0.19.0.linux-amd64.tar.gz
blackbox_exporter-0.19.0.linux-amd64.tar.gz
$ tar zxvf blackbox_exporter-0.19.0.linux-amd64.tar.gz 
blackbox_exporter-0.19.0.linux-amd64/
blackbox_exporter-0.19.0.linux-amd64/blackbox.yml
blackbox_exporter-0.19.0.linux-amd64/LICENSE
blackbox_exporter-0.19.0.linux-amd64/NOTICE
blackbox_exporter-0.19.0.linux-amd64/blackbox_exporter
```
#### 2. 動作確認
##### 2.1. 解凍したディレクトリに移動して実行します。
```
$ cd blackbox_exporter-0.19.0.linux-amd64
$ ./blackbox_exporter --config.file=blackbox.yml
level=info ts=2021-10-22T09:22:59.513Z caller=main.go:224 msg="Starting blackbox_exporter" version="(version=0.19.
0, branch=HEAD, revision=5d575b88eb12c65720862e8ad2c5890ba33d1ed0)"
--- 省略 ---
```
##### 2.2. ブラウザでアクセスできるか確認します。
```
http://<ホスト名>:9115/metrics
```
下記のように表示されれば成功です。
![image](https://user-images.githubusercontent.com/91726058/138430572-58d28c30-a282-48aa-880d-f43dce99c0b6.png)
* ホスト名の確認方法は[EC2インスタンスへの接続方法](https://github.com/kichiram/aws/tree/main/connect_ec2_instance/README.md)の「2.2」をご確認ください。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
#### 3. 常駐プロセス化
daemon（常駐プロセス）にして管理しやすいようにします。
##### 3.1. ファイル整理
/etc/prometheus/blackbox_exporterディレクトリを作成し、ファイルをその配下に移動します。
```
$ sudo mkdir /etc/prometheus/blackbox_exporter
$ sudo mv ~/blackbox_exporter-0.19.0.linux-amd64/* /etc/prometheus/blackbox_exporter
$ ls /etc/prometheus/blackbox_exporter
blackbox_exporter  blackbox.yml  LICENSE  NOTICE
```
##### 3.2. daemonの設定ファイル作成
```
$ sudo vi /usr/lib/systemd/system/blackbox_exporter.service
[Unit]
Description=The blackbox exporter allows blackbox probing of endpoints over HTTP, HTTPS, DNS, TCP and ICMP.
Documentation=https://github.com/prometheus/blackbox_exporter
After=network-online.target

[Service]
Type=simple
ExecStart=/etc/prometheus/blackbox_exporter/blackbox_exporter --config.file=/etc/prometheus/blackbox_exporter/blackbox.yml

[Install]
WantedBy=multi-user.target
```
##### 3.3. daemonの自動起動設定と起動
```
$ sudo systemctl enable blackbox_exporter.service
Created symlink from /etc/systemd/system/multi-user.target.wants/blackbox_exporter.service to /usr/lib/systemd/sys
tem/blackbox_exporter.service.
$ sudo systemctl start blackbox_exporter.service
```
##### 3.4. 起動確認
```
$ sudo systemctl status blackbox_exporter.service
● blackbox_exporter.service - The blackbox exporter allows blackbox probing of endpoints over HTTP, HTTPS, DNS, TC
P and ICMP.
   Loaded: loaded (/usr/lib/systemd/system/blackbox_exporter.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2021-10-22 09:38:47 UTC; 2s ago
     Docs: https://github.com/prometheus/blackbox_exporter
 Main PID: 21673 (blackbox_export)
   CGroup: /system.slice/blackbox_exporter.service
           └─21673 /etc/prometheus/blackbox_exporter/blackbox_exporter --config.file=/etc/prometheus/blackbox_e...

Oct 22 09:38:47 ip-172-31-39-113.ap-northeast-1.compute.internal systemd[1]: Started The blackbox exporter all....
--- 省略 ---
```
active (running)と表示されていれば成功です。念のためブラウザでアクセスできるか確認しましょう。
```
http://<ホスト名>:9115/metrics
```
