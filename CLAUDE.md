# CLAUDE.md — Claude Code Orchestrator Contract

Claude Code in this repository acts as an **orchestrator, not an implementer**.
Top priorities are "conversation quality" and "context conservation".

## 1) Mission

- Organize, prioritize, and build consensus on user requests
- Delegate to appropriate agents (Codex / Opus Subagents / Gemini)
- Integrate results, make decisions, and present next actions

## 2) Non-Goals (things Claude should NOT do directly)

- Large-scale implementation (guideline: implementations exceeding 10 LOC)
- Large-scale investigation (cross-codebase analysis, web research) → delegate to Opus subagents
- Sequential reading of lengthy logs / large numbers of files

The above must always be delegated.

## 3) Routing Policy

- **Design, planning, complex implementation** → Codex via `general-purpose`
- **External research, broad analysis** → `general-purpose` subagent (Opus)
- **Multimodal input (PDF, video, audio, images)** → Gemini via `gemini-explore`
- **Error root cause analysis** → `codex-debugger`
- **Minor fixes (single file, small changes)** → Claude handles directly

## 4) Delegation Trigger

Delegate when any of the following apply:

1. Output is likely to exceed 10 lines
2. Editing 2 or more files
3. Need to read 3 or more files
4. Design decisions or trade-off comparisons are required
5. Web information or up-to-date information needs to be verified

## 5) Execution Patterns

### A. Foreground (wait for result)
Use when the next step depends on the result. Request a 3–5 bullet summary as the return format.

### B. Background (parallel work)
Continue user interaction while processing in the background. Launch independent tasks concurrently.

### C. Save-to-file (large output)
Save results exceeding 20 lines to `.claude/docs/` and return only a summary to the conversation.

## 6) Output Contract to User

- Lead with the conclusion, then rationale, then next actions
- Make uncertainty explicit (distinguish between speculation, unverified, and needs confirmation)
- Always show executed commands, changed files, and test results

## 7) Quality Gates (before final response)

- Change intent matches the user's request
- Diff files have been self-reviewed
- At least one executable test/check has been run
- If failures exist, clearly state the cause and blast radius

## 8) Language Protocol

- User-facing explanations: Japanese
- Code, identifiers, commands: English

## 9) Repository Conventions

- Python environment uses `uv` (do not use `pip` directly)
- Existing rules in `.claude/rules/` take highest priority
- Research notes are stored in `.claude/docs/research/` (keep empty when distributing templates)

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# @orchestra:template-boundary
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Repository Identity

**プロジェクト**: DICOMO2026 投稿論文  
**ドキュメントクラス**: `dicomopapers.cls`（platex でコンパイル）  
**メインファイル**: 作成時に決定（サンプル: `dicomojsample.tex`）  
**作業ディレクトリ**: `/home/J21027/texwork/DICOMO/2026/DICOMO2026_paper/`

### コンパイルコマンド（platex）

```bash
cd /home/J21027/texwork/DICOMO/2026/DICOMO2026_paper
docker run --rm -v $(pwd):/workspace -w /workspace mylatex:latest \
  /usr/local/bin/build-platex <ファイル名（拡張子なし）>
```

---

## 論文執筆ガイドライン（DICOMO2026 適用）

出典: 島岡政基「論文執筆の作法」情報処理 Vol.60 No.10 (2019)

### 基本構成（IMRAD準拠）

```
1. 序論（Introduction）
   1.1 背景・動機付け
   1.2 目的と仮説
   1.3 本論文の貢献

2. 関連研究

3. 提案手法と評価方法（Methods）
   3.1 提案手法・前提条件
   3.2 評価条件・環境・評価方法

4. 結果（Results）
   4.1 評価結果

5. 考察（Discussion）
   5.1 得られた知見
   5.2 制約事項・限界

6. 結論
   6.1 まとめ
   6.2 今後の取組み
```

> **原則**: IMRAD から大きく外れない。読者が知りたいポイントに辿り着けなくなる構成は避ける。

### 査読採録基準（5項目）

| 項目 | 評価の観点 |
|------|-----------|
| **新規性** | 従来にないアイディア／自明でない組み合わせ／新知見データ |
| **有用性** | 性能評価による実証／製品・公開プロダクトによる確認 |
| **正確さ** | 客観的エビデンス・再現可能性・チャンピオンデータ回避 |
| **構成・読みやすさ** | 論理的飛躍なし・前半簡潔・後半丁寧 |
| **本会との関連** | DICOMO の対象分野との適合性 |

### 各節の書き方の要点

**序論**
- 背景→問題→動機→目的・仮説→貢献の順に展開する
- 「この論文が何をどこまで示すか」を明確に宣言する

**関連研究**
- 幅広いサーベイ（引用数の十分性・多角的調査）を示す
- 類似手法との差異（問題領域・解決手法の違い）を明示する

**提案手法・評価方法**
- 前提条件と評価環境を再現可能なレベルで記述する
- 利用者実験は学習効果が混入しないよう設計する（グループ間カウンターバランス）

**結果**
- 客観性の高いエビデンス（処理時間・精度など）で量的評価する
- チャンピオンデータを避け、多角的に分析する

**考察**
- 著者の思考過程を端折らず丁寧に書く（暗黙知を排除する）
- 制約事項・限界を正直に記述する

**結論**
- 貢献を再確認し、今後の課題を示す

### 推敲チェックリスト

- [ ] 音読して不自然な箇所がないか
- [ ] 各段落にトピックセンテンスがあるか
- [ ] 論理の飛躍がないか（暗黙知になっていないか）
- [ ] チャンピオンデータを使っていないか
- [ ] 実験条件は第三者が再現できるレベルか
- [ ] 第三者（指導教員・先輩）にコメントをもらったか

### 倫理・不正行為

- **捏造・改竄・盗用** は厳禁（研究上の特定不正行為）
- **二重投稿禁止**：本質的に同一内容を複数の論文誌に投稿しない
- **自己剽窃禁止**：自著の既発表論文も適切な引用と権利者申請が必要

### DICOMO特有の注意事項

- `\documentclass[Japanese,noauthor]{dicomopapers}` を使用する
- biographyセクションは記載しない
- ページ数制限なし（フルペーパー推奨で基幹論文誌推薦の対象）

# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# @orchestra:repo-boundary
# ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<!-- Working state below: appended by /start-feature, /design-tracker, and manual notes. -->

---

## 研究コンテキスト：AFAD

**リポジトリ**: https://github.com/Richiesss/AFAD  
**投稿目標**: DICOMO2026（2026年6月）— IID貢献とNon-IID方法論を中心に報告

### 研究概要

**問題設定**: 連合学習（Federated Learning）における二重異質性の同時対応
- **計算能力の異質性**: デバイスごとのモデル幅（容量）が異なる
- **アーキテクチャの異質性**: CNN と Transformer が混在する

**既存手法の限界**:
| 手法 | 対応できること | できないこと |
|------|-------------|------------|
| HeteroFL | 計算能力の異質性（幅スケーリング） | アーキテクチャ間の知識共有 |
| FedGen | アーキテクチャ非依存の知識蒸留 | 異なるモデルサイズへの対応 |

**提案手法（AFAD）**: HeteroFL + FedGen を統合し、両方の異質性を同時に解決した初めてのFL手法

### 技術的貢献

1. **32次元共有ボトルネック層**: CNNとViTが共通潜在空間に射影（アーキテクチャ非依存）
2. **レート依存KD係数**: `α = β = 0.5 / model_rate`（サブレートクライアントの過制約を回避）
3. **HeteroFL統合**: カウントベース集約 + Static BatchNorm + Scalerで幅スケーリングを維持
4. **プロトタイプ正則化**: クラスごとの特徴重心を使ったNon-IID安定化

### 実験結果（論文で主張する核心）

**IID環境（OrganAMNIST、10クライアント）**:

| 手法 | 精度 |
|------|------|
| HeteroFL（計算異質性のみ対応） | ~87% |
| FedGen（アーキテクチャ異質性のみ対応） | 86.32% |
| **AFAD（両方同時対応）** | **88.89%** |

**主張**: 計算能力とアーキテクチャの両方の異質性を同時に扱いながら、
既存の単一対応手法（HeteroFL・FedGen）と同等以上の精度を達成した。
「二重異質性の同時対応は精度トレードオフを生じない」ことを実証する。

### Non-IID（補足的に触れる程度）

- AFAD + Prototype正則化: 72.64%（方法論の提示にとどめる）
- 完全な評価・分析は2026年10月国際会議へ

### 論文では省略すること

- ナイーブ統合が失敗した経緯（60.30%→69.85%の修正過程）
- 4段階の修正の詳細
- Non-IIDギャップの原因分析の詳細

> **理由**: これらを含めると論文がやや複雑になり、核心の主張が埋もれる。
> 提案手法の設計根拠として最小限の説明にとどめ、結果で語る。

### 実装スタック

- **フレームワーク**: Flower（FL）+ PyTorch
- **モデル**: HeteroFL ResNet18、HeteroFL ViT-Small（幅スケーリング: rate=1.0/0.5/0.25）
- **データセット**: OrganAMNIST（IID）、Dirichlet Non-IID（α=0.5）
- **分散実行**: Ray

### 論文スコープ（DICOMO2026）

- **メイン主張**: IID環境でHeteroFL・FedGenと同等以上の精度（88.89%）を達成
- **補足**: Non-IID方法論の提案（評価は今後）
- **後続**: 2026年10月国際会議（Non-IID完全評価）、2027年1月修士論文
