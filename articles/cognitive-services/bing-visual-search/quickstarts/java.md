---
title: Rychlý start Java pro Bing Visual vyhledávání rozhraní API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak nahrát bitovou kopii do rozhraní API vyhledávání Visual Bing a získat zpět přehledy o bitové kopii.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 8160302faa373d69b65afe6b68a8efb44442850d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343615"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>První Bing Visual vyhledávací dotaz v jazyce Java

Rozhraní API vyhledávání Visual Bing vrací informace o bitovou kopii, kterou zadáte. Můžete zadat bitovou kopii pomocí adresy URL bitové kopie, insights tokenu, nebo tím, že nahrajete image. Informace o těchto možnostech najdete v tématu [co je rozhraní API vyhledávání Visual Bing?](../overview.md) Tento článek ukazuje, odešlou obrázek. Nahrávání obrázku může být užitečné v mobilních situacích, kde pořídit snímek dobře známé významné a vrátit informace o něm. K přehledům může obsahovat třeba trivia o významné. 

Odešlete obrázek místní následující zobrazí formulář data, že je nutné zahrnout text příspěvku. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr lze nastavit na libovolný řetězec. Obsah formuláře je binární soubor bitové kopie. Maximální obrázek, který může odeslat je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduché konzolové aplikace, která se odešle požadavek Visual Bing rozhraní API služby Search a zobrazí výsledky hledání JSON. Při této aplikace je napsána v jazyce Java, rozhraní API je kompatibilní s žádný programovací jazyk, který můžete nastavit požadavků HTTP a analyzovat JSON RESTful webová služba. 


## <a name="prerequisites"></a>Požadavky

Budete potřebovat [JDK 7 nebo 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) pro zkompilování a spuštění tohoto kódu. Pokud máte Oblíbené, ale bude stačit textového editoru můžete používat Java IDE.

Pro tento rychlý start, můžete použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatné nebo placené předplatné klíče.

## <a name="running-the-application"></a>Spouštění aplikace.

Následující ukazuje, jak nahrát bitovou kopii pomocí MultipartEntityBuilder v jazyce Java.

Ke spuštění této aplikace, postupujte takto:

1. Stáhnout nebo nainstalovat [gson knihovny](https://github.com/google/gson). Může je také získat prostřednictvím Maven.
2. Vytvoření nového projektu Java vaše oblíbené IDE nebo editoru.
3. Zadaný kód přidejte do souboru s názvem `VisualSearch.java`.
4. Nahraďte `subscriptionKey` hodnotu s svůj klíč předplatného.
4. Nahraďte `imagePath` hodnotu s cestou bitové kopie a nahrajte.
5. Spusťte program.


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

[Získejte přehledy o bitovou kopii pomocí tokenu statistiky](../use-insights-token.md)  
[Kurz jednostránkové aplikace Bing Visual Search](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Visual vyhledávání v Bingu](../overview.md)  
[Vyzkoušet](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace rozhraní API Visual pro hledání Bing](https://aka.ms/bingvisualsearchreferencedoc)

