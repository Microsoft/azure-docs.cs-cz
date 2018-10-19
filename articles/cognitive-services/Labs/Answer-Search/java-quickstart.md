---
title: 'Rychlý start: Project Answer Search, Java'
titlesuffix: Azure Cognitive Services
description: Začněte používat Project Answer Search s využitím Javy.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 1e10ecf685aaf5b60b94a5e9e0d8e118f45e5168
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867021"
---
# <a name="quickstart-project-answer-search-query-in-java"></a>Rychlý start: Dotaz Project Answer Search v Javě
Tento článek představuje použití rozhraní API Bingu pro vyhledávání odpovědí, které je součástí služeb Microsoft Cognitive Services v Azure, pomocí Javy. Rozhraní API je webová služba REST kompatibilní s každým programovacím jazykem, který dokáže provádět požadavky HTTP a parsovat JSON.
 
Příklad kódu používá Java s minimálními externími závislostmi.  Můžete ho spustit taky v Linuxu nebo Mac OS X pomocí platformy Mono.

## <a name="prerequisites"></a>Požadavky

Získání přístupového klíče k bezplatné zkušební verzi služby [Cognitive Services Labs](https://aka.ms/answersearchsubscription)

## <a name="request"></a>Žádost 

Následující kód vytvoří požadavek `WebRequest`, nastaví hlavičku přístupového klíče a přidá řetězec dotazu „Gibraltar“.  Potom požadavek odešle a přiřadí odpověď k řetězci obsahujícímu text JSON.

````
    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/answerSearch/v7.0/search";

    // construct URL of search request (endpoint + query string)

    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="complete-code"></a>Celý kód

Rozhraní API Bingu pro vyhledávání odpovědí vrací výsledky z vyhledávače Bing.
1. Stáhněte nebo nainstalujte knihovnu gson.
2. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Javy.
3. Přidejte níže uvedený kód.
4. Hodnotu subscriptionKey nahraďte přístupovým klíčem platným pro vaše předplatné.
5. Spusťte program.

````
package knowledgeAPI;
import java.io.InputStream;
import java.net.*;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class KnowledgeSrch {

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.labs.cognitive.microsoft.com";
        static String path = "/answerSearch/v7.0/search";

        static String searchTerm = "Gibraltar";

        public static SearchResults SearchKnowledge (String searchQuery) throws Exception {

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

                SearchResults result = SearchKnowledge(searchTerm);

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

## <a name="next-steps"></a>Další kroky
- [Rychlý start pro jazyk C#](c-sharp-quickstart.md)
- [Rychlý start pro Javu](java-quickstart.md)
- [Rychlý start pro Node](node-quickstart.md)