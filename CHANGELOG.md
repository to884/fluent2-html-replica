# 変更履歴

このスキルの主な変更は、すべてこのファイルに記録する。

書式は [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) にもとづき、このプロジェクトは [Semantic Versioning](https://semver.org/spec/v2.0.0.html) に従う。版は `skills/fluent2-html-replica/SKILL.md` の frontmatter の `metadata.version` にある。

## [Unreleased]

## [0.1.4] - 2026-11-16

### Fixed

- Tree で、子の行にポインターを置くと、祖先の枝のクイックアクション（`fui-TreeItemLayout__actions`）まで出て、祖先の aside が隠れていた。actions は、その行（`fui-TreeItemLayout`）自身のホバーと、その項目か actions の中のフォーカスの間だけ出す（v9 と同じく、子の木からのホバーを除く）。
- Tree で、葉の Enter が既定の動作を止めていたので、葉を `a` にした木で Enter を押してもリンクの行き先に移れなかった。葉の Enter は止めない（v9 も止めない）。枝の Enter は、これまでどおり開閉する。
- 葉の TreeItem を `a` にすると、リンクの下線が名前に付いていた。`a.fui-TreeItem` の下線を消す。

### Changed

- 章 14 の Tree に、葉を `a` にして画面を移る木（ナビゲーション）のマークアップを足した。

## [0.1.3] - 2026-11-15

### Fixed

- 1 つの Tooltip を複数のトリガーで使い、開いたままポインターを別のトリガーへ移すと、Tooltip が前のトリガーに付いたままになっていた。閉じずに、移った先のトリガーへ付け替える。

## [0.1.2] - 2026-11-15

### Fixed

- IME の変換中のキー（`isComposing` が真か、`keyCode` が 229）を、`assets/fluent2.js` のすべてのキーの処理で受けないようにした。0.1.1 で直した Combobox と SpinButton のほかに、Toolbar の中の入力欄の上下の矢印でフォーカスが移ること、non-modal の Dialog が変換を取り消す Esc で閉じること、入力欄に付けた Tooltip が同じ Esc で消えることがなくなった。Menu、Toaster、TabList、Tree、Nav、Card、Combobox の展開アイコンのキーも同じである。v9 はこれを見ないので、v9 との意図した差である。
- Tooltip が、トリガーの `anchor-name` を自分の名前で置き換えて、利用者の CSS や style が書いた名前を消していた。利用者の CSS の `!important` には負けて、Tooltip がトリガーに付かなかった。今は、利用者の名前に自分の名前を足して important で書き、閉じたら足す前の style の宣言（値と優先度）に戻す。開いている間に利用者が style の `anchor-name` を書いたら、その宣言には触らない。名前は開くたびに新しく作る（`--fui-tooltip-<id>-<連番>`）。
- CarouselButton（前・次）で、`aria-disabled="true"` のクリックが止まっていなかった。`data-fui-enhance` の中なら、Button と同じく止める（v9 と同じ）。端の判定が、利用者の書いた `disabled` と `aria-disabled` を上書きしていた。今は端で付ける無効だけを書き、端を離れたら戻す。付けている間に利用者が変えた属性は戻さない。

### Changed

- CarouselButton の端でないボタンに、`aria-disabled="false"` を書かなくなった（属性なしと同じ意味である）。

## [0.1.1] - 2026-11-15

### Fixed

- Combobox で、IME の変換を Enter で確定すると、強調中の選択肢が選ばれて入力が上書きされていた。変換中のキー（`isComposing` が真か、`keyCode` が 229）は一覧の操作にせず、変換で入れたあとは入力中として扱う（確定の直後の Space を選択にしない）。v9 はこれを見ないので、v9 との意図した差である。
- SpinButton で、変換中の Enter と上下の矢印が、値の確定と増減になっていた。Combobox と同じく、変換中のキーでは何もしない。
- SpinButton の長押しの間隔が、1000ms を過ぎても縮み続け、0 に近づいていた。300ms から 80ms へ縮めたあとは、80ms で繰り返す。v9 の `useSpinButton` も比を止めていないので、v9 との意図した差である。
- `aria-disabled="true"` の BreadcrumbButton で、クリックと Enter、Space が止まっていなかった。`data-fui-enhance` の中なら、Button と同じく `assets/fluent2.js` が止める。現在の項目も止まる（v9 と同じ）。
- MenuButton を下矢印で開くと、トリガーがアンカーにならず、クリックで開いたときと位置がずれていた。開いている `popover="auto"` の中のトリガーでは、親の popover が閉じていた。`showPopover({ source: トリガー })` で開き、どちらもクリックと同じにした。無効のトリガーと、ほかの処理が既定の動作を止めた keydown では開かない（v9 と同じ）。`source` に対応しないブラウザーでは、これまでと同じ動きになる。

### Changed

- 章 14 の Breadcrumb の見本に `data-fui-enhance` を付けた。無効の `<a>` には `href` を書かない、と約束に加えた（v9 も外す。残すと、中クリックやコンテキストメニューから開ける）。

## [0.1.0] - 2026-11-13

### Added

- 最初の版。トークンの CSS 変数、FluentProvider に当たる土台、47 種類のコンポーネントの CSS と小さな JS、日本語の設計書、ブランドの 16 段の生成器。

[Unreleased]: https://github.com/to884/fluent2-html-replica/compare/v0.1.3...HEAD
[0.1.3]: https://github.com/to884/fluent2-html-replica/compare/v0.1.2...v0.1.3
[0.1.2]: https://github.com/to884/fluent2-html-replica/compare/v0.1.1...v0.1.2
[0.1.1]: https://github.com/to884/fluent2-html-replica/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/to884/fluent2-html-replica/releases/tag/v0.1.0
