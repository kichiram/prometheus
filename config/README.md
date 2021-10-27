# prometheusの設定ファイルについて
[公式ドキュメント](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)
#### 1. global configuration
共通的な設定を記述します。
#### 2. rule_files configuration
[アラートルール]（https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/）、[レコーディングルール](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/)を記載したファイルを指定します。
#### 3. scrape_configs configuration
メトリクスを収集するターゲットを指定します。
#### 4. alerting configuration
アラートに関する設定を記述します。
#### 5. remote_write / remote_read configuration
外部ストレージへの書き込み、読み込みに関する設定を記述します。
