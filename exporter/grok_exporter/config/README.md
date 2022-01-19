# grok_exporterの設定
[公式ドキュメント](https://github.com/fstab/grok_exporter)
## 設定概要
* ログのフォーマットを定義した[パターンファイル](patterns/test_httpserver)を用意します。
* ログの監視設定を[config.yml](config.yml)に設定します。

今回は[test_httpserver](https://github.com/kichiram/golang/tree/main/http_server)で「Hello!」と出力されたログのカウンタを入れます。また、labelにnameパラメータの値を設定します。

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
# メトリクスの出力と確認方法
#### 1. grok_exporterのメトリクス確認
下記にアクセスします。この段階では該当するログが無いため「a_httpserver_log_hello_total」は表示されません。
```
http://<ホスト名>:9144/metrics
```
#### 2. test_httpserverにリクエスト
heollにアクセスします。（複数回やってみてください）
```
http://<ホスト名>:8080/hello
```
heollにnameパラメータを指定してアクセスします。（nameの値は適宜いじって大丈夫です）
```
http://<ホスト名>:8080/hello?name=hogehoge
```
hello以外のログをカウントしない事を確認するため他にもアクセスします。
```
http://<ホスト名>:8080/world
```
#### 3. test_httpserverのログを確認
下記コマンドでログを確認します。設定変更後に出力された「Hello!」ログがメトリクスの対象となります。
```
$ cat /var/log/test_httpserver.log
```
#### 4. 再度grok_exporterのメトリクス確認
下記にアクセスします。ログ出力の件数と「a_httpserver_log_hello_total」のカウンタが一致していることを確認します。
```
http://<ホスト名>:9144/metrics
```
