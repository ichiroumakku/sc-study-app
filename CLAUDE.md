# 支援士クエスト(sc-study-app)プロジェクト概要

## アプリの目的
情報処理安全確保支援士試験の合格を目指す学習アプリ。
過去問の出題・採点・解説に加え、RPG風のレベル可視化でモチベーションを維持する。

## 開発上の制約・方針
- **完全無料構成にこだわる**: GitHub Pages(静的ホスティング) + Supabase(無料枠)のみで運用する
- Claude API(従量課金)は使わない。記述式(科目B)の添削は、開発者がClaude.aiのチャット(Proプラン)に問題を貼り付けて別途対応する運用とする
- ビルドツール(React/npm build等)は使わず、単一の`index.html`にHTML/CSS/JSをすべて記述する
  - 理由: 開発者はiPhone(Safari)のみで開発することも多く、Node.js/npmのビルド環境に依存しない構成が必須
- Supabase-jsはCDN経由(`<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2">`)で読み込む

## 技術構成
- フロントエンド: GitHub Pages(https://ichiroumakku.github.io/sc-study-app/)
- バックエンド: Supabase(プロジェクト名: sc-study-app, リージョン: Tokyo)
  - Project URL: https://gmtfzlfxswzkwqxnldpw.supabase.co
  - 認証: Supabase Auth(メール/パスワード、Confirm emailはOFF設定)

## データベース構成(Supabase)
- `profiles`: user_id, display_name(表示名、ランキング用)
- `questions`: id, exam_type(A1/A2/B), year, question_no, field_tags(配列), difficulty(1-5), body, choices(jsonb), official_answer, explanation
- `field_status`: user_id, field_id, level, exp, mastery_rate(分野別のレベル・EXP管理)
- `answer_logs`: user_id, question_id, user_answer, is_correct, exp_gained(回答履歴)
- `chat_messages`: 将来のチャット深掘り機能用(未実装)
- `leaderboard`: view。profilesと field_status を集計したランキング用ビュー(security_invoker=true設定済み)

### RLS(Row Level Security)方針
- 閲覧: `questions`は認証済み全員、`field_status`と`profiles`も認証済み全員(ランキング表示のため)、`answer_logs`と`chat_messages`は本人のみ
- 書き込み: すべて本人のデータのみ

## 分野タグ(11分野・確定)
- `crypto`: 暗号技術
- `auth`: 認証・アクセス制御
- `network`: ネットワークセキュリティ
- `webapp`: Webアプリセキュリティ
- `malware`: マルウェア対策
- `vuln`: 脆弱性管理
- `secure_coding`: セキュアプログラミング
- `incident`: インシデント対応・フォレンジック
- `management`: セキュリティマネジメント
- `law`: 法律・規格・倫理
- `cloud`: クラウド・ゼロトラスト
