---
title: 'Úvodní příručka: Získání přehledů obrázků pomocí rozhraní REST API a node.js – vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat o něm přehledy.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379704"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Úvodní příručka: Získejte přehledy obrázků pomocí rozhraní REST API pro vizuální vyhledávání Bingu a souboru Node.js

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní API pro vizuální vyhledávání Bingu a zobrazit výsledky hledání. Tato jednoduchá javascriptová aplikace nahraje obrázek do rozhraní API a zobrazí informace, které o něm byly vráceny. Zatímco tato aplikace je napsána v JavaScriptu, API je RESTful webová služba kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* Modul Požadavku pro JavaScript. K instalaci `npm install request` modulu můžete použít příkaz.
* Modul formuláře a dat. Modul můžete `npm install form-data` nainstalovat pomocí příkazu. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializovat aplikaci

1. Vytvořte soubor JavaScriptu ve svém oblíbeném rozhraní IDE nebo editoru a nastavte následující požadavky:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a cestu k bitové kopii. `baseUri`může být globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na webu Azure Portal pro váš prostředek:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Vytvořte funkci `requestCallback()` s názvem pro tisk odpovědi z rozhraní API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Vytvoření a odeslání požadavku na vyhledávání

Při nahrávání místního obrázku musí data `Content-Disposition` formuláře obsahovat záhlaví. Je nutné `name` nastavit jeho parametr na `filename` "image" a parametr lze nastavit na libovolný řetězec. Obsah formuláře obsahuje binární data obrazu. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Vytvořte nový objekt `FormData()` **FormData** pomocí aplikace a přidejte `fs.createReadStream()`k němu cestu k obrázku pomocí :
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Pomocí knihovny požadavků nahrajte obrázek a voláním `requestCallback()` otiskněte odpověď. Nezapomeňte přidat klíč předplatného do hlavičky požadavku:

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](../tutorial-bing-visual-search-single-page-app.md)
