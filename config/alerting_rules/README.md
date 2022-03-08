# alerting_rules
[公式ドキュメント](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)
## 設定概要
* アラート設定ファイルを用意します。
* アラート設定ファイルを[prometheus.yml](prometheus.yml)に設定します。

# アラート設定ファイルの設置
#### 1. ダウンロード
```
$ cd; mkdir -p ~/alerting_rules
$ cd ~/alerting_rules; rm -rf *
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/blackbox.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/node_monitoring.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/test_httpserver_log.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/test_httpserver_request.yml
$ ls
blackbox.yml  node_monitoring.yml  test_httpserver_log.yml  test_httpserver_request.yml
```
#### 2. アラート設定ファイルのチェック
```
$ /etc/prometheus/promtool check rules *
Checking blackbox.yml
  SUCCESS: 1 rules found

Checking node_monitoring.yml
  SUCCESS: 4 rules found

Checking test_httpserver_log.yml
  SUCCESS: 1 rules found

Checking test_httpserver_request.yml
  SUCCESS: 1 rules found
```
#### 3. アラート設定ファイルの配置
```
$ sudo cp -pr ~/alerting_rules /etc/prometheus/
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
#### 3. 上書き
```
$ sudo cp -p prometheus.yml /etc/prometheus/
```
#### 4. リロード（運用開始後はリスタートするとメトリクスが途切れるので設定ファイルの変更の場合はリロードを推奨）
```
$ sudo systemctl reload prometheus.service
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
復旧
```
$ sudo systemctl start test_httpserver
```
##### 6.4. HighCPUUsage
CPUに負荷をかける（1度で上がらない場合は数回。ただし、やりすぎると応答なくなります）
```
$ for `seq 1 10`; do sh -c "yes > /dev/null &"; done
```
復旧
```
$ pgrep yes | xargs sudo kill
```
