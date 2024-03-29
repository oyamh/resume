# バックエンド開発(Go)
## マイクロサービス
- フロントエンドと複数のAPIサーバーが、それぞれ必要に応じてデータ通信を行います。
- 認証情報はトークン（JWT: JSON Web Token）としてユーザーのブラウザが保有し、HTTPヘッダーに付与して通信を行います。
    - トークンはアクセストークンとリフレッシュトークンの二種類
        - アクセストークン
            - LocalStorageで管理
            - JS上でアクセスできる
            - 通信で使用する
            - HTTP Authorization Bearerヘッダーで送信
        - リフレッシュトークン
            - Cookieで管理
            - HttpOnly & Secure
                - HTTPS通信でのみ使用
                - JSからアクセスできない
            - アクセストークンの再取得でのみ使用する
            - HTTP Cookieヘッダーで送信
    - 各リソースAPIサーバー内で認証情報を検証できます。
        - リソースにアクセスする度に認証APIサーバーへの認証情報の照会をせずに済みます。
## クリーンアーキテクチャ
- 各レイヤーで依存を分離しつつ、依存関係を単一方向にしています。
## データベース関連
- テーブル設計
    - 外部キーを最大限に活用しています。
        - テーブル結合によって効率的な検索を実現しました。
    - 複合的な操作を実現するために、正規化の余地を残しています。
        - フォルダやタグもブックマークも同じテーブルに入れています。
            - ブックマークに対する操作と同じ操作を可能にします。
            - フォルダにタグを付けたり、タグ自体にもタグを付けることができます。
- ORM（Object Relational Mapping）用のライブラリを使わずに実装
    - 事前にSQLを定義し、Stored Procedureとして実行しています。
        - Placeholder機能を使うことで、セキュアなSQLを構築できます。
    - 検索では、サーバー内でSQLを動的に構築して高度な検索を実現しています。
        - 検索クエリに応じて、WHERE句とINNER JOIN句を動的に構築しています。
        - Stored ProcedureとPlaceholderを使うことで、SQLをセキュアに実行しています。
        - フォルダ検索の場合には、WITH RECURSIVE句を使ってフォルダ階層の再帰的な検索ができます。
## キャッシュ
- 依存の分離
    - 開発中はRedisのホスティングを想定していました。
    - 本番環境では費用を抑えるため、Redisのホスティングを諦めてGoogle Cloud Firestoreに変えました。
        - 依存を適切に分離していたので、変更に対して楽に対応できました。
## メールアドレスのみによる認証
- メールアドレスによる認証では、独自に認証コードを発行してEメールで送信しています。
## gRPC通信
- gRPCを使って通信を行います。
    - 通信データには、Protocol Bufferスキーマで定義されたデータ型を使っています。
    - Connectで出力した型安全なコードを使ってクライアントと通信を行います。

---

# 実装内容の例

## 課題
- 検索クエリの組み合わせによる柔軟な検索の実現
- Goを使ったバックエンド
- MySQLデータベース

## 機能の内容
- リクエストとして検索クエリの配列を受け取る
- データベースに検索SQLを実行させて検索結果を引き出す
- レスポンスとして検索結果を表すブックマークの配列を送り返す

## 工夫した点
- 検索クエリは抽象化して受け取って、一つ一つダウンキャスト（型アサーション）して判別
    - 内部的には検索クエリはProtobufのoneofで定義されたものを受け取る
        - oneofは別々の型の配列
    - Protobufの型ごとにGoで定義した検索クエリ型でWrapして、各クエリを空のインターフェイス（interface{}）として返すQueueに変換する
    - 値を使う際にはQueueから取り出した空のインターフェイスにダウンキャスト（型アサーション）を行い、どの検索クエリ型なのかを判別してクエリの値を引き出す
- 検索クエリに沿ったSQLを文字列で構築
    - 検索クエリに合わせてWHERE句のパーツSQLを定義しておく
    - テーブル結合を使って複数のテーブルから同時にデータを引き出す
    - ベースとなるSQLとして通常検索用のSQLと再帰的な検索用のSQLを定義しておく
        - 再帰的な検索はWITH RECURSIVE句をベースとして定義する

## 重視した点
- セキュリティの安全性
    - SQLを動的に構築する際にはSQLインジェクション対策が必要
        - SQLのプレースホルダを使用
- 最小限のトランザクション
    - データを効率的に取得するため、一つのSQLトランザクションで全てのデータを取得
    - データを効率的に挿入するため、沢山のデータをBulk Insertを使って一つのトランザクションで挿入

## 問題点
- Goのダウンキャスト使用によるパフォーマンスの低下
    - 具体的な型を使うよりも速度が落ちてしまう
    - 処理する検索クエリの量はパフォーマンスに影響を与えるほど多くはないはずなので容認
- SQLの保守性の低下
    - SQLの構築をGoの文字列で行うため、エディタの補助を受けにくい

## 使用した技術
- Go
    - インターフェイス
    - ダウンキャスト
- MySQL
    - ストアドプロシージャ
    - プレースホルダ
    - テーブル結合
