# scrape_config
* メトリクスを収集したいターゲットを[prometheus.yml](prometheus.yml)に設定します。
* また、blackbox_exporterを利用した死活監視用の設定も合わせて行ないます。

# prometheus.ymlの入れ替え方法
#### 1.ダウンロード
```
$ cd;rm -rf prometheus.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/config/scrape_configs/prometheus.yml
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
設定したターゲットが確認できます。
```
http://<ホスト名>:9090/targets
```

