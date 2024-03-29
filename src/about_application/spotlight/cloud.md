# クラウド＆ネットワーク構築
## Google Cloud Engine
- 内部ではMySQLのDockerイメージを入れたコンテナを運用しています。
- Cloud SQLは使いませんでした。
    - 目につきやすく、情報も多かったのですが、費用が掛かるため却下しました。
- Cloud Engineを無料で運用しています。
## Google Cloud Run
- APIサーバーのDockerイメージを入れたDockerコンテナを運用しています。
    - scratchイメージにバイナリファイルのみを組み込んで、軽量かつセキュアなコンテナを運用しています。
## Google Cloud Storage
- リンク先のサイズ変更済みFaviconを保存しています。
## Google Cloud Firestore
- Black Listに登録されたTokenのIDなど、ユーザー制限についての情報を保管しています。
## CloudFlare
- Pages
    - Viteを使って軽量化したコードを展開しています。
- Worker
    - メンテナンスページの表示に使用しています。
        - IPアドレスをWhite List変数に登録することで、特定のアクセスのみを許可できます。
## ネットワーク構築
- 複数のAPIサーバー、DBサーバー、キャッシュサービスのネットワークを構築しました。
- クライアント/サーバー間はgRPCで通信を行っています。
- APIサーバーはそれぞれ個別のドメインで管理しています。
