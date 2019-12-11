---
title: 'Rychlý start: Rozpoznávání tváří na obrázku pomocí rozhraní REST API a JavaScriptu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete pomocí rozhraní API pro rozpoznávání tváře s JavaScriptem rozpoznávat tváře z obrázku ve službách Cognitive Services.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 3946f6d034baee528f9cd5abd6467b8c393e73de
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977977"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-javascript"></a>Rychlý start: Rozpoznávání tváří na obrázku pomocí rozhraní REST API a JavaScriptu

V tomto rychlém startu použijete REST API Azure Face s JavaScriptem k detekci lidských plošek v obrázku.

## <a name="prerequisites"></a>Předpoklady

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- Editor kódu, jako je například [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="initialize-the-html-file"></a>Inicializovat soubor HTML

Vytvořte nový soubor HTML *detectFaces. html*a přidejte následující kód.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Detect Faces Sample</title>
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    </head>
    <body></body>
</html>
```

Poté do `body`ho prvku dokumentu přidejte následující kód. Tento kód nastaví základní uživatelské rozhraní s polem Adresa URL, tlačítko **analyzovat plochu** , podokno odpovědi a podokno zobrazení obrázku.

```html
<h1>Detect Faces:</h1>
Enter the URL to an image that includes a face or faces, then click
the <strong>Analyze face</strong> button.<br><br>
Image to analyze: <input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg" />
<button onclick="processImage()">Analyze face</button><br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:<br><br>
        <textarea id="responseTextArea" class="UIInput"
            style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:<br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
```

## <a name="write-the-javascript-script"></a>Zápis skriptu JavaScriptu

Přidejte následující kód hned nad `h1` prvek v dokumentu. Tento kód nastaví kód JavaScriptu, který volá Face API.

```html
<script type="text/javascript">
    function processImage() {
        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";
    
        var uriBase =
            "https://<My Endpoint String>.com/face/v1.0/detect";
    
        // Request parameters.
        var params = {
            "returnFaceId": "true",
            "returnFaceLandmarks": "false",
            "returnFaceAttributes":
                "age,gender,headPose,smile,facialHair,glasses,emotion," +
                "hair,makeup,occlusion,accessories,blur,exposure,noise"
        };
    
        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;
    
        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),
    
            // Request headers.
            beforeSend: function(xhrObj){
                xhrObj.setRequestHeader("Content-Type","application/json");
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
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
            errorString += (jqXHR.responseText === "") ?
                "" : (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                        jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
```

Musíte aktualizovat pole `subscriptionKey` hodnotou vašeho klíče předplatného a je třeba změnit řetězec `uriBase` tak, aby obsahoval správný řetězec koncového bodu. Pole `returnFaceAttributes` určuje, které atributy obličeje se mají načíst. Tento řetězec možná budete chtít změnit v závislosti na zamýšleném použití.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Spuštění skriptu

V prohlížeči otevřete *detectFaces. html* . Když kliknete na tlačítko **analyzovat plošku** , aplikace by měla zobrazit obrázek z dané adresy URL a vytisknout řetězec formátu obličeje.

![GettingStartCSharpScreenshot](../Images/face-detect-javascript.png)

Následující text je příkladem úspěšné odpovědi JSON.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste napsali skript JavaScriptu, který volá Face API Azure pro detekci plošek v obrázku a vrácení jejich atributů. Dále si Projděte referenční dokumentaci Face API, kde najdete další informace.

> [!div class="nextstepaction"]
> [Rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
