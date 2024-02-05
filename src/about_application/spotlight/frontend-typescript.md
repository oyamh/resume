# フロントエンド開発(TypeScript)
## 外部依存の回避
- ReactとCodeMirror(マークダウン対応のテキストエリア)のみを使って作成しました。
- URLルーティング機構を自作しました。
    - シンプルなSPA(シングルページアプリケーション)を実現できました。
    - 複雑なクエリによる検索を、URLによるアクセスに落とし込みました。
        - 検索クエリ自体を、リンクとしてブックマークして再利用できます。
        - ブラウザのHistory APIのBack & Forwardが使えます。
        - 外部から隠蔽された状態を減らしてシンプルな仕様になったので、機能拡張がしやすくなりました。
- コンポーネントを自作しました。
    - 設計を学ぶため、余計なライブラリを使わずに実装しました。
        - 後のYew(Rust版React)による開発に経験を活かすことができました。
    - 自由度の高いコンポーネントを作って、複数箇所で流用できました。
        - 補完入力ポップアップを表示するコンポーネント
- 可変レイアウトを自作しました。
    - 各カラム、サイドバー、編集パネルのDrag ResizeをCSS Gridで実現しました。
        - 軽快なエリアのリサイズができます。
## gRPC通信
- gRPCを使って通信を行います。
- 通信データには、Protocol Bufferスキーマで定義されたデータ型を使っています。
- Connectで出力した型安全なコードを使ってサーバーと通信を行います。