---
title: 'Rychlý Start: generování miniatur – REST, Node. js'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5c3a6ba009771a879694fe869862f2c3e5836114
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685089"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Rychlý Start: vygenerování miniatury pomocí Počítačové zpracování obrazu REST API a Node. js

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí REST API Počítačové zpracování obrazu. Miniaturu obrázku můžete vygenerovat pomocí metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu používá inteligentní ořezávání k inteligentně identifikaci oblasti zájmu a generování souřadnic oříznutí na základě této oblasti.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít nainstalovaný [Node.js](https://nodejs.org) 4.x nebo novější.
- Musíte mít nainstalovaný [npm](https://www.npmjs.com/).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `COMPUTER_VISION_SUBSCRIPTION_KEY` a v `COMPUTER_VISION_ENDPOINT` uvedeném pořadí.

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Nainstalujte balíček npm [`request`](https://www.npmjs.com/package/request) .
   1. Otevřete okno příkazového řádku jako správce.
   1. Spusťte následující příkaz:

      ```console
      npm install request
      ```

   1. Po úspěšné instalaci balíčku zavřete okno příkazového řádku.

1. Zkopírujte do textového editoru následující kód.
1. Volitelně můžete hodnotu `imageUrl` nahradit adresou URL jiného obrázku, který chcete analyzovat.
1. Uložte kód jako soubor s příponou `.js`. Například, `get-thumbnail.js`.
1. Otevřete okno příkazového řádku.
1. Ke spuštění souboru na příkazovém řádku použijte příkaz `node`. Například, `node get-thumbnail.js`.

```javascript
'use strict';

const fs = require('fs');
const request = require('request').defaults({ encoding: null });

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']

var uriBase = endpoint + 'vision/v3.0/generateThumbnail';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

// Construct the request
const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
}

// Post the request and get the response (an image stream)
request.post(options, (error, response, body) => {
    // Write the stream to file
    var buf = Buffer.from(body, 'base64');
    fs.writeFile('thumbnail.png', buf, function (err) {
        if (err) throw err;
    });

    console.log('Image saved')
});
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Zobrazí se automaticky otevíraná okna obrázku miniatury.
Úspěšná odpověď se vrátí jako binární data, která představují data miniatury obrázku. Pokud požadavek selže, odpověď se zobrazí v okně konzoly. Odpověď požadavku, který selhal, bude obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="next-steps"></a>Další kroky

Dále Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané k analýze obrázku, detekci celebrit a orientačních bodů, vytvoření miniatury a extrakci vytištěného a rukopisného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
