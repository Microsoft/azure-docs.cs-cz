---
title: 'Rychlý Start: extrakce vytištěného textu – REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete extrahovat tištěný text z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a JavaScriptu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e28196a4de6e487b7119a00dc9b10f5c97fc006
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685133"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-javascript"></a>Rychlý Start: extrakce vytištěného textu (OCR) pomocí Počítačové zpracování obrazu REST API a JavaScriptu

> [!NOTE]
> Pokud extrahujete text v anglickém jazyce, zvažte použití nové [operace čtení](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text). K dispozici je [rychlý Start pro JavaScript](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/javascript-hand-text) .

V tomto rychlém startu extrahujete vytištěný text pomocí optického rozpoznávání znaků (OCR) z obrázku pomocí REST API Počítačové zpracování obrazu. Pomocí metody [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) můžete detekovat tištěný text v obrázku a extrahovat rozpoznané znaky do znakového proudu, který je strojově využitelný.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Uložte klíč předplatného a adresu URL koncového bodu do dočasného umístění.

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Vytvořte soubor s názvem _Get-printed-text. html_, otevřete ho v textovém editoru a zkopírujte do něj následující kód.
1. Volitelně můžete hodnotu atributu `value` pro ovládací prvek `inputImage` nahradit adresou URL jiného obrázku, který chcete analyzovat.
1. Otevřete okno prohlížeče.
1. Přetáhněte daný soubor do okna prohlížeče.
1. Po zobrazení webové stránky v prohlížeči vložte klíč předplatného a adresu URL koncového bodu do příslušných vstupních polí.
1. Vyberte tlačítko **načíst obrázek** .

```html
<!DOCTYPE html>
<html>
<head>
    <title>OCR Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v3.0/ocr";

        // Request parameters.
        var params = {
            "language": "unk",
            "detectOrientation": "true",
        };

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ?
                "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>

<h1>Optical Character Recognition (OCR):</h1>
Enter the URL to an image of printed text, then
click the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage" 
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková webová stránka provede analýzu a zobrazí úspěšnou odpověď v okně prohlížeče, podobně jako v následujícím příkladu:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

Dále Prozkoumejte aplikaci JavaScriptu, která používá Počítačové zpracování obrazu k provedení optického rozpoznávání znaků (OCR); vytvořit miniatury s inteligentním oříznutím; a k detekci, kategorizaci, označení a popisu vizuálních funkcí v obrázcích. 

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu v JavaScriptu](../Tutorials/javascript-tutorial.md)

* Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
