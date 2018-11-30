---
title: 'Rychlý start: Vytvoření dotazu pro vizuální vyhledávání, Node.js – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Zde najdete informace, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat zpět přehledy o obrázku.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 553d068d70f7e722f3c8e4de3978f3583b941963
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442522"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Rychlý start: Váš první dotaz Vizuálního vyhledávání Bingu v JavaScriptu

Rozhraní API pro Vizuální vyhledávání Bingu vrátí informace o obrázku, který zadáte. Obrázek můžete zadat pomocí adresy URL obrázku, tokenu insights nebo nahráním obrázku. Informace o těchto možnostech najdete v článku [Co je rozhraní API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahrávání obrázku. Nahrání obrázku může být užitečné v situacích s mobilním zařízením, když pořídíte snímek dobře známé pamětihodnosti a získáte o ní informace. Přehledy můžou například obsahovat základní informace o pamětihodnosti. 

Pokud nahrajete místní obrázek, ukazuje následující kód data formuláře, která musíte zahrnout do textu žádosti POST. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která pošle žádost do rozhraní API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Aplikace je sice napsaná v Javascriptu, ale rozhraní API je webová služba RESTful kompatibilní s každým programovacím jazykem, který dokáže provádět požadavky HTTP a parsovat JSON. 

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto rychlého startu budete muset spustit si předplatné v cenové úrovni S9, jak je znázorněno v [Cognitive Services ceny – rozhraní API Bingu pro vyhledávání](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

K zahájení předplatného na webu Azure portal:
1. Zadejte do textového pole v horní části webu Azure portal s textem "BingSearchV7" `Search resources, services, and docs`.  
2. V části Marketplace v rozevíracím seznamu vyberte `Bing Search v7`.
3. Zadejte `Name` pro nový prostředek.
4. Vyberte `Pay-As-You-Go` předplatného.
5. Vyberte `S9` cenovou úroveň.
6. Klikněte na tlačítko `Enable` k zahájení odběru.

Abyste mohli tento kód spustit, potřebujete [Node.js 6](https://nodejs.org/en/download/).

## <a name="running-the-application"></a>Spouštění aplikace.

Níže najdete ukázku, jak zprávu odeslat pomocí objektu FormData v Node.js.

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Vytvořte složku pro váš projekt (nebo použijte vaše oblíbené prostředí IDE nebo oblíbený editor).
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Nainstalujte moduly žádosti:  
  ```  
  npm install request  
  ```  
3. Nainstalujte moduly objektu form-data:  
  ```  
  npm install form-data  
  ```  
4. Vytvořte soubor s názvem GetVisualInsights.js a přidejte do něj následující kód.
5. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
6. Hodnotu `imagePath` nahraďte cestou nahrávaného obrázku.
7. Spusťte program.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Další postup

[Získání přehledu o obrázku pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků Vizuálního vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[Kurz k jednostránkové aplikaci Vizuálního vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Vizuálního vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získání bezplatné zkušební verze přístupového klíče](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Reference k rozhraní API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
