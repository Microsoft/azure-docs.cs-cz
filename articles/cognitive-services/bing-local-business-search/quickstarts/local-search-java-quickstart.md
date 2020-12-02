---
title: Rychlý Start – odeslání dotazu do rozhraní API pomocí jazyka Java – vyhledávání místních obchodních obchodů
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu můžete začít posílat požadavky v jazyce Java do rozhraní API Bingu pro vyhledávání v místním prostředí, což je služba pro rozpoznávání Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 8bd2c174456c35da872a0e995333503a72ce7c7a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493300"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Rychlý Start: odeslání dotazu do rozhraní API Bingu pro vyhledávání místních obchodních aplikací pomocí jazyka Java

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

V tomto rychlém startu se dozvíte, jak odesílat požadavky do rozhraní API Bingu pro vyhledávání v místním obchodu, což je služba pro rozpoznávání Azure. I když je tato jednoduchá aplikace napsaná v jazyce Java, rozhraní API je webová služba RESTful, která je kompatibilní s libovolným programovacím jazykem schopným vytvářet požadavky HTTP a analyzovat JSON.

Tato ukázková aplikace získá data místních odpovědí z rozhraní API pro vyhledávací dotaz.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" vytvořte prostředek vyhledávání Bingu vytvoření prostředku "  target="_blank"> Vyhledávání Bingu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.

## <a name="create-the-request"></a>Vytvoření žádosti 

Následující kód vytvoří `WebRequest` , nastaví hlavičku přístupového klíče a přidá řetězec dotazu pro *Hotel do Bellevue*.  Potom požadavek odešle a přiřadí odpověď k řetězci obsahujícímu text JSON.

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

Následující kód používá rozhraní API Bingu pro vyhledávání výsledků hledání z vyhledávacího stroje Bingu. Spusťte tento kód pomocí následujících kroků:
1. Stáhněte nebo nainstalujte knihovnu gson.
2. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Javy.
3. Přidejte níže uvedený kód.
4. Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
5. Spustíte program.

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
    // **_ Update or verify the following values. _*_
    // _*********************************************

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
- [Rychlé zprovoznění hledání v jazyce C# pro místní obchod](local-quickstart.md)
- [Rychlý Start Node.js místního hledání firmy](local-search-node-quickstart.md)
- [Rychlý Start pro místní vyhledávání v Pythonu](local-search-python-quickstart.md)