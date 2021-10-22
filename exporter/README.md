# expoterについて
[公式サイト](https://prometheus.io/docs/instrumenting/exporters/)
### prometheusは基本的にexporterが出力したメトリクスを収集して監視します。
### 公式、非公式のexporterなど多数存在しますがここでは下記のexporterのインストール方法を紹介します。
#### 1. [Node/system metrics exporter](https://github.com/prometheus/node_exporter)
ハードウェアおよびOSのメトリクスを出力します。負荷状況などの監視が実現できます。監視対象サーバにインストールして利用します。
##### 1.1. [node_exporterのインストール方法](node_exporter/README.md)
#### 2. [Blackbox exporter](https://github.com/prometheus/blackbox_exporter)
http/https/TCPなどの死活監視を実現できます。基本的には監視サーバ（prometheusがインストールされているサーバ）にインストールして利用します。
##### 2.1. [blackbox_exporterのインストール方法](blackbox_exporter/README.md)
#### 3. [Grok exporter](https://github.com/fstab/grok_exporter)
非構造化ログデータからメトリクスを出力します。ログ監視が実現できます。監視対象サーバにインストールして利用します。
