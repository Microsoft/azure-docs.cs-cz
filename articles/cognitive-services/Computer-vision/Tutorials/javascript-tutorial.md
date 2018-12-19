---
title: 'Kurz: Provádění operací image – JavaScript'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte základní aplikaci v JavaScriptu, která v Azure Cognitive Services používá rozhraní API pro počítačové zpracování obrazu. Provádějte optické rozpoznávání znaků (OCR), vytvářejte miniatury a pracujte s vizuálními funkcemi obrázku.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 46d992fc44a0c3b2d355861d5753aa6f8c22215e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581364"
---
# <a name="tutorial-computer-vision-api-javascript"></a>Kurz: Počítač pro zpracování obrazu rozhraní API jazyka JavaScript

Tento kurz ukazuje funkce rozhraní REST API pro počítačové zpracování obrazu služby Azure Cognitive Services.

Prozkoumejte aplikaci v JavaScriptu, která používá rozhraní REST API pro počítačové zpracování obrazu k optickému rozpoznávání znaků (OCR), vytvořte chytře ořezané miniatury a rozpoznávejte, kategorizujte, označujte a popisujte vizuální vlastnosti na obrázku včetně obličejů. Tento příklad vám umožňuje odeslat adresu URL obrázku k analýze nebo zpracování. Tento opensourcový příklad můžete použít jako šablonu k vytvoření vlastní aplikace v JavaScriptu, která bude používat rozhraní REST API pro počítačové zpracování obrazu.

Aplikace v JavaScriptu už je napsaná, ale nemá žádnou funkci počítačového zpracování obrazu. V tomto kurzu přidáte konkrétní kód rozhraní REST API pro počítačové zpracování obrazu, kterým aplikaci funkci přidáte.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Tento kurz jsme vyvinuli pomocí jednoduchého textového editoru.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Získejte předplatné rozhraní API pro počítačové zpracování obrazu a získejte klíč předplatného 

Před vytvořením příkladu musíte získat předplatné rozhraní API pro počítačové zpracování obrazu, které je součástí Azure Cognitive Services. Podrobnosti o správě předplatného a klíče najdete v [předplatných](https://azure.microsoft.com/try/cognitive-services/). V tomto kurzu můžete použít jak primární, tak i sekundární klíče. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Získání neúplného výukového projektu

### <a name="download-the-tutorial-project"></a>Stažení výukového projektu

Naklonujte úložiště [Cognitive Services JavaScript Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) nebo stáhněte soubor ZIP a extrahujte ho do prázdného adresáře.

Pokud byste chtěli raději použít dokončený kurz s veškerým výukovým kódem, můžete použít soubory ve složce **Completed** (Dokončeno).

## <a name="add-the-tutorial-code-to-the-project"></a>Přidání výukového kódu do projektu

Javascriptová aplikace používá šest souborů HTML – jeden pro každou funkci. Každý soubor představuje jinou funkci počítačového zpracování obrazu (analýza, OCR atd.). Následujících šest částí kurzu na sobě nezávisí, takže výukový kód můžete přidat do kolika souborů chcete – do jednoho, několika nebo všech šesti. A můžete ho přidávat v libovolném pořadí.

Pusťme se do toho.

### <a name="analyze-an-image"></a>Analýza obrázku

Funkce Analyze v rozhraní API pro počítačové zpracování obrazu během prohledávání obrázků analyzuje více než 2 000 rozpoznatelných objektů, živých bytostí, krajin a akcí. Po dokončení analýzy se vrátí objekt JSON, který popisuje obrázek popisnými značkami, analýzou barev, titulky a dalšími položkami.

K dokončení funkce Analyze výukové aplikace musíte provést následující kroky:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **analyze.html** a na konci souboru najděte funkci **analyzeButtonClick**.

Funkce obslužné rutiny události **analyzeButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá funkci **AnalyzeImage**, která obrázek analyzuje.

Do funkce **analyzeButtonClick** zkopírujte a vložte následující kód.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

Funkce **AnalyzeImage** zabalí volání rozhraní REST API k analýze obrázku. Po úspěšně provedeném vrácení se v zadané textové oblasti zobrazí analýza formátovaného objektu JSON a v zadaném elementu span se zobrazí titulek.

Kód funkce **AnalyzeImage** zkopírujte a vložte těsně pod funkci **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **analyze.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku, který chcete analyzovat, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

### <a name="recognize-a-landmark"></a>Rozpoznání památky

Funkce Landmark počítačového zpracování obrazu vyhledá v obrázku přirozené nebo umělé památky, jako jsou hory nebo známé budovy. Po dokončení analýzy vrátí funkce Landmark objekt JSON, který památky nalezené v obrázku identifikuje.

K dokončení funkce Landmark výukové aplikace musíte provést následující kroky:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **landmark.html** a na konci souboru najděte funkci **landmarkButtonClick**.

Funkce obslužné rutiny události **landmarkButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá funkci **IdentifyLandmarks**, která obrázek analyzuje.

Do funkce **landmarkButtonClick** zkopírujte a vložte následující kód.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

Funkce **IdentifyLandmarks** zabalí volání rozhraní REST API k analýze obrázku. Po úspěšně provedeném vrácení se v zadané textové oblasti zobrazí analýza formátovaného objektu JSON a v zadaném elementu span se zobrazí titulek.

Kód funkce **IdentifyLandmarks** zkopírujte a vložte těsně pod funkci **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **landmark.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku, který chcete analyzovat, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

### <a name="recognize-celebrities"></a>Rozpoznání celebrit

Funkce Celebrities počítačového zpracování obrazu vyhledá na obrázku známé osobnosti. Po dokončení analýzy vrátí funkce Celebrities objekt JSON, který celebrity nalezené v obrázku identifikuje.

K dokončení funkce Celebrities výukové aplikace musíte provést následující kroky:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **celebrities.html** a na konci souboru najděte funkci **celebritiesButtonClick**.

Funkce obslužné rutiny události **celebritiesButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá funkci **IdentifyCelebrities**, která obrázek analyzuje.

Do funkce **celebritiesButtonClick** zkopírujte a vložte následující kód.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **celebrities.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku, který chcete analyzovat, potom klikněte na tlačítko **Analyze Image** (Analyzovat obrázek) a podívejte se na výsledek.

### <a name="intelligently-generate-a-thumbnail"></a>Inteligentní vygenerování miniatury

Funkce Thumbnail počítačového zpracování obrazu vygeneruje z obrázku miniaturu. Pomocí funkce **Smart Crop** (Inteligentní oříznutí) identifikuje funkce Thumbnail na obrázku oblast zájmu a kolem této oblasti vycentruje miniaturu, aby se vygenerovaly co nejhezčí miniatury obrázku.

K dokončení funkce Thumbnail výukové aplikace musíte provést následující kroky:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **thumbnail.html** a na konci souboru najděte funkci **thumbnailButtonClick**.

Funkce obslužné rutiny události **thumbnailButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom dvakrát zavolá funkci **getThumbnail**, aby vytvořila dvě miniatury – jednu s inteligentním oříznutím a jednu bez něj.

Do funkce **thumbnailButtonClick** zkopírujte a vložte následující kód.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

Funkce **getThumbnail** zabalí volání rozhraní REST API k analýze obrázku. Po úspěšně provedeném vrácení se miniatura zobrazí v zadaném elementu img.

Funkci **getThumbnail** zkopírujte a vložte těsně pod funkci **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **thumbnail.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku, který chcete analyzovat, potom klikněte na tlačítko **Generate Thumbnails** (Vygenerovat miniatury) a podívejte se na výsledek.

### <a name="read-printed-text-ocr"></a>Přečtení tištěného textu (OCR)

Funkce optického rozpoznávání znaků (OCR) počítačového zpracování obrazu analyzuje obrázky s tištěným textem. Po dokončení analýzy vrátí funkce OCR objekt JSON, který obsahuje text a umístění textu na obrázku.

K dokončení funkce OCR výukové aplikace musíte provést následující kroky:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR krok 1: Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **ocr.html** a na konci souboru najděte funkci **ocrButtonClick**.

Funkce obslužné rutiny události **ocrButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá funkci **ReadOcrImage**, která obrázek analyzuje.

Do funkce **ocrButtonClick** zkopírujte a vložte následující kód.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

Funkce **ReadOcrImage** zabalí volání rozhraní REST API k analýze obrázku. Po úspěšně provedeném vrácení se v zadané textové oblasti zobrazí formátovaný objekt JSON popisující text a jeho umístění.

Funkci **ReadOcrImage** zkopírujte a vložte těsně pod funkci **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **ocr.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku s textem, který se má přečíst, potom klikněte na tlačítko **Read Image** (Přečíst obrázek) a podívejte se na výsledek.

### <a name="read-handwritten-text-handwriting-recognition"></a>Přečtení rukou psaného textu (rozpoznávání rukopisu)

Funkce Handwriting Recognition počítačového zpracování obrazu analyzuje obrázek s ručně psaným textem. Po dokončení analýzy vrátí funkce Handwriting Recognition objekt JSON, který obsahuje text a umístění textu na obrázku.

K dokončení funkce Handwriting Recognition výukové aplikace musíte provést následující kroky:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Přidání kódu obslužné rutiny události k tlačítku formuláře

V textovém editoru otevřete soubor **handwriting.html** a na konci souboru najděte funkci **handwritingButtonClick**.

Funkce obslužné rutiny události **handwritingButtonClick** vymaže formulář, zobrazí obrázek zadaný adresou URL a potom zavolá funkci **HandwritingImage**, která obrázek analyzuje.

Do funkce **handwritingButtonClick** zkopírujte a vložte následující kód.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Přidání obálky k volání rozhraní REST API

Funkce **ReadHandwrittenImage** zabalí dvě volání rozhraní REST API potřebné k analýze obrázku. Rozpoznávání rukopisu je časově náročný proces, takže se provádí ve dvou krocích. První volání odešle obrázek ke zpracování a jakmile se zpracování dokončí, druhé volání načte rozpoznaný text.

Po načtení textu se v zadané textové oblasti zobrazí formátovaný objekt JSON popisující text a jeho umístění.

Funkci **ReadHandwrittenImage** zkopírujte a vložte těsně pod funkci **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-application"></a>Spuštění aplikace

Soubor **handwriting.html** uložte a otevřete ho ve webovém prohlížeči. Do pole **Subscription Key** (Klíč předplatného) vložte svůj klíč předplatného a zkontrolujte, že v poli **Subscription Region** (Oblast předplatného) používáte správnou oblast. Zadejte adresu URL obrázku s textem, který se má přečíst, potom klikněte na tlačítko **Read Image** (Přečíst obrázek) a podívejte se na výsledek.

## <a name="next-steps"></a>Další postup

- [Kurz – rozhraní API pro počítačové zpracování obrazu s C&#35;](CSharpTutorial.md)
- [Kurz k rozhraní API pro počítačové zpracování obrazu a Pythonu](PythonTutorial.md)
