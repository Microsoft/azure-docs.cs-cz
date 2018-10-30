---
title: 'Rychlý start: Získání délky vět, Node.js – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte délky vět v textu pomocí služby Translator Text API s Node.js.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 60b7bf8de0f0f296d0efb49a1e08030c2d5999e3
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644895"
---
# <a name="quickstart-get-sentence-lengths-with-the-translator-text-rest-api-nodejs"></a>Rychlý start: Získání délky vět pomocí rozhraní REST API služby Translator Text (Node.js)

V tomto rychlém startu zjistíte délky vět v textu pomocí služby Translator Text API.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Node.js 6](https://nodejs.org/en/download/).

Abyste mohli použít službu Translator Text API, budete potřebovat klíč předplatného. Přečtěte si, [jak se zaregistrovat ve službě Translator Text API](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Žádost BreakSentence

Následující kód rozdělí zdrojový text do vět pomocí metody [BreakSentence](./reference/v3-0-break-sentence.md).

1. V oblíbeném editoru kódu vytvořte nový projekt Node.js.
2. Přidejte níže uvedený kód.
3. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/breaksentence?api-version=3.0';

let params = '';

let text = 'How are you? I am fine. What did you do today?';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let BreakSentences = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

BreakSentences (content);
```

## <a name="breaksentence-response"></a>Odpověď metody BreakSentence

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte vzorový kód pro tento rychlý start a další, včetně překladu a transkripce, a také další vzorové projekty Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady Node.js na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=javascript)
