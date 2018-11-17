---
title: Rychlý start – zaslání dotazu místní firmy API pro vyhledávání Bingu pomocí Javy | Dokumentace Microsoftu
titleSuffix: Azure Cognitive Services
description: Pokud chcete začít používat API Bingu pro místní obchodní vyhledávání v Javě pomocí tohoto článku.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-local-business
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 5c31bcfaecb956bf0168a1485f3ee0fa985b9ceb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852516"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Rychlý start: Odešle dotaz do API Bingu pro místní obchodní vyhledávání pomocí Javy

V tomto rychlém startu můžete zahájit odesílání požadavků na místní firmy hledání rozhraní API Bingu, což je kognitivní služby Azure. Zatímco tato jednoduchá aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s libovolný programovací jazyk umožňuje zasílat požadavky HTTP a parsování formátu JSON.

Tato ukázková aplikace získá data místní odpověď z rozhraní API pro vyhledávací dotaz `hotel in Bellevue`.

## <a name="prerequisites"></a>Požadavky

* [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

Musíte mít [účet API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s přístupem k rozhraním API pro vyhledávání Bingu. Pro účely tohoto rychlého startu stačí [bezplatná zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Budete potřebovat přístupový klíč k dispozici při aktivaci vaší bezplatné zkušební verze.

Tato ukázková aplikace získá data místní odpovědi z dotazu pro *hotelu ve Bellevue*.

## <a name="create-the-request"></a>Vytvořit požadavek 

Následující kód vytvoří `WebRequest`, nastaví hlavičku klíčů přístup a přidá řetězec dotazu pro "hotel Bellevue".  Potom požadavek odešle a přiřadí odpověď k řetězci obsahujícímu text JSON.

````
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "appid=AEA845921DC03F506DC317A90EDDBF33074523F7&market=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="run-the-complete-application"></a>Spusťte kompletní aplikace

Místní firmy API pro vyhledávání Bingu vrátí výsledky z vyhledávacího webu Bingu.
1. Stáhněte nebo nainstalujte knihovnu gson.
2. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java.
3. Přidejte níže uvedený kód.
4. Hodnotu subscriptionKey nahraďte přístupovým klíčem platným pro vaše předplatné.
5. Spusťte program.

````
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
        static String path = "/v7.0/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + 
                         "&appid=" + subscriptionKey + "&market=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            //connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

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

````

## <a name="next-steps"></a>Další postup
- [Rychlý start místní firmy vyhledávání](local-quickstart.md)
- [Rychlý start místní uzel hledat firmy](local-search-node-quickstart.md)
- [Rychlý start místní firmy hledání Pythonu](local-search-python-quickstart.md)