---
title: 'Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Node.js'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak k nahrání obrázku do Visual API Bingu pro vyhledávání a získávat přehledy o něm.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 5fca4e960b449988a0e77b2ecc2d0a9c8ca1988f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741467"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Rychlý start: Získejte přehledy obrázků pomocí API REST pro vizuální vyhledávání Bingu a Node.js

V tomto rychlém startu můžete provést první volání do rozhraní API vizuální vyhledávání Bingu a zobrazení výsledků hledání. Tato jednoduchá aplikace JavaScript odešle obrázek do rozhraní API a zobrazí informace vrácené o něm. Zatímco tato aplikace je napsána v jazyce JavaScript, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

Při nahrávání místní image, data formuláře musí zahrnovat hlavičku Content-Disposition. Její parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/download/)
* Modul požadavku pro JavaScript
    * Můžete nainstalovat pomocí tohoto modulu `npm install request`
* Modul dat formuláře
    * Můžete nainstalovat pomocí tohoto modulu `npm install form-data`


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Inicializace aplikace

1. Vytvořte nový soubor JavaScript ve vašich oblíbených prostředím IDE nebo editorem a nastavte následující požadavky:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Vytváření proměnných pro váš koncový bod rozhraní API, klíč předplatného a cestu k bitové kopii.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Vytvořit funkci s názvem `requestCallback()` k tisku odpověď z rozhraní API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Sestavit a odeslat žádost o vyhledávání

1. Vytvořte nové s využitím dat formuláře `FormData()`a připojte vaše cesta k bitové kopii, pomocí `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Použít knihovnu žádost odešlete image, volání `requestCallback()` k tisku odpověď. Nezapomeňte přidat klíč předplatného. do hlavičky žádosti. 

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](../tutorial-bing-visual-search-single-page-app.md)