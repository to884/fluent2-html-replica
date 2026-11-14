# fluent2-html-replica

Microsoft の Fluent 2 の Web 版のデザイン言語を、React を使わない HTML と CSS で再現する Agent Skill である。トークン（色、書体、間隔、角丸、影、動き）の CSS 変数、47 種類のコンポーネントの CSS と小さな JS、日本語の設計書、1 つの色からブランドの 16 段を作る生成器を同梱する。

非公式の再現であり、Microsoft の製品ではなく、Microsoft が推奨するものでもない。見た目の基準は Fluent UI React v9（`@fluentui/react-components` 9.75.2、microsoft/fluentui の commit `f9720c35`）である。

- スキル: [skills/fluent2-html-replica/](skills/fluent2-html-replica/)（`SKILL.md` が入口）
- 設計書: [docs/fluent2-web-spec.html](docs/fluent2-web-spec.html)（1 ファイルで完結する。ダウンロードしてブラウザーで開く）

## 導入

[skills CLI](https://github.com/vercel-labs/skills) で入れる。

```bash
npx skills add to884/fluent2-html-replica
```

手で入れるときは、`skills/fluent2-html-replica/` のフォルダーを、使うエージェントのスキルのフォルダーに写す。

| エージェント | 写し先 |
|---|---|
| Claude Code（すべてのプロジェクトで使う） | `~/.claude/skills/fluent2-html-replica/` |
| Claude Code（1 つのプロジェクトだけで使う） | そのプロジェクトの `.claude/skills/fluent2-html-replica/` |
| Codex など、Agent Skills に対応するエージェント | そのプロジェクトの `.agents/skills/fluent2-html-replica/` |

```bash
git clone https://github.com/to884/fluent2-html-replica.git
```

```bash
cp -r fluent2-html-replica/skills/fluent2-html-replica ~/.claude/skills/
```

導入したら、エージェントを起動し直す。スキルの一覧に `fluent2-html-replica` が出ればよい。

## 使い方

このスキルは 2 通りに使える。エージェントに頼んでページを作らせる使い方と、エージェントを使わずに CSS と JS を自分のページに読み込む使い方である。React の `@fluentui/react-components` で作るときは、このスキルではなく React の公式の文書を使う。

### エージェントに頼む

Fluent 2 の見た目にしたいことを、ふつうの言葉で頼む。エージェントは `SKILL.md` を読んでこのスキルを使う。

- 「Fluent 2 のデザインで、問い合わせフォームの HTML を作って」
- 「この HTML ページ（`docs/index.html`）を Fluent 2 の見た目にして」
- 「Teams 風の設定画面を、React を使わずに HTML と CSS で作って」
- 「Fluent 2 のトークンで、このプロジェクトのデザインシステムの CSS を作って」
- 「ブランドの色を #5b5fc7 にして、ダークにも対応させて」

エージェントは、次の順に作業する。

1. `assets/fluent2.css`（と、振る舞いが要れば `assets/fluent2.js`）を、作るページのプロジェクトに写して読み込む。
2. 部品は、`references/` の該当する章のマークアップを使って組む。
3. 独自の装飾も、色や間隔はトークンの CSS 変数（`var(--colorNeutralBackground1)` など）で書く。

うまく使われないときは、「fluent2-html-replica のスキルを使って」と名前で頼む。

### エージェントを使わずに、CSS と JS を直接使う

`skills/fluent2-html-replica/assets/` の `fluent2.css` と `fluent2.js` を、自分のプロジェクトに写して読み込む。どちらもほかのファイルに依存せず、外部への通信もしない。

```html
<!doctype html>
<html lang="ja" data-theme="light">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Fluent 2 のページ</title>
  <link rel="stylesheet" href="fluent2.css">
</head>
<body class="fui-FluentProvider">
  <div class="fui-Field">
    <label class="fui-Field__label fui-Label" for="email">メールアドレス</label>
    <span class="fui-Input"><input class="fui-Input__input" id="email" type="email"></span>
  </div>
  <button class="fui-Button" data-appearance="primary" type="button">送信</button>

  <!-- 振る舞いが要る部品（トグル、矢印キーの移動、TabList の下線、Toast など）は data-fui-enhance の中に置く -->
  <div data-fui-enhance>
    <button class="fui-ToggleButton fui-Button" aria-pressed="false" type="button">ピン留め</button>
  </div>
  <script src="fluent2.js"></script>
</body>
</html>
```

書き方の決まりは次のとおり。部品ごとのマークアップは、設計書か `references/` の章にある。

| 決まり | 例 |
|---|---|
| class 名は、Fluent UI React v9 と同じ名前にする | `fui-Button`、`fui-Button__icon`、`fui-Input__input` |
| バリエーションは、v9 の props 名に合わせた data 属性で表す | `data-appearance="primary"`、`data-size="small"`、`data-shape="circular"` |
| 状態は、HTML の属性と ARIA で表す | `disabled`、`aria-pressed="true"`、`aria-selected="true"`、`aria-expanded="true"` |
| JS の振る舞いは、`data-fui-enhance` を付けた要素とその中だけに付く | `<div data-fui-enhance>…</div>` |
| 部品の値は、style 属性ではなく data 属性で渡す | ProgressBar の `data-percent="42"` |

### テーマ、ブランド、日本語

- **テーマ**: `<html>` の `data-theme` を `light`、`dark`、`auto`（OS の設定に従う）、`high-contrast` のどれかにする。区画だけを替えるときは、その要素に `data-theme` を付ける。
- **ブランドの色**: 1 つのキーの色から、`scripts/brand_ramp.py`（依存なしの Python 3.11 以降）で 16 段の CSS を作り、`fluent2.css` の後に読む。Fluent UI のテーマデザイナーと同じ計算で、コントラストの確認の結果も出す。

  ```bash
  python skills/fluent2-html-replica/scripts/brand_ramp.py "#5b5fc7" > brand.css
  ```

  オプション（`--vibrancy`、`--hue-torsion`、`--selector`、`--dark-mapping`、`--format json`）と注意は、設計書の「色」の章の「16 段の作り方」にある。
- **日本語**: `<html lang="ja">` にすると、欧文は Segoe UI のまま、日本語は Yu Gothic UI や Meiryo UI などで表示する。長い本文だけを読みやすくするときは、段落に `class="f2-prose"` を付ける。

### インラインの style と script を禁じるページで使う

`default-src 'none'; script-src 'self'; style-src 'self'` のような厳しい CSP のページでも使える。`fluent2.css` と `fluent2.js` は、`<link>` と `<script src>` でファイルとして読み込む。マークアップの style 属性は CSP で無視されるので、部品の値は data 属性で渡す。

### 設計書を読む

[docs/fluent2-web-spec.html](docs/fluent2-web-spec.html) をダウンロードして、ブラウザーで開く。全 15 章に、部品の動く見本、マークアップ、寸法と色の表、全トークンの表がある。右上でテーマを切り替えられる。設計書はスキルのフォルダーの外にあるので、スキルを導入しても写されない。CSS と JS を中に埋め込んだ 1 ファイルなので、インラインの style と script を禁じる CSP のページの中では表示できない。

## 版と変更履歴

版は [Semantic Versioning](https://semver.org/spec/v2.0.0.html) に従い、`SKILL.md` の frontmatter の `metadata.version` に書く。`npx skills add` は main を入れる。版ごとの変更は [CHANGELOG.md](CHANGELOG.md) に、版ごとのタグ（`v0.1.1` など）と GitHub のリリースもある。前の版を使うときは、タグの版を取り出して手で写す。

## 注意

- `skills/fluent2-html-replica/` の中のファイルと設計書は、開発用のリポジトリ（非公開）の原稿と原本から組み立てた生成物である。このリポジトリで直接直しても、次に組み立て直したときに上書きされる。
- Segoe UI などの書体は同梱しない。Web フォントとして配ることもできない。Segoe UI のない環境では、代わりの書体で表示する。
- 配布するページや CSS には、Fluent UI などの第三者のライセンスの表示を残す。`fluent2.css` と `fluent2.js` は、先頭のコメントに表示を入れてある。
- 本物の React v9 と比べて確かめた範囲と、確かめていない範囲は、`SKILL.md` の「検証の範囲」にある。

## ライセンス

このリポジトリのコードと文書は MIT License（[LICENSE](LICENSE)）とする。値と規則の元になった Fluent UI、Fluent UI System Icons、three.js、css.land、CSS Color 4 のサンプルコードの著作権表示と許諾の文は、[THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt) にある。スキルのフォルダーにも同じ 2 つのファイルを入れてある。Fluent、Microsoft、Segoe は Microsoft Corporation の商標である。
