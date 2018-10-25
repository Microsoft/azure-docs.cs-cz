---
title: 'Rychlý start: Volání koncového bodu pomocí jazyka C# – Vlastní vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání prostřednictvím volání koncového bodu Vlastního vyhledávání Bingu pomocí jazyka C#.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: c0e315f9b96133d68bf1f9c02da1436b877baf40
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468411"
---
# <a name="quickstart-call-bing-custom-search-endpoint-c"></a>Rychlý start: Volání koncového bodu Vlastního vyhledávání Bingu (C#)

Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání prostřednictvím volání koncového bodu Vlastního vyhledávání Bingu pomocí jazyka C#. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instance vlastního vyhledávání připravená k použití. Přečtěte si téma [Vytvoření první instance vlastního vyhledávání Bingu](quick-start.md).
- Nainstalované rozhraní [.Net Core](https://www.microsoft.com/net/download/core).
- Klíč předplatného. Klíč předplatného můžete získat aktivací [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) nebo můžete použít klíč placeného předplatného z řídicího panelu Azure (informace najdete v tématu [Účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete tuto ukázku spustit, postupujte takto:

1. Vytvořte pro svůj kód složku.  
  
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.  
  
3. Spusťte následující příkazy:
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
    ```
  
4. Do souboru Program.cs zkopírujte následující kód. Položky **YOUR-SUBSCRIPTION-KEY** a **YOUR-CUSTOM-CONFIG-ID** nahraďte klíčem předplatného a ID konfigurace.

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
6. Sestavte aplikaci pomocí následujícího příkazu. Poznamenejte si cestu ke knihovně DLL, na kterou odkazuje výstup příkazu.

    <pre>
    dotnet build 
    </pre>
    
7. Spusťte aplikaci pomocí následujícího příkazu, ve kterém položku **PATH TO OUTPUT** nahradíte cestou ke knihovně DLL zmíněnou v kroku 6.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Další kroky
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
