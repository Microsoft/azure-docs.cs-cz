---
title: 'Kurz: Detekce anomáliíC#'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte aplikaci jazyka C#, která používá rozhraní API pro detekci anomálií. Odešlete původní datové body do rozhraní API a získejte očekávanou hodnotu a body anomálie.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 64183ea09203ea965bb889fe4741fdaf4dbd1e02
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228011"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Kurz: Detekce anomálií pomocí služby C# aplikace

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Prozkoumejte základní aplikaci Windows, která používá rozhraní API pro detekci anomálií k detekci anomálií ze vstupu. V příkladu budou do rozhraní API pro detekci anomálií odeslána data časové řady s vaším klíčem předplatného. Poté budou z rozhraní API získány všechny body anomálií a očekávané hodnoty pro každý datový bod.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky platformy

Tento příklad jsme vyvinuli pro .NET Framework pomocí sady [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Předplaťte si službu detekce anomálií a získejte klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získání a použití příkladu

Můžete naklonovat detekce anomálií ukázková aplikace do počítače z [Githubu](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Instalace příkladu

Na ploše GitHubu otevřete Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Sestavení příkladu

Stiskněte Ctrl+Shift+B nebo klikněte na Sestavení na pásu karet a pak vyberte Sestavit řešení.

<a name="Step3"></a>
### <a name="run-the-example"></a>Spuštění příkladu

1. Po dokončení sestavení příklad spusťte stisknutím klávesy **F5** nebo kliknutím na **Spustit** na pásu karet.
2. Vyhledejte okno pro detekci anomálií uživatelského rozhraní pomocí pole pro úpravy textu zobrazujícího „{your_subscription_key}“.
3. Nahraďte soubor request.json, obsahující ukázková data, vlastními daty a potom klikněte na tlačítko „Odeslat“. Microsoft přijme data, která nahrajete, a rozpozná mezi nimi všechny body anomálií. Data, která načtete, na serveru společnosti Microsoft nezůstanou. Ke zjištění bodu anomálií znovu je třeba data znovu nahrát.
4. Pokud jsou data v pořádku, najdete v poli „Odpověď" výsledek detekce anomálií. Pokud dojde k jakékoli chybě, zobrazí se informace o chybě v poli odpovědi.

<a name="Review"></a>
### <a name="read-the-result"></a>Čtení výsledku

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Kontrola a poznatky

Když teď máte spuštěnou aplikaci, můžeme se podívat, jak se tato ukázková aplikace integruje se službami Microsoft Cognitive Services. Tento krok vám usnadní další vývoj této aplikace nebo vlastní aplikace s využitím detekce anomálií Microsoftu.

Tato ukázková aplikace využívá koncový bod rozhraní RESTful API pro detekci anomálií.

Pokud se chcete podívat, jak se rozhraní RESTful API používá v ukázkové aplikaci, prohlédněte si fragment kódu ze souboru **AnomalyDetectionClient.cs**. Soubor obsahuje komentáře ke kódu „KEY SAMPLE CODE STARTS HERE“ (Začátek klíčového vzorového kódu) a „KEY SAMPLE CODE ENDS HERE“ (Konec klíčového vzorového kódu), které vám pomůžou vyhledat níže uvedené fragmenty kódu.

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
Následující fragment kódu ukazuje, jak můžete pomocí HTTPClient odeslat svůj klíč předplatného a datové body do koncového bodu rozhraní API pro detekci anomálií.

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
