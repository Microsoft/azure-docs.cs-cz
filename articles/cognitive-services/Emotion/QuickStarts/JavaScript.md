---
title: Rychlý start API JavaScript pro rozpoznávání emocí | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat rozhraní API pro rozpoznávání Emocí na jazyce JavaScript ve službě Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072088"
---
# <a name="emotion-api-javascript-quick-start"></a>Rychlý Start API JavaScript pro rozpoznávání emocí

> [!IMPORTANT]
> Verze Preview rozhraní Video API se ukončí 30. října 2017. Vyzkoušejte novou [verze Preview rozhraní Video Indexer API](https://azure.microsoft.com/services/cognitive-services/video-indexer/) snadno extrahovat přehledy z videí a k vylepšení možností zjišťování obsahu, jako jsou výsledky hledání podle detekce mluveného slova, tváří, charakteristik a emocí. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat [rozpoznat rozhraní API pro rozpoznávání Emocí metoda](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s použitím jazyka JavaScript pro rozpoznávání emocí vyjádřena pomocí jednoho nebo víc uživatelů v obraze.

## <a name="prerequisite"></a>Požadavek
* Získejte bezplatný klíč předplatného [tady](https://azure.microsoft.com/try/cognitive-services/), nebo pokud máte předplatné Azure, vytvoření prostředku rozhraní API pro rozpoznávání Emocí a získat klíč předplatného a koncový bod existuje.

![Vytvoření prostředku rozhraní API pro rozpoznávání Emocí](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Rozpoznávání Emocí požadavek příklad v jazyce JavaScript

Zkopírujte do něj následující a uložte ho do souboru jako `test.html`. Žádost o změnu `url` použít tak umístění, kde jste získali klíče předplatného a nahraďte hodnotu "Ocp-Apim-Subscription-Key" klíčem platné předplatné. Ty najdete na webu Azure Portal v části Přehled a klíče vašeho prostředku rozhraní API pro rozpoznávání Emocí v uvedeném pořadí. 

![Koncový bod rozhraní API](../Images/api-url.png)

![Klíč rozhraní API předplatného](../Images/keys.png)

Změňte text požadavku na umístění obrázku, který chcete použít. Ke spuštění souboru vzorek, přetáhněte myší do prohlížeče.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
```

## <a name="recognize-emotions-sample-response"></a>Rozpoznávání Emocí ukázková odpověď
Úspěšné volání vrátí pole položek pro rozpoznávání tváře a jejich přidružené pro rozpoznávání emocí skóre, seřazené podle velikosti obdélník tváří v sestupném pořadí. Odpověď je prázdná označuje, že nebyly zjištěny žádné tváře. Záznam pro rozpoznávání emocí obsahuje následující pole:
* faceRectangle - obdélník umístění tvář na obrázku.
* skóre - skóre pro rozpoznávání Emocí pro každou tvář na obrázku. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
