---
title: Použití tokenu přehledů – vizuální vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak pomocí tokenu přehledu obrázku pomocí rozhraní API pro vizuální vyhledávání Bingzískat přehledy o obrázku.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/26/2019
ms.author: scottwhi
ms.openlocfilehash: 251197c456ece4fe2dbbe264219d52f3502b7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67341726"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Použití tokenu přehledů k získání přehledů pro obrázek

Rozhraní API pro Vizuální vyhledávání Bingu vrátí informace o obrázku, který zadáte. Obrázek můžete zadat pomocí adresy URL obrázku, tokenu insights nebo nahráním obrázku. Informace o těchto možnostech naleznete v tématu [Co je rozhraní API pro vizuální vyhledávání Bing?](overview.md). Tento článek ukazuje pomocí tokenu přehledy. Příklady, které ukazují, jak nahrát obrázek získat přehledy, naleznete v tématu rychlé starty ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)).

Pokud odešlete vizuální vyhledávání Bingu token obrázku nebo adresu URL, zobrazí se následující data formuláře, která je nutné zahrnout do těla příspěvku. Data formuláře musí `Content-Disposition` obsahovat záhlaví a `name` je nutné nastavit jeho parametr na "knowledgeRequest". Podrobnosti o `imageInfo` objektu naleznete v žádosti:

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

Příklady v tomto článku ukazují, jak používat token přehledů. Získáte token přehledů `Image` z objektu v odpovědi rozhraní API /images/search. Informace o získání tokenu přehledů najdete [v tématu Co je rozhraní API pro vyhledávání obrázků Bingu?](../Bing-Image-Search/overview.md).

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

Příklady, které používají token přehledů, naleznete [v tématu C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Použití s C #

### <a name="c-prerequisites"></a>Požadavky jazyka C#

- Všechny verze [Visual Studia 2019,](https://www.visualstudio.com/downloads/) aby tento kód běžel v systému Windows.
- Předplatné Azure. Pro tento rychlý start můžete použít bezplatný klíč [zkušebního](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného nebo klíč placeného předplatného.

## <a name="run-the-application"></a>Spuštění aplikace

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Vytvořte konzolové řešení v sadě Visual Studio.
2. Nahraďte obsah Program.cs kódem zobrazeným v tomto rychlém startu.
3. Hodnotu `accessKey` nahraďte klíčem předplatného.
4. Nahraďte hodnotu `insightsToken` tokenem přehledů z odpovědi /images/search.
5. Spusťte program.

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

## <a name="use-with-java"></a>Použití s Javou

### <a name="java-prerequisites"></a>Java předpoklady

- Ke kompilaci a spuštění tohoto kódu je nutné použít [jdk 7 nebo 8.](https://aka.ms/azure-jdks) IDE java můžete použít, pokud máte oblíbené, ale textový editor bude stačit.
- Pro tento rychlý start můžete použít bezplatný klíč [zkušebního](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného nebo klíč placeného předplatného.

## <a name="run-the-java-application"></a>Spuštění aplikace Java

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Stáhněte nebo nainstalujte [knihovnu Gson Java](https://github.com/google/gson). Můžete také získat Gson přes Maven.
2. Ve svém oblíbeném integrovaném vývojovém prostředí nebo editoru vytvořte nový projekt Javy.
3. Přidejte poskytnutý kód do souboru s názvem `VisualSearch.java`.
4. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
5. Spusťte program.

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

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

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

## <a name="use-with-nodejs"></a>Použití s souborem Node.js

### <a name="nodejs-prerequisites"></a>Požadavky node.js

- Chcete-li spustit tento kód, musíte mít [soubor Node.js 6.](https://nodejs.org/en/download/)
- Pro tento rychlý start můžete použít bezplatný klíč [zkušebního](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného nebo klíč placeného předplatného.

## <a name="run-the-javascript-application"></a>Spuštění aplikace JavaScript

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Vytvořte složku pro váš projekt (nebo použijte vaše oblíbené prostředí IDE nebo oblíbený editor).
2. Z příkazového řádku nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Nainstalujte moduly žádosti:
  
   ```
   npm install request  
   ```
1. Nainstalujte moduly objektu form-data:  
   ```
   npm install form-data  
   ```
1. Vytvořte soubor s názvem GetVisualInsights.js a přidejte do něj následující kód.
1. Hodnotu `subscriptionKey` nahraďte klíčem předplatného.
1. Spusťte program.  
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

## <a name="use-with-python"></a>Použití v Pythonu

### <a name="python-prerequisites"></a>Požadavky pythonu

- Chcete-li spustit tento kód, musíte mít [Python 3.](https://www.python.org/)
- Pro tento rychlý start můžete použít klíč [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného, nebo klíč placeného předplatného.

## <a name="run-the-python-application"></a>Spuštění aplikace Python

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru.
2. Vytvořte soubor s názvem visualsearch.py a přidejte do něj kód zobrazený v tomto rychlém startu.
3. Hodnotu `SUBSCRIPTION_KEY` nahraďte klíčem předplatného.
4. Spusťte program.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests
import json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest': (None, formData)}


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

## <a name="next-steps"></a>Další kroky

[Vytvoření jednostránkové webové aplikace vizuálního vyhledávání](tutorial-bing-visual-search-single-page-app.md)  
[Co je rozhraní API pro vizuální vyhledávání Bingu?](overview.md)  
[Zkuste Cognitive Services](https://aka.ms/bingvisualsearchtryforfree)  
[Získání bezplatné zkušební verze přístupového klíče](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Obrázky - Vizuální vyhledávání](https://aka.ms/bingvisualsearchreferencedoc)
