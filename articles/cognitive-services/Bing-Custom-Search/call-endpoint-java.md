---
title: 'Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí Javy | Dokumenty společnosti Microsoft'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu v Javě.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 02c86e5a4c1a04b98ebba73653980e8e5e00f645
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238878"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Úvodní příručka: Volání vlastního koncového bodu služby Bing pomocí Javy

Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v Jazyce Java, Rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Požadavky

- Instance vlastního vyhledávání Bingu. Další informace najdete [v tématu Úvodní příručka: Vytvoření první instance vlastního vyhledávání Bingu.](quick-start.md)

- Nejnovější [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- [Knihovna Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Java a naimportujte následující knihovny.

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
    ```

2. Vytvořte třídu s názvem `CustomSrchJava`a vytvořte proměnné pro klíč předplatného, vlastní koncový bod hledání a ID vlastní konfigurace instance hledání. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Vytvořte jinou třídu s názvem `SearchResults` obsahující odpověď z instance vlastního vyhledávání Bingu.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Vytvořte funkci `prettify()` s názvem pro formátování odpovědi JSON z rozhraní API pro vlastní vyhledávání Bingu.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí požadavku na vyhledávání 

1. Vytvořte funkci `SearchWeb()` s názvem, která `SearchResults` odešle požadavek a vrátí objekt. Adresu URL požadavku vytvořte kombinací id vlastní konfigurace, dotazu a informací o koncovém bodu. Přidejte klíč předplatného `Ocp-Apim-Subscription-Key` do záhlaví.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Vytvořte datový proud a uložte `SearchResults` odpověď JSON do objektu.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. V hlavní metodě vaší `SearchWeb()` aplikace volejte s hledaným výrazem, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Spusťte program.
    
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace pro vlastní vyhledávání](./tutorials/custom-search-web-page.md)
