---
title: 'Rychlý Start: Získání přehledů obrázků pomocí REST API a Node.js-Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Naučte se nahrát obrázek pomocí rozhraní API pro vizuální vyhledávání Bingu a Node.js a získat přehled o imagi.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: 94a642886b626eb84da3a2d02684b5dd170dcbb1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499063"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Rychlý Start: Získání přehledů obrázků pomocí Vizuální vyhledávání Bingu REST API a Node.js

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

V tomto rychlém startu můžete provést první volání rozhraní API pro vizuální vyhledávání Bingu. Tato jednoduchá aplikace JavaScriptu nahraje obrázek do rozhraní API a zobrazí vrácené informace. I když je tato aplikace napsaná v JavaScriptu, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Předpoklady

* [Node.js](https://nodejs.org/en/download/)
* Modul žádosti pro JavaScript `npm install request`K instalaci modulu můžete použít příkaz.
* Modul dat formuláře. `npm install form-data`K instalaci modulu můžete použít příkaz. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte soubor JavaScriptu a nastavte následující požadavky:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Vytvořte proměnné pro svůj koncový bod rozhraní API, klíč předplatného a cestu k vašemu obrázku. Pro tuto `baseUri` hodnotu můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Vytvořte funkci nazvanou `requestCallback()` pro tisk odpovědi z rozhraní API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Sestavit a odeslat požadavek hledání

1. Když nahrajete místní obrázek, data formuláře musí obsahovat `Content-Disposition` hlavičku. Nastavte jeho `name` parametr na "image" a nastavte `filename` parametr na název souboru vaší image. Obsah formuláře zahrnuje binární data obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB.

   ```
   --boundary_1234-abcd
   Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

   ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

   --boundary_1234-abcd--
   ```

2. Vytvořte nový `FormData` objekt s `FormData()` a přidejte k němu cestu k imagi pomocí `fs.createReadStream()` .
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

3. Použijte knihovnu požadavků k nahrání obrázku a zavolejte `requestCallback()` k vytištění odpovědi. Do hlavičky žádosti přidejte svůj klíč předplatného.

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