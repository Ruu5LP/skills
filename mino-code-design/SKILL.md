---
name: mino-code-design
description: Use when writing, reviewing, or refactoring code — designing a class/function, naming a symbol, handling a growing conditional branch, or introducing/extracting an abstraction (interface, base class, shared helper). Applies purpose-driven design checks (naming, encapsulation, branch/interface design, abstraction) to catch responsibility bloat, ambiguous names, branch sprawl, and premature abstraction before they accumulate.
---

# 目的駆動コード設計

命名・責務肥大化・条件分岐増殖・抽象化の罠の4つの崩れを防ぐための判断規則。ミノ駆動氏の公開資料（`purpose-abstraction-design`, `encapsulation2`, `kusokododong-hua-switchwen-jie-shuo`, `effective-learning-of-good-code`）にある原則を、日常のコーディングで自己適用できる形に絞ったもの。DDDの全体プロセスやガバナンス層(Evidence追跡、承認フロー等)は含めない。あくまでコードを書く/直す瞬間に効く軽量チェックとして使う。

## いつ使うか

新しいクラス・関数・変数を命名するとき、既存クラスへメソッドを追加するとき、if/switchを追加または複製するとき、重複コードを共通化・抽象化しようとするときに適用する。1行の修正やタイポ直しなど、責務や分岐、抽象化に関わらない変更には適用しない。

## 1. 命名: 曖昧な名前は責務混在のサイン

名前を付ける・見直すときは、まず主語と目的を特定する。

```
誰のためのものか / 何を達成するものか / 何を所有するものか / 何を保証するものか
```

次の語が名前に出てきたら、責務が本当にひとつか疑う。

- `Manager`, `Service`, `Processor`, `Handler` → 具体的な目的を再調査する
- `Common`, `Base`, `Util`, `Helper` → 意味ではなく実装都合で寄せ集めていないか
- `Data`, `Info` → 業務上の役割や制約が名前に出ていない
- `And`/`Or`が必要な説明、非常に長い名前 → 複数責務が1つに同居している可能性
- `process`, `handle`, `execute` → 何を達成するか不明。業務上の結果を表す動詞に変える

既存の名前に引っ張られないよう、一度クラス名を伏せて「このユースケースで何を達成するものか」から名前候補を作り直し、現在名と比較する。命名を変えても説明しきれない場合は、名前の問題ではなく責務分割が必要な兆候として扱う。

## 2. 責務肥大化: カプセルは目的単位で見る

クラス・モジュールへ手を加える前に、そのメンバーが「同じ目的」に属するか確認する。

- setterやpublic fieldでどこからでも状態を変更できる状態を避け、`setStatus(APPROVED)`ではなく`approve(by, at)`のように業務上の意図を表す操作を公開する。
- インスタンスメソッドが自分の状態を使わず、引数の別オブジェクトばかり操作している場合、責務の置き場所が違う可能性がある。
- 「クラスを増やしたくない」という理由だけで、目的の異なる処理を既存クラスへ足し続けない。目的が違うなら新しいクラス・モジュールの方が健全。
- 逆に、1つの不変条件を守るために多数のオブジェクトを行き来する必要が出たら分割しすぎ。

判断に迷ったら「このメンバーは、誰の・どの目的のためにここにあるか」を一行で言えるか試す。言えないメンバーは移動候補。

## 3. 条件分岐: 増える前に分類する

if/switchを追加・複製する前に、分岐の意味を分類する。

| 分岐の意味 | 扱い方 |
|---|---|
| 種類ごとの振る舞い（type/enum） | Strategy候補。interfaceは呼び出し側の目的から命名する（`IProcessor.process()`ではなく`ShippingFeePolicy.calculateFor(destination, parcel)`） |
| ライフサイクル中に変わる状態ごとの振る舞い | State候補 |
| 一時的な入力判定・ガード | 単純なifのままが明快なことが多い |
| 業務ルール表 | Decision table / rule object候補 |
| 機能フラグ・移行分岐 | owner・期限・削除条件を持たせる。恒久化させない |

interface化する価値があるのは、同じ条件を使う分岐が複数箇所にあり、今後も種類が増える見込みがあるとき。1箇所だけの短い分岐や、今後増える根拠のない分岐は、素直にifのまま残す方が理解コストが低い。interface化する場合は、実装の選択責任（factory/DI/registry）を1箇所へ集約し、呼び出し側がtype codeを読まないようにする。

## 4. 抽象化: 罠を避ける

重複コードや似た構造を見つけて共通化・抽象化したくなったら、まず次を一文で具体的に書けるか試す。

```
この抽象は、<利用者> が <実現方法> を知らずに <望む結果> を得られるようにする。
```

具体的に書けない場合、抽象化の軸がまだ定まっていない。共通化を急がず、目的が定まるまで具体のまま置いておく。

次の2つが特に危険な罠。

- **形の類似と意味の同一性の混同**: 二つのコードが同じアルゴリズム・構造でも、目的や変更理由が違うなら原則として共通化しない。同じに見えて別の理由で変わるものを一つにまとめると、片方だけ直したいときに直せなくなる。共通化前に「同じ利用者の目的を支えるか」「同じ理由で一緒に変更されるか」を確認する。
- **将来のための拡張点**: 「再利用できるかもしれない」「後で種類が増えるかもしれない」だけを理由に、利用例のないinterfaceや拡張ポイントを先回りで作らない。具体例が1つしかないなら、抽象化せず具体実装のままにする方が安全。ただし外部技術の隔離やテスト境界など、他に明確な理由がある場合は例外として検討してよい。

抽象化した後は、「新しい具体例を追加するシナリオ」と「既存の一方だけを変更するシナリオ」の両方で、その抽象が本当に役立つか検証する。理解のためのファイル移動が増えただけなら、抽象化をやめて戻す選択肢も残す。

## 適用の強さ

4つとも「兆候が見えたら立ち止まって一言診断する」レベルで運用する。すべてのコードを毎回この基準でフル分析しない。小さな個人開発やプロトタイプでは、兆候が明確に繰り返し出たときだけ言及する。

## 出典

- https://speakerdeck.com/minodriven/purpose-abstraction-design
- https://speakerdeck.com/minodriven/encapsulation2
- https://speakerdeck.com/minodriven/kusokododong-hua-switchwen-jie-shuo
- https://speakerdeck.com/minodriven/effective-learning-of-good-code

これらの原則は [my-take-dev/inspired-mino-design-skills](https://github.com/my-take-dev/inspired-mino-design-skills) の `mino-doc/10`, `11`, `12`, `13` を精読し、そこで引用されているミノ駆動氏の一次資料に基づいて再構成した。同リポジトリのEvidence追跡・decision_maturity・12-dimension監査などはこのリポジトリ独自の追加ガバナンス層であり、ミノ駆動氏本人の手法として帰属させない。本SKILLでは意図的にそれらを持ち込んでいない。
