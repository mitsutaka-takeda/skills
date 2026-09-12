# Skills audit: GPT-6 Astra

監査日: 2026-09-12。対象: `skills/` 以下の全37 `SKILL.md`。

本文、frontmatter、呼び出しポリシーを確認した。関連資料は、invocation規約、setupのADR、質問数制限の却下理由、setupのdomainテンプレート、ask-mattのPHASE-BOUNDARIESなど、指摘の判断に必要なものを参照した。付属スクリプト全体のコード監査やモデルによる実行評価は含まない。

初回監査時点ではこのメモのみ作成した。その後、ユーザーの「修正してコミットして」を受け、説明文8件と優先本文修正を適用した。以下の指摘・行番号は修正前のcommit `131ec0c` に対する記録。適用範囲と検証結果は末尾に記載する。

## 判断基準

[OpenAI: Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)は、簡潔で限定的なdescription、条件付きの資料読み込み、過剰な固定手順の削減、権限境界と完了条件の再点検を勧めている。以下はその観点をこのリポジトリに適用した監査判断であり、OpenAIが個別スキルの変更を指定したものではない。

長さだけでは不合格にしない。TDD、手動セットアップ、共同執筆など、その操作自体に意味のある順序や人間の判断を残す。単一エージェントを既定とする方針、秘密情報の扱い、外部更新の権限、既存のinvocation区分も維持する。

## 優先して修正する箇所

| 対象 | 根拠となる現状 | 修正案 |
| --- | --- | --- |
| `engineering/diagnosing-bugs/SKILL.md:55,66,90` | 再現コマンド完成前の仮説・コード調査を禁止。仮説を必ず3〜5件作らせる | 再現に必要な読み取りと仮説形成を許容する。再現できないときは証拠と未検証事項を区別し、必要なアクセスだけ確認する。仮説数と試行回数を原因の不確実性に合わせる |
| `engineering/ask-matt/SKILL.md:30,32` | compact禁止と、実際のcontextに応じてcompactする指示が同居 | 後者に統一し、決定と根拠を保存して続行する。prototype detourの毎回のhandoffも必要な場合に限定する |
| `engineering/ask-matt/SKILL.md:88`、setupのdescription | 全engineering flowの前提としてsetupを案内 | setupが必須なのはtrackerやlabelに依存する処理と明示する。既存ADR 0001のhard/soft dependency区分に合わせる |
| `engineering/to-spec/SKILL.md:7,17,33,41` | 再インタビューしない一方、seamを毎回確認。user storyにLONG/extremely extensiveを指定 | 合意済みseamを再利用し、未解決の設計判断だけ確認する。storyは合意した利用者・振る舞いの網羅性で評価し、分量を要求しない |
| `engineering/code-review/SKILL.md:19,27` | PRから取得できるbaseも未指定なら質問。明示されたspecよりcommit内のissueを優先 | 明示された比較範囲・specを優先し、PRのbase等で一意に解決できる情報は取得する。未コミット変更を対象にする場合はそれを含むdiffを選ぶ |
| `engineering/prototype/SKILL.md:26` | prototype作成の完了処理に実コードへの反映を含む | prototype依頼の完了は実行可能な成果物と問いへの回答。実装まで依頼されている場合だけ本体へ反映する |
| `engineering/resolving-merge-conflicts/SKILL.md:14` | Stage everythingで無関係な既存変更も含み得る | 操作に必要な解消済みファイルだけstageする。ユーザーの既存変更を保持してmerge/rebaseを完了する |
| `productivity/teach/SKILL.md:110` | 選択肢を同じ単語数、可能なら文字数に強制 | 正解だけ長いなどの手掛かりを避ける、という評価基準にする。文字数合わせ自体を目的にしない |

diagnosing-bugsの「再現後に修正を検証する」規則は残す。再現なしで修正済みと宣言してよい、という変更ではない。

## descriptionの具体案

まず次の8件を対象とする。工程、出力フォーマット、delegation方針などの詳細は本文に残す。説明文の語数に一律の上限は設けない。

| スキル | 新しいdescription案 |
| --- | --- |
| code-review | Review a PR, branch, or working-tree diff against repository standards and the supplied requirements. |
| codebase-design | Design deep modules. Use when choosing module interfaces, seams, or deepening an existing design. |
| diagnosing-bugs | Diagnose hard-to-reproduce bugs and performance regressions with a focused feedback loop. |
| research | Investigate a scoped question using primary sources and save a cited research note. |
| wizard | Generate an interactive Bash guide for setup or cutover steps that require human action. |
| setup-matt-pocock-skills | Configure the issue tracker, triage labels, and domain documentation used by these skills. |
| to-tickets | Publish a plan or spec as independently verifiable tickets with explicit blocking dependencies. |
| migrate-to-shoehorn | Migrate TypeScript test assertions to @total-typescript/shoehorn when that migration is requested. |

`tdd`の「integration testsだけでも発火」と「red-green-refactorをtriggerに含むがrefactorはreview工程に限定」は別の仕様判断。自動的には変更しない。`migrate-to-shoehorn`は現在のpartial test dataという条件だけで依存追加を伴うmigrationへ誘導し得るため、migrationを求める条件に限定する。

## 全件の判定

「維持」は今回の観点で優先的な変更が不要という意味であり、実行品質の保証ではない。「要調整」は上の案、または行内の具体案が対象。「設計確認」は既存の利用方法を変えるため、一括の文章整理には含めない。

| バケット | スキル | 判定・理由 |
| --- | --- | --- |
| engineering | ask-matt | 要調整: context指示の矛盾とsetup前提を修正。詳細なflow説明は選んだflowで読む参照に分ける候補 |
| engineering | code-review | 要調整: description、比較範囲、明示specの優先順位。Standards/Specの別報告は維持 |
| engineering | codebase-design | 要調整: descriptionを設計判断に限定。共有語彙と条件付きDEEPENING/DESIGN-IT-TWICE参照は維持 |
| engineering | diagnosing-bugs | 要調整: 発火条件と過剰な順序制約。再現手段の長いカタログ、flaky/perfの詳細は条件付き参照へ |
| engineering | domain-modeling | 維持: 能動的な用語整理と単なるglossary参照を区別でき、formatも別ファイル |
| engineering | grill-with-docs | 維持: 短い組合せスキル。対話とdomain記録が用途そのもの |
| engineering | implement | 維持: 合意再利用、関連検証、レビュー指摘解消までの完了条件が既に明確 |
| engineering | improve-codebase-architecture | 維持: 候補選択を待つ理由が明確。HTML scaffoldは別参照。本文との重複は後続の整理候補 |
| engineering | prototype | 要調整: 作成依頼と本体への実装権限を区別。LOGIC/UIの分岐は既に適切 |
| engineering | research | 要調整: descriptionの同義語列挙を削減。スコープと終了条件は維持 |
| engineering | resolving-merge-conflicts | 要調整: stage対象を解消ファイルに限定。意図に基づく解消は維持 |
| engineering | setup-matt-pocock-skills | 要調整: descriptionとdomain.mdの無条件な先読みを限定。複数回の確認は合意済み事項を再利用する形へ |
| engineering | tdd | 設計確認: integration-test triggerとrefactor方針。各cycleで全節を再確認させる表現は削減候補 |
| engineering | to-spec | 要調整: storyの分量強制と合意済みseamの再確認を削減。tracker/label前提は維持 |
| engineering | to-tickets | 要調整: descriptionを簡潔化。公開前のbreakdown承認と依存順の発行は維持 |
| engineering | triage | 維持: 状態遷移と人間の判断が本来の用途。単なる状態照会では個別issue調査の工程に入らない構成 |
| engineering | wayfinder | 設計確認: setup要求とlocal既定の優先順位が曖昧。1session 1ticketは意図的な製品境界なので短縮だけを理由に撤廃しない |
| engineering | wizard | 要調整: descriptionと調査範囲を人間の操作に限定。変数名はexampleや参照箇所で調査し、秘密の値を表示しない。操作直前の確認は維持 |
| productivity | grill-me | 維持: 短い対話スキルの入口 |
| productivity | grilling | 維持: 合意済みの決定を再利用し、実装依頼があれば続行する。質問数上限は既存の却下理由に従い追加しない |
| productivity | handoff | 維持: 一時ファイル、参照による重複回避、秘匿情報除外が簡潔 |
| productivity | teach | 要調整: quizの文字数強制を削減。mission設定、lesson作成、community探索は各用途の参照へ分ける候補 |
| productivity | to-questionnaire | 要調整: 宛先と必要な回答が既知なら再質問せず作成する。質問票を作る用途と送信は分離されたまま維持 |
| productivity | wait-what | 維持: 直前の説明の言い直しに限定されている |
| productivity | writing-for-agents | 要調整: 同じprocessの再現より成果と判断基準を優先する記述へ。長い理論節は必要時に読む参照へ分ける候補 |
| in-progress | claude-handoff | 維持: background起動は明示依頼時のみ。通常はhandoff保存で完了 |
| in-progress | implement-spec | 維持: 受入条件とPRのcheckを含む完了条件。delegationは条件付き |
| in-progress | loop-me | 設計確認: 「質問が1つも残らない」を、実装に必要な判断が揃う条件に変えるか。対話型であることは維持 |
| in-progress | retro | 要調整: reviewはdiffだけで探索不要という前提と、参照分割を1000行まで待つ基準を修正。実際に生じた問題を根拠に提案する |
| in-progress | setup-ts-deep-modules | 維持: 順序は依存関係と違反検出の実証に必要。既存repoへのexample常設は将来の設計確認候補 |
| in-progress | writing-beats | 維持: 人間がbeatを選ぶことが用途。各beatで停止する指示を自律実装の早期停止と混同しない |
| in-progress | writing-fragments | 維持: append形式と編集保護が目的に即している |
| in-progress | writing-shape | 維持: paragraph単位の共同執筆が用途。入力資料を編集しない境界を維持 |
| misc | git-guardrails-claude-code | 維持: blockする操作が明示された任意導入の安全機能。モデル変更を理由に解除しない |
| misc | migrate-to-shoehorn | 要調整: partial test dataだけでmigrationを開始しないdescriptionへ |
| misc | scaffold-exercises | 維持: 固有のdirectory/lint制約に価値がある。例の分離は低優先 |
| misc | setup-pre-commit | 設計確認: 既存hook/configの統合、stage範囲、full testを毎commitで走らせる既定は利用者の運用判断。機械的な短縮に含めない |

## 適用する場合の範囲と検証

1. 上記8件のdescription修正と優先本文修正から着手する。「設計確認」は別途判断する。
2. 動作が変わるpromoted skillの`docs/<bucket>/<name>.md`を同期し、flowが変わる箇所はask-mattも同期する。READMEの説明が不一致になる場合のみ更新する。既存のinvocation policyとpromoted setは維持する。
3. 条件付き参照への分離は、上記変更後に残る長い分岐を対象とし、入口に読む条件を明示する。全スキルを同じtemplateに書き換えない。
4. frontmatter、ローカルリンク、invocationの一致、docsとrouterの整合、diffの空白と禁止文字を確認する。manifestを変更する場合のみ指定のstrict validationを行う。
5. 挙動評価では、小さなbug、再現困難なbug、baseが判明しているPR、合意済みspec、prototypeのみの依頼、対話型執筆を使う。発火の適否、不要な停止、スコープ超過、完了証拠を確認する。編集した語句との一致だけをテストにしない。

## 今回の確認結果と限界

- 37件を棚卸し: engineering 18、productivity 7、in-progress 8、misc 4。deprecatedにはSKILL.mdなし。
- SKILL.md全体は25,391 whitespace-separated words。トークン数ではなく、削減効果の実測も未実施。
- 全37件に`agents/openai.yaml`があり、user-invoked指定と`allow_implicit_invocation: false`の有無は一致。
- promoted 25件とplugin.jsonのskills集合は一致。promoted 25件すべてにdocsページあり。
- この監査で全参照先の意味整合、全docsの内容同期、全スクリプト動作まで検証したわけではない。
- この棚卸しの時点ではスキル未編集。モデルを実行した比較評価やトークン削減率の測定は行っていない。

## 適用結果

- descriptionの具体案8件と、優先本文修正8項目を適用。対象は計13スキル。
- diagnosing-bugsの再現手段・間欠障害・性能測定を`FEEDBACK-LOOPS.md`へ分離し、必要な場合だけ読む入口を設けた。
- prototypeのLOGIC/UI参照にも、実コードへの反映は実装依頼がある場合という条件を反映した。
- 関連する公開docs8ページ、ask-matt、READMEの該当紹介文、changesetを同期した。
- 「設計確認」の項目と、全件表にある追加の整理候補は今回の優先修正には含めていない。invocation policyとpluginの公開対象集合は維持。
- 過去issueの検索はGitHub APIへ接続できず、既存docsとCHANGELOGの記録を参照した。外部リンクの到達確認とモデルによる挙動評価は未実施。
- 検証成功: 変更13スキルのfrontmatterと本文、全37件のinvocation整合、公開25件のmanifest・README・docsの対応、変更Markdownのローカルリンクと必須節、新規行の禁止文字、`git diff --check`、`npm run check-plugin-version`。
- 同梱quick_validateはこのrepoの`disable-model-invocation`と`argument-hint`を受理しないため、原本の両フィールドと変更前からの保持を別途検証し、それらを除いた一時コピーに対して実行した。原本の設定は変更していない。
- 変更したSKILL.mdの合計語数は10,917から10,039へ減少。diagnosing-bugsの入口は1,402から576語になった。参照ファイルやdocsを含む総量・実際のトークン使用量の削減率ではない。
