---
title: 'Kurz: Detekce anomálií pomocí jazyka Javascript'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte webovou aplikaci v JavaScriptu, která používá rozhraní API pro detekci anomálií. Odešlete původní datové body do rozhraní API a získejte očekávanou hodnotu a body anomálie.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 9e66b24987b2318f3022404d951fbb911e7b592d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227824"
---
# <a name="tutorial-anomaly-detection-with-javascript-application"></a>Kurz: Detekce anomálií pomocí aplikace jazyka Javascript

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Prozkoumejte webovou aplikaci, která detekuje anomálie s využitím rozhraní REST API pro detekci anomálií. V příkladu se do rozhraní API pro detekci anomálií odešlou data časové řady s vaším klíčem předplatného. Pak se z rozhraní API získají všechny body anomálií a očekávané hodnoty pro jednotlivé datové body.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Tento kurz jsme vyvinuli pomocí jednoduchého textového editoru.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlášení k odběru detekce anomálií a získání klíče předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získání a použití příkladu

Tento kurz obsahuje dva scénáře detekce anomálií v datech časových řad. Pusťme se do toho.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Stažení výukového projektu

Naklonujte úložiště [Cognitive Services JavaScript Anomaly Detection Tutorial](https://github.com/MicrosoftAnomalyDetection/javascript-sample) nebo stáhněte soubor ZIP a extrahujte ho do prázdného adresáře.

<a name="Step2"></a>
### <a name="run-the-example"></a>Spuštění příkladu

V tomto příkladu můžete vyzkoušet dva scénáře.
1. Vložte svůj **klíč předplatného** do pole Subscription Key (Klíč předplatného) ve funkci detect v souboru anomalydetection.html.
2. Vložte koncový bod rozhraní API pro detekci anomálií a v poli Subscription Region (Oblast předplatného) ověřte, že používáte správnou oblast.
3. Ve webovém prohlížeči otevřete soubor **anomalydetection.html**.

**Scénář 1: Detekce v datech týdenních časových řad**
1. Do pole Period (Období) vložte hodnotu **7**. 
2. V poli Points (Body) nahraďte ukázková data za své datové body týdenní časové řady (ve formátu JSON) nebo použijte přímo ukázková data.
3. Klikněte na tlačítko Anomaly Detection (Detekce anomálií) a v pravém textovém poli Response (Odpověď) zkontrolujte výsledek.

**Scénář 2: Detekce v datech časových řad bez zadaného období**
1. Předpokládejme, že neznáte období časové řady. Pole Period (Období) tedy ponechte prázdné.
2. Použijte data stejné časové řady jako ve scénáři 1.
3. Klikněte na tlačítko Anomaly Detection (Detekce anomálií) a v pravém textovém poli Response (Odpověď) zkontrolujte pole Period (Období).

<a name="Step3"></a>
### <a name="read-the-result"></a>Čtení výsledku

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Kontrola a poznatky

Teď máte spuštěnou aplikaci. Pojďme se podívat, jak se ukázková aplikace integruje s technologií Cognitive Services. Tento krok vám usnadní další vývoj této aplikace nebo vlastní aplikace s využitím detekce anomálií Microsoftu.
Tato ukázková aplikace využívá koncový bod rozhraní RESTful API pro detekci anomálií.
Pokud se chcete podívat, jak se rozhraní RESTful API používá v ukázkové aplikaci, prohlédněte si fragment kódu ze souboru anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
