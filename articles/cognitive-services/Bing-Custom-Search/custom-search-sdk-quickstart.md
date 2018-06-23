---
title: Vlastní vyhledávání SDK jazyka C# rychlý start | Microsoft Docs
titleSuffix: Cognitive Services
description: Instalační program konzolovou aplikaci vlastní vyhledávání SDK C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342378"
---
# <a name="custom-search-sdk-c-quickstart"></a>Vlastní vyhledávání SDK jazyka C# rychlý start

SDK služby Bing vlastní Search obsahuje funkci rozhraní REST API pro vyhledávání entity a analýza výsledků.

## <a name="application-dependencies"></a>Závislosti aplikací

Pokud chcete nastavit konzolovou aplikaci pomocí sady SDK Bing vlastní vyhledávání, vyhledejte `Manage NuGet Packages` možnost v Průzkumníku řešení v sadě Visual Studio. Přidat `Microsoft.Azure.CognitiveServices.Search.CustomSearch` balíčku.

Instalace [NuGet vlastní vyhledávání](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) balíček nainstaluje taky závislosti, včetně následujících sestavení:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Klienta vyhledávání entity

K vytvoření instance CustomSearchAPI klienta, přidání direktivy using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Vytváření instancí klienta vlastní vyhledávání: Nahraďte `YOUR-CUSTOM-SEARCH-KEY` a `YOUR-CUSTOM-CONFIG-ID` s vaším přístupovým klíčem a konfiguraci koncového bodu rozhraní API v přiřazen ID [instance Moje](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Klient použijte při hledání se text dotazu:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analýza výsledků

`SearchAsync` Metoda vrátí `WebData` objekt, který obsahuje `WebPages` li nějaké nalezeny pro dotaz. Tento kód najde první výsledek a získá jeho `Name` a `URL`.
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
## <a name="complete-console-application"></a>Dokončení konzolové aplikace

Následující kód prohledá na dotaz "Xbox" a vytiskne `Name` a `URL` pro první webovou výsledek.
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

[Kognitivní services .NET SDK ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
