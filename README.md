# 就活進捗シェア

就活仲間・友人グループ内で、各自の選考進捗をひと目で共有できるWebアプリです。ログイン不要、共有の「グループコード」を入力するだけで使い始められます。

**公開URL: https://tt-prog-commits.github.io/WebSite_4_ShukatuShare/**

## できること

- グループコード（合言葉）を入力するだけで参加でき、アカウント登録・パスワードは不要
- メンバー × 企業のマトリクス表で、全員の選考ステージを一覧表示（ES提出 〜 内定・お祈りまで7段階）
- 自分の行のバッジをタップして、その場でステージをワンタップ更新
- 企業ごとに業界・本選考orインターン区分・WebサイトURL・自分のログインIDを登録可能
- 業界・区分での絞り込み表示、企業列のページ送り（5社ずつ）
- 「内定・お祈り結果」タブで、確定した結果をメンバー別に自動集計
- Firebase（Firestore）を使ったリアルタイム同期。誰かが更新すると、他のメンバーの画面にもすぐ反映される
- 複数端末対応。1つの端末につき1つの名前しか登録できない仕組みで、なりすまし・重複登録を防止

## 使い方

1. 上記URLを開く
2. 仲間内で決めたグループコードを入力（初めての場合は「ランダム生成してコピー」で作成し、友達に共有）
3. 自分の名前を選択、または新規登録
4. 「進捗一覧」「企業追加・更新」「内定・お祈り結果」の3タブで利用

## 技術構成

- フロントエンドのみの単一HTMLファイル（ビルド不要、ライブラリはFirebase SDKのみ使用）
- データストア: [Firebase Firestore](https://firebase.google.com/docs/firestore)
  - `groups/{groupId}` … メンバー一覧
  - `groups/{groupId}/companies/{companyId}` … 企業名・業界・区分・URL
  - `groups/{groupId}/records/{recordId}` … メンバー×企業ごとの選考ステージ・更新日時・ログインID
- 認証機能は無く、グループコードを知っている人だけが該当グループのデータを読み書きできる設計
- GitHub Pages（GitHub Actions経由）で静的ホスティング

## 自分用にセットアップする場合

このリポジトリをフォーク・複製して自分たちのグループ用に使う場合は、以下が必要です。

1. [Firebaseコンソール](https://console.firebase.google.com/)で無料プロジェクトを作成し、Firestore Databaseを有効化
2. ウェブアプリを登録し、発行された `firebaseConfig` を `index.html` 先頭付近の該当箇所に貼り付け
3. Firestoreの「ルール」を以下の内容で公開（グループコードを知っている人だけが読み書きでき、コード一覧の総当たり探索は禁止する設定）

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /groups/{groupId} {
         allow get, write: if true;
         allow list: if false;

         match /{document=**} {
           allow read, write: if true;
         }
       }
     }
   }
   ```

4. リポジトリの Settings → Pages で GitHub Actions を使って公開

## 注意事項

- ログイン機能が無いため、グループコードを知っている人は誰でもそのグループのデータを読み書きできます。他人に推測されにくいランダムなコードの利用を推奨します。
- 個人の就活情報を扱うツールのため、公開リポジトリで運用する場合はグループコードの管理に注意してください。
