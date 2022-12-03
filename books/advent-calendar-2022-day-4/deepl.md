---
title: "DeepL(RestAPI)"
---

## DeepL
マネーフォワードでは非日本語話者なエンジニアも増えています。メンバーによっては、英語の方が得意なメンバーもいます。DeepL を利用し、公平な審査ができる仕組みにしています。
DeepL の API の利用方法は、 [pnizo](https://qiita.com/pnizo)さん の [【GoogleAppsScript】GASでDeepL APIを叩く - Qiita](https://qiita.com/pnizo/items/338b1a1398127a8942e5) 記事を参考にさせていただきました。

公式ドキュメント： [DeepL API](https://www.deepl.com/docs-api/translate-text/)

関数化し、メッセージ、翻訳元の言語、翻訳語の言語の形にします。

```js:sample_call.gs
deeplTranslate([message], "JA", "EN")[0].text
```

中身は、よくある REST API の形式でリクエストを行います。


```js:deepl.gs
const DEEPL_API_TOKEN = 'DEEPL_API_TOKEN'
const DEEPL_API_URL = 'https://api-free.deepl.com/v2/translate';

const FETCH_WAIT = 200;

// DeepL翻訳関数
const deeplTranslate = (texts, src, tgt) => {
  textsParams = texts.map(text => `text=${text}`).join('&')
  const content = encodeURI(`auth_key=${DEEPL_API_TOKEN}&${textsParams}&source_lang=${src}&target_lang=${tgt}`);

  const postheader = {
    "accept":"gzip, */*",
    "timeout":"20000",
    "Content-Type":"application/x-www-form-urlencoded"
  }

  const parameters = {
    "method": "post",
    "headers": postheader,
    'payload': content
  }

  // スプレッドシートから大量に呼ばれる可能性があるのでウェイトを入れておく
  Utilities.sleep(FETCH_WAIT);

  try {
    const response = UrlFetchApp.fetch(DEEPL_API_URL, parameters);
  } catch (e) {
    return 'DeepL:Exception';
  }

  const responseCode = response.getResponseCode().toString();

  if (responseCode != 200) return 'DeepL:HTTP Error(' + responseCode + ')'

  // JSONからテキストを取り出す
  const json = JSON.parse(response.getContentText('UTF-8'));
  return json.translations
}
```
