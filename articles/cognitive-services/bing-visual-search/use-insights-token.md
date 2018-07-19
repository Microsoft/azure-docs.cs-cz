---
title: Pomocí přehledů token API pro vizuální vyhledávání Bingu | Dokumentace Microsoftu
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak používat obrazu insight tokenu s využitím rozhraní API pro vizuální vyhledávání získat přehled o bitovou kopii.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 569ae89a712d14fb36989e756f99725dce398c0a
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126554"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>Chcete-li získat přehled o bitovou kopii pomocí tokenu insights

API pro vizuální vyhledávání Bingu vrátí informace o bitovou kopii, která zadáte. Image můžete zadat pomocí adresy URL obrázku, insights, token, nebo nahráním image. Informace o těchto možnostech najdete v tématu [co je API pro vizuální vyhledávání Bingu?](overview.md). Tento článek ukazuje použití tokenu insights. Příklady, které si předvedli nahrání obrázku, který má získat přehledy, najdete rychlých startech ([jazyka C#](quickstarts\csharp.md) | [Java](quickstarts\java.md) | [Node.js](quickstarts\nodejs.md)  |  [Python](quickstarts\python.md)).


Při odesílání pro vizuální vyhledávání image token nebo adresu URL, následuje data formuláře, že je třeba zahrnout text příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition a jeho `name` parametr musí být nastaven na "knowledgeRequest". Podrobnosti o `imageInfo` objektu, najdete v článku [žádost](#the-request).

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

V příkladech v tomto článku ukazují, jak používat insights token. Získáte přehledy token od objektu Image v odpovědi rozhraní API /images/search. Informace o tom, jak insights token najdete v tématu [API pro vyhledávání obrázků Bingu](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```


Příklady, které používají insights token najdete v tématu [jazyka C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python).

<a name="using-csharp" />

## <a name="using-c"></a>Pomocí C#

### <a name="prerequisites"></a>Požadavky

Budete potřebovat [Visual Studio 2017](https://www.visualstudio.com/downloads/) Chcete-li získat tento kód běžící na Windows. (Bezplatná edice Community bude fungovat.)

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Vytvořte nové konzoly řešení v sadě Visual Studio.
1. Nahraďte obsah `Program.cs` kódem zobrazeným v tomto rychlém startu.
2. Nahraďte `accessKey` hodnotu s klíči předplatného.
2. Nahraďte `insightsToken` hodnotu s tokenem přehledy z odpovědi hledání/imagí /.
3. Spuštění programu.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

<a name="using-java" />

## <a name="using-java"></a>Pomocí Javy

### <a name="prerequisites"></a>Požadavky

Budete potřebovat [JDK 7 nebo 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) kompilace a spuštění tohoto kódu. Pokud máte Oblíbené, ale bude stačit textový editor, můžete použít prostředí Java IDE.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Stáhnout nebo nainstalovat [gson knihovny](https://github.com/google/gson). To může získat prostřednictvím nástroje Maven.
2. Vytvoření nového projektu Java v Oblíbené prostředí IDE nebo editoru.
3. Přidejte poskytnutý kód do souboru s názvem `VisualSearch.java`.
4. Nahraďte `subscriptionKey` hodnotu s klíči předplatného.
5. Spuštění programu.

```java
package insightstoken;

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


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
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


<a name="using-nodejs" />

## <a name="using-nodejs"></a>Pomocí Node.js

### <a name="prerequisites"></a>Požadavky

Potřebujete [Node.js 6](https://nodejs.org/en/download/) tento kód spustit.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-application"></a>Spouštění aplikace.

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Vytvořte složku pro váš projekt (nebo použijte vaše oblíbené prostředí IDE nebo editoru).
2. Z příkazového řádku nebo v terminálu přejděte do složky, kterou jste právě vytvořili.
3. Instalace modulů žádosti:  
  ```  
  npm install request  
  ```  
3. Instalace modulů data formuláře:  
  ```  
  npm install form-data  
  ```  
4. Vytvořte soubor s názvem GetVisualInsights.js a přidejte do ní následující kód.
5. Nahraďte `subscriptionKey` hodnotu s klíči předplatného.
7. Spuštění programu.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


<a name="using-python" />

## <a name="using-python"></a>Pomocí Pythonu


### <a name="prerequisites"></a>Požadavky

Potřebujete [Python 3](https://www.python.org/) tento kód spustit.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Vytvoření nového projektu Pythonu v Oblíbené prostředí IDE nebo editoru.
2. Vytvořte soubor s názvem visualsearch.py a přidejte kódu zobrazeného v rámci tohoto rychlého startu.
3. Nahraďte `SUBSCRIPTION_KEY` hodnotu s klíči předplatného.
4. Spuštění programu.


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Další postup

[Kurz jednostránkovou aplikaci pro vizuální vyhledávání Bingu](tutorial-bing-visual-search-single-page-app.md)  
[Přehled pro vizuální vyhledávání Bingu](overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
