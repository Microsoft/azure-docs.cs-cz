---
title: Vlastní vyhledávání SDK jazyka C# quickstart | Dokumentace Microsoftu
titleSuffix: Cognitive Services
description: Nastavení vlastního vyhledávání SDK jazyka C# konzolové aplikace.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 6b41dfbde0c2af776ee2c35220f731e40de334a0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367844"
---
# <a name="custom-search-sdk-c-quickstart"></a>Rychlé zprovoznění sady SDK pro vlastní vyhledávání v C#

Sady SDK vlastní vyhledávání Bingu obsahuje funkce rozhraní REST API pro vyhledávání entit a parsování výsledků.

Zdrojový kód pro tuto ukázku je k dispozici z [Githubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).
## <a name="application-dependencies"></a>Závislosti aplikace

Nastavit aplikaci konzoly pomocí sady SDK vlastní vyhledávání Bingu, přejděte na `Manage NuGet Packages` možnost z Průzkumníku řešení v sadě Visual Studio. Přidejte balíček `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

Instalace [NuGet vlastního vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) balíček nainstaluje také závislosti, včetně následující sestavení:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Klient hledat entity

Pokud chcete vytvořit instanci klienta CustomSearchAPI, přidání direktiv using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Vytvoření instance vlastního vyhledávání klienta: nahradit `YOUR-CUSTOM-SEARCH-KEY` a `YOUR-CUSTOM-CONFIG-ID` se přístupový klíč a ID přiřazené při konfiguraci koncového bodu rozhraní API [Moje instance](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Pomocí klienta na hledání text dotazu:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analyzovat výsledky

`SearchAsync` Metoda vrátí hodnotu `WebData` objekt, který obsahuje `WebPages` li nějaké nalezeny pro dotaz. Tento kód najde první výsledek a získá jeho `Name` a `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

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
## <a name="complete-console-application"></a>Kompletní konzolová aplikace

Následující kód vyhledá na dotaz "Xbox" a vytiskne `Name` a `URL` pro první výsledek web.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Další postup

[Ukázky kognitivních služeb sady .NET SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
