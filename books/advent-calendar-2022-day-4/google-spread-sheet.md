---
title: "Google Spread Sheet"
---

## Google Spread Sheet

Spread Sheet では、 Spread Sheet にインポートする機能と Google Form にエクスポートする機能を実装します。一度 Spread SHeet に、インポートすることで、文言の修正、絵文字、markdown 記述の修正などが可能になります。また、カスタムメニューを設置し、ボタンを押すと、この2つの機能を呼び出せるようにし、誰でも実行しやすいようにします。

## カスタムメニューの設定

公式ドキュメント： [Class Menu | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/base/menu)

Spread Sheet には、カスタムメニューという機能があり、GASで定義した関数を Spread Sheet の UI から直接呼び出すことが可能です。
![カスタムメニュー](/images/books/advent-calendar-2022-day-4/google-spread-sheet-custom-menu.png =400x)

```js:setCustomMenu
const setCustomMenu = () => {
  const ui = Spread SheetApp.getUi();
  ui.createMenu('★ 独自メニュー ★')
      .addItem('🍣 LGTM賞候補をkibelaからインポート', 'functionA')
      .addItem('🍺 LGTM賞候補をformにエクスポート', 'functionB')
      .addToUi();
}
```

関数を追加したら、 時計のアイコン > トリガーを追加 > 追加した関数 > 保存をします。
するとスプレッドシートを開いて関数が呼ばれると、カスタムメニューを表示することができます。

![トリガーの設定](/images/books/advent-calendar-2022-day-4/google-spread-sheet-set-trigger.png)

## Spread Sheet からの読み取り

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#getrangea1notation)

シートから値を読み込むときは、getRange(`A1`).getValue() を利用します。
複数要素を取得したい場合は getRange(`A1:A2`).getValues() のようにすると、複数要素の取得が可能です。

```js
const SHEET_URL = 'https://docs.google.com/Spread Sheets/d/xxxxxx/edit#gid=0'
const Spread Sheet = Spread SheetApp.openByUrl(SHEET_URL);
const targetSheet = Spread Sheet.getSheetByName("spread sheet tab name");
const a1Value = targetSheet.getRange(`A1`).getValue();
```

## Spread Sheet への書き込み

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#appendrowrowcontents)

読み込みのように getRange(`A1`).setValue(value) で値の設定が可能です。
今回は複数要素を、最後に追加する appendRow を利用します

```js
const SHEET_URL = 'https://docs.google.com/Spread Sheets/d/xxxxxx/edit#gid=0' // 読み取りと同じなので、どちらかだけで良い
const Spread Sheet = Spread SheetApp.openByUrl(SHEET_URL);                     // 上と同じ
const targetSheet = Spread Sheet.getSheetByName("spread sheet tab name");
targetSheet.appendRow([
  'message 1',
  'message 2',
]);
```
