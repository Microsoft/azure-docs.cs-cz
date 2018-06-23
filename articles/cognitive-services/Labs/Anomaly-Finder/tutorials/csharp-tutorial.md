---
title: Aplikace anomálií detekce C# – kognitivní služby Microsoft | Microsoft Docs
description: Prozkoumejte C# aplikaci, která používá rozhraní API detekce anomálií v kognitivní služby společnosti Microsoft. Odesílat původní datové body k rozhraní API a očekávaná hodnota a body anomálií.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343024"
---
# <a name="anomaly-detection-c-application"></a>Aplikace v C# detekce anomálií

Prozkoumejte základní aplikace Windows, která používá rozhraní API detekce anomálií ke zjištění anomálií ze vstupu. V příkladu odešle data časové řady na rozhraní API detekce anomálií s svůj klíč předplatného a pak získá všechny body anomálií a očekávaná hodnota pro každý datový bod z rozhraní API.

## <a name="prerequisites"></a>Požadavky

### <a name="platform-requirements"></a>Požadavky na platformu

V příkladu byla vyvinuta pro rozhraní .NET Framework pomocí [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Přihlášení k odběru detekce anomálií a získat klíč předplatného 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Získání a použijte tento příklad

Může klonovat detekce anomálií ukázková aplikace do počítače z [Githubu](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Nainstalujte v příkladu

V Githubu ploše otevřete Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>V příkladu sestavení

Stiskněte kombinaci kláves Ctrl + Shift + B, nebo klikněte na pásu karet na sestavení a potom vyberte sestavit řešení.

<a name="Step3"></a>
### <a name="run-the-example"></a>Spuštění ukázkového

1. Po dokončení sestavení stiskněte **F5** nebo klikněte na tlačítko **spustit** na pásu karet pro spuštění příkladu.
2. Vyhledejte okno detekce anomálií uživatelské rozhraní s pole pro úpravy textu čtení "{your_subscription_key}".
3. Nahraďte vlastními daty request.json soubor, který obsahuje ukázková data, a pak klikněte na tlačítko "Odeslat". Microsoft přijímá data, odesílání a použít je k zjištění všech bodů anomálií mezi potom. Data, která jste aktualizovali nebude uchovávané v serveru společnosti Microsoft. Ke zjištění anomálií bod znovu, budete potřebovat nahrát data znovu.
4. Pokud data je vhodný, najdete v poli "Odpověď" výsledek detekce anomálií. Pokud dojde k chybě, zobrazí se informace o chybě v poli odpovědi.

<a name="Review"></a>
### <a name="read-the-result"></a>Přečtěte si výsledek

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Zkontrolujte a další informace

Teď, když máte spuštěné aplikace, umožňuje zkontrolujte, jak se aplikace příklad integruje s technologií kognitivní služby. Tento krok bude usnadňují pokračovat sestavení do této aplikace nebo vytvořte vlastní aplikaci pomocí Microsoft detekce anomálií.

Tento příklad aplikace využívá rozhraní Restful API detekce anomálií koncový bod.

Kontrola, jak získá ukázková aplikace používá rozhraní Restful API, podíváme se na fragment kódu z **AnomalyDetectionClient.cs**. Tento soubor obsahuje komentáře kódu označující "Klíč UKÁZKOVÝ kód SPUSTÍ TADY" a "Klíč ukázka kódu KONČÍ zde" abychom vám pomohli najít kód, který fragmenty uvedeno níže.

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
**Request(...)**  Níže uvedeného kódu fragment kódu ukazuje způsob použití HttlClient odeslat vaše předplatné klíč a datové body na koncový bod rozhraní API detekce anomálií.

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
