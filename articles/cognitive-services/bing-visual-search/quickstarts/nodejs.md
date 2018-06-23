---
title: Rychlý start JavaScript pro Bing Visual vyhledávání rozhraní API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak nahrát bitovou kopii do rozhraní API vyhledávání Visual Bing a získat zpět přehledy o bitové kopii.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd28c829d8d24980a746244dc6aca880d2d69224
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343601"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>První Bing Visual vyhledávací dotaz v jazyce JavaScript

Rozhraní API vyhledávání Visual Bing vrací informace o bitovou kopii, kterou zadáte. Můžete zadat bitovou kopii pomocí adresy URL bitové kopie, insights tokenu, nebo tím, že nahrajete image. Informace o těchto možnostech najdete v tématu [co je rozhraní API vyhledávání Visual Bing?](../overview.md) Tento článek ukazuje, odešlou obrázek. Nahrávání obrázku může být užitečné v mobilních situacích, kde pořídit snímek dobře známé významné a vrátit informace o něm. K přehledům může obsahovat třeba trivia o významné. 

Odešlete obrázek místní následující zobrazí formulář data, že je nutné zahrnout text příspěvku. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr lze nastavit na libovolný řetězec. Obsah formuláře je binární soubor bitové kopie. Maximální obrázek, který může odeslat je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduché konzolové aplikace, která se odešle požadavek Visual Bing rozhraní API služby Search a zobrazí výsledky hledání JSON. Při této aplikace je napsána v jazyce JavaScript, rozhraní API je kompatibilní s žádný programovací jazyk, který můžete nastavit požadavků HTTP a analyzovat JSON RESTful webová služba. 

## <a name="prerequisites"></a>Požadavky

Je třeba [Node.js 6](https://nodejs.org/en/download/) pro spuštění tohoto kódu.

Pro tento rychlý start, můžete použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatné nebo placené předplatné klíče.

## <a name="running-the-application"></a>Spouštění aplikace.

Následující ukazuje, jak odeslat zprávu pomocí FormData v Node.js.

Ke spuštění této aplikace, postupujte takto:

1. Vytvořte složku pro svůj projekt (nebo pomocí Oblíbené IDE nebo editoru).
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Instalace modulů žádost:  
  ```  
  npm install request  
  ```  
3. Instalace modulů data formuláře:  
  ```  
  npm install form-data  
  ```  
4. Vytvořte soubor s názvem GetVisualInsights.js a přidejte do ní následující kód.
5. Nahraďte `subscriptionKey` hodnotu s svůj klíč předplatného.
6. Nahraďte `imagePath` hodnotu s cestou bitové kopie a nahrajte.
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

[Získejte přehledy o bitovou kopii pomocí tokenu statistiky](../use-insights-token.md)  
[Kurz jednostránkové aplikace Bing Visual Search](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Visual vyhledávání v Bingu](../overview.md)  
[Vyzkoušet](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace rozhraní API Visual pro hledání Bing](https://aka.ms/bingvisualsearchreferencedoc)
