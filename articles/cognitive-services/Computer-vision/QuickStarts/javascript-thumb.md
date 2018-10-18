---
title: 'Rychlý start: Vygenerování miniatury – REST, JavaScript – počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní API pro počítačové zpracování obrazu a JavaScriptu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: e221e2f65182092123ba3a61cf8d8844d9b6ee96
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343074"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-javascript-in-computer-vision"></a>Rychlý start: Vygenerování miniatury pomocí rozhraní REST API a JavaScriptu v počítačovém zpracování obrazu

V tomto rychlém startu vygenerujete miniaturu z obrázku pomocí rozhraní REST API počítačového zpracování obrazu. Miniaturu obrázku můžete vygenerovat pomocí metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb). Zadáte výšku a šířku, které se mohou od poměru stran vstupního obrázku lišit. Počítačové zpracování obrazu použije chytré oříznutí, které inteligentně identifikuje oblast zájmu a na základě této oblasti vygeneruje souřadnice oříznutí.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Vytvoření a spuštění ukázky

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující kód.
1. Proveďte v kódu na příslušných místech následující změny:
    1. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
    1. Hodnotu `uriBase` nahraďte adresou URL koncového bodu metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Volitelně můžete hodnotu atributu `value` pro ovládací prvek `inputImage` nahradit adresou URL jiného obrázku, který chcete analyzovat.
1. Uložte kód jako soubor s příponou `.html`. Například, `get-thumbnail.html`.
1. Otevřete okno prohlížeče.
1. Přetáhněte daný soubor do okna prohlížeče.
1. Když se v prohlížeči zobrazí webová stránka, zvolte tlačítko **Generate thumbnail** (Vygenerovat miniaturu).

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the West Central US region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
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
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí jako binární data, která představují data miniatury obrázku. Pokud je požadavek úspěšný, vygeneruje se z binárních dat v odpovědi miniatura a zobrazí se v okně prohlížeče. Pokud požadavek selže, odpověď se zobrazí v okně konzoly. Odpověď požadavku, který selhal, bude obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už soubor nepotřebujete, odstraňte ho.

## <a name="next-steps"></a>Další kroky

Prozkoumejte aplikaci v JavaScriptu, která používá počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro počítačové zpracování obrazu v JavaScriptu](../Tutorials/javascript-tutorial.md)
