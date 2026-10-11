# SPIXD Protocol

SPIXDファミリーのアプリ間連携を定義する、実装言語に依存しない共通仕様です。

## 対象
- [SPIXD Motion](https://github.com/sterakawa/spixd-motion): ジェスチャー撮影、撮影セッション、JPEG保存、QR表示
- [SPIXD Windows Client](https://github.com/sterakawa/spixd-windows-client): ローカル連携、サーバーへのアップロード、結果通知
- SPIXDサーバー: アルバム/公開URL/画像の管理（既存APIの確認後に確定）

## v0.1 草案: ローカルファイル連携
1. Motion はセッションごとのフォルダにJPEGを保存する。
2. 撮影完了かつ全JPEGの書き込みが済んだ時点で `ready.json` を**一時ファイルから原子的にリネーム**して公開する。
3. Windows Client は `ready.json` を検知して1セッションを同一アルバムとして処理する。
4. Windows Client は `result.json` で処理状態とQR表示に必要なURLをMotionに通知する。
5. Motion は利用可能になったURLをQR表示する。写真一覧や撮影後の選択画面は表示しない。

**本仕様は草案です。** SPIXDサーバーの既存API、先行URL発行、画像取り込み方法を調べるまでは、サーバー向け要求形式は確定しません。

## 決定事項
- 最大5枚/組（初期値）
- 無操作30秒で撮影終了（初期値）
- 0枚セッションはアップロードしない
- 撮影後15秒の写真確認画面は**廃止**
- 写真は全部送る。選択・削除UIは設けない
- 公開用SPIXD IDとローカルsessionIdを分離する

## ファイル
- [docs/workflow.md](docs/workflow.md): 状態遷移・失敗時の扱い
- [docs/integration.md](docs/integration.md): 実装者向けの役割・未決事項
- [schemas/ready.schema.json](schemas/ready.schema.json): Motion → アップローダー
- [schemas/result.schema.json](schemas/result.schema.json): アップローダー → Motion
- [examples/ready.json](examples/ready.json), [examples/result.json](examples/result.json): メッセージ例

## 変更管理
変更提案はIssue/PRで行います。アプリ担当Workは共通仕様の破壊的変更を独断で行わず、まず本リポジトリで合意してください。バージョンは `protocolVersion` で識別します。
