---
title: Detekce anomálií Javascript aplikace – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte službu jazyka Javascript webovou aplikaci, která používá rozhraní API pro detekci anomálií v Microsoft Cognitive Services. Odeslat původní datové body k rozhraní API a získat očekávanou hodnotu a anomálií body.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 5bb123648a683454597b0561f9f82dffb70eab04
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248359"
---
# <a name="anomaly-detection-javascript-application"></a>Aplikace Javascript detekce anomálií

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Prozkoumejte službu webové aplikace, která používá rozhraní REST API pro detekci anomálií pro detekci anomálií. V příkladu s klíči předplatného odešle data časových řad pro rozhraní API pro detekci anomálií a získá všechny body anomálií a očekávané hodnotě. pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V tomto kurzu byla vyvinuta pomocí jednoduchého textového editoru.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlaste se k detekci anomálií odběru a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získat a pomocí příkladu

Tento kurz obsahuje dva scénáře pro detekci anomálií data řady čas. Pusťme se do práce.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Stáhněte si kurz projekt

Klonování [Cognitive Services JavaScript anomálií detekce kurzu](https://github.com/MicrosoftAnomalyDetection/javascript-sample), nebo ji stáhněte soubor .zip a rozbalte ho prázdný adresář.

<a name="Step2"></a>
### <a name="run-the-example"></a>Spustit příklad

Existují dva scénáře, můžete zkusit v příkladu.
1. Vložit vaše **klíč předplatného** do pole klíč předplatného na detekci funkce na anomalydetection.html.
2. Vložit koncový bod rozhraní API pro detekci anomálií a ověřte, že používáte správnou oblast v oblasti předplatného.
3. Otevřít **anomalydetection.html** soubor ve webovém prohlížeči.

**Data týdenní časových řad pro scénář 1 rozpoznat**
1. V období pole zadat období **7**. 
2. Nahraďte váš týdenní čas datových bodů řady (Json) v poli body ukázková data, nebo přímo použít vzorová data.
3. Klikněte na tlačítko pro detekci anomálií a ověření výsledku v textovém poli správné odpovědi.

**Scénář 2 rozpoznat dat časových řad bez období**
1. Nechte prázdný v časovém období nezaznamenaly, předpokládají neznáte období časové řady.
2. Scénář 1 pomocí stejného data časových řad.
3. Klikněte na tlačítko pro detekci anomálií a ověřte pole období v textovém poli správné odpovědi.

<a name="Step3"></a>
### <a name="read-the-result"></a>Přečtěte si výsledek

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Zkontrolujte a další

Teď získáte běžící aplikaci. Pojďme se podívat na tom, jak ukázková aplikace integruje technologie služeb Cognitive Services. Tento krok usnadní pokračujte v sestavování v této aplikaci, nebo vyvíjet vlastní aplikace pomocí Microsoft pro detekci anomálií.
Tato ukázková aplikace využívá rozhraní Restful API detekce anomálií koncový bod.
Kontrola, jak se používá rozhraní Restful API v ukázkové aplikaci, Podívejme se na fragment kódu z anomalydetection.html.
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
