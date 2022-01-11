# alerting_rules
* アラート設定ファイルを用意します。
* アラート設定ファイルをを[prometheus.yml](prometheus.yml)に設定します。

# アラート設定ファイルの設置
#### 1. ダウンロード
```
$ cd; mkdir ~/alerting_rules
$ cd ~/alerting_rules rm -rf *.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/node_monitoring.yaml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/alerting_rules/test_httpserver_request.yaml
$ ls *.yaml
node_monitoring.yaml  test_httpserver_request.yaml
```
#### 2. アラート設定ファイルのチェック
```
$ /etc/prometheus/promtool check rules *
```
#### 2. アラート設定ファイルの配置
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
設定したターゲットが確認できます。
```
http://<ホスト名>:9090/targets
```

