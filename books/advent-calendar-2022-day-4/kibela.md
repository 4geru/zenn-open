---
title: "Kibela(GraphQL)"
---

## Kibela

マネーフォワードでは、社内ドキュメントツールとして、 Kibela を利用しています。LGTM賞の候補は Kibela に毎月集められます。

公式ドキュメント： [KibelaのWeb APIについて – Kibelaヘルプセンター](https://support.kibe.la/hc/ja/articles/360035089312-Kibela%E3%81%AEWeb-API%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

関数化し、指定した URL から記事を取得できるようにします。
Kibela には、 チームの概念があります。
チームとは、 GitHubにおけるOrganizationsに近い概念です

```js:sample_call.gs
let articleUrl = 'https://KIBELA_TEAM.kibe.la/notes/000000';
const articleNumber = articleUrl.split('/').slice(-1)[0]
const articles = getArgicles(articleNumber).data.noteFromPath
```

Kibela API は GraphQL で定義されています。[api/console](https://moneyforward.kibe.la/api/console) が提供されており、ブラウザから簡単にクエリを試すことができます。

```js:kibela.gs
const TEAM = 'KIBELA_TEAM';
const KIBELA_API_TOKEN = 'KIBELA_API_TOKEN';
const API_ENDPOINT = `https://${TEAM}.kibe.la/api/v1`;

const getArticleGraphql = (notePath) => {
  return `
  {
    noteFromPath(path: "/notes/${notePath}") {
      title
      content
    }
  }
  `
};

const buildGetArticleRequestOption = (notePath) => {
  return {
    method: "post",
    contentType: "application/json",
    headers: {
      Authorization: `Bearer ${KIBELA_API_TOKEN}`,
      Accept: "application/json",
      "Content-Type": "application/json"
    },
    payload: JSON.stringify({ query: getArticleGraphql(notePath) }),
  };
};

const getArgicles = (notePath) => {
  const option = buildGetArticleRequestOption(notePath);
  const res = UrlFetchApp.fetch(API_ENDPOINT, option);
  
  const json = JSON.parse(res.getContentText());
  return json;
};
```
