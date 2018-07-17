---
title: Rychlý start jazyka JavaScript pro vizuální vyhledávání Bingu rozhraní API | Dokumentace Microsoftu
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak k nahrání obrázku do API pro vizuální vyhledávání Bingu a získat přehledy o imagi.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071698"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Svůj první dotaz v jazyce JavaScript pro vizuální vyhledávání Bingu

API pro vizuální vyhledávání Bingu vrátí informace o bitovou kopii, která zadáte. Image můžete zadat pomocí adresy URL obrázku, insights, token, nebo nahráním image. Informace o těchto možnostech najdete v tématu [co je API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahráváním obrázku. Nahrání image může být užitečné v mobilních situacích, kde pořídit snímek dobře známé památek a získat informace o něm. Triviální prvek o památek může obsahovat třeba přehledy. 

Pokud odešlete místní image, následuje data formuláře musí obsahovat v textu příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr může být nastaven na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost, kterou můžete k nahrání je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která odešle žádost o API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Zatímco tato aplikace je napsána v jazyce JavaScript, je rozhraní API RESTful webová služba, která je kompatibilní s programovací jazyk, který může vytvářet požadavky HTTP a parsování formátu JSON. 

## <a name="prerequisites"></a>Požadavky

Potřebujete [Node.js 6](https://nodejs.org/en/download/) tento kód spustit.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-application"></a>Spouštění aplikace.

Následující ukazuje, jak odesílat zprávy pomocí FormData v Node.js.

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Vytvořte složku pro váš projekt (nebo použijte vaše oblíbené prostředí IDE nebo editoru).
2. Z příkazového řádku nebo v terminálu přejděte do složky, kterou jste právě vytvořili.
3. Instalace modulů žádosti:  
  ```  
  npm install request  
  ```  
3. Instalace modulů data formuláře:  
  ```  
  npm install form-data  
  ```  
4. Vytvořte soubor s názvem GetVisualInsights.js a přidejte do ní následující kód.
5. Nahraďte `subscriptionKey` hodnotu s klíči předplatného.
6. Nahraďte `imagePath` hodnotu s cestou k nahrání obrázku.
7. Spuštění programu.  
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

[Získejte přehled o bitovou kopii pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků pro vizuální vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[kurzu jednostránkovou aplikaci pro vizuální vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled pro vizuální vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
