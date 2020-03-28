---
title: Úvodní příručka – Odeslání dotazu do rozhraní API pomocí javy – vyhledávání místních společností Bing
titleSuffix: Azure Cognitive Services
description: Tento rychlý start můžete začít odesílat požadavky do rozhraní API pro vyhledávání místních společností Bingu, což je služba Azure Cognitive Service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 8ff70bea8d0e4810b6d5a0d35853077ed0a630cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665181"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Úvodní příručka: Odeslání dotazu do rozhraní API pro vyhledávání místních společností Bingu pomocí Javy

Tento rychlý start můžete začít odesílat požadavky do rozhraní API pro vyhledávání místních společností Bingu, což je služba Azure Cognitive Service. Zatímco tato jednoduchá aplikace je napsána v Jazyce Java, API je RESTful webová služba kompatibilní s libovolným programovacím jazykem schopným provádět požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místní odpovědi `hotel in Bellevue`z rozhraní API pro vyhledávací dotaz .

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

Musíte mít [účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraními API pro vyhledávání Bing. Pro účely tohoto rychlého startu vám bude stačit [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Při aktivaci bezplatné zkušební verze budete potřebovat přístupový klíč.  Viz také [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Tento příklad aplikace získá místní data odpovědi z dotazu na *hotel v Bellevue*.

## <a name="create-the-request"></a>Vytvořit požadavek 

Následující kód vytvoří `WebRequest`, nastaví hlavičku přístupového klíče a přidá řetězec dotazu pro "hotel v Bellevue".  Potom požadavek odešle a přiřadí odpověď k řetězci obsahujícímu text JSON.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>Spuštění úplné aplikace

Rozhraní API pro vyhledávání místních společností Bingvrací výsledky z vyhledávače Bing.
1. Stáhněte nebo nainstalujte knihovnu gson.
2. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Javy.
3. Přidejte níže uvedený kód.
4. Hodnotu subscriptionKey nahraďte přístupovým klíčem platným pro vaše předplatné.
5. Spusťte program.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>Další kroky
- [Rychlý start hledání místní firmy](local-quickstart.md)
- [Rychlý start uzlu místního obchodního hledání](local-search-node-quickstart.md)
- [Rychlý start pythonu pro místní obchodní vyhledávání](local-search-python-quickstart.md)
