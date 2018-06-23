---
title: Koncový bod volání pomocí jazyka C# - Bing vlastní vyhledávání - kognitivní službám Microsoftu
description: Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí jazyka C# k vyvolání koncový bod služby Bing vlastní vyhledávání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: be4cc79d16b9a22124f16878b11ca04a916f98ae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342856"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Koncový bod služby Bing vlastní vyhledávání volání (C#)

Tento rychlý start ukazuje, jak požádat výsledky hledání instance vlastní vyhledávání pomocí jazyka C# k vyvolání koncový bod služby Bing vlastní vyhledávání. 

## <a name="prerequisites"></a>Požadavky

-  Instance vlastní vyhledávání připravené k použití. V tématu [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).
-  [.NET core](https://www.microsoft.com/net/download/core) nainstalována.
- A [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API pro Bing vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) stačí pro tento rychlý start. Je nutné přístupový klíč zadaný při aktivaci bezplatné zkušební verze, nebo může použít klíč placené předplatné z řídicího panelu Azure.  

  >[!NOTE]  
  >Existující Bing vlastní vyhledávání zákazníci, kteří mají klíč preview zřízený, nebo před 15 říjen 2017 bude moci používat své klíče až do 30. listopadu 2017, nebo dokud budou mít vyčerpá maximální počet dotazů, které jsou povoleny. Pak se musí migrovat na verzi všeobecně dostupná v Azure. 
 
## <a name="run-the-code"></a>Spuštění kódu

Pro spuštění tohoto příkladu, postupujte takto:

1. Vytvořte složku pro váš kód.
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Spusťte následující příkazy:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Zkopírujte následující kód do souboru Program.cs.
5. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** s vaším ID klíče a konfigurace.

    ``` CSharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Sestavení aplikace pomocí následujícího příkazu. Poznámka: cesta dll odkazuje výstupu příkazu.
    <pre>
    dotnet build 
    </pre>
7. Spusťte aplikaci pomocí následujícího příkazu nahraďte **cesta k výstupní** s cestou odkazuje krok sestavení.
    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
