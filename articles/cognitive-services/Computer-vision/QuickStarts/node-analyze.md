---
title: Rychlý start k analýze obrázku pomocí rozhraní API pro počítačové zpracování obrazu s Node.js | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu analyzujete obrázek pomocí počítačového zpracování obrazu s Node.js ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: dab6547e08b1b01a9090a817d728c86359c680f2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769297"
---
# <a name="quickstart-analyze-a-remote-image---rest-nodejs"></a>Rychlý start: Analýza vzdáleného obrázku – REST, Node.js

V tomto rychlém startu analyzujete obrázek za účelem extrakce vizuálních prvků pomocí počítačového zpracování obrazu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-image-request"></a>Žádost Analyze Image

Pomocí [metody Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální funkce na základě obsahu obrázku. Obrázek můžete nahrát nebo můžete určit jeho adresu URL a vybrat funkce, které se mají vrátit, včetně:

* Podrobného seznamu značek souvisejících s obsahem obrázku
* Popisu obsahu obrázku v celé větě
* Souřadnic, pohlaví a věku veškerých obličejů na obrázku
* Typu obrázku (klipart nebo perokresba)
* Převládající barvy, doplňkové barvy a toho, jestli je obrázek černobílý
* Kategorie definované v této [taxonomii](../Category-Taxonomy.md)
* Je obsah obrázku určený pro dospělé nebo se jedná o sexuálně sugestivní obsah?

Pokud chcete spustit ukázku, postupujte následovně:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete změnit hodnotu `imageUrl` na obrázek, který chcete analyzovat.
1. Volitelně můžete změnit jazyk odpovědi (`'language': 'en'`).
1. Uložte soubor s příponou `.js`.
1. Otevřete příkazový řádek Node.js a spusťte soubor, například: `node myfile.js`.

Tato ukázka používá balíček [požadavku](https://www.npmjs.com/package/request) npm.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze';

const imageUrl =
    'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="analyze-image-response"></a>Odpověď metody Analyze Image

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumejte rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
