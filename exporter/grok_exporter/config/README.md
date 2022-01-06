# grok_exporterの設定
* ログのフォーマットを定義した[パターンファイル](config/patterns/test_httpserver)を用意します。
* ログの監視設定を[config.yml](config/config.yml)に設定します。

今回設定するのは[test_httpserver](https://github.com/kichiram/golang/tree/main/http_server)で「Hello!」と出力されたログのカウンタを入れます。

# パターンファイルの追加方法
#### 1.ダウンロード
```
$ cd;rm -rf test_httpserver
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/exporter/grok_exporter/config/patterns/test_httpserver
$ ls test_httpserver
test_httpserver
```
#### 2.パターンフォルダに配置
```
$ sudo cp -p test_httpserver /etc/prometheus/grok_exporter/patterns/
```
# configファイルの設定方法
#### 1.ダウンロード
```
$ cd;rm -rf config.yml
$ wget https://raw.githubusercontent.com/kichiram/prometheus/main/exporter/grok_exporter/config/config.yml
$ ls config.yml
config.yml
```
#### 2.設定ファイルを上書き
```
$ sudo cp -p config.yml /etc/prometheus/grok_exporter/
```
#### 3.リスタート
```
$ sudo systemctl restart grok_exporter.service
$ sudo systemctl status grok_exporter.service
● grok_exporter.service - Grok exporter - Export Prometheus metrics from arbitrary unstructured log data.
   Loaded: loaded (/usr/lib/systemd/system/grok_exporter.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2022-01-06 09:20:39 UTC; 23s ago
     Docs: https://github.com/fstab/grok_exporter
 Main PID: 5507 (grok_exporter)
   CGroup: /system.slice/grok_exporter.service
           └─5507 /etc/prometheus/grok_exporter/grok_exporter -config /etc/prometheus/grok_exporter/config.yml

Jan 06 09:20:39 ip-172-31-39-113.ap-northeast-1.compute.internal systemd[1]: Started Grok exporter - Export Pr....
Jan 06 09:20:39 ip-172-31-39-113.ap-northeast-1.compute.internal grok_exporter[5507]: Starting server on http:/...
Hint: Some lines were ellipsized, use -l to show in full.
```
