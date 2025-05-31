# mc-mirror-job

このリポジトリは、MinIO Client (`mc`) を使用してデータをミラーリングするための Kubernetes CronJob をデプロイする Helm チャートです。

## 概要

指定されたスケジュールに従って、`mc mirror` コマンドを実行する CronJob を Kubernetes クラスタに作成します。これにより、あるストレージロケーションから別のストレージロケーションへ、定期的にデータを同期することができます。

## 設定

Helm チャートの設定は `values.yaml` ファイルで行います。主な設定項目は以下の通りです。

*   `cronjob.schedule`: CronJob の実行スケジュールを Cron 形式で指定します。(例: `"0 * * * *"` は毎時0分に実行)
*   `cronjob.mcMirror.source`: ミラーリング元のパスを指定します。(例: `"s3alias/source-bucket/path"`)
*   `cronjob.mcMirror.destination`: ミラーリング先のパスを指定します。(例: `"s3alias/destination-bucket/path"`)
*   `cronjob.mcConfigSecretName`: `mc` の設定ファイル (`config.json`) を含む Kubernetes Secret の名前を指定します。この Secret は事前に作成しておく必要があります。

### `pke` ディレクトリについて

`pke` ディレクトリ内のファイル (`pke/valuie.oky.yaml` など) は、作者の特定の環境 (PKE - Polestar Kubernetes Engine) 向けの設定例です。
ご自身の環境に合わせて、プロジェクトルートの `values.yaml` を作成・編集するか、`helm install` コマンド実行時に `-f` オプションで独自の値ファイルを指定してください。

## 利用方法

1.  `mc` の設定ファイル (`config.json`) を含む Secret を作成します。
    ```bash
    kubectl create secret generic mc-config-secret --from-file=config.json=/path/to/your/mc/config.json
    ```
    (Secret名は `cronjob.mcConfigSecretName` の値に合わせてください)

2.  Helm チャートをインストールします。
    ```bash
    helm install <release-name> . -n <namespace>
    ```
    必要に応じて、`-f` オプションでカスタム値ファイルを指定できます。
    ```bash
    helm install <release-name> . -n <namespace> -f my-values.yaml
    ```

## 注意事項

*   ミラーリング元と先の両方に対して、適切なアクセス権限を持つ `mc` 設定を使用してください。
*   CronJob のスケジュールは、データ量やネットワーク帯域を考慮して適切に設定してください。
