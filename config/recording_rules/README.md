# recording_rules
[公式ドキュメント](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/)
## 設定概要
* レコーディング設定ファイルを用意します。
* レコーディング設定ファイルを[prometheus.yml](prometheus.yml)に設定します。

# レコーディング設定ファイルの設置
#### 1. ダウンロード
```
$ cd; mkdir -p ~/recording_rules
$ cd ~/recording_rules; rm -rf *
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/recording_rules/node.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/recording_rules/test_httpserver.yml
$ ls
node.yml  test_httpserver.yml
```
#### 2. アラート設定ファイルのチェック
```
$ /etc/prometheus/promtool check rules *
```
#### 2. アラート設定ファイルの配置
```
$ sudo cp -pr ~/recording_rules /etc/prometheus/
```
# prometheus.ymlの入れ替え方法
#### 1.ダウンロード
```
$ cd;rm -rf prometheus.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/prometheus.yml
$ ls prometheus.yml 
prometheus.yml
```
#### 2. 設定ファイルのチェック
```
$ /etc/prometheus/promtool check config prometheus.yml 
Checking prometheus.yml
  SUCCESS: 0 rule files found
```
#### 2. 上書き
```
$ sudo cp -p prometheus.yml /etc/prometheus/
```
#### 3. リスタート
```
$ sudo systemctl restart prometheus.service
$ sudo systemctl status prometheus.service
● prometheus.service - Prometheus - Monitoring system and time series database
   Loaded: loaded (/usr/lib/systemd/system/prometheus.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-10-27 08:50:47 UTC; 38s ago
     Docs: https://prometheus.io/docs/introduction/overview/
 Main PID: 20496 (prometheus)
   CGroup: /system.slice/prometheus.service
           └─20496 /etc/prometheus/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=...
-- 省略 --
```
#### 5. 確認
設定内容が確認できます。
```
http://<ホスト名>:9090/config
```
設定したアラートルールが確認できます。
```
http://<ホスト名>:9090/rules
```
アラートの発生状況が確認できます。
```
http://<ホスト名>:9090/alerts
```
#### 6. アラート発生確認
##### 6.1. HighRequest
下記に数回アクセスする
```
http://<ホスト名>:8080/world
```
##### 6.2. EmptyNameFound
下記にアクセスする
```
http://<ホスト名>:8080/hello
```
##### 6.3. serviceDown
test_httpserverを停止する
```
sudo systemctl stop test_httpserver
```
