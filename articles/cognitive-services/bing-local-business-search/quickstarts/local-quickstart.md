---
title: Rychlý start – odeslat dotaz do API Bingu pro místní obchodní vyhledávání v C# | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto článku můžete začít používat API Bingu pro místní obchodní vyhledávání v C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: c05dc646cae3c2a7c6f70ed8df711a0efa77e05e
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748635"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>Rychlý start: Odeslání dotazu do API Bingu pro místní obchodní vyhledávání vC#

V tomto rychlém startu můžete zahájit odesílání požadavků na místní firmy hledání rozhraní API Bingu, což je kognitivní služby Azure. Zatímco tato jednoduchá aplikace je napsána v C#, rozhraní API je RESTful webová služba, která je kompatibilní s libovolný programovací jazyk umožňuje zasílat požadavky HTTP a parsování formátu JSON.

Tato ukázková aplikace získá data místní odpověď z rozhraní API pro vyhledávací dotaz `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Pokud používáte Linux nebo MacOS, je možné tuto aplikaci spustit pomocí [Mono](http://www.mono-project.com/).

Musíte mít [účet API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Budete potřebovat přístupový klíč k dispozici při aktivaci vaší bezplatné zkušební verze.

## <a name="create-the-request"></a>Vytvořit požadavek 

Následující kód vytvoří `WebRequest`, nastaví hlavičku klíčů přístup a přidá řetězec dotazu pro "restaurace v Bellevue".  Potom požadavek odešle a přiřadí odpověď k řetězci obsahujícímu text JSON.

````
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://www.bingapis.com/api/v7/localbusinesses/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "appid=" + accessKey;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
````

## <a name="run-the-complete-application"></a>Spusťte kompletní aplikace

Místní firmy hledání rozhraní API Bingu vrátí výsledky lokalizované vyhledávání z Bingu vyhledávací web.
1. Vytvořte nové konzolové řešení v sadě Visual Studio (Community Edition je v pořádku).
2. Nahraďte soubor Program.cs níže uvedeným kódem.
3. Nahraďte hodnotu accessKey přístupový klíč platný pro vaše předplatné.
4. Spusťte program.

````
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://www.bingapis.com/api/v7/localbusinesses/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + 
                                "&appid=" + accessKey + "&traffictype=Internal_monitor&market=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            //request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

````

## <a name="next-steps"></a>Další postup
- [Rychlý start místní firmy hledání Java](local-search-java-quickstart.md)
- [Rychlý start místní uzel hledat firmy](local-search-node-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](local-search-python-quickstart.md)
