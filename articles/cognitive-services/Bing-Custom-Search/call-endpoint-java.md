---
title: 'Rychlý start: Volání koncového bodu pomocí Javy – vlastní vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání za použití Javy k volání koncového bodu pro vlastní vyhledávání Bingu.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: ba1eb9799bc95dede081c6f1a6d972896c126da6
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814107"
---
# <a name="quickstart-call-bing-custom-search-endpoint-java"></a>Rychlý start: Volání koncového bodu pro vlastní vyhledávání Bingu (Java)

Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání za použití Javy k volání koncového bodu pro vlastní vyhledávání Bingu. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instance vlastního vyhledávání připravená k použití. Přečtěte si téma [Vytvoření první instance vlastního vyhledávání Bingu](quick-start.md).
- Nainstalovaný jazyk [Java](https://www.java.com).
- Klíč předplatného. Klíč předplatného můžete získat aktivací [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) nebo můžete použít klíč placeného předplatného z řídicího panelu Azure (informace najdete v tématu [Účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    

## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete tuto ukázku spustit, postupujte takto:

1. Pomocí svého oblíbeného vývojového prostředí Javy vytvořte balíček.  
  
2. V tomto balíčku vytvořte soubor s názvem CustomSrchJava.java a zkopírujte do něj následující kód. Položky **YOUR-SUBSCRIPTION-KEY** a **YOUR-CUSTOM-CONFIG-ID** nahraďte klíčem předplatného a ID konfigurace.  
  
    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    
    import javax.net.ssl.HttpsURLConnection;
    
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    public class CustomSrchJava {       
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";  
    
        static String searchTerm = "Microsoft";  // Replace with search term specific to your search scenario.
    
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
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
            if (subscriptionKey.length() != 32) {
                System.out.println("Invalid Custom Search subscription key!");
                System.out.println("Please paste yours into the source code.");
                System.exit(1);
            }
    
            try {
                System.out.println("Searching your slice of the Web for: " + searchTerm);
    
                SearchResults result = SearchWeb(searchTerm);
    
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
  
4. Spusťte program.
    
## <a name="next-steps"></a>Další kroky
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)