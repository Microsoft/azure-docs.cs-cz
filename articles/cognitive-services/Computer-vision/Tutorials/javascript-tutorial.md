---
title: Kurz vize API JavaScript počítače | Microsoft Docs
description: Prozkoumejte základní aplikaci JavaScript, která používá rozhraní API vize počítače v kognitivní služby společnosti Microsoft. Provést rozpoznávání znaků, vytváření miniatur a pracovat s visual funkce v obraze.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342541"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Kurz počítače vize rozhraní API jazyka JavaScript

Tento kurz ukazuje funkce Microsoft kognitivní služby počítače vize REST API.

Prozkoumejte aplikace JavaScript, která používá rozhraní API REST vize počítače k provedení optické rozpoznávání znaků (rozpoznávání znaků), vytvořit oříznout čipové miniatur plus zjistit, kategorizace značky a popisují visual funkcí, včetně řezy, v bitovou kopii. Tento příklad umožňuje odeslat adresu URL obrázku pro analýzy nebo zpracování. Můžete v tomto příkladu s otevřeným zdrojem jako šablonu pro vytvoření vlastní aplikace v jazyce JavaScript použít rozhraní API REST vize počítače.

Formuláře aplikace JavaScript již byla zapsána, ale nemá používat žádné funkce vize počítače. V tomto kurzu přidáte kód konkrétní počítač vize REST API pro dokončení funkcí aplikace.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V tomto kurzu byla vyvinuta pomocí jednoduchého textového editoru.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Přihlášení k počítači vize API odběru a získat klíč předplatného 

Před vytvořením v příkladu, se musíte přihlásit k rozhraní API vize počítače, které je součástí kognitivní služby společnosti Microsoft. Předplatné a správu klíčů podrobnosti najdete v tématu [odběry](https://azure.microsoft.com/try/cognitive-services/). Primární a sekundární klíče jsou platná pro použití v tomto kurzu. 

## <a name="download-the-tutorial-project"></a>Stáhněte si kurz projekt

Klon [kognitivní Services JavaScript počítače vize kurzu](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), nebo ji stáhněte soubor .zip a rozbalte ho prázdný adresář.

Pokud si přejete použít dokončení kurzu všechny kurz kódem přidat, můžete soubory v **dokončeno** složky.

## <a name="add-the-tutorial-code"></a>Přidat kód kurz

Aplikace JavaScript je nastavený s šesti soubory HTML, jeden pro každou funkci. Každý soubor ukazuje různé funkce vize počítače (analyzovat, rozpoznávání znaků atd.). Šest části kurzu nemají vzájemné závislosti, takže kód kurzu můžete přidat jeden soubor, všechny soubory šesti nebo pouze několik souborů. A kód kurzu můžete přidat k souborům v libovolném pořadí.

Můžeme začít.

## <a name="analyze-an-image"></a>Analýza obrázku

Funkci analyzovat vize počítače analyzuje bitovou kopii pro více než 2 000 rozpoznatelném objekty, životností lidé, kulisy a akce. Po dokončení analýzy, analýza vrátí objekt JSON, který popisuje bitové kopie s popisné značky, barva analýzy, titulky a další.

K dokončení funkci analyzovat kurz aplikace, proveďte následující kroky:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Analýza krok 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **analyze.html** soubor v textovém editoru a najděte **analyzeButtonClick** funkce v dolní části souboru.

**AnalyzeButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **AnalyzeImage** funkce pro analýzu bitovou kopii.

Zkopírujte a vložte následující kód do **analyzeButtonClick** funkce.

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

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Analýza krok 2: Přidat obálku pro volání rozhraní REST API

**AnalyzeImage** funkce zabalí volání rozhraní REST API k analýze bitovou kopii. Po úspěšné return formátovaný analysis JSON se zobrazí v zadané textarea a titulek se zobrazí v dané rozpětí.

Zkopírujte a vložte **AnalyzeImage** funkce kódu do právě pod **analyzeButtonClick** funkce.

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

### <a name="analyze-step-3-run-the-application"></a>Analýza krok 3: spuštění aplikace

Uložit **analyze.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii analyzovat a pak klikněte na **analyzovat Image** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="recognize-a-landmark"></a>Rozpoznat významné

Funkci významné vize počítače analyzuje bitovou kopii pro přirozené a umělé zajímavá, jako je například hory nebo famous budovy. Po dokončení analýzy významné vrátí objekt JSON, který identifikuje zajímavá najít v bitové kopii.

K dokončení funkci významné kurz aplikace, proveďte následující kroky:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Významné krok 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **landmark.html** soubor v textovém editoru a najděte **landmarkButtonClick** funkce v dolní části souboru.

**LandmarkButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **IdentifyLandmarks** funkce pro analýzu bitovou kopii.

Zkopírujte a vložte následující kód do **landmarkButtonClick** funkce.

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

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Významné krok 2: Přidat obálku pro volání rozhraní REST API

**IdentifyLandmarks** funkce zabalí volání rozhraní REST API k analýze bitovou kopii. Po úspěšné return formátovaný analysis JSON se zobrazí v zadané textarea a titulek se zobrazí v dané rozpětí.

Zkopírujte a vložte **IdentifyLandmarks** funkce kódu do právě pod **landmarkButtonClick** funkce.

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

### <a name="landmark-step-3-run-the-application"></a>Významné krok 3: spuštění aplikace

Uložit **landmark.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii analyzovat a pak klikněte na **analyzovat Image** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="recognize-celebrities"></a>Rozpoznání celebrit

Funkci celebrit vize počítače analyzuje bitovou kopii pro famous osoby. Po dokončení analýzy celebrit vrátí objekt JSON, který identifikuje celebrit najít v bitové kopii.

K dokončení funkci celebrit kurz aplikace, proveďte následující kroky:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Celebrit krok 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **celebrities.html** soubor v textovém editoru a najděte **celebritiesButtonClick** funkce v dolní části souboru.

**CelebritiesButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **IdentifyCelebrities** funkce pro analýzu bitovou kopii.

Zkopírujte a vložte následující kód do **celebritiesButtonClick** funkce.

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

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Celebrit krok 2: Přidat obálku pro volání rozhraní REST API

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

### <a name="celebrities-step-3-run-the-application"></a>Celebrit krok 3: spuštění aplikace

Uložit **celebrities.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii analyzovat a pak klikněte na **analyzovat Image** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="intelligently-generate-a-thumbnail"></a>Inteligentně generovat miniaturu

Funkci miniaturu vize počítače vygeneruje Miniatura z bitové kopie. Pomocí **inteligentní ořezové** funkce, funkce miniaturu určují oblast zájmu do bitové kopie a center miniaturu v této oblasti k vygenerování více vkusnou obrázky miniatur.

K dokončení funkci miniaturu kurz aplikace, proveďte následující kroky:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Miniatury krok 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **thumbnail.html** soubor v textovém editoru a najděte **thumbnailButtonClick** funkce v dolní části souboru.

**ThumbnailButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **getThumbnail** funkce dvakrát k vytvoření dvou miniatur, jeden čipové oříznout a jeden bez inteligentní oříznutí.

Zkopírujte a vložte následující kód do **thumbnailButtonClick** funkce.

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

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Miniatury krok 2: Přidat obálku pro volání rozhraní REST API

**GetThumbnail** funkce zabalí volání rozhraní REST API k analýze bitovou kopii. Po úspěšné vrátit se zobrazí v elementu zadaný img miniaturu.

Zkopírujte a vložte následující **getThumbnail** funkce, která se právě pod **thumbnailButtonClick** funkce.

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

### <a name="thumbnail-step-3-run-the-application"></a>Miniatury krok 3: spuštění aplikace

Uložit **thumbnail.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii analyzovat a pak klikněte na **generovat miniatur** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="read-printed-text-ocr"></a>Čtení textu tištěné (rozpoznávání znaků)

Funkci optické rozpoznávání znaků (rozpoznávání znaků) vize počítače analyzuje obrázek tištěné textu. Po dokončení analýzy rozpoznávání znaků vrátí objekt JSON, který obsahuje text a umístění textu v bitové kopii.

K dokončení kurzu aplikace funkce rozpoznávání znaků, proveďte následující kroky:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>Rozpoznávání znaků krok 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **ocr.html** soubor v textovém editoru a najděte **ocrButtonClick** funkce v dolní části souboru.

**OcrButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **ReadOcrImage** funkce pro analýzu bitovou kopii.

Zkopírujte a vložte následující kód do **ocrButtonClick** funkce.

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

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>Rozpoznávání znaků krok 2: Přidat obálku pro volání rozhraní REST API

**ReadOcrImage** funkce zabalí volání rozhraní REST API k analýze bitovou kopii. Po úspěšně vrátí formátovaný JSON s popisem text a zobrazí se umístění textu v zadané textarea.

Zkopírujte a vložte následující **ReadOcrImage** funkce, která se právě pod **ocrButtonClick** funkce.

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

### <a name="ocr-step-3-run-the-application"></a>Rozpoznávání znaků krok 3: spuštění aplikace

Uložit **ocr.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii textu pro čtení a pak klikněte na **bitovou kopii pro čtení** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="read-handwritten-text-handwriting-recognition"></a>Čtení textu psané (rozpoznávání rukopisu)

Funkce rozpoznávání rukopisu vize počítače analyzuje bitové kopie ručně psaný text. Po dokončení analýzy rozpoznávání rukopisu vrátí objekt JSON, který obsahuje text a umístění textu v bitové kopii.

K dokončení kurzu aplikace funkce rozpoznávání rukopisu, proveďte následující kroky:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Krok rozpoznávání rukopisu 1: přidat kód pro obslužnou rutinu události pro tlačítko formuláře

Otevřete **handwriting.html** soubor v textovém editoru a najděte **handwritingButtonClick** funkce v dolní části souboru.

**HandwritingButtonClick** funkce obslužné rutiny událostí vymaže formuláře, zobrazí obrázek zadaného v adrese URL, pak zavolá **HandwritingImage** funkce pro analýzu bitovou kopii.

Zkopírujte a vložte následující kód do **handwritingButtonClick** funkce.

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

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Krok rozpoznávání rukopisu 2: Přidat obálku pro volání rozhraní REST API

**ReadHandwrittenImage** funkce zabalí dvě volání rozhraní REST API, které jsou potřebné k analýze bitovou kopii. Protože rozpoznávání rukopisu je časově náročný proces, se používá dvou krocích. První volání odešle bitovou kopii pro zpracování; druhé volání načte zjištěné text po dokončení zpracování.

Po načtení textu, zobrazí se v zadané textarea formátovaný JSON popisující text a umístění textu.

Zkopírujte a vložte následující **ReadHandwrittenImage** funkce, která se právě pod **handwritingButtonClick** funkce.

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

### <a name="handwriting-recognition-step-3-run-the-application"></a>Krok rozpoznávání rukopisu 3: spuštění aplikace

Uložit **handwriting.html** souborů a otevřete ve webovém prohlížeči. Uveďte svůj klíč předplatného do **klíč předplatného** pole a ověřte, že používáte správné oblast v **předplatné oblast**. Zadejte adresu URL pro bitovou kopii textu pro čtení a pak klikněte na **bitovou kopii pro čtení** tlačítko k analýze bitovou kopii a zobrazit výsledky.

## <a name="next-steps"></a>Další postup

- [Počítač vize rozhraní API C&#35; kurzu](CSharpTutorial.md)
- [Počítač vize API Python kurzu](PythonTutorial.md)
