# 開発モチベーション
機械学習を学ぼうと考えたのですが、学んでどうしたいかの目標がありませんでした。
そこで、まずは自身の情報資産を機械学習に活用できる基盤アプリケーションを作りたいと考えました。
日々蓄積していたブックマークデータを利用したアプリケーションを作ることにしました。
ブックマークをより使いやすくするためのアプローチを考え、実装していきました。

## 開発要件
### ブックマーク管理機能
- 基本的なブックマーク管理機能
- ブラウザのブックマークファイルやブックマークバックアップのインポート機能
### 高度な検索機能
- キーワード、複数タグ、時間範囲指定などで**同時に**絞り込み検索できる機能
### 補足情報の追加機能
- アノテーションとして自由なテキストを追加できる機能
    - ブックマークした理由などのメモや、内容の要約などの追加機能
    - アノテーションの検索機能
- マークダウン形式
### 手軽に扱えるUI
- タグやフォルダの補完入力機能
- キーボードでの操作
    - ブラウザ拡張機能のVimiumやTridactylでの操作