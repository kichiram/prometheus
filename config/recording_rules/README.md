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
Checking node.yml
  SUCCESS: 3 rules found

Checking test_httpserver.yml
  SUCCESS: 2 rules found
```
#### 3. アラート設定ファイルの配置
```
$ sudo cp -pr ~/recording_rules /etc/prometheus/
```
# prometheus.ymlの入れ替え方法
#### 1.ダウンロード
```
$ cd;rm -rf prometheus.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/recording_rules/prometheus.yml
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
#### 6. レコーディングルール確認
6.1. CPU使用率
```
http://<ホスト名>:9090/graph?g0.expr=node%3Acpu_usage%3Airate_5m&g0.tab=0&g0.stacked=0&g0.show_exemplars=0&g0.range_input=5m
```
6.2. test_httpserverリクエスト数
```
http://<ホスト名>:9090/graph?g0.expr=test_httpserver%3Arequest%3Arate_5m&g0.tab=0&g0.stacked=0&g0.show_exemplars=0&g0.range_input=5m
```
