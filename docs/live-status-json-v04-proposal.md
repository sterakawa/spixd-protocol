# ライブ状態JSON（v0.4提案）

## 目的
SPIXD Motionの状態を同一PC上のC#アップローダーや監視UIから参照可能にする。これは終了通知 `ready.json` と別の通信である。

## status.json（上書き更新）
```json
{
  "protocolVersion": "0.4",
  "updatedAt": "2026-10-11T15:29:00+09:00",
  "sessionId": "session_20261011_152800_001",
  "sessionState": "capturing",
  "personPresent": true,
  "gestureDetected": null,
  "photoCount": 2,
  "maxPhotos": 5,
  "secondsRemaining": 18
}
```

- Motionが発行元。受信側はC#アップローダー、任意の監視UI。
- `status.json` は一時ファイルに書き、同一ボリューム内の置換で更新する。読み取り側は更新時刻を確認し、古い情報を「現在」と扱わない。
- 書き込み頻度は状態変化時＋必要なら1秒ごとの残り秒数更新を初期案とする。
- `personPresent` は人物検出の有無のステータス。撮影終了判定の直接条件にはしない。
- `sessionState` は `idle`, `capturing`, `finalizing`, `uploading`, `printed` を候補とする。アップロード・印刷状態の確定情報はアップローダーが持つため、Motionが受信結果から反映する。
- セッション未開始時の `sessionId` は `null`。
- 撮影時のCCAPIモニター表示に案内・カウントダウンを重ねる。
- 有効な撮影ジェスチャーが30秒間なければ終了。最大5枚でも終了。
- 0枚ならreadyを出さず待機に戻る。

## ready.json（セッションごとに発行）
JPEGが全て保存された後に発行し、`sessionId`, `photoCount`, `files`, `endReason` を含める。C#アップローダーはこれを一括アップロード開始の合図として扱う。

## 注意
この文書は設計提案。既存のv0.1 JSONスキーマ、Motion、アップローダー、サーバーの実装が変更されたことを意味しない。実装前に両担当で項目と更新頻度を確認する。
