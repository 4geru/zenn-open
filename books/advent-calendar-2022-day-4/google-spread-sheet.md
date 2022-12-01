---
title: "Google Spread Sheet"
---

## カスタムメニューの設定

公式ドキュメント： [Class Menu | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/base/menu)

SpreadSheet には、カスタムメニューという機能があり、GASで定義した関数を SpreadSheet の UI から直接呼び出すことが可能です。
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
するとスプレッドシートを開いて関数が呼ばれたら、カスタムメニューを表示することができます。

![トリガーの設定](/images/books/advent-calendar-2022-day-4/google-spread-sheet-set-trigger.png)

## SpreadSheet からの読み取り

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#getrangea1notation)

シートから値を読み込むときは、getRange(`A1`).getValue() を利用します。
複数要素を取得したい場合は getRange(`A1:A2`).getValues() のようにすると、複数要素の取得が可能です。

```js
const SHEET_URL = 'https://docs.google.com/spreadsheets/d/xxxxxx/edit#gid=0'
const spreadSheet = SpreadsheetApp.openByUrl(SHEET_URL);
const targetSheet = spreadSheet.getSheetByName("spread sheet tab name");
const a1Value = targetSheet.getRange(`A1`).getValue();
```

## SpreadSheet への書き込み

公式ドキュメント： [Class Sheet | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/spreadsheet/sheet#appendRow(Object))

読み込みのように getRange(`A1`).setValue(value) で値の設定が可能です。
今回は複数要素を、最後に追加する appendRow を利用します

```js
const SHEET_URL = 'https://docs.google.com/spreadsheets/d/xxxxxx/edit#gid=0' // 読み取りと同じなので、どちらかだけで良い
const spreadSheet = SpreadsheetApp.openByUrl(SHEET_URL);                     // 上と同じ
const targetSheet = spreadSheet.getSheetByName("spread sheet tab name");
targetSheet.appendRow([
  'message 1',
  'message 2',
]);
```
