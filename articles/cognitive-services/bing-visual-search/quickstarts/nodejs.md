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
ms.openlocfilehash: 3490f7722ca0c1331ccea26cd18398cff1317aee
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887408"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>Rychlý start: Váš první dotaz Vizuálního vyhledávání Bingu v JavaScriptu

Rozhraní API pro Vizuální vyhledávání Bingu vrátí informace o obrázku, který zadáte. Obrázek můžete zadat pomocí adresy URL obrázku, tokenu insights nebo nahráním obrázku. Informace o těchto možnostech najdete v článku [Co je rozhraní API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahrávání obrázku. Nahrání obrázku může být užitečné v situacích s mobilním zařízením, když pořídíte snímek dobře známé pamětihodnosti a získáte o ní informace. Přehledy můžou například obsahovat základní informace o pamětihodnosti. 

Pokud nahrajete místní obrázek, ukazuje následující kód data formuláře, která musíte zahrnout do textu žádosti POST. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která pošle žádost do rozhraní API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Aplikace je sice napsaná v Javascriptu, ale rozhraní API je webová služba RESTful kompatibilní s každým programovacím jazykem, který dokáže provádět požadavky HTTP a parsovat JSON. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Node.js 6](https://nodejs.org/en/download/).

Pro tento rychlý start můžete použít klíč [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného, nebo klíč placeného předplatného.

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


## <a name="next-steps"></a>Další kroky

[Získání přehledu o obrázku pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků Vizuálního vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[Kurz k jednostránkové aplikaci Vizuálního vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Vizuálního vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získání bezplatné zkušební verze přístupového klíče](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Reference k rozhraní API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
