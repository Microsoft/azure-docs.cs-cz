---
title: 'Rychlý start: Sada SDK pro vlastní vyhledávání, C#'
titleSuffix: Azure Cognitive Services
description: Nastavení konzolové aplikace sady SDK pro vizuální vyhledávání v jazyce C#.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6dc10bc2dedfe99573b5ad646461e66827c6df9e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320165"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Rychlý start: Použití sady SDK pro Vlastní vyhledávání Bingu s jazykem C#

Sada SDK pro Vlastní vyhledávání Bingu poskytuje jednodušší programovací model než rozhraní REST API pro vlastní vyhledávání Bingu. Tato část vás provede prvními voláními vlastního vyhledávání s využitím sady C# SDK.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instance vlastního vyhledávání připravená k použití. Přečtěte si téma [Vytvoření první instance Vlastního vyhledávání Bingu](quick-start.md).  
  
- Klíč předplatného. Klíč předplatného můžete získat aktivací [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) nebo můžete použít klíč placeného předplatného z řídicího panelu Azure (informace najdete v tématu [Účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Nainstalovaná sada Visual Studio 2017. Pokud ji ještě nemáte, můžete stáhnout **bezplatnou** verzi [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- Nainstalovaný balíček [NuGet pro vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). V Průzkumníku řešení v sadě Visual Studio klikněte pravým tlačítkem na projekt a v nabídce vyberte `Manage NuGet Packages`. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Při instalaci balíčku NuGet pro vlastní vyhledávání se nainstalují také následující sestavení:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Spuštění kódu

> [!TIP]
> Získejte z [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) nejnovější verzi kódu jako řešení sady Visual Studio.

Pokud chcete tuto ukázku spustit, postupujte takto:

1. Otevřete sadu Visual Studio 2017.
  
2. Klikněte na nabídku **Soubor**, pak na **Nový** > **Projekt** a pak na šablonu **Konzolová aplikace Visual C#**.
  
3. Pojmenujte řešení CustomSearchSolution a přejděte do složky, kam ho chcete umístit.
  
4. Kliknutím na OK vytvořte řešení.  
  
4. V Průzkumníku řešení klikněte na projekt (má stejný název jako řešení) pravým tlačítkem a vyberte `Manage NuGet Packages`. Ve Správci balíčků NuGet klikněte na **Procházet**. Do vyhledávacího pole zadejte Microsoft.Azure.CognitiveServices.Search.CustomSearch a stiskněte Enter. Vyberte balíček a klikněte na Nainstalovat.  
  
4. Do souboru Program.js zkopírujte následující kód. Položky **YOUR-SUBSCRIPTION-KEY** a **YOUR-CUSTOM-CONFIG-ID** nahraďte klíčem předplatného a ID konfigurace.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Sestavte a spusťte (ladění) řešení. 




## <a name="breaking-it-down"></a>Podrobné vysvětlení

Po instalaci balíčku NuGet pro vlastní vyhledávání je potřeba přidat pro něj direktivu using.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Pak vytvořte instanci klienta pro vlastní vyhledávání, kterou můžete použít k odesílání požadavků hledání. Nezapomeňte nahradit `YOUR-SUBSCRIPTION-KEY` vlastním klíčem.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Teď můžete pomocí klienta odeslat požadavek hledání. Nezapomeňte nahradit `YOUR-CUSTOM-CONFIG-ID` za ID konfigurace vaší instance (toto ID najdete na [portálu Vlastní vyhledávání](https://www.customsearch.ai/)). Tento příklad vyhledá „Xbox“. Podle potřeby ho můžete aktualizovat.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

Metoda `SearchAsync` vrátí objekt `WebData`. Pomocí objektu `WebData` můžete iterovat nalezenými webovými stránkami (`WebPages`). Tento kód vyhledá první výsledek hledání webových stránek a vypíše název (`Name`) a adresu URL (`URL`) webové stránky.

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>Další kroky

Prohlédněte si ukázky sady SDK. Každá ukázka obsahuje soubor ReadMe s podrobnostmi o požadavcích a instalaci a spuštění ukázky.

* Začínáme s [ukázkami .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Definice a závislosti najdete v [knihovnách .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch).
* Začínáme s [ukázkami Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Definice a závislosti najdete v [knihovnách Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch).
* Začínáme s [ukázkami Javy](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Definice a závislosti najdete v [knihovnách Javy](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch).
* Začínáme s [ukázkami Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Definice a závislosti najdete v [knihovnách Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch).

