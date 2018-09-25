---
title: Volání koncového bodu pomocí jazyka C# – vlastní vyhledávání Bingu – Microsoft Cognitive Services
description: Tento rychlý start ukazuje, jak si vyžádat výsledky hledání s použitím jazyka C# k volání koncového bodu pro vlastní vyhledávání Bingu z vaší instance vlastního hledání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: ed00b75fa956d0197d3672d84b097f99ec3c35ec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956372"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Volání koncového bodu pro vlastní vyhledávání Bingu (C#)

Tento rychlý start ukazuje, jak si vyžádat výsledky hledání z vaší instance vlastního hledání pomocí jazyka C# k volání koncového bodu pro vlastní vyhledávání Bingu. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání připravené k použití. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).
- [.Net Core](https://www.microsoft.com/net/download/core) nainstalované.
- Klíč předplatného. Klíč předplatného můžete získat, když aktivujete vaše [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), nebo můžete použít klíč placené předplatné z řídicího panelu Azure (naleznete v tématu [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Spuštění kódu

Chcete-li spustit tento příklad, postupujte podle těchto kroků:

1. Vytvořte složku pro váš kód.  
  
2. Z příkazového řádku nebo v terminálu přejděte do složky, kterou jste právě vytvořili.  
  
3. Spusťte následující příkazy:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Zkopírujte následující kód do souboru Program.cs. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** s klíč předplatného a konfigurací ID.

    ```csharp
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
6. Vytvoření aplikace pomocí následujícího příkazu. Poznamenejte si cestu knihovny DLL odkazuje výstup příkazu.

    <pre>
    dotnet build 
    </pre>
    
7. Spusťte aplikaci pomocí následujícího příkazu nahraďte **CESTU k výstupní** s cestou knihovnu DLL odkazuje v kroku 6.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
