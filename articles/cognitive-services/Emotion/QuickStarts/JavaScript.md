---
title: 'Rychlý start: Rozpoznávání emocí na tváří v obrázku – rozhraní API pro rozpoznávání Emocí, JavaScript'
titlesuffix: Azure Cognitive Services
description: Získejte informace a vzorové kódy, které vám pomůžou rychle začít používat rozhraní API pro rozpoznávání emocí pomocí JavaScriptu.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 96388bb3cf2b73000fe871bc9e08720038c5c5a3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234692"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rychlý start: Vytvářejte aplikace pro rozpoznávání emocí na tváří v obrázku.

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou rychle začít používat [metodu Recognize rozhraní API pro rozpoznávání emocí](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) s JavaScriptem k rozpoznávání emocí vyjadřovaných jedním člověkem nebo několika lidmi na obrázku.

## <a name="prerequisite"></a>Požadavek
* [Tady](https://azure.microsoft.com/try/cognitive-services/) získejte klíč svého bezplatného předplatného. Případně, pokud už máte předplatné Azure, vytvořte prostředek rozhraní API pro rozpoznávání emocí a klíč předplatného a koncový bod získejte z tohoto prostředku.

![Vytvoření prostředku rozhraní API pro rozpoznávání emocí](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Ukázka požadavku na rozpoznávání emocí s JavaScriptem

Zkopírujte následující kód a uložte ho do souboru, jako je třeba `test.html`. Změňte adresu `url` požadavku na umístění, ve kterém jste získali klíče předplatného, a hodnotu Ocp-Apim-Subscription-Key nahraďte platným klíčem předplatného. Tyto hodnoty najdete na webu Azure Portal v částech Přehled a Klíče vašeho prostředku rozhraní API pro rozpoznávání emocí.

![Koncový bod rozhraní API](../Images/api-url.png)

![Klíč předplatného rozhraní API](../Images/keys.png)

Změňte text požadavku na umístění obrázku, který chcete použít. Ukázku spustíte přetažením souboru do prohlížeče.

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

## <a name="recognize-emotions-sample-response"></a>Ukázka odpovědi rozpoznávání emocí
Úspěšné volání vrátí pole položek tváří s přidruženým skóre rozpoznávání emocí, které bude seřazené podle velikosti obdélníku tváře v sestupném pořadí. Prázdná odpověď znamená, že se nezjistily žádné tváře. Položka rozpoznávání emocí obsahuje následující pole:
* faceRectangle – umístění obdélníku tváře na obrázku.
* scores – skóre rozpoznávání emocí pro jednotlivé tváře na obrázku.

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
