# prometheusのdaemonの設定ファイル修正
reloadを利用できるようにします。（この設定を入れると再起動無しに設定ファイルの再読み込みが可能となります）

# 1. daemonの設定ファイル修正
```
$ sudo vi /usr/lib/systemd/system/prometheus.service
```
ExecStart行の下に下記を追記して保存してください。
```
ExecReload=/bin/kill -HUP $MAINPID
```
# 2. daemonの設定ファイル再読み込み
daemonの設定ファイルを修正した場合に必要なコマンドです。
```
$ sudo systemctl daemon-reload
```
# 3. 再起動
```
$ sudo systemctl restart prometheus.service
```
# 4. 起動確認
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
# 5. reloadお試し
```
$ sudo systemctl reload prometheus.service
```
