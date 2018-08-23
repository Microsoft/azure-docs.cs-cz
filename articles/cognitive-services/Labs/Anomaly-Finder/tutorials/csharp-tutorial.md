---
title: Anomálií detekce jazyka C# aplikace – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Prozkoumání aplikace C#, která používá rozhraní API pro detekci anomálií v Microsoft Cognitive Services. Odeslat původní datové body k rozhraní API a získat očekávanou hodnotu a anomálií body.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7d4f6a12c94620f447b5d6df4d7715d32eac2d98
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "41987956"
---
# <a name="anomaly-detection-c-application"></a>Aplikace v C# detekce anomálií

Prozkoumejte základní aplikace Windows, který používá rozhraní API pro detekci anomálií k detekci anomálií ze vstupu. V příkladu s klíči předplatného odešle data časových řad pro rozhraní API pro detekci anomálií a získá všechny body anomálií a očekávaná hodnota pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V příkladu jsme vyvinuli pro rozhraní .NET Framework pomocí [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlaste se k detekci anomálií odběru a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získat a pomocí příkladu

Můžete naklonovat detekce anomálií ukázková aplikace do počítače z [Githubu](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Instalace v příkladu

GitHub Desktop otevřete Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Sestavení příkladu

Stiskněte kombinaci kláves Ctrl + Shift + B, nebo na pásu karet klikněte na sestavení a potom vyberte sestavit řešení.

<a name="Step3"></a>
### <a name="run-the-example"></a>Spustit příklad

1. Po dokončení sestavení stiskněte **F5** nebo klikněte na tlačítko **Start** na pásu karet pro spuštění příkladu.
2. Vyhledejte okno pro detekci anomálií uživatelského rozhraní pomocí pole pro úpravy textu "{your_subscription_key}" pro čtení.
3. Nahraďte vlastními daty request.json soubor, který obsahuje ukázková data, a potom klikněte na tlačítko "Odeslat". Microsoft přijímá data nahrát a použít k detekci všechny body anomálií mezi potom. Data, která načtete, nebudou trvale serveru společnosti Microsoft. Ke zjištění anomálií bod znovu, třeba nahrajete data znovu.
4. Pokud je v pořádku data, najdete v poli "Odpověď" výsledek detekce anomálií. Pokud dojde k jakékoli chybě, zobrazí se informace o chybě v poli odpovědi.

<a name="Review"></a>
### <a name="read-the-result"></a>Přečtěte si výsledek

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Zkontrolujte a další

Teď, když máte spuštěnou aplikaci, Pojďme se podívat na tom, jak ukázková aplikace integruje technologie služeb Cognitive Services. Tento krok se usnadňují pokračujte v sestavování do této aplikace nebo vyvíjet vlastní aplikace pomocí Microsoft pro detekci anomálií.

Tato ukázková aplikace využívá rozhraní Restful API detekce anomálií koncový bod.

Kontrola, jak se používá rozhraní Restful API v ukázkové aplikaci, Podívejme se na fragment kódu z **AnomalyDetectionClient.cs**. Tento soubor obsahuje komentáře ke kódu označující "Klíč UKÁZKOVÝ kód SPUSTÍ TADY" a "Klíč UKÁZKOVÝ kód ukončení sem" abychom vám pomohli najít kód, který fragmenty uveden níže.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Požadavek**
Následující fragment kódu ukazuje, jak pomocí HttpClient odešlete vaše předplatné klíč a datové body do koncového bodu rozhraní API pro detekci anomálií.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
