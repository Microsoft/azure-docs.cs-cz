---
title: 'Rychlý start: Vygenerování miniatury – REST, Node.js – Počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete ve službě Cognitive Services pomocí počítačového zpracování obrazu s Node.js miniaturu obrázku.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: ebc9c89969641ace679b3d6677efd67138424712
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842575"
---
# <a name="quickstart-generate-a-thumbnail---rest-nodejs---computer-vision"></a>Rychlý start: Vygenerování miniatury – REST, Node.js – Počítačové zpracování obrazu

V tomto rychlém startu vygenerujete pomocí počítačového zpracování obrazu miniaturu obrázku.

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak klíče předplatného získat](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Žádost Get Thumbnail

Miniaturu obrázku můžete vygenerovat pomocí [metody Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud chcete spustit ukázku, postupujte takto:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte hodnotu `uriBase` na umístění, kde jste získali klíče předplatného.
1. Volitelně můžete změnit hodnotu `imageUrl` na obrázek, který chcete analyzovat.
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
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
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
});
```

## <a name="get-thumbnail-response"></a>Odpověď metody Get Thumbnail

Úspěšná odpověď obsahuje binární kód miniatury. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
