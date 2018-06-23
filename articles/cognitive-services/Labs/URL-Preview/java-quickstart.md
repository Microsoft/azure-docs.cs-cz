---
title: Rychlý start Java pro náhled adresy URL projektu - kognitivní služby Microsoft | Microsoft Docs
description: Ukázka skriptu a začněte využívat ve verzi Preview adresa URL projektu v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 2de74f48882605bfcf05f65723ba5d8993587f51
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343247"
---
# <a name="url-preview-java-quickstart"></a>Adresa URL Preview Java rychlý start

V následujícím příkladu Java vytvoří Náhled adresy Url pro web SwiftKey: https://swiftkey.com/en.

## <a name="prerequisites"></a>Požadavky

Získat přístupový klíč pro bezplatné zkušební verze [kognitivní Labs služby](https://aka.ms/answersearchsubscription)

## <a name="request"></a>Žádost 

Následující kód vytvoří `WebRequest`, nastaví hlavičku přístup klíče a přidá řetězec dotazu pro "https://swiftkey.com/en".  Pak odešle žádost a přiřadí odpověď na řetězec tak, aby obsahovala JSON text.

````
    // construct URL of search request (endpoint + query string)

    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/urlpreview/v7.0/search";

    static String searchTerm = "https://swiftkey.com/en";

    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
````

## <a name="complete-code"></a>Kompletní kód

Rozhraní API služby Bing odpovědí Search vrátí výsledky z vyhledávacího webu Bing.
1. Stáhnout nebo nainstalovat gson knihovny.
2. Vytvoření nového projektu Java vaše oblíbené IDE nebo editoru.
3. Přidejte kód níže uvedenou.
4. Nahraďte hodnotu subscriptionKey přístupový klíč platný pro vaše předplatné.
5. Spusťte program.

````
package UrlPreviewpkg;

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
 * When you have compiled or downloaded gson-2.8.1.jar and have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac UrlPreviewcls.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar UrlPreview
 */

public class UrlPreview 
{
    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.labs.cognitive.microsoft.com";
    static String path = "/urlpreview/v7.0/search";

    static String searchTerm = "https://swiftkey.com/en";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
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

````

## <a name="next-steps"></a>Další postup
- [Rychlý start C#](csharp.md)
- [Rychlý start JavaScript](javascript.md)
- [Rychlé spuštění uzlu](node-quickstart.md)
- [Rychlý start PYthon](python-quickstart.md)