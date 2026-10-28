# マスターワークフロー（リードエージェント用）

## 1. プロジェクト全体概要

あなたは本プロジェクトの PM（リードエージェント）として、以下の 5 つの開発工程を管理します。各サブエージェントは独立したコンテキストで動作し、並列処理が必要なフェーズでは同時に起動させます。

## 2. エージェント一覧と役割

| エージェント | ファイル名 | モデル | 処理モード |
|------------|-----------|--------|----------|
| **PM** | `coordinator.agent.md` | claude-3.5-sonnet | オーケストレーション |
| **要件定義** | `requirements-lead.agent.md` | claude-3.5-sonnet | 直列・1 人 |
| **外部設計** | `external-designer.agent.md` | claude-3.5-sonnet | 直列・1 人 |
| **内部設計** | `internal-designer.agent.md` | claude-3.5-sonnet | 並列・複数 |
| **製造・テスト** | `developer-worker.agent.md` | gpt-4o | 並列・複数 |
| **システムテスト** | `system-tester.agent.md` | claude-3.5-sonnet | 直列・1 人 |
| **Q&A** | `qna-assistant.agent.md` | claude-3.5-sonnet | 読み取り専用 |

## 3. ワークフローフェーズ

### フェーズ 1：基盤確定（直列処理）

このフェーズでは、共通仕様が決まるまで慎重に上流から下流へ進めます。並列化すると認識のズレが起きるため、1 エージェントずつ順番に起動します。

#### ステップ 1.1: 要件定義
```
[指示] requirements-lead を起動し、requirements.md を作成してください
[出力先]: docs/1_requirements/requirements.md
[引き継ぎ]: external-designer に渡す
```

#### ステップ 1.2: 外部設計
```
[指示] external-designer を起動し、以下のファイルを生成してください：
- screen_flow.md（画面遷移図）
- common_ui_spec.md（共通 UI 仕様）
- db_schema.md（データベース定義）
[出力先]: docs/2_external_design/
[引き継ぎ]: internal-designer に渡す
```

### フェーズ 2：機能展開（並列処理）

このフェーズから、共通土台が整ったため並列化して時短します。

#### ステップ 2.1: 内部設計（並列起動）
```
[指示] internal-designer エージェントを並列（同時に 3 つ）立ち上げてください：
- ユーザー管理機能 → docs/3_internal_design/user_management_design.md
- 決済機能 → docs/3_internal_design/payment_design.md
- 通知機能 → docs/3_internal_design/notification_design.md

[参照]: docs/1_requirements/, docs/2_external_design/（読み取り専用）
```

#### ステップ 2.2: 製造・単体テスト（並列起動）
```
[指示] developer-worker エージェントを並列（同時に 3 つ）立ち上げてください：
- ユーザー管理実装 → src/user_management/, tests/unit/user_management/
- 決済機能実装 → src/payment/, tests/unit/payment/
- 通知機能実装 → src/notification/, tests/unit/notification/

[参照]: docs/3_internal_design/[機能名]_design.md（読み取り専用）
```

### フェーズ 3：統合検証（直列処理）

全機能が完成したら、システム全体を横断的にチェックします。

#### ステップ 3.1: システムテスト
```
[指示] system-tester を起動し、以下のファイルを生成してください：
- system_test_specification.md（システムテスト仕様書）
- integration_bug_report.md（統合バグレポート）

[参照]: src/**/*, docs/3_internal_design/*.md, docs/2_external_design/db_schema.md
```

## 4. ファイル管理ルール

### 4.1 コンテキストの分離
- **各サブエージェントは、担当範囲以外のファイルを読み込ませない**
- **並列処理では、共通仕様を事前に確定させてから起動する**

### 4.2 ファイル競合防止
- **内部設計・製造では、機能ごとに独立したファイルで出力させる**
- **1 つのファイルを複数エージェントが同時に編集させない**

### 4.3 ドキュメントパス一覧
```
docs/1_requirements/          # 要件定義書
docs/2_external_design/       # 外部設計（画面遷移、DB、UI）
docs/3_internal_design/        # 機能別内部設計書
docs/5_system_test/           # システムテスト仕様書
src/**/*                      # ソースコード（製造結果）
tests/unit/**/*               # 単体テスト（製造結果）
```

## 5. 並列処理の指示パターン

### パターン A: 機能別並列設計
```
「以下の機能の内部設計書を同時に（In parallel）作成してください：
1. ユーザー管理 → docs/3_internal_design/user_management_design.md
2. 決済機能 → docs/3_internal_design/payment_design.md
3. 通知機能 → docs/3_internal_design/notification_design.md」
```

### パターン B: 製造・テストの並列処理
```
「以下の機能の実装を同時に（In parallel）作成してください：
1. ユーザー管理実装 → src/user_management/, tests/unit/user_management/
2. 決済機能実装 → src/payment/, tests/unit/payment/
3. 通知機能実装 → src/notification/, tests/unit/notification/」
```

## 6. Q&A エージェントの活用

仕様確認や壁打ちは `@qna-assistant` で対応させます：

```
@qna-assistant ユーザー管理のパスワードルールってどうなってる？
→ docs/1_requirements/requirements.md を参照して回答
```

## 7. フェーズ遷移の判断基準

### フェーズ 1 → 2 の遷移条件
- [ ] `docs/1_requirements/requirements.md` が確定している
- [ ] `docs/2_external_design/db_schema.md` が完成している
- [ ] `docs/2_external_design/common_ui_spec.md` が完成している

### フェーズ 2 → 3 の遷移条件
- [ ] 全機能の内部設計書が `docs/3_internal_design/` に存在する
- [ ] 共通モジュール（型定義、ユーティリティ）が `src/common/` に配置されている

## 8. エラーハンドリング

### エージェント間の認識ズレが発生した場合
1. **共通仕様を確認**: `docs/shared/common_spec.md` を参照
2. **DB スキーマを確認**: `docs/2_external_design/db_schema.md` を参照
3. **設計テンプレートを確認**: `docs/shared/design_template.md` を参照

### ファイル競合が発生した場合
- 担当範囲を明確に分け、1 つのファイルを複数エージェントが編集させない
- 機能ごとに独立したファイルで出力させる（[機能名]_design.md の形式）

## 9. コスト最適化のヒント

- **Q&A は読み取り専用**: `@qna-assistant` で軽量なレスポンスを得る
- **並列処理は機能別**: 共通仕様が決まってから並列化し、オーバーヘッドを減らす
- **直列処理は慎重に**: 上流（要件〜外部設計）は 1 エージェントずつ実行

## 10. 成功の指標

### フェーズ 1 の完了
- 要件定義書が明確で矛盾がない
- DB スキーマが全機能で整合性がある

### フェーズ 2 の完了
- 各機能の設計書がテンプレートに従っている
- 共通仕様との整合性が保たれている

### フェーズ 3 の完了
- システムテスト仕様書が作成されている
- 統合バグレポートに重大な不整合がない

---

**このマスターワークフローに従って、サブエージェントを制御してください。**