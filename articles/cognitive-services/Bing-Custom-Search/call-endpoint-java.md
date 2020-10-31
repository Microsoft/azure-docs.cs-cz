---
title: 'Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí jazyka Java | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto rychlého startu můžete začít požadovat výsledky hledání z vaší instance Vlastní vyhledávání Bingu v jazyce Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 1dfc9c79e41a0dbeb08c9416316e601b05b2cb32
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080137"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Rychlý Start: volání koncového bodu Vlastní vyhledávání Bingu pomocí jazyka Java

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu se dozvíte, jak vyžádat výsledky hledání z vaší instance Vlastní vyhledávání Bingu. I když je tato aplikace napsaná v jazyce Java, rozhraní API pro vlastní vyhledávání Bingu je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto ukázku je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Předpoklady

- Instance Vlastní vyhledávání Bingu. Další informace najdete v tématu [rychlý Start: Vytvoření první instance vlastní vyhledávání Bingu](quick-start.md).

- Nejnovější [sada Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

- [Knihovna gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Vytvoření a inicializace aplikace

1. V oblíbených IDE nebo editoru vytvořte nový projekt Java a importujte následující knihovny:

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

2. Vytvořte třídu s názvem `CustomSrchJava` a pak vytvořte proměnné pro klíč předplatného, vlastní koncový bod hledání a ID vlastní konfigurace instance hledání. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Vytvořte další třídu s názvem `SearchResults` , aby obsahovala odpověď z vaší instance vlastní vyhledávání Bingu.

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Vytvořte funkci nazvanou `prettify()` pro naformátování odpovědi JSON z rozhraní API pro vlastní vyhledávání Bingu.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Odeslání a přijetí žádosti o vyhledávání 

1. Vytvořte funkci s názvem `SearchWeb()` , která odešle požadavek a vrátí `SearchResults` objekt. Vytvořte adresu URL požadavku kombinováním vlastního ID konfigurace, dotazu a informací o koncovém bodu. Přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` .

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

2. Vytvořte datový proud a uložte odpověď JSON do `SearchResults` objektu.

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

3. Vytiskněte odpověď JSON.

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Spustíte program.
    
## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření vlastní vyhledávací webové aplikace](./tutorials/custom-search-web-page.md)
