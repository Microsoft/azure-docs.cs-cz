---
title: Hledání obrázků GIF pomocí rozhraní API Bingu pro vyhledávání obrázků
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro vyhledávání obrázků Bingu pro vyhledávání obrázků GIF webu.
services: cognitive-services
author: MikeDodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh
ms.custom: seodec2018
ms.openlocfilehash: a18aaceb4840ffc7e3e3dca4eeb8f5fd8e88049c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876398"
---
# <a name="search-for-gif-images"></a>Vyhledávání obrázků GIF 

Rozhraní API Bingu pro vyhledávání obrázků můžete také prohledat napříč celým webovým nejrelevantnější obrázků GIF.  Vývojáři mohou integrovat poutavé GIF v různých scénářích konverzace. 

Následující adresa URL je dotaz pro Image animovaný obrázek GIF.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
[q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) parametr určuje hledaným výrazům.  Předchozí dotaz také určuje `animatedGif` pomocí [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) parametr filtru.

Pokud chcete zobrazit příklady výsledků, použijte následující adresu URL pro hledání bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parametry dotazu

Další informace o možnostech a parametrech dotazů najdete v článku [odkaz API pro vyhledávání obrázků](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). Následuje příklad pod záhlavím [příklad prohledávání pro animovaný obrázek gif pomocí Javy](#gifExample).

## <a name="tips-and-suggestions"></a>Tipy a návrhy

- Můžete zadat [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) a [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) parametry. Doporučujeme nastavit maxFileSize = 2000000, protože většina GIF v indexu je v části 2MB.  Také vám pomáhají řídit velikost dat, pokud šířka pásma je problém, jako třeba v mobilních situacích, mobilní.
- Chcete-li zvýšit dosahovaný výkon, načtěte miniaturu nejprve před načítání zdrojová adresa url.  
- Pro první spuštění nebo cílová stránka prostředí, kde uživatelský dotaz ještě nemáte, zkuste použít naše populární gif hledání umožňující z [vzroste zájem o Image API](trending-images.md).
- Existují tři nastavení [bezpečné hledání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) parametru.  `strict` Zablokuje možnost obsahu pro dospělé.
- Zobrazit [mkt](supported-countries-markets.md) úplný seznam jazyků podporovaných umístění.
- *AnimatedGifHttps* pouze vrátí animovat obrázků gif, které jsou z adresy protokolu https. Mnoho aplikací pro zabezpečení, vyžadují připojení k externí webové odkazy přes protokol https. Například App Store společnosti Apple vyžaduje připojení k webovým službám přes protokol HTTPS, který šifruje zabezpečení v průběhu přenosu dat uživatele.

<a name="gifExample" />
## <a name="example-search-for-animated-gif-using-java"></a>Příklad prohledávání pro animovaný obrázek gif pomocí Javy

Na následující adrese URL vyhledává animovaný obrázek GIF imagí: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Jak je znázorněno v následujícím příkladu, adresa URL dotazu vyžaduje [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers) záhlaví.

V následujícím příkladu Java se sestaví a odešle žádost.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
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
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

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

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Výsledky
Kód získá následující výsledky jako objekty JSON:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Další postup
- [Rychlý start pro jazyk C#](quickstarts/csharp.md)
- [Kurz pro vyhledávání obrázků jednostránková aplikace](tutorial-bing-image-search-single-page-app.md)
