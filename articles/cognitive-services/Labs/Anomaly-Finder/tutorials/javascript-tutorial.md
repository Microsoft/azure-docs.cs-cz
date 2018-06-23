---
title: Detekce anomálií aplikace Javascript – kognitivní služby Microsoft | Microsoft Docs
description: Prozkoumejte Javascript webovou aplikaci, která používá rozhraní API detekce anomálií v kognitivní služby společnosti Microsoft. Odesílat původní datové body k rozhraní API a očekávaná hodnota a body anomálií.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343016"
---
# <a name="anomaly-detection-javascript-application"></a>Aplikace Javascript detekce anomálií

Prozkoumejte webovou aplikaci, která používá rozhraní API REST detekce anomálií ke zjištění anomálií. V příkladu odešle data časové řady na rozhraní API detekce anomálií s svůj klíč předplatného a pak získá všechny body anomálií a očekávaná hodnota pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V tomto kurzu byla vyvinuta pomocí jednoduchého textového editoru.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlášení k odběru detekce anomálií a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získání a použijte tento příklad

V tomto kurzu poskytuje dva scénáře pro zjišťování anomálií data časové řady. Můžeme začít.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Stáhněte si kurz projekt

Klon [kognitivní kurzu detekce anomálií služby JavaScript](https://github.com/MicrosoftAnomalyDetection/javascript-sample), nebo ji stáhněte soubor .zip a rozbalte ho prázdný adresář.

<a name="Step2"></a>
### <a name="run-the-example"></a>Spuštění ukázkového

Existují dva scénáře můžete zkusit v příkladu.
1. Uveďte vaše **klíč předplatného** do pole klíč předplatného, na zjištění funkce na anomalydetection.html.
2. Vložit koncový bod rozhraní API detekce anomálií a ověřte, že používáte správné oblast v oblasti předplatného.
3. Otevřete **anomalydetection.html** soubor ve webovém prohlížeči.

**Scénář 1 zjistit týdenní časové řady dat**
1. V poli období, zadejte období **7**. 
2. Nahraďte váš týdenní čas datové body řady (Json) v poli bodů ukázková data, nebo přímo pomocí ukázková data.
3. Klikněte na tlačítko detekce anomálií a ověřte výsledek do textového pole pravé odpovědi.

**Scénář 2 zjistit časové údaje řady bez období**
1. Ponechejte, kterým zařazen prázdný v časovém období, předpokládá, že nevíte období časové řady.
2. Pomocí stejné data řady čas jako scénář 1.
3. Klikněte na tlačítko detekce anomálií a ověřte pole období do textového pole pravé odpovědi.

<a name="Step3"></a>
### <a name="read-the-result"></a>Přečtěte si výsledek

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Zkontrolujte a další informace

Nyní můžete získat běžící aplikaci. Pojďme si, jak se aplikace příklad integruje s technologií kognitivní služby. Tento krok bude usnadňují pokračovat ve vytváření na tuto aplikaci nebo vytvořte vlastní aplikaci pomocí Microsoft detekce anomálií.
Tento příklad aplikace využívá rozhraní Restful API detekce anomálií koncový bod.
Kontrola, jak získá ukázková aplikace používá rozhraní Restful API, podíváme se na fragment kódu z anomalydetection.html.
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
