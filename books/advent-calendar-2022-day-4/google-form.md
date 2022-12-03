---
title: "Google Form API"
---

## Google Form API

公式ドキュメント： [Class FormApp | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/forms/form-app)

Google Form 言語選択セクション と LGTM賞選択セクション の 2つを作ります。LGTM賞選択 section は、日本語 / 英語それぞれのフォームを用意していました。
※ ユーザー視点では、自分の言語だけ表示されてよかったのですが、運営視点から集計が大変になり、現在は同じフォームで出しているようです。

フォームの作成は、 Item 単位で、選択肢を追加します。ラジオボタン、チェックボックスは、それぞれ MultipleChoiceItem 、 CheckboxItem で、指定可能です。
参考： [GASでGoogleフォームに追加できる質問の種類とそのメソッドまとめ](https://tonari-it.com/gas-form-add-items/)

言語選択セクション から LGTM賞選択セクション のページ切り替えは、 PageBreakItem を利用し、次のページに遷移します。公式のサンプルがわかりやすいので、興味のある方は見てください。
参考：[Class PageBreakItem | Apps Script | Google Developers](https://developers.google.com/apps-script/reference/forms/page-break-item)

## 言語選択セクション

言語選択セクションでは、日本語のフォームと英語のフォームの出し分けをしています。
Form を新規作成し、言語選択のラジオボタンを追加します。
この関数では、 回答URL と 編集URL を返しています。

```js:google_form.ts
const separatedExportGooleForm = () => {
  const form = FormApp.create(separatedGoogleFormTitle())
    .setDescription(formDescription);

  let item = form.addMultipleChoiceItem();
  item.setTitle('言語を選択してください。 / Please select your language.')

  item.setChoices([
    item.createChoice('日本語 / Japanese', separatedJapaneseForm(form)),
    item.createChoice('英語 / English', separatedEnglishForm(form)),
  ]);

  return {
    publishedUrl: form.getPublishedUrl(), // アンケートを回答する URL
    editUrl: form.getEditUrl()            // アンケートを編集する URL
  }
}
```

![言語選択画面](/images/books/advent-calendar-2022-day-4/google-forms-first-section.png =500x)

## LGTM賞選択セクション

LGTM賞選択セクション では、日本語、英語のフォームをそれぞれ表示します。
日本語フォームと英語フォームの違いは、deepl 使って、言語を切り替えるだけで、大きな違いはないです。
LGTM 賞の選択は、複数投稿を可能にしており、チェックボックスを利用しています。
`エンジニアブログ`, `OSS関係`, `有志活動`, `slackでの議論など` などのカテゴリーごとに、選択フォームを分けています。

```diff js:google_form.ts
-const separatedJapaneseForm = (form) => {
+const separatedEnglishForm = (form) => {
- let japanesePage = form.addPageBreakItem().setTitle('日本語');
+ let englishPage = form.addPageBreakItem().setTitle('English');
  const spreadSheetHash = separatedFetchSpreadSheetHash();
  // カテゴリーごとの item の作成
  for (let category in spreadSheetHash) {
    let item = form.addCheckboxItem();
-   item.setTitle(category);
+   item.setTitle(deeplTranslate([category], "JA", "EN")[0].text);
    // LGTM賞の候補の追加をしています
    const choices = spreadSheetHash[category].map((row) => {
+     const choice = deeplTranslate([row[1] + row[2]], "JA", "EN")[0].text
      return item.createChoice(choice)
    })
    item.setChoices(choices);
  }
  // ご意見・感想の自由記述フォーム
  form.addParagraphTextItem()
-   .setTitle('励まし・推薦メッセージ・その他ご意見感想などなんでも自由記入欄')
+   .setTitle('Encouragement, recommendation messages, and any other opinions and impressions you may have.')

- japanesePage.setGoToPage(FormApp.PageNavigationType.SUBMIT)
- return japanesePage
+ englishPage.setGoToPage(FormApp.PageNavigationType.SUBMIT)
+ return englishPage
}
```

実行すると、次のようなアンケートフォームが作成されます。

![英語フォーム](/images/books/advent-calendar-2022-day-4/google-forms-english-section.png =500x)
