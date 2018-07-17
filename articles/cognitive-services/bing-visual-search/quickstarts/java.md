---
title: Rychlý start Java pro vizuální vyhledávání Bingu rozhraní API | Dokumentace Microsoftu
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak k nahrání obrázku do API pro vizuální vyhledávání Bingu a získat přehledy o imagi.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 41e0855b126ca6e54d0a487a88fe59a0be6f72f6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071991"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>Svůj první dotaz v jazyce Java pro vizuální vyhledávání Bingu

API pro vizuální vyhledávání Bingu vrátí informace o bitovou kopii, která zadáte. Image můžete zadat pomocí adresy URL obrázku, insights, token, nebo nahráním image. Informace o těchto možnostech najdete v tématu [co je API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahráváním obrázku. Nahrání image může být užitečné v mobilních situacích, kde pořídit snímek dobře známé památek a získat informace o něm. Triviální prvek o památek může obsahovat třeba přehledy. 

Pokud odešlete místní image, následuje data formuláře musí obsahovat v textu příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr může být nastaven na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost, kterou můžete k nahrání je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která odešle žádost o API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Zatímco tato aplikace je napsána v jazyce Java, je rozhraní API RESTful webová služba, která je kompatibilní s programovací jazyk, který může vytvářet požadavky HTTP a parsování formátu JSON. 


## <a name="prerequisites"></a>Požadavky

Budete potřebovat [JDK 7 nebo 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) kompilace a spuštění tohoto kódu. Pokud máte Oblíbené, ale bude stačit textový editor, můžete použít prostředí Java IDE.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-application"></a>Spouštění aplikace.

Následující ukazuje, jak nahrát image pomocí MultipartEntityBuilder v jazyce Java.

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Stáhnout nebo nainstalovat [gson knihovny](https://github.com/google/gson). To může získat prostřednictvím nástroje Maven.
2. Vytvoření nového projektu Java v Oblíbené prostředí IDE nebo editoru.
3. Přidejte poskytnutý kód do souboru s názvem `VisualSearch.java`.
4. Nahraďte `subscriptionKey` hodnotu s klíči předplatného.
4. Nahraďte `imagePath` hodnotu s cestou k nahrání obrázku.
5. Spuštění programu.


```java
package uploadimage;

import java.util.*;
import java.io.*;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    
}
```

## <a name="next-steps"></a>Další postup

[Získejte přehled o bitovou kopii pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků pro vizuální vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[kurzu jednostránkovou aplikaci pro vizuální vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled pro vizuální vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)

