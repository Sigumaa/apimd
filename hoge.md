# Google Antigravity と主要AIコーディングツールの位置付け

調査基準日: 2026-05-19  
対象読者: 上層部向けPPTXの説明者、エンジニア、技術責任者、IT部門長  
前提: 公式ドキュメント、公式ブログ、公式リリースノート、公式価格、公式セキュリティ情報を優先。公式情報で確認できない内容は「不明」とする。PPTXは2枚資料のため、根拠URLと詳細補足は本資料に集約する。

## 1. エグゼクティブサマリー

- Google周辺サービスは同列の競合ではない。Gemini はAIブランド/モデル/API、Antigravity は開発環境、Google AI Studio は試作、Gemini Enterprise Agent Platform は本番エージェント基盤、Gemini Enterprise app / Agent Gallery は業務ユーザー向けの利用面。
- 主要AIコーディングツールは、提供形態だけでなく、機能領域ごとに差が出る。Copilot / Cursor は補完・IDE・PR周辺が強く、Kiro は仕様/要件/設計が特徴的。Claude Code はCLI自律、Codex はローカル/クラウド横断、Antigravity はブラウザ検証とArtifact体系が差分になる。
- Antigravity は公式ブログで public preview として紹介され、公式価格ページでは Individual plan に Generally Available ラベル、Developer plan via Google One、Organization plan via Google Cloud coming soon が表示される。企業向けの契約、SLA、DPA、Antigravity専用監査ログ、組織価格は未確認。Workspace add-on としての AI Ultra Access は旧 Google AI Ultra for Business だが、新規購入不可・2026-07-07以降削除予定の移行中サービスである。
- Vertex AI の公式ドキュメントは「Vertex AI documentation is no longer being updated」とし、Vertex AI services は Gemini Enterprise Agent Platform の一部になったと説明している。既存ML/MLOps資産の理解では Vertex AI 名称が残るが、新規エージェント基盤の主語は Agent Platform とする。
- 国内導入実績は公表ベースのみ採用した。確認できないものは未確認とし、公表事例の有無を導入規模や効果の比較には使わない。

## 2. 主要AIコーディングツールの機能比較

この章は、PPTX Slide 1 の根拠となる機能棚卸しである。  
旧版の「提供形態 / 強み / 弱み」ではなく、同じ機能カテゴリを6ツールに当て、できること、条件付きでできること、公式情報で確認できないことを分ける。

評価記号:

- ◎: 公式情報で確認でき、当該ツールの中核または差別化要素として扱える
- ○: 公式情報で確認できる
- △: 公式情報で確認できるが、Preview、Beta、限定提供、外部連携依存、または一部制約が強い
- 未確認: 公式情報で確認できない

### 2.1 機能カテゴリ別の差分表

| 機能カテゴリ | Google Antigravity | GitHub Copilot | Cursor | Kiro | Claude Code | Codex |
|---|---|---|---|---|---|---|
| 補完 / 編集支援 | ◎ Tab / Supercomplete | ◎ inline / next edit | ◎ Tab | △ indexing / CLI補完。Tab型は未確認 | △ IDE補助。Tab型は未確認 | △ agent型。inline補完は未確認 |
| チャット / 質問 | ◎ Agent Side Panel | ◎ IDE / GitHub.com Chat | ◎ Agent / Ask | ◎ Agentic chat | ◎ CLI / IDE / Web | ◎ App / CLI / IDE / Web thread |
| コードベース理解 | ◎ semantic search / Knowledge | ○ repo探索 / deep research | ◎ semantic search / grep | ◎ indexing / diagnostics | ◎ repo読取 / regex / git状態 | ◎ 既存コード読解 |
| 複数ファイル編集 | ◎ diff / stage / commit | ◎ Edits / Agent mode | ◎ Agent / Inline Edit | ◎ Autopilot / Supervised | ◎ 複数ファイル修正 | ◎ diff / worktree |
| ターミナル / 実行 / テスト | ◎ integrated terminal | ○ CLI / cloud agent | ◎ terminal sandbox | ○ terminal / dev server | ◎ CLI実行 | ◎ CLI / app terminal / cloud |
| ブラウザ / GUI検証 | ◎ Browser subagent、録画Artifact | △ Copilot app technical preview | ◎ Browser Tool | △ web fetch/search。GUIはMCP依存 | △ Chrome beta / Computer use preview | ◎ in-app browser。Computer Useは制約あり |
| PR / レビュー / Issue | △ diffコメント。直接PRは未確認 | ◎ code review、issue割当、cloud agent | ◎ Bugbot、Agent Review、PR作成 | ○ Web PR、issue label | ◎ GitHub Actions、Auto-fix | ◎ PR review、Slack / Linear委任 |
| クラウド / 非同期委任 | △ ローカル非同期。クラウド継続は未確認 | ◎ cloud agent | ◎ Cloud Agents | △ Kiro Web Preview | △ Claude Code on the web research preview | ◎ Codex Cloud |
| 仕様 / 要件 / 設計 | ○ Planning、Implementation Plan、Artifact | ○ Plan mode / deep research | ○ Plan Mode / Rules / Skills | ◎ Specs、EARS、tasks、PBT | ○ Plan mode / Ultraplan | ○ AGENTS.md、skills、automations |
| MCP / 外部連携 | ○ MCP Store / custom MCP | ◎ GitHub MCP / registry管理 | ◎ MCP、Plugins、Team marketplace | ◎ MCP、Powers、governance | ◎ MCP、hooks、skills、routines | ◎ MCP、Slack、Linear、GitHub |
| モバイル / リモート監視 | 未確認 | ◎ mobile / Web / VS Code remote control | △ Web / PWA / remote desktop | △ Web task lifecycle。専用アプリ未確認 | △ mobile / Web Remote Control preview | △ ChatGPT mobile remote access preview |
| 権限 / sandbox | ◎ Ask / Allow / Deny、Strict Mode、sandbox | ○ policy、firewall、human review | ◎ approval、sandbox、Browser制御 | ◎ protected paths、Web sandbox | ◎ permission modes、server settings | ◎ approval policy、sandbox、network default-off |
| 組織管理 / 監査 / 分析 | △ AI Ultra Accessは移行中。専用監査は未確認 | ◎ policies、metrics、audit logs | ◎ SSO、SCIM、audit、analytics、MDM | ◎ IAM Identity Center、governance、prompt logs | ◎ SSO、SCIM、analytics、Compliance API | ◎ RBAC、managed config、Analytics / Compliance API |
| 料金 / 上限管理 | △ Individual無料、Org plan coming soon | ◎ premium requests、AI Credits移行予定 | ◎ seat / usage / spend limits | ◎ credits / overage | ○ plan枠 + API従量 | ◎ plan別credits / rate card |

### 2.2 PPTX Slide 1 に残す比較軸

PPTXは機能一覧をすべて載せる資料ではないため、公式確認済み機能に基づく比較上の設計判断として、差が読み取りやすい機能領域だけを抽出する。採用軸は以下。

| 採用軸 | 採用理由 |
|---|---|
| 補完・編集支援 | Copilot / Cursor / Antigravity はTab型・inline補完まで確認できる。Claude Code / Codex はagent型編集中心で、inline補完は未確認 |
| コマンド実行 | CLI自律、統合ターミナル、クラウド実行、sandbox実行の提供形態が分かれる |
| 画面操作・検証 | ネイティブブラウザ操作、Web fetch/search、MCP依存、beta/previewで粒度が分かれる |
| 変更レビュー・課題 | PRレビュー、issue割当、外部チャネル委任、差分確認中心のどれを公式に持つかで差が出る |
| クラウド委任 | cloud agent / Cloud Agents / Cloud tasks / Web Preview / ローカル非同期で成熟度と実行場所が分かれる |
| 仕様 / 要件 / 設計 | Kiroは仕様駆動のRequirements / Design / Tasksを公式に持つ。他ツールは計画機能やルール機能として確認される |
| 拡張 / 外部連携 | 全ツールがMCP等を持つが、配布/管理/ガバナンスの粒度が異なる |
| 企業管理 / 統制 | 日本の大企業向け説明では必要。ただしPPTXでは詳細管理表ではなく「企業利用で確認しやすいか」の差として扱う |

### 2.3 プロダクト別機能棚卸し

#### Google Antigravity

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | Tab、Supercomplete、Tab-to-Jump、Tab-to-Import | 確認済み | https://antigravity.google/docs/tab |
| チャット / 質問 | Agent / Agent Side Panel、画像添付、モデル/モード選択、ツール実行 | 確認済み | https://antigravity.google/docs/agent |
| コードベース理解 | semantic search、Knowledge Items | 確認済み | https://antigravity.google/docs/models / https://antigravity.google/docs/knowledge |
| 複数ファイル編集 | Review Changes、code diffs、stage / unstage / commit | 変更上限は不明 | https://antigravity.google/docs/review-changes-manager |
| ターミナル / 実行 | Integrated terminal、Agent-used terminal、Request Review / Always Proceed | 確認済み | https://antigravity.google/docs/terminal / https://antigravity.google/docs/agent-modes-settings |
| ブラウザ / GUI検証 | Browser subagent、Chrome操作、console logs、DOM、screenshots、recordings | 別Chrome profile。Chrome検出またはpath指定が必要 | https://antigravity.google/docs/browser-subagent / https://antigravity.google/docs/separate-chrome-profile |
| テスト / 検証 | localhost等の開発サイト確認、screenshots / recordings / walkthrough Artifact | 対応テストフレームワーク一覧は不明 | https://antigravity.google/docs/browser / https://antigravity.google/docs/screenshots |
| PR / レビュー | diff comments、Source Control、PRコメント対応Workflow例 | 直接PR作成/レビュー専用UIは不明 | https://antigravity.google/docs/review-changes-manager / https://antigravity.google/docs/rules-workflows |
| issue / タスク | Task Groups、Task List、MCP経由issue作成例 | issue専用ライフサイクル管理は不明 | https://antigravity.google/docs/task-list / https://antigravity.google/docs/mcp |
| クラウド / 非同期 | Manager Surfaceで複数workspaceのローカル非同期Agentを管理 | クラウド継続実行は不明 | https://antigravity.google/docs/home / https://antigravity.google/docs/agent-manager |
| 仕様 / 設計 | Planning mode、Implementation Plan、Skills、Artifacts | Artifact review policyに依存 | https://antigravity.google/docs/implementation-plan / https://antigravity.google/docs/skills |
| MCP / 外部連携 | MCP Store、custom MCP、stdio / remote serverUrl | server別認証条件あり | https://antigravity.google/docs/mcp |
| モバイル / リモート | 公式情報で確認できず | 不明 | 不明 |
| 権限 / sandbox | Allow / Deny / Ask、Strict Mode、Sandbox、URL allowlist / denylist | Sandboxは既定無効。Windows sandbox対応は不明 | https://antigravity.google/docs/permissions / https://antigravity.google/docs/strict-mode / https://antigravity.google/docs/sandbox-mode |
| 組織管理 / 監査 | AI Ultra Access（旧 Google AI Ultra for Business）側のライセンス購入・一般管理は確認済み | AI Ultra Accessは新規購入不可・2026-07-07以降削除予定。Antigravity専用の中央管理、監査ログ、SIEM、DLP、CMEKは不明 | https://support.google.com/a/answer/16345165?hl=en / https://support.google.com/a/answer/9197205 |
| 料金 / 上限 | Individual、Developer via Google One、Ultra、Organization plan coming soon | rate limit数値、Org plan価格は不明 | https://antigravity.google/pricing / https://support.google.com/googleone/answer/16287445 |

#### GitHub Copilot

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | inline / ghost text、自然言語コメントからの生成、next edit suggestions、code referencing | Free / paidで上限差あり。next editはIDEによりpreview | https://docs.github.com/en/copilot/concepts/completions/code-suggestions / https://docs.github.com/en/copilot/concepts/completions/code-referencing / https://docs.github.com/en/copilot/get-started/plans |
| チャット / 質問 | Copilot Chat in IDE、GitHub.com Chat | GitHub.comページ文脈Chatは2026-05-18にGA | https://docs.github.com/en/copilot/how-tos/chat-with-copilot/chat-in-ide?tool=vscode / https://github.blog/changelog/2026-05-18-ask-questions-in-context-with-copilot-on-web/ |
| コードベース理解 | repo探索、deep research、設計把握、実装計画 | deep research / planningはcloud agentで提供 | https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/research-plan-iterate |
| 複数ファイル編集 | Copilot Edits / Edit mode、Agent mode | premium request / モデル倍率対象 | https://docs.github.com/en/copilot/how-tos/chat-with-copilot/chat-in-ide?tool=vscode |
| ターミナル / 実行 | Copilot CLI、GitHub操作、PR作成、remote control | macOS / Linux / Windows PowerShell / WSL | https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-copilot-cli |
| ブラウザ / GUI検証 | Copilot appで統合ターミナル/ブラウザ/検証/PR workflow | technical preview。自動視覚検証の範囲は不明 | https://github.blog/changelog/2026-05-14-github-copilot-app-is-now-available-in-technical-preview/ |
| テスト / 実行 | cloud agentでテスト/リンター実行、失敗Actions修正 | repo設定/依存関係に依存。Actions修正はBusiness/Enterprise | https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent / https://github.blog/changelog/2026-05-18-one-click-fixes-for-failing-actions-with-copilot-cloud-agent/ |
| PR / レビュー | Copilot code review、指摘、適用可能な修正案 | 2026-06-01からActions minutesも消費予定 | https://docs.github.com/en/copilot/concepts/agents/code-review |
| issue / タスク | Issue assigneeにCopilotを指定、作業・PR作成・レビュー依頼 | issue割り当て/APIはpublic preview記載あり | https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions |
| クラウド / 非同期 | Copilot cloud agent、agent tasks REST API | REST APIはBusiness/Enterprise public preview | https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent / https://github.blog/changelog/2026-05-13-start-copilot-cloud-agent-tasks-via-the-rest-api/ |
| 仕様 / 設計 | Plan mode、read-only調査、実装計画、未解決質問提示 | 一部IDEでpublic preview | https://docs.github.com/en/copilot/how-tos/chat-with-copilot/chat-in-ide?tool=vscode |
| MCP / 外部連携 | GitHub MCP Server、MCP registry管理、外部MCP server連携 | Jira / Slack / Teams / Linear / Azure Boards / Raycast等の個別対応差は要確認 | https://docs.github.com/en/copilot/concepts/mcp-management |
| モバイル / リモート | Mobile / Web / VS CodeでCLI session監視・操作・停止・承認 | Business/Enterpriseは管理者ポリシーが必要 | https://github.blog/changelog/2026-05-18-remote-control-for-copilot-cli-sessions-now-generally-available-on-mobile-web-and-vs-code/ |
| 権限 / sandbox | cloud agentのbranch、human review、Actions承認、firewall | 完全な漏えい防止保証ではない | https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations |
| 組織管理 / 監査 | policies、content exclusion、usage metrics、activity report、audit logs、MCP registry | audit logは180日。content exclusionはEdit/Agent/cloud agentに未対応制約あり | https://docs.github.com/en/copilot/concepts/policies / https://docs.github.com/en/copilot/concepts/context/content-exclusion |
| 料金 / 上限 | premium requests、2026-06-01からGitHub AI Creditsへ移行予定 | Free追加購入不可。paidはbudget設定あり | https://docs.github.com/en/copilot/concepts/billing/copilot-requests |

#### Cursor

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | Tab、複数行補完、import補完、linter errorを文脈に補完 | 拡張子単位の制御あり | https://cursor.com/help/ai-features/tab |
| チャット / 質問 | Agent、Ask Mode、Checkpoint、queued message | Askは読み取り専用 | https://cursor.com/docs/agent/overview.md / https://cursor.com/help/ai-features/ask-mode |
| コードベース理解 | semantic search、Instant Grep、indexing、file search | `.gitignore` / `.cursorignore`尊重。Cloud Agentsはmulti-root非対応 | https://cursor.com/docs/agent/tools/search.md |
| 複数ファイル編集 | Agent編集、Inline Edit | 複雑な複数ファイル変更はAgent利用 | https://cursor.com/docs/agent/overview.md / https://cursor.com/help/ai-features/inline-edit |
| ターミナル / 実行 | Terminal Tool、macOS/Linux/Windows sandbox | networkは既定制限。環境要件未満では承認要求 | https://cursor.com/docs/agent/tools/terminal.md |
| ブラウザ / GUI検証 | Browser Tool、navigate/click/type/scroll/screenshot、console/network確認 | approval mode、origin allowlist、Enterprise制御あり | https://cursor.com/docs/agent/tools/browser.md |
| テスト / 実行 | local terminal、Cloud Agent VMでbuild/test/UI確認 | Cloud Agentは独立VM | https://cursor.com/docs/cloud-agent/capabilities.md |
| PR / レビュー | Bugbot、Autofix、Agent Review | BugbotはGitHub/GitLab連携、AutofixはCloud Agent使用 | https://cursor.com/docs/bugbot.md / https://cursor.com/docs/agent/agent-review.md |
| issue / タスク | Linear / GitHub / SlackからCloud Agent起動 | repo/model/branch指定あり。Teams連携はSection 3の詳細比較で扱う | https://cursor.com/docs/integrations/linear.md / https://cursor.com/docs/integrations/github.md / https://cursor.com/docs/integrations/slack.md |
| クラウド / 非同期 | Cloud Agents、isolated cloud VM、Web/desktop/API起動 | paid planとusage-based billing/spend limitが必要 | https://cursor.com/docs/cloud-agent.md |
| 仕様 / 設計 | Plan Mode、Rules、Skills、Team rules | Team rulesはTeam/Enterprise dashboard | https://cursor.com/docs/agent/plan-mode.md / https://cursor.com/docs/rules.md / https://cursor.com/docs/skills.md |
| MCP / 外部連携 | MCP、Plugins、Marketplace、Team marketplace | MCP tool callは既定で承認 | https://cursor.com/docs/mcp.md / https://cursor.com/docs/plugins.md |
| モバイル / リモート | Web / PWA、Cloud Agent確認、remote desktop、artifact確認 | ネイティブモバイルアプリは未確認 | https://cursor.com/docs/cloud-agent.md |
| 権限 / sandbox | approval、sandbox、allowlist、CLI permission、Browser origin controls | allowlistはsecurity guaranteeではない | https://cursor.com/docs/agent/security.md / https://cursor.com/docs/cli/reference/permissions.md |
| 組織管理 / 監査 | SSO、SCIM、MDM、analytics、audit logs、Admin API、model/MCP/browser制御 | 一部Enterprise限定 | https://cursor.com/docs/account/teams/dashboard.md / https://cursor.com/docs/enterprise.md |
| 料金 / 上限 | Pro / Pro Plus / Ultra / Teams / Enterprise、team spend limit、pooled usage | EnterpriseはCustom | https://cursor.com/docs/models-and-pricing.md / https://cursor.com/docs/account/teams/pricing.md |

#### Kiro

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | Codebase indexing、CLI autocomplete。indexingはcode suggestions / navigation / context-aware assistanceの文脈支援 | Tab型/inline completionの専用機能は未確認。索引上限は不明 | https://kiro.dev/docs/editor/codebase-indexing/ / https://kiro.dev/docs/cli/autocomplete/ |
| チャット / 質問 | Agentic chat、`#file`、`#folder`、`#terminal`、`#mcp` | 履歴保持期間は不明 | https://kiro.dev/docs/chat/ |
| コードベース理解 | indexing、diagnostics、code intelligence | 診断は導入済み拡張に依存 | https://kiro.dev/docs/chat/diagnostics/ / https://kiro.dev/docs/cli/code-intelligence |
| 複数ファイル編集 | Autopilot / Supervised、hunk単位承認 | Supervisedはsandboxではなくレビュー手段 | https://kiro.dev/docs/chat/autopilot/ / https://kiro.dev/docs/privacy-and-security/ |
| ターミナル / 実行 | terminal integration、dev servers、trusted command | commandはデフォルト承認制 | https://kiro.dev/docs/chat/terminal/ / https://kiro.dev/docs/chat/dev-servers/ |
| ブラウザ / GUI検証 | Web fetch/search、Chrome/PlaywrightはMCP server経由 | Kiro本体のネイティブGUI検証は未確認 | https://kiro.dev/docs/enterprise/governance/web-tools/ / https://kiro.dev/docs/mcp/servers/ |
| テスト / 実行 | diagnostics、property-based tests、hooks | PBTは任意。対応framework詳細は不明 | https://kiro.dev/docs/specs/correctness/ / https://kiro.dev/docs/hooks/ |
| PR / レビュー | Kiro Web PR作成・更新、PRコメント対応、`/kiro all`、`/kiro fix` | Kiro WebはPreview | https://kiro.dev/docs/web/github/ |
| issue / タスク | Specs tasks、GitHub issue assignment、`kiro` label / `/kiro` comment | Web taskは10 concurrent tasks到達時にQueued | https://kiro.dev/docs/web/using-the-agent/creating-tasks/ / https://kiro.dev/docs/specs/ |
| クラウド / 非同期 | Kiro Web autonomous mode | Preview、US regionのみ。モデル手動選択不可 | https://kiro.dev/docs/web/autonomous-mode/ |
| 仕様 / 設計 | Specs、Requirements / Design / Tasks、EARS、Analyze Requirements、Quick Plan | 並列上限は不明 | https://kiro.dev/docs/specs/ / https://kiro.dev/docs/specs/analyze-requirements/ |
| MCP / 外部連携 | MCP、Powers、MCP governance | Web MCPはlocal中心。第三者MCPはKiroが検証しない | https://kiro.dev/docs/mcp/ / https://kiro.dev/docs/powers/ / https://kiro.dev/docs/web/sandbox/mcp/ |
| モバイル / リモート | Web task lifecycle、notifications、dashboard | iOS/Android専用アプリは未確認 | https://kiro.dev/docs/chat/notifications/ / https://kiro.dev/docs/cli/enterprise/monitor-and-track/dashboard/ |
| 権限 / sandbox | protected paths、trusted commands、Web sandbox、network allow-list、secrets/env管理 | MCP stdioはsandbox外で同等権限 | https://kiro.dev/docs/privacy-and-security/ / https://kiro.dev/docs/web/sandbox/ |
| 組織管理 / 監査 | IAM Identity Center、model/MCP/API key/web tools governance、prompt logs、activity reports | Kiro固有CloudTrailイベント一覧は不明 | https://kiro.dev/docs/enterprise/settings/ / https://kiro.dev/docs/enterprise/monitor-and-track/ |
| 料金 / 上限 | Free / Pro / Pro+ / Power credits、overage、model cost | Freeはoverage不可 | https://kiro.dev/pricing/ / https://kiro.dev/docs/billing/ / https://kiro.dev/docs/models/ |

#### Claude Code

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | VS Code inline diff、@mentions、plan review、会話履歴 | Tab型/行内コード補完は未確認 | https://code.claude.com/docs/en/overview |
| チャット / 質問 | コードベースQ&A、構造/実行フロー/設計意図の質問 | コンテキスト上限の影響あり | https://code.claude.com/docs/en/how-claude-code-works |
| コードベース理解 | ファイル読取、regex検索、git状態、`CLAUDE.md` | 型定義ジャンプ等はcode intelligence plugins依存 | https://code.claude.com/docs/en/how-claude-code-works |
| 複数ファイル編集 | 作成、編集、リネーム、再編成 | 権限モード、保護パス、作業ディレクトリ制約あり | https://code.claude.com/docs/en/overview |
| ターミナル / 実行 | Bash/PowerShell、git、ビルド、テスト | 既定は編集/実行/ネットワークで承認要求 | https://code.claude.com/docs/en/permission-modes |
| ブラウザ / GUI検証 | Chrome/Edge操作、console/DOM確認、GIF記録、Computer UseでGUI操作 | Chromeはbeta。Computer Useはpreview / research preview相当の扱いで、利用環境・プラン条件あり | https://code.claude.com/docs/en/chrome / https://code.claude.com/docs/en/computer-use |
| テスト / 実行 | テスト作成・実行・失敗修正 | クラウド実行はVM資源・ネットワーク制約あり | https://code.claude.com/docs/en/claude-code-on-the-web |
| PR / レビュー | `@claude` issue/PR対応、PR作成、Web Auto-fix | GitHub App権限、repo admin、secrets設定が必要 | https://code.claude.com/docs/en/github-actions / https://code.claude.com/docs/en/claude-code-on-the-web |
| issue / タスク | GitHub issue/PR、外部trackerはMCP経由 | 非GitHub trackerの標準内蔵範囲は不明 | https://code.claude.com/docs/en/mcp |
| クラウド / 非同期 | Claude Code on the web | Research Preview。ZDR組織ではWeb/Remote等が無効 | https://code.claude.com/docs/en/claude-code-on-the-web |
| 仕様 / 設計 | Plan mode、Ultraplan | Plan mode中も探索コマンドは実行し得る | https://code.claude.com/docs/en/permission-modes |
| MCP / 外部連携 | HTTP / stdio MCP、OAuth、Claude.ai connectors。SSE transportはdeprecated扱い | 外部prompt injectionリスクあり | https://code.claude.com/docs/en/mcp |
| モバイル / リモート | Remote Control、mobile/Webからローカルsession操作 | research preview。Team/Enterpriseは管理者有効化 | https://code.claude.com/docs/en/remote-control |
| 権限 / sandbox | default / acceptEdits / plan / auto / dontAsk / bypass | bypassは隔離環境向け | https://code.claude.com/docs/en/permission-modes |
| 組織管理 / 監査 | Analytics、OpenTelemetry、Compliance API、server-managed settings | Contribution metricsはpublic beta。ZDRでは利用指標のみ | https://code.claude.com/docs/en/analytics / https://code.claude.com/docs/en/server-managed-settings |
| 料金 / 上限 | Pro / Max / Team / Enterprise、API従量 | 厳密な個別利用回数は不明 | https://claude.com/pricing |

#### Codex

| カテゴリ | 公式確認内容 | ステータス / 制約 | 根拠URL |
|---|---|---|---|
| 補完 / 編集支援 | IDE拡張はcoding agentとして確認 | inline補完専用機能は未確認 | https://developers.openai.com/codex/ide |
| チャット / 質問 | App / CLI / IDE / Webでthread形式の対話 | 提供中 | https://developers.openai.com/codex/app/features |
| コードベース理解 | 既存コード読解、変更、実行 | 提供中 | https://developers.openai.com/codex/cli |
| 複数ファイル編集 | Git差分、stage/revert、inline comment、worktree分離 | Git repository前提の機能あり | https://developers.openai.com/codex/app/review |
| ターミナル / 実行 | CLI、App統合ターミナル、Cloud container | sandbox / approval制約あり | https://developers.openai.com/codex/agent-approvals-security |
| ブラウザ / GUI検証 | in-app browser、Browser use、Computer Use | Computer Useは地域制約あり | https://developers.openai.com/codex/app/browser / https://developers.openai.com/codex/app/computer-use |
| テスト / 実行 | lint/test/build、Cloud checks | project環境とAGENTS.mdに依存 | https://developers.openai.com/codex/cloud/environments |
| PR / レビュー | `@codex review`、自動レビュー、P0/P1中心のGitHub review | Codex Cloud設定が必要 | https://developers.openai.com/codex/integrations/github |
| issue / タスク | Linear issue割当、SlackメンションからCloud task | GitHub接続、environment、paid plan等が必要 | https://developers.openai.com/codex/integrations/linear / https://developers.openai.com/codex/integrations/slack |
| クラウド / 非同期 | Codex Cloudで並列・バックグラウンドtask、PR作成、follow-up | GitHub接続が必要。agent phaseのネットワークは既定オフ | https://developers.openai.com/codex/cloud |
| 仕様 / 設計 | AGENTS.md、skills、automations | 仕様管理専用機能としての網羅仕様は不明 | https://developers.openai.com/codex/app/automations |
| MCP / 外部連携 | STDIO / Streamable HTTP / Bearer token / OAuth MCP | 管理側でallowlist可能 | https://developers.openai.com/codex/mcp |
| モバイル / リモート | ChatGPT mobile remote access | Preview。Windows hostはcoming soon | https://developers.openai.com/codex/remote-connections |
| 権限 / sandbox | approval policy、OS sandbox、OpenAI管理container、network default-off | full access bypassは高リスク | https://developers.openai.com/codex/agent-approvals-security |
| 組織管理 / 監査 | Workspace settings、RBAC、managed configuration、Analytics API、Compliance API | Enterprise中心。一部Business対応範囲は機能ごとに差あり | https://developers.openai.com/codex/enterprise/governance |
| 料金 / 上限 | Plus / Pro / Business / Enterprise / Edu、token-based credit rate card | Free / Goの具体的上限は不明。Pro増量など期間限定の対象は別途記載 | https://developers.openai.com/codex/pricing |

## 3. 各サービスの詳細比較

「強み」「弱み・課題」「典型ユースケース」は、公式情報に基づく筆者評価であり、公式の優劣表現ではない。

| サービス | キャッチフレーズ | 公式情報で確認した提供形態 | 最新ステータス | 想定顧客層 | 強み | 弱み・課題 | 典型ユースケース | 国内導入実績 | 根拠URL |
|---|---|---|---|---|---|---|---|---|---|
| Google Antigravity | ブラウザ込み実装環境 | デスクトップアプリ、Editor、Terminal、Agent Manager、Chrome/browser連携。CLI/モバイル/組織Web管理は不明 | 公式ブログ: public preview。価格ページ: Individual plan generally available、Developer plan via Google One、Organization plan coming soon。初回 2025-11-18。直近 v1.23.2 2026-04-16 | 個人開発者、Google AI Pro / Ultra利用者、限定PoCチーム | ブラウザ検証 | 組織機能未確認 | UIを実装しブラウザで確認するときに使う | 未確認 | https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/ / https://antigravity.google/pricing / https://antigravity.google/changelog |
| GitHub Copilot | GitHub中心の標準AI | CLI、Web、IDE拡張、GitHub Desktop、GitHub Mobile、Windows Terminal、Spark、Spaces、MCP、SDK、Code Review、Cloud agent | Business / Enterprise はGA。Spark、Copilot app、third-party agents等はpreviewを含む。GPT-5.3-Codex は2026-03-18 base/LTS指定、2026-05-17にbase model切替と公式Changelog記載 | GitHub利用企業、開発組織、個人開発者 | GitHub統合 | オンプレ版GitHub未対応 | GitHub上の開発と変更レビューを支援するときに使う | 日立、LINEヤフー | https://docs.github.com/en/copilot/get-started/what-is-github-copilot / https://www.microsoft.com/en/customers/story/23738-hitachi-ltd-github-copilot / https://www.lycorp.co.jp/en/news/release/000877/ |
| Cursor | AI前提の統合IDE | CLI、Web dashboard / Cloud Agents、JetBrains ACP、desktop macOS/Windows/Linux、phone管理、Slack/Teams/GitHub/Linear連携 | 全体GA明示は不明。Desktop latest 3.4。直近 2026-05-13 Cursor 3.4 | AI前提のIDE体験を標準化したい開発チーム | IDE体験 | 全体GA不明 | IDE中心に実装・レビューを回すときに使う | Money Forward公式事例 | https://cursor.com/product / https://cursor.com/pricing / https://cursor.com/security / https://cursor.com/blog/money-forward |
| Kiro | 仕様駆動の開発AI | CLI、Web Preview、Kiro IDE、ACP対応IDE、CI/CD | Kiro本体は2025-11-17にGA。IDE / CLIは現行提供クライアント。WebはPreview。初回 2025-07-14 preview、直近 2026-05-12 | AWS利用企業、仕様駆動開発を重視するチーム | 仕様駆動 | Web制約 | 要件から設計・実装・検証まで追うときに使う | サミット、ヤマトプロテック | https://kiro.dev/blog/general-availability/ / https://kiro.dev/docs/ / https://kiro.dev/docs/web/ / https://aws.amazon.com/jp/blogs/news/summit-kiro-case-study/ / https://aws.amazon.com/jp/blogs/news/184038-2/ |
| Claude Code | CLI起点の自律実装 | CLI、Web、IDE拡張、Claude desktop、Claude iOS監視、Slack、GitHub/GitLab CI、MCP、Agent SDK | 本体GA。2025-05-22 v1.0.0。直近 2026-05-15 v2.1.143。一部 research preview / beta | CLI中心の開発チーム、DevOps/SRE、企業開発組織 | CLI自律 | 従量費注意 | 既存コードの修正・調査・変更提案に使う | 楽天 | https://code.claude.com/docs/en/overview / https://code.claude.com/docs/en/changelog / https://claude.com/ja/customers/rakuten |
| Codex | ローカルとクラウド横断 | CLI、Web / Cloud、IDE拡張、desktop macOS/Windows、mobile remote、GitHub/Slack/Linear、SDK、Security、MCP/plugins/skills | 製品全体ステータス不明。機能別maturity。初回 2025-05-16 research preview。直近 2026-05-14 | OpenAI / ChatGPT導入企業、個人開発者、開発組織 | 環境横断 | 全体GA不明 | ローカル作業とクラウド委任をまたぐときに使う | 未確認 | https://developers.openai.com/codex / https://developers.openai.com/codex/changelog / https://developers.openai.com/codex/enterprise/governance |

### 3.1 Google Antigravity

事実:

- Google の agentic development platform。macOS / Windows / Linux のデスクトップアプリとして提供される。根拠: https://antigravity.google/download
- 公式ブログは Antigravity を public preview と説明する。根拠: https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/
- 価格ページでは Individual plan に Generally Available ラベル、Developer plan via Google One、Organization plan via Google Cloud coming soon が表示される。根拠: https://antigravity.google/pricing
- 主要機能は Editor View、Agent Manager、複数エージェントの非同期実行、browser / terminal / editor をまたぐ実行、Artifacts、Knowledge、Chrome browser control。根拠: https://antigravity.google/docs/editor / https://antigravity.google/docs/agent-manager / https://antigravity.google/docs/browser
- v1.22.2 で unified permissions system が追加された。Agent Permissions、Strict Mode、sandboxing、browser URL allowlist / denylist、`.gitignore`尊重が公式に説明されている。根拠: https://antigravity.google/changelog / https://antigravity.google/docs/agent-permissions / https://antigravity.google/docs/strict-mode / https://antigravity.google/docs/sandbox-mode / https://antigravity.google/docs/allowlist-denylist

推測・解説:

- Antigravity は、AI補完ツールというより、エージェントが実装、ブラウザ確認、成果物レビューを行う開発環境として説明する方が誤解が少ない。
- 日本の大企業では、全社標準候補ではなく、フロントエンド/フルスタック領域の限定PoCから始める位置付けが妥当。

不明:

- Organization plan の価格、GA時期、契約条件、SLA、DPA、監査ログ、管理者による中央制御、国内導入実績。

プラン別に公式情報で確認できる範囲:

| プラン | 契約主体 | 管理主体 | 監査ログ | データ処理条件 | 備考 |
|---|---|---|---|---|---|
| Individual plan | 個人Googleアカウント | 個人 | 未確認 | 未確認 | 価格ページでは Generally Available ラベル |
| Developer plan via Google One | 個人/Google One契約 | 個人 | 未確認 | 未確認 | Google AI Pro / Ultra の利用者向け |
| Organization plan via Google Cloud | Google Cloud経由予定 | 組織想定 | 未確認 | 未確認 | coming soon。価格、SLA、DPA、中央管理は未確認 |

### 3.2 GitHub Copilot

事実:

- GitHub の AI coding assistant。IDE、GitHub.com、GitHub Mobile、GitHub CLI、Windows Terminal、GitHub Desktop 等で利用可能。根拠: https://docs.github.com/en/copilot/get-started/what-is-github-copilot
- Business / Enterprise では、組織/Enterpriseポリシー、利用メトリクス、監査、content exclusion、モデル制御、public code filter、IP indemnity 等を確認できる。根拠: https://docs.github.com/en/copilot/get-started/plans / https://docs.github.com/en/copilot/concepts/context/content-exclusion
- GPT-5.3-Codex は 2026-03-18 に Business / Enterprise 向け base / LTS model に指定され、2026-05-17 のChangelogでbase modelになったと記載された。根拠: https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/fallback-and-lts-models / https://github.blog/changelog/2026-05-17-gpt-5-3-codex-is-now-the-base-model-for-copilot-business-and-enterprise/
- 2026-06-01 から request-based billing から usage-based billing へ移行予定。根拠: https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing
- GitHub Enterprise Server では Copilot は現在利用不可。根拠: https://docs.github.com/en/copilot/get-started/plans

推測・解説:

- GitHub Enterprise Cloud を主開発基盤にしている組織では、管理・監査・開発導線が最も説明しやすい。
- GitHub以外のSCMやオンプレ中心の組織では、接続方法、管理、監査の確認が別に必要になる。

不明:

- Copilot appのGA時期、Preview機能のGA時期、2026-06-01以降の実請求実績、日本国内データ所在地保証。

### 3.3 Cursor

事実:

- Anysphere のAI前提IDE。desktop、CLI、Cloud Agents、JetBrains ACP、Bugbot、Slack / Teams / GitHub / Linear連携を提供。根拠: https://cursor.com/product / https://cursor.com/cloud / https://cursor.com/cli
- Teams / Enterprise で SSO、SCIM、audit logs、model controls、Privacy Mode、usage analytics、centralized billing を確認できる。根拠: https://cursor.com/pricing / https://cursor.com/enterprise
- Securityページでは SOC 2 Type II、TLS、AES-256、ZDR、Privacy Modeが説明される。根拠: https://cursor.com/security / https://cursor.com/data-use
- 公式customer storyとしてMoney Forwardが公表されている。根拠: https://cursor.com/blog/money-forward

推測・解説:

- 開発者体験を重視する現場には受け入れられやすいが、全社標準にする場合はIDE標準化、データ利用、監査、SLAの確認が必要。

不明:

- Cursor全体のGA / Public Preview / Beta表記、初回公開リリース日、公開SLA、Enterprise具体価格、Trust Center非公開資料の詳細。

### 3.4 Kiro

事実:

- AWSのagentic coding service。Kiro本体は2025-11-17にGA。IDE / CLI は現行提供クライアントで、WebはPreview。根拠: https://kiro.dev/blog/general-availability/ / https://kiro.dev/docs/ / https://kiro.dev/docs/web/ / https://kiro.dev/changelog/
- Spec-driven development、agentic chat、hooks、steering files、MCP、チェックポイント、Autonomous mode、Quick Plan、Requirements analysis を提供。根拠: https://kiro.dev/docs/
- Enterprise で SAML/SCIM SSO via AWS IAM Identity Center、組織管理、集中請求、利用分析、governance、Okta / Microsoft Entra、AWS GovCloud、出力補償を確認できる。根拠: https://kiro.dev/enterprise / https://kiro.dev/docs/web/identity-center/
- Enterpriseユーザーのコンテンツはサービス改善に使用しないとされる。根拠: https://kiro.dev/pricing/
- Kiro Web Preview は Kiro IDE / CLI と別に評価する。Web Previewにはリージョン、CMK、governance、MCP等に関する制約がある。根拠: https://kiro.dev/docs/web/identity-center/
- サミットの国内事例がAWS公式ブログで確認できる。根拠: https://aws.amazon.com/jp/blogs/news/summit-kiro-case-study/
- ヤマトプロテックの国内事例がAWS公式ブログで確認できる。根拠: https://aws.amazon.com/jp/blogs/news/184038-2/

推測・解説:

- AWS利用企業、仕様駆動や設計文書との接続を重視する組織に説明しやすい。

不明:

- Kiro単体SLA、コンプライアンス一覧、保持期間詳細、Web GA時期、日本円価格、国内導入社数。

### 3.5 Claude Code

事実:

- Anthropic の agentic coding tool。CLI、IDE、Web、desktop、mobile監視、Slack、GitHub/GitLab CI/CD、MCP、Agent SDKで利用可能。根拠: https://code.claude.com/docs/en/overview
- 2025-05-22 v1.0.0でGA。2026-05-15 v2.1.143が確認時点の公式changelog最新更新。根拠: https://code.claude.com/docs/en/changelog
- Team / Enterprise で座席管理、SSO、audit logs、RBAC、managed settings、利用分析、OpenTelemetry等を確認できる。SCIM/JIT provisioning は Enterprise / Console 向けの設定として扱う。根拠: https://code.claude.com/docs/en/iam / https://code.claude.com/docs/en/monitoring-usage / https://support.claude.com/en/articles/13133195-setting-up-jit-or-scim-provisioning
- ZDRはClaude for EnterpriseのAPI routing等で説明されるが、Claude Code Reviewとは併用不可とされる。根拠: https://code.claude.com/docs/en/zero-data-retention / https://code.claude.com/docs/en/code-review
- 既定は read-only。編集・Bash実行などは承認対象。商用ユーザーのコード・プロンプトは明示opt-inなしに生成モデル訓練に使わない。根拠: https://code.claude.com/docs/en/permissions / https://code.claude.com/docs/en/data-usage
- 楽天の国内事例が公式customer storyで確認できる。根拠: https://claude.com/ja/customers/rakuten

推測・解説:

- CLI中心で既存リポジトリに深く入り込む用途に向く。従量費、承認ルール、ローカルtranscript保存の扱いは企業ポリシーと照合が必要。

不明:

- Preview / beta機能のGA時期、楽天以外の国内導入、Trust Center証跡本文詳細。

### 3.6 Codex

事実:

- OpenAIのcoding agent。CLI、Web / Cloud、IDE、desktop、mobile remote、GitHub / Slack / Linear、Codex SDK、Codex Security、MCP/plugins/skillsを提供。根拠: https://developers.openai.com/codex
- Codex Cloud はデフォルトでインターネットアクセスなし。GitHub連携では短命・最小権限のGitHub App installation tokenを使う。根拠: https://developers.openai.com/codex/security
- Business / Enterprise / Eduでは、デフォルトで入力・出力をモデル改善に使わない。根拠: https://openai.com/enterprise-privacy/
- Enterprise admin / governance、RBAC、SAML SSO、SCIM、Compliance API、Analytics、managed configuration、sandbox mode制御、MCP allowlist等を確認できる。根拠: https://developers.openai.com/codex/enterprise/governance

推測・解説:

- ローカル作業とクラウド委任をまたぐ開発体験を重視する組織に向く。製品全体のGA表記がないため、機能別maturityを確認する必要がある。

不明:

- 製品全体のGA / Public Preview / Beta、地域別可用性、Enterprise / Edu具体単価、国内導入実績、Codex Securityの一般提供範囲。

## 4. Google周辺サービスの位置付け整理

| サービス | 役割（一言） | 利用シーン（具体例） | 想定ユーザー | Antigravityとの関係 | 根拠URL |
|---|---|---|---|---|---|
| Gemini | Googleの共通AIブランド / モデル / アプリ | 調査、文章生成、コード支援、Gemini API、Workspace連携 | 個人、業務部門、開発者、企業 | Antigravityが利用するモデル/AI体験の上位概念。競合ではない | https://ai.google.dev/gemini-api/docs |
| Gemini CLI | ターミナル上のGoogle製AIエージェント | シェルからコード調査、修正、CI/CD、Cloud作業を行う | 開発者、DevOps、SRE | AntigravityのIDE型体験に対するCLI型補完 | https://geminicli.com/docs/ |
| Google Antigravity | エージェント型開発環境 | editor、terminal、browserをまたいで実装と検証を行う | 開発者、限定PoCチーム | コーディング作業の実行面 | https://antigravity.google/ |
| Google AI Studio | Gemini API試作・アプリ生成 | プロンプト試行、API key、Build mode、Cloud Run / GitHub export | 開発者、プロトタイパー | API/アプリ試作の場。Build modeは自然言語でアプリを生成・プレビュー・公開する開発前段の導線 | https://ai.google.dev/aistudio / https://ai.google.dev/gemini-api/docs/aistudio-build-mode |
| Gemini Enterprise Agent Platform | 本番AI・エージェント構築運用基盤 | Agent Studio、ADK、Agent Runtime、Memory、Registry、Gateway、Vertex AI由来サービス | AI基盤チーム、EA、セキュリティ管理者 | Antigravityがコードを書く対象の本番プラットフォーム | https://docs.cloud.google.com/gemini-enterprise-agent-platform/overview |
| Vertex AI | 既存ML/MLOps資産の文脈 | Model Garden、学習、推論、RAG、Vector Search、MLOpsの既存理解 | MLエンジニア、MLOps、既存Google Cloud利用者 | 既存ML資産側。最新公式導線はAgent Platform | https://docs.cloud.google.com/vertex-ai/docs/start/introduction-unified-platform |
| Gemini Enterprise app内 Agent Gallery | 利用可能エージェントの発見・利用・管理 | Google製、組織製、自作、Marketplace由来エージェントを探して使う | 知識労働者、業務部門、管理者 | Antigravityとは利用層が違う。作られたエージェントの配布/利用面 | https://docs.cloud.google.com/gemini/enterprise/docs/agent-gallery |
| Gemini Code Assist | Google系IDE/PRコード支援 | IDE補完、チャット、Agent mode preview、GitHubレビュー | 開発者、Google Cloud利用チーム | Antigravityとは別のAIコーディング支援 | https://developers.google.com/gemini-code-assist/docs/overview |
| Jules | 非同期クラウドコーディングエージェント | issueやrepo作業をクラウドVM上で修正・テスト | 開発者、OSS/チーム開発者 | AntigravityはローカルIDE型、Julesはクラウド非同期型。現行docsではexperimental coding agent、2025-08-06にout of beta。GAラベルは未確認 | https://jules.google/docs / https://jules.google/docs/changelog |
| Firebase Studio | サンセット中のブラウザ型AI開発環境 | 自然言語プロトタイピング、Firebase連携 | Web/Firebase開発者 | 2026-06-22新規workspace停止、2027-03-22終了予定。公式移行先にGoogle AI StudioとAntigravity | https://firebase.google.com/docs/studio/migrating-project |
| Chrome DevTools AI / MCP | ブラウザ検証・デバッグ支援 | CSS、Network、Performance、実ブラウザ操作をAIに渡す | Web開発者 | Antigravityのブラウザ検証と補完関係 | https://developer.chrome.com/docs/devtools/agents |
| Firebase AI Logic / Genkit / ADK | AIアプリ・エージェント実装基盤 | Gemini API接続、agentic app、A2A連携 | アプリ開発者、AIエージェント開発者 | Antigravityで実装する対象ライブラリ/基盤 | https://genkit.dev/docs/js/overview/ / https://adk.dev/ |

公式事実、筆者整理、不明点の分離:

| サービス | 公式確認事項 | 筆者整理 | 不明点 |
|---|---|---|---|
| Gemini | Gemini API / app / Workspace / Cloudで使われるAIブランドとモデル群 | Antigravityの上位モデル/AI体験層 | Gemini app全体の単一GA表記 |
| Antigravity | public preview、desktop app、browser / terminal / editor横断 | コードを書くための開発環境 | Organization plan詳細、監査ログ、中央管理 |
| AI Studio | Gemini API試作、Build mode、Cloud Run / GitHub export。Build modeは自然言語でfull-stack appsを生成し、Cloud RunやGitHubへ出せる | API/アプリ試作の場。Antigravityの前段または隣接ツール | 単体SLA、監査ログ、SSO |
| Agent Platform | 本番エージェント構築・運用基盤 | 企業AI/エージェントの本番基盤 | 全体の単一GA表記、一部機能GA |
| Vertex AI | ドキュメントは更新停止扱い、Vertex AI servicesはAgent Platformの一部 | 既存ML/MLOps資産の文脈 | 既存機能の包括的移行期限 |
| Agent Gallery | Gemini Enterprise app内のエージェント発見・利用・管理 | 業務ユーザーの利用面 | 単体ステータス、国内導入 |
| Gemini Code Assist | IDE補完、チャット、Agent mode preview、GitHubレビュー | Google系のAIコーディング支援。Antigravityとは別製品 | Agent modeのGA時期、国内導入 |
| Jules | docsはexperimental coding agent、changelogは2025-08-06 out of betaを記載 | クラウド非同期型のコーディングエージェント。Antigravityとは利用形態が違う | 単一のGAラベル、国内導入 |
| Firebase Studio | 2026-06-22新規workspace停止、2027-03-22終了予定。移行先にGoogle AI StudioとAntigravity | 比較上は新規採用候補ではなく移行対象 | 既存workspace終了までの個別移行条件 |
| Chrome DevTools AI / MCP | DevTools AI assistanceとChrome DevTools MCPを確認 | ブラウザ検証・デバッグの周辺支援 | 企業向け統制、国内導入 |
| Firebase AI Logic / Genkit / ADK | Gemini API接続、Genkit、ADKを確認 | Antigravityで実装する対象ライブラリ/基盤 | 国内導入、サービス単位の成熟度 |

## 5. 「GeminiとAntigravityは何が違うのか」に30秒で答える整理

Gemini は、GoogleのAIモデル、チャットアプリ、API、Workspace/Cloud機能にまたがる共通ブランド。  
Antigravity は、そのGemini等のモデルを使ってコードを読み、編集し、ブラウザで検証する開発環境。  
Google AI Studio はGemini APIやAIアプリを試作する場所。Gemini Enterprise Agent Platform は本番AI・エージェントを作って運用する基盤。Gemini Enterprise app内 Agent Gallery は、業務ユーザーが利用可能なエージェントを探して使う画面。

短く言うと:

| 用語 | 一言 |
|---|---|
| Gemini | AIそのもの、またはAI体験のブランド |
| Antigravity | AIがコードを書くための開発環境 |
| AI Studio | AI APIとアプリを試す場所 |
| Agent Platform | 本番AI/エージェントを作り運用する基盤 |
| Agent Gallery | 作られたエージェントを業務ユーザーが使う場所 |

## 6. 競合関係 / 補完関係の整理

| 関係 | 対象 | 整理 |
|---|---|---|
| 直接競合に近い | Antigravity、Cursor、Claude Code、Codex、Kiro、GitHub Copilot、Gemini Code Assist、Jules | いずれもコード作成・修正・レビューをAIで支援する。ただしIDE型、CLI型、クラウド委任型、仕様駆動型で責務が異なる。Julesはdocs上experimental coding agent、changelog上out of betaで、単一のGAラベルは未確認 |
| 補完 | Gemini | モデル/API/アプリの共通ブランドであり、Antigravityの上位概念。単純な競合ではない |
| 補完 / 一部重複 | Gemini CLI | CLIでAIエージェントを動かす。AntigravityのIDE型体験と利用場面が違う |
| 補完 / 試作で一部重複 | Google AI Studio | API試作、Build mode、Cloud Run / GitHub export。コード作成の前段・軽量試作に近い |
| 補完 | Gemini Enterprise Agent Platform | 本番AI/エージェント基盤。Antigravityで作るコードの配置先・連携先になり得る |
| 既存資産文脈 | Vertex AI | 既存ML資産側。最新公式導線では Vertex AI services は Agent Platform の一部として扱う |
| 補完 | Agent Gallery | 業務ユーザー向けのエージェント利用面。開発環境ではない |
| 移行対象 | Firebase Studio | 公式にサンセット中。移行先としてGoogle AI Studio / Antigravityが示される |
| 補完 | Chrome DevTools MCP / AI assistance | 実ブラウザ検証・デバッグのための周辺機能 |

## 7. ステークホルダー別の見え方

| 論点 | 経営層 | IT部門長 / CIO | 開発マネージャー | 現場エンジニア | 判断上の注意 |
|---|---|---|---|---|---|
| Google Antigravityをどう位置付けるべきか | Googleの新しい開発AI。ただし投資対象は限定PoC | 管理、監査、契約が未確認。全社標準は不可 | UI/フルスタックの高速検証に使える | ブラウザ確認込みの開発環境として有用 | public previewとIndividual planラベルを企業GAと混同しない |
| GitHub Copilot / Cursor / Claude Code / Codex / Kiroとの違い | 利用中のSCM、クラウド、IDEとの相性で分かれる | GitHub、AWS、OpenAI、Anthropic、独立IDEの契約/統制差を見る | チームの作業スタイルで選定が変わる | IDE、CLI、クラウド委任の好みが分かれる | 単一の勝者を決めず、用途別に評価する |
| Google周辺サービスとの関係 | GeminiはAIブランド、Antigravityは開発環境 | Agent PlatformやEnterprise appは別レイヤー | AI StudioやGemini CLIと使い分ける | 実装、試作、CLI、API基盤で道具が違う | Google製品同士も競合ではなく層が違う |
| 全社導入に向くか | Copilot等の既存実績が説明しやすい | 管理/監査/契約/データ利用の確認が必須 | 標準化しすぎると現場適合を落とす | ツール選択の自由度が生産性に影響 | Antigravityは現時点で全社標準よりPoC向き |
| チーム単位導入に向くか | 効果検証の単位として妥当 | ガードレール付きで許可しやすい | ユースケース別の評価ができる | 実作業で差が見える | 機密コード、外部送信、ログを事前に決める |
| セキュリティ・管理面の懸念 | 事業リスクと説明責任 | データ利用、保持、監査、ID管理、DLP | レビュー責任、承認ルール | ローカル保存、コマンド実行、権限が気になる | 公式情報で不明な項目は不明として扱う |
| 現場生産性への影響 | ROIは実測が必要 | 利用ログと成果指標が必要 | レビュー負荷と教育コストが変わる | 繰り返し作業の削減が期待できる | 公表事例の数値を自社へ一般化しない |
| 今すぐ導入すべきか、検証から始めるべきか | 全社導入より段階導入 | 契約と統制確認後にPoC | チーム単位で比較検証 | 実コードで試さないと分からない | 30〜60日のPoCで評価軸を固定する |

## 8. 日本の大企業での導入検討観点

### 8.1 PoC評価仮説

評価基準: この表は自社検証前の仮説。生産性期待は既存作業フローとの近さと自律実装範囲、教育コストは新しいIDE/CLI/承認運用の学習量、標準化難度は既存SCM/ID/端末管理への適合、レビュー負荷はAI生成差分の確認量、費用変動はseat課金と従量/credit課金の比率で評価した。公式情報で数値がない項目は推測・解説であり、自社PoCで実測する。

| サービス | 使う条件 | 避ける条件 | PoC確認点 | 生産性期待 | 教育コスト | 標準化難度 | レビュー負荷 | 費用変動 |
|---|---|---|---|---|---|---|---|---|
| Google Antigravity | UI実装、ブラウザ検証、複数エージェント体験を試す | 全社管理、監査、契約が先に必要な場合 | 権限、sandbox、ブラウザ操作ログ、機密入力制御 | 中 | 中 | 高 | 中 | 不明 |
| GitHub Copilot | GitHub Enterprise Cloudが標準 | オンプレ版GitHub/オンプレSCM中心 | policy、content exclusion、課金上限、Pull Requestレビュー制約 | 中 | 低 | 低 | 中 | 中 |
| Cursor | AI IDEをチーム標準にできる | IDE標準が固定、端末統制が厳しい | Privacy Mode、audit logs、Cloud Agents、IDE配布 | 高 | 中 | 中-高 | 中 | 中 |
| Kiro | 仕様駆動、AWS/IAM Identity Center親和性が高い | AWS以外の統制基盤中心、Web制約が許容不可 | IDE/CLIとWeb Previewを分けて統制確認 | 中 | 中 | 中 | 中 | 中 |
| Claude Code | CLI運用に強いチーム、既存repo調査/修正が多い | CLI/Bash承認を許可できない | permissions、ZDR条件、transcript、Code Review制約 | 高 | 中 | 中 | 高 | 高 |
| Codex | OpenAI/ChatGPT Enterprise導入済み、クラウド委任を使う | 製品全体GA表記や国内事例が必須 | feature maturity、sandbox、MCP allowlist、Compliance API | 高 | 中 | 中 | 中 | 高 |

### 8.2 既存SCM / IDE / クラウドとの接点

| サービス | SCM | IDE/端末 | CI/CD | ID/管理 | ネットワーク/隔離 | 確認上の注意 |
|---|---|---|---|---|---|---|
| Google Antigravity | Git連携は確認。SCM別管理は要確認 | desktop app、Editor、Terminal、browser | 不明 | 中央管理不明 | sandbox、allowlist/denylistは確認 | 企業管理が未確認 |
| GitHub Copilot | GitHub Enterprise Cloud中心。オンプレ版GitHubは不可 | 主要IDE、CLI、GitHub.com | GitHub Actions等との親和性 | GitHub Enterprise管理 | content exclusion等 | GitHub以外のSCMは別評価 |
| Cursor | GitHub/Linear等の連携あり | Cursor desktop、CLI、JetBrains ACP | CI補助は要確認 | SSO/SCIM/audit logs | Privacy Mode、ZDR説明あり | IDE標準化が前提 |
| Kiro | repo連携、PR系はWeb含む | Kiro IDE / CLI / Web | CI/CD連携あり | IAM Identity Center、governance | Web Previewは制約あり | IDE/CLIとWebを分けて評価 |
| Claude Code | GitHub/GitLab連携 | CLI、IDE、Web、desktop/mobile監視 | GitHub/GitLab CI | Team/Enterprise管理 | permissions、ZDR条件 | Code ReviewとZDR条件に注意 |
| Codex | GitHub連携中心、Slack/Linear連携 | CLI、IDE、desktop、Web/Cloud | クラウドタスク/PR連携 | Enterprise governance | Cloudはデフォルトネットなし | 機能別maturityで評価 |

### 8.3 契約成熟度の確認表

| サービス | 法人契約/販売経路 | DPA/SLA/サポート | データ処理条件 | 企業利用時の扱い |
|---|---|---|---|---|
| Google Antigravity | Organization plan via Google Cloud は coming soon。Termsページは確認 | Organization planのDPA/SLA/監査ログ/中央管理は未確認 | Individual/Google One中心。Terms上確認できる範囲と、企業向けデータ処理条件は分けて確認が必要 | 全社標準として必要な契約・管理情報は未確認 |
| GitHub Copilot | Business / Enterprise plansを確認 | GitHub Enterprise契約条件で要確認 | content exclusion、policy、モデル制御を確認 | GitHub Enterprise Cloud利用組織では確認項目をそろえやすい |
| Cursor | Teams / Enterprise / contact salesを確認 | 公開SLA、契約条項は要確認 | Privacy Mode、ZDR、audit logs説明を確認 | IDE標準化を含めてチーム単位で確認する |
| Kiro | Individual / Team / Enterpriseを確認 | 単体SLA、保持期間詳細は要確認 | Enterprise content not used、IAM Identity Center等を確認 | 仕様駆動/AWS系チームで確認しやすい |
| Claude Code | Team / Enterprise、Anthropic商用条件を確認 | Enterprise条件、SCIM/JIT、ZDR条件は個別確認 | 商用データの学習利用抑制、ZDR条件を確認 | CLI運用、従量費、ZDR条件を分けて確認する |
| Codex | ChatGPT Business / Enterprise / Edu、Codex pricingを確認 | Enterprise/Edu単価、契約条項は要確認 | Enterprise privacy、governance、Compliance APIを確認 | OpenAI導入済み部門では既存契約との関係を確認する |

### 8.4 PoC開始前の必須条件

| 責任主体 | 必須条件 |
|---|---|
| IT部門 | 対象repoは2〜3件、参加者は5〜15名、期間は30〜60日を標準とする。認証方式、端末配布、許可ネットワーク、ログ取得先を開始前に確定する |
| セキュリティ | 対象データは公開/社内低機密を原則とする。秘密情報、顧客個人情報、本番credential、未公開M&A/IR情報の入力を禁止する。停止条件はsecret流出、重大脆弱性混入、ログ欠落、規約違反 |
| 開発マネージャー | AI生成コードのレビュー責任者、承認フロー、禁止タスク、PoCタスク、教育時間、撤退条件を明文化する。全PRは人間レビュー必須とする |
| 経理/購買 | 月額上限、seat数、token/credit上限、部署別配賦、超過時停止条件を設定する。従量費のあるツールは日次または週次で消費を確認する |

### 8.5 本番展開前の確認事項

本表は導入可否を決めるための星取表ではなく、PPTXの比較後に実務側が確認する項目。閾値は自社baselineに対する標準例であり、PoC開始前に自社基準へ置き換える。

| 確認項目 | 確認できる状態 | 確認できない場合のリスク |
|---|---|---|
| 契約 | 法人契約、DPA、SLA、サポート、準拠法が確認済み | 個人利用条件しか確認できない |
| 管理 | SSO/SCIM/RBAC、監査ログ、退職者処理、利用分析が運用可能 | 管理者が利用者・権限・ログを把握できない |
| データ | 学習利用、保持、削除、外部送信、ローカル保存の扱いが明文化済み | 機密コードやプロンプトの扱いが不明 |
| 品質 | AI生成コードのレビュー責任、テスト、脆弱性確認が定義済み | 生成コードの責任主体が曖昧 |
| コスト | 月額上限内に収まり、1PRあたりコストまたは1開発者あたり月額が事前上限を超えない | 従量費やcredit消費の上限がない、または週次で説明できない |
| 成果 | PR lead timeまたはレビュー時間が10%以上改善し、欠陥率/重大手戻り/脆弱性検出が悪化しない | 感想以外の評価指標がない、または欠陥率/重大手戻り/脆弱性検出が悪化 |
| 教育 | 初回教育が半日以内、チーム標準手順が1週間以内に定着 | 教育時間が過大、禁止事項や承認ルールが守られない |

## 9. 不明点・未確認事項

| 対象 | 不明点 |
|---|---|
| Google Antigravity | Organization planの価格、GA時期、契約条件、DPA/SLA、監査ログ、中央管理、国内導入実績 |
| GitHub Copilot | Copilot app GA、Preview機能GA、2026-06-01以降の実請求実績、日本国内データ所在地保証 |
| Cursor | 全体GA表記、初回公開日、公開SLA、Enterprise価格、Trust Center詳細、日本国内導入社数 |
| Kiro | 単体SLA、コンプライアンス一覧、保持期間詳細、Web GA、日本円価格、国内導入社数 |
| Claude Code | Preview/Beta機能GA、楽天以外の国内導入、Trust Center証跡本文 |
| Codex | 製品全体ステータス、地域別可用性、Enterprise/Edu単価、国内導入、Codex Security一般提供範囲 |
| Gemini | Gemini app全体GA表記、日本円価格網羅、機能別日本提供可否 |
| Gemini CLI | 正式GA/Betaラベル、国内導入、単体SLA、国別固定価格 |
| Google AI Studio | 全体GA/Beta表記、単体SLA、監査ログ、SSO、国内導入 |
| Agent Platform / Vertex AI | Agent Platform全体の単一GA、Private Preview機能GA、Vertex AI既存機能の包括的移行期限 |
| Agent Gallery | 単体ステータス、単体SLA、モバイル対応範囲、国内導入 |

## 10. レビュー指摘と修正履歴

詳細なレビュー・修正履歴は [review-history-2026-05.md](/Users/shiyui/Documents/antigravity/docs/review-history-2026-05.md) に分離した。本文には、読者が確認すべき未確認事項と導入前の論点だけを残す。

## 11. 参照URL一覧

機能棚卸しの行単位URLは Section 2.3 に記載した。以下は、本文全体で参照した主要URLである。

### Google Antigravity

- https://antigravity.google/
- https://antigravity.google/download
- https://antigravity.google/pricing
- https://antigravity.google/docs/get-started
- https://antigravity.google/docs/plans
- https://antigravity.google/docs/models
- https://antigravity.google/docs/editor
- https://antigravity.google/docs/agent-manager
- https://antigravity.google/docs/browser
- https://antigravity.google/docs/agent-permissions
- https://antigravity.google/docs/sandbox-mode
- https://antigravity.google/docs/strict-mode
- https://antigravity.google/docs/allowlist-denylist
- https://antigravity.google/changelog
- https://antigravity.google/terms
- https://developers.googleblog.com/build-with-google-antigravity-our-new-agentic-development-platform/
- https://codelabs.developers.google.com/getting-started-google-antigravity

### GitHub Copilot

- https://github.com/features/copilot
- https://github.com/features/copilot/plans
- https://docs.github.com/en/copilot/get-started/what-is-github-copilot
- https://docs.github.com/en/copilot/get-started/features
- https://docs.github.com/en/copilot/get-started/plans
- https://docs.github.com/en/copilot/reference/ai-models/supported-models
- https://docs.github.com/en/copilot/reference/ai-models/model-hosting
- https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing
- https://docs.github.com/en/copilot/concepts/context/content-exclusion
- https://docs.github.com/en/copilot/concepts/agents/code-review
- https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/fallback-and-lts-models
- https://github.blog/changelog/2026-05-17-gpt-5-3-codex-is-now-the-base-model-for-copilot-business-and-enterprise/
- https://github.blog/changelog/2026-05-14-github-copilot-app-is-now-available-in-technical-preview/
- https://copilot.github.trust.page/
- https://www.microsoft.com/en/customers/story/23738-hitachi-ltd-github-copilot
- https://www.lycorp.co.jp/en/news/release/000877/

### Cursor

- https://cursor.com/
- https://cursor.com/product
- https://cursor.com/download
- https://cursor.com/pricing
- https://cursor.com/enterprise
- https://cursor.com/security
- https://cursor.com/data-use
- https://trust.cursor.com/
- https://cursor.com/changelog/05-13-26
- https://cursor.com/blog/cursor-3
- https://cursor.com/cloud
- https://cursor.com/cli
- https://cursor.com/bugbot
- https://cursor.com/blog/money-forward

### Kiro

- https://kiro.dev/
- https://kiro.dev/docs/
- https://kiro.dev/blog/general-availability/
- https://kiro.dev/docs/web/
- https://kiro.dev/docs/web/identity-center/
- https://kiro.dev/pricing/
- https://kiro.dev/changelog/
- https://kiro.dev/enterprise
- https://aws.amazon.com/jp/blogs/news/summit-kiro-case-study/
- https://aws.amazon.com/jp/blogs/news/184038-2/

### Claude Code

- https://code.claude.com/docs/en/overview
- https://code.claude.com/docs/en/changelog
- https://code.claude.com/docs/en/permissions
- https://code.claude.com/docs/en/security
- https://code.claude.com/docs/en/data-usage
- https://code.claude.com/docs/en/zero-data-retention
- https://code.claude.com/docs/en/code-review
- https://code.claude.com/docs/en/iam
- https://code.claude.com/docs/en/monitoring-usage
- https://support.claude.com/en/articles/13133195-setting-up-jit-or-scim-provisioning
- https://www.anthropic.com/pricing
- https://claude.com/ja/customers/rakuten

### Codex

- https://developers.openai.com/codex
- https://developers.openai.com/codex/changelog
- https://developers.openai.com/codex/feature-maturity
- https://developers.openai.com/codex/enterprise/governance
- https://developers.openai.com/codex/security
- https://developers.openai.com/codex/pricing
- https://openai.com/enterprise-privacy/
- https://trust.openai.com/

### Google周辺サービス

- https://ai.google.dev/gemini-api/docs
- https://geminicli.com/docs/
- https://ai.google.dev/aistudio
- https://ai.google.dev/gemini-api/docs/aistudio-build-mode
- https://docs.cloud.google.com/vertex-ai/docs/start/introduction-unified-platform
- https://docs.cloud.google.com/gemini-enterprise-agent-platform/overview
- https://cloud.google.com/gemini-enterprise
- https://docs.cloud.google.com/gemini/enterprise/docs/agent-gallery
- https://docs.cloud.google.com/gemini/enterprise/docs/agents-overview
- https://developers.google.com/gemini-code-assist/docs/overview
- https://jules.google.com/
- https://jules.google/docs
- https://jules.google/docs/changelog
- https://firebase.google.com/docs/studio/migrating-project
- https://developer.chrome.com/docs/devtools/agents
- https://adk.dev/
- https://genkit.dev/docs/js/overview/
