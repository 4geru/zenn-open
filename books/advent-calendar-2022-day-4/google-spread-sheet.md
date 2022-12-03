---
title: "Google Sheets"
---

## Google Sheets

Google Sheets では、 Google Sheets にインポートする機能と Google Form にエクスポートする機能を実装します。一度 Google Sheets に、インポートすることで、文言の修正、絵文字、markdown 記述の修正などが可能になります。また、カスタムメニューを設置し、ボタンを押すと、この2つの機能を呼び出せるようにし、誰でも実行しやすいようにします。

## カスタムメニューの設定

公式ドキュメント： [Class Menu | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/base/menu)

Google Sheets には、カスタムメニューという機能があり、GASで定義した関数を Google Sheets の UI から直接呼び出すことが可能です。
![カスタムメニュー](/images/books/advent-calendar-2022-day-4/google-spread-sheet-custom-menu.png =400x)

```js:setCustomMenu
const setCustomMenu = () => {
  const ui = SpreadsheetApp.getUi();
  ui.createMenu('★ 独自メニュー ★')
      .addItem('🍣 LGTM賞候補をkibelaからインポート', 'functionA')
      .addItem('🍺 LGTM賞候補をformにエクスポート', 'functionB')
      .addToUi();
}
```

関数を追加したら、 時計のアイコン > トリガーを追加 > 追加した関数 > 保存をします。
するとスプレッドシートを開いて関数が呼ばれると、カスタムメニューを表示することができます。

![トリガーの設定](/images/books/advent-calendar-2022-day-4/google-spread-sheet-set-trigger.png)

## Google Sheets からの読み取り

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#getrangea1notation)

getRange では [A1 表記または R1C1 表記](https://developers.google.com/sheets/api/guides/concepts?hl=id#cell) が可能です。
getValue() で 1 つの要素を、 getValues() で、2重配列の複数要素を取得できます。

- getValue の例

```js
getRange(`A1`).getValue()
// => A1
```

- getValues の例

```js
getRange(`A:A`).getValue()
// => [[A1], [A2], ... [An]]
getRange(`A1:B2`).getValue()
// => [[A1, B1], [A2, B2]]
```

複数要素を取得したい場合は getRange(`A1:A2`).getValues() のようにすると、複数要素の取得が可能です。

```js
const SHEET_URL = 'https://docs.google.com/spreadsheets/d/xxxxxx/edit#gid=0'
const spreadSheet = SpreadsheetApp.openByUrl(SHEET_URL);
const targetSheet = spreadSheet.getSheetByName("spread sheets tab name");
const a1Value = targetSheet.getRange(`A1`).google();
```

## Google Sheets への書き込み

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#appendrowrowcontents)

読み込みのように getRange(`A1`).setValue(value) で値の設定が可能です。
今回は複数要素を、最後に追加する appendRow を利用します

```js
const SHEET_URL = 'https://docs.google.com/spreadsheets/d/xxxxxx/edit#gid=0' // 読み取りと同じなので、どちらかだけで良い
const spreadSheet = SpreadsheetApp.openByUrl(SHEET_URL);                     // 上と同じ
const targetSheet = spreadSheet.getSheetByName("google sheets tab name");
targetSheet.appendRow([
  'message 1',
  'message 2',
]);
```
