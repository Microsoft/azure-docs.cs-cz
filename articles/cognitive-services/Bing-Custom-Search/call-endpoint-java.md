---
title: 'Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Javy | Dokumentace Microsoftu'
titlesuffix: Azure Cognitive Services
description: V tomto rychlém startu pomocí zahájíte si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu v jazyce Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: ece4a8a7b0d311484f632b03ba12c71ce19190a2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868629"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Rychlý start: Volání vlastního vyhledávání Bingu koncový bod pomocí Javy

V tomto rychlém startu můžete začít si vyžádat výsledky hledání od vaší instance vlastního vyhledávání Bingu. Zatímco tato aplikace je napsána v jazyce Java, rozhraní API pro vlastní vyhledávání Bingu je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba. Zdrojový kód k této ukázce je dostupný na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Požadavky

- Instanci vlastního vyhledávání Bingu. Zobrazit [rychlý start: Vytvoření první instanci vlastního vyhledávání Bingu](quick-start.md) Další informace.

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

2. Vytvořte třídu s názvem `CustomSrchJava`, vytvářet proměnné pro váš klíč předplatného, koncový bod pro vlastní vyhledávání a ID search instance vlastní konfigurace 
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Vytvořte další třídu s názvem `SearchResults` tak, aby obsahovala odpověď z vaší instance vlastního vyhledávání Bingu.

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

4. Vytvoření funkce s názvem `prettify()` formátovat odpověď JSON z rozhraní API pro vlastní vyhledávání Bingu.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslat a přijmout žádost o vyhledávání 

1. Vytvoření funkce s názvem `SearchWeb()` , který odešle požadavek a vrátí `SearchResults` objektu. Vytvoření požadavku adresa url díky kombinaci ID vlastní konfigurace, dotaz a koncový bod informace. Přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví.

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

2. Vytvoření datového proudu a uložit v odpovědi JSON `SearchResults` objektu.

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

3. V hlavní metodě vaší aplikace, zavolejte `SearchWeb()` se hledaný termín 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Spusťte program.
    
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Sestavení webové aplikace s vlastní vyhledávání](./tutorials/custom-search-web-page.md)
