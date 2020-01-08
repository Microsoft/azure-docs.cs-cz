---
title: 'Rychlý Start: Získání přehledů obrázků pomocí REST API a Node. js – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat přehled o něm.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379704"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Rychlý Start: Získání přehledů imagí pomocí Vizuální vyhledávání Bingu REST API a Node. js

V tomto rychlém startu můžete provést první volání rozhraní API pro vizuální vyhledávání Bingu a zobrazit výsledky hledání. Tato jednoduchá aplikace JavaScriptu nahraje obrázek do rozhraní API a zobrazí vrácené informace. I když je tato aplikace napsaná v JavaScriptu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* Modul žádosti pro JavaScript K instalaci modulu můžete použít příkaz `npm install request`.
* Modul dat formuláře. K instalaci modulu můžete použít příkaz `npm install form-data`. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte soubor JavaScriptu a nastavte následující požadavky:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Vytvořte proměnné pro svůj koncový bod rozhraní API, klíč předplatného a cestu k vašemu obrázku. `baseUri` může být globální koncový bod nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Vytvořte funkci s názvem `requestCallback()` pro tisk odpovědi z rozhraní API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Sestavit a odeslat požadavek hledání

Při nahrávání místní image musí data formuláře obsahovat hlavičku `Content-Disposition`. Je nutné nastavit jeho parametr `name` na hodnotu "image" a parametr `filename` lze nastavit na libovolný řetězec. Obsah formuláře zahrnuje binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Vytvořte nový objekt **FormData** pomocí `FormData()`a přidejte do něj cestu k imagi pomocí `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Použijte knihovnu požadavků k nahrání obrázku a zavolejte `requestCallback()` k vytištění odpovědi. Nezapomeňte do hlavičky žádosti přidat svůj klíč předplatného:

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
> [Vytvoření webové aplikace Vizuální vyhledávání jednostránkového stránkování](../tutorial-bing-visual-search-single-page-app.md)
