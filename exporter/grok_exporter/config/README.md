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
#### 2.パターンフォルダに配置
```
$ sudo cp -p test_httpserver /etc/prometheus/grok_exporter/patterns/
```
