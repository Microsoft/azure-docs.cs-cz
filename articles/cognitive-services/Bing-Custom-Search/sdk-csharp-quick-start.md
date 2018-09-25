---
title: Vlastní vyhledávání SDK jazyka C# quickstart | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: Nastavení vlastního vyhledávání SDK jazyka C# konzolové aplikace.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949923"
---
# <a name="c-sdk-quickstart"></a>Rychlý start C# SDK

Sady SDK vlastní vyhledávání Bingu poskytuje snadněji programovací model, než REST API pro vyhledávání Bingu vlastní. Tato část vás provede volání vaší první vlastní vyhledávání pomocí sady SDK jazyka C#.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání připravené k použití. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).  
  
- Klíč předplatného. Klíč předplatného můžete získat, když aktivujete vaše [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), nebo můžete použít klíč placené předplatné z řídicího panelu Azure (naleznete v tématu [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Nainstalovanou sadu Visual Studio 2017. Pokud ho nemáte, můžete stáhnout **bezplatné** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- [NuGet vlastního vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) nainstalovaný balíček. V Průzkumníku řešení v sadě Visual Studio, klikněte pravým tlačítkem na projekt a vyberte `Manage NuGet Packages` z nabídky. Nainstalujte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Instaluje se balíček NuGet vlastního vyhledávání, nainstaluje se také na následující sestavení:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Spuštění kódu

Chcete-li spustit tento příklad, postupujte podle těchto kroků:

1. Otevřít Visual Studio 2017.
  
2. Klikněte na tlačítko **souboru** nabídky, klikněte na tlačítko **nový**, **projektu**a pak **Visual konzolovou aplikaci C#** šablony.
  
3. Název řešení CustomSearchSolution a přejděte do složky, které chcete změnit na.
  
4. Klikněte na tlačítko OK, abyste vytvořili řešení.  
  
4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt (má stejný název jako řešení) a vyberte `Manage NuGet Packages`. Klikněte na tlačítko **Procházet** ve správci balíčků NuGet. Do vyhledávacího pole zadejte Microsoft.Azure.CognitiveServices.Search.CustomSearch a stiskněte klávesu enter. Vyberte balíček a klikněte na nainstalovat.  
  
4. Zkopírujte následující kód do souboru Program.cs. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** s klíč předplatného a konfigurací ID.  
  
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
  
5. Sestavení a spuštění (ladění) řešení. 




## <a name="breaking-it-down"></a>Jeho rozdělení

Po instalaci balíčku NuGet vlastního vyhledávání, budete muset přidat do pomocí direktiv pro něj.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

V dalším kroku vytvoření instance vlastního vyhledávání klienta, který použijete k podání žádostí o hledání. Nezapomeňte nahradit `YOUR-SUBSCRIPTION-KEY` vaším klíčem.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Teď můžete použít klienta odeslat žádost o vyhledávání. Nezapomeňte nahradit vaše `YOUR-CUSTOM-CONFIG-ID` s ID konfigurace vaší instance (ID v najdete [portál pro prohledávání vlastní](https://www.customsearch.ai/)). Tento příklad vyhledá Xbox. Aktualizace podle potřeby.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

`SearchAsync` Metoda vrátí hodnotu `WebData` objektu. Použití `WebData` k iteraci v rámci žádné `WebPages` , které nebyly nalezeny. Tento kód najde první výsledek webové stránky a vytiskne na webovou stránku `Name` a `URL`.

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


## <a name="next-steps"></a>Další postup

Prohlédněte si ukázky SDK. Každá ukázka obsahuje soubor ReadMe s podrobnostmi o předpokladů a instalaci a spuštění ukázky.

* Začínáme s [ukázky .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Viz také [knihovny .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) pro definice a závislosti.
* Začínáme s [ukázky Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Viz také [NodeJS knihovny](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) pro definice a závislosti.
* Začínáme s [ukázky v Javě](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Viz také [knihovny Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) pro definice a závislosti.
* Začínáme s [ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Viz také [knihoven Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) pro definice a závislosti.

