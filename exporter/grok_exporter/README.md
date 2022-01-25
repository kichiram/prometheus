# grok_exporterのインストール方法
EC2のインスタンス上で実施します。
#### 1. ダウンロード
##### 1.1. homeディレクトリにwgetを利用してダウンロードします。
```
$ cd 
$ wget https://github.com/fstab/grok_exporter/releases/download/v0.2.8/grok_exporter-0.2.8.linux-amd64.zip
--- 省略 ---
HTTP request sent, awaiting response... 200 OK
Length: 7150603 (6.8M) [application/octet-stream]
Saving to: ‘grok_exporter-0.2.8.linux-amd64.zip’

100%[========================================================================>] 7,150,603   25.6MB/s   in 0.3s   

2021-10-22 09:43:30 (25.6 MB/s) - ‘grok_exporter-0.2.8.linux-amd64.zip’ saved [7150603/7150603]
```
##### 1.2. ダウンロードされたことを確認して解凍します。
```
$  ls grok_exporter-0.2.8.linux-amd64.zip 
grok_exporter-0.2.8.linux-amd64.zip
$ unzip grok_exporter-0.2.8.linux-amd64.zip
Archive:  grok_exporter-0.2.8.linux-amd64.zip
   creating: grok_exporter-0.2.8.linux-amd64/
  inflating: grok_exporter-0.2.8.linux-amd64/grok_exporter 
--- 省略 ---
```
#### 2. 動作確認
##### 2.1. 解凍したディレクトリに移動して設定ファイルを一部変更して実行します。
```
$ cd grok_exporter-0.2.8.linux-amd64
$ cp example/config.yml .;sed -i -e '/host: localhost/d' config.yml
$ ./grok_exporter -config ./config.yml
Starting server on http://ip-172-31-39-113.ap-northeast-1.compute.internal:9144/metrics
--- 省略 ---
```
##### 2.2. ブラウザでアクセスできるか確認します。
```
http://<ホスト名>:9144/metrics
```
下記のように表示されれば成功です。
![image](https://user-images.githubusercontent.com/91726058/138435085-0994710f-666d-4572-894e-7723039c4989.png)
* ホスト名の確認方法は[EC2インスタンスへの接続方法](https://github.com/kichiram/aws/tree/main/connect_ec2_instance/README.md)の「2.2」をご確認ください。
##### 2.3. 実行中を中断します。
EC2のインスタンスに戻りCTRL-Cで実行を中断します。
#### 3. 常駐プロセス化
daemon（常駐プロセス）にして管理しやすいようにします。
##### 3.1. ファイル整理
/etc/prometheus/grok_exporterディレクトリを作成し、ファイルをその配下に移動します。
```
$ sudo mv ~/grok_exporter-0.2.8.linux-amd64 /etc/prometheus/grok_exporter
$ ls /etc/prometheus/grok_exporter
config.yml  example  grok_exporter  patterns
```
##### 3.2. 設定ファイル内のパスを置換
```
$ sed -i -e 's/\.\//\/etc\/prometheus\/grok_exporter\//g' /etc/prometheus/grok_exporter/config.yml
```
##### 3.3. daemonの設定ファイル作成
```
$ sudo vi /usr/lib/systemd/system/grok_exporter.service
[Unit]
Description=Grok exporter - Export Prometheus metrics from arbitrary unstructured log data.
Documentation=https://github.com/fstab/grok_exporter
After=network-online.target

[Service]
Type=simple
ExecStart=/etc/prometheus/grok_exporter/grok_exporter -config /etc/prometheus/grok_exporter/config.ym

[Install]
WantedBy=multi-user.target
```
##### 3.4. daemonの自動起動設定と起動
```
$ sudo systemctl enable grok_exporter.service
Created symlink from /etc/systemd/system/multi-user.target.wants/grok_exporter.service to /usr/lib/systemd/system/
grok_exporter.service.
$ sudo systemctl start grok_exporter.service
```
##### 3.5. 起動確認
```
$ sudo systemctl status grok_exporter.service
● grok_exporter.service - Grok exporter - Export Prometheus metrics from arbitrary unstructured log data.
   Loaded: loaded (/usr/lib/systemd/system/grok_exporter.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2021-10-22 10:17:47 UTC; 1s ago
     Docs: https://github.com/fstab/grok_exporter
 Main PID: 24075 (grok_exporter)
   CGroup: /system.slice/grok_exporter.service
           └─24075 /etc/prometheus/grok_exporter/grok_exporter -config /etc/prometheus/grok_exporter/config.yml...

Oct 22 10:17:47 ip-172-31-39-113.ap-northeast-1.compute.internal systemd[1]: Started Grok exporter - Export Pr....
--- 省略 ---
```
active (running)と表示されていれば成功です。念のためブラウザでアクセスできるか確認しましょう。
```
http://<ホスト名>:9144/metrics
```
