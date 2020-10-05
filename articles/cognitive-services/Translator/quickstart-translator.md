---
title: 'Rychlý Start: Začínáme s překladatelem'
titleSuffix: Azure Cognitive Services
description: Naučte se překládat text, přepisovat text, rozpoznávat jazyk a více pomocí služby Translator. Příklady jsou k dispozici v jazycích C#, Java, JavaScript a Python.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: erhopf
ms.custom: cog-serv-seo-aug-2020
keywords: Překladatel, služba Translator, překlad textu, přepis textu, rozpoznávání jazyka
ms.openlocfilehash: e5b0bc17e8df1d82fb23a098f857be4fea13b982
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318836"
---
# <a name="quickstart-get-started-with-translator"></a>Rychlý Start: Začínáme s překladatelem

V tomto rychlém startu se naučíte používat službu Translator přes REST. Začínáte základními příklady a přejdete na některé základní možnosti konfigurace, které se běžně používají během vývoje, včetně těchto:

* [NAT](#translate-text)
* [Transkripci](#transliterate-text)
* [Identifikace/detekce jazyka](#detect-language)
* [Vypočítat délku věty](#get-sentence-length)
* [Získat alternativní překlady](#dictionary-lookup-alternate-translations) a [Příklady použití slov ve větě](#dictionary-examples-translations-in-context)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek překladatele](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) a Získejte svůj klíč a koncový bod. Po nasazení vyberte **Přejít k prostředku**.
  * K připojení aplikace ke službě Translator budete potřebovat klíč a koncový bod z prostředku. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
  * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň (F0) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="platform-setup"></a>Nastavení platformy

# <a name="c"></a>[C#](#tab/csharp)

* Vytvořit nový projekt: `dotnet new console -o your_project_name`
* Nahraďte Program.cs kódem jazyka C# zobrazeným níže.
* Nastavte klíč předplatného a hodnoty koncových bodů v Program.cs.
* [Přidejte Newtonsoft.Jsk používání rozhraní příkazového řádku .NET](https://www.nuget.org/packages/Newtonsoft.Json/).
* Spusťte program z adresáře projektu: ``dotnet run``

# <a name="go"></a>[Přejít](#tab/go)

* Ve svém oblíbeném editoru kódu vytvořte nový projekt Go.
* Přidejte níže uvedený kód.
* Hodnotu `subscriptionKey` nahraďte přístupovým klíčem platným pro vaše předplatné.
* Uložte soubor s příponou .go.
* Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
* Sestavte soubor, například: ' jít příklad sestavení-Code. přejít '.
* Spusťte soubor, například: "ukázkový kód".

# <a name="java"></a>[Java](#tab/java)

* Vytvořte pracovní adresář pro váš projekt. Například: `mkdir sample-project`.
* Inicializujte svůj projekt pomocí Gradle: `gradle init --type basic` . Po zobrazení výzvy k výběru **DSL**vyberte **Kotlin**.
* Aktualizace `build.gradle.kts` . Mějte na paměti, že budete muset aktualizovat v `mainClassName` závislosti na ukázce.
  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "<NAME OF YOUR CLASS>"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
  }
  ```
* Vytvořte soubor Java a zkopírujte kód z poskytnuté ukázky. Nezapomeňte přidat svůj klíč předplatného.
* Spusťte ukázku: `gradle run` .

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Vytvořte nový projekt v oblíbených IDE nebo editoru.
* Zkopírujte kód z jedné z ukázek do projektu.
* Nastavte klíč předplatného.
* Spustíte program. Například: `node Translate.js`.

# <a name="python"></a>[Python](#tab/python)

* Vytvořte nový projekt v oblíbených IDE nebo editoru.
* Zkopírujte kód z jedné z ukázek do projektu.
* Nastavte klíč předplatného.
* Spustíte program. Například: `python translate.py`.

---

## <a name="headers"></a>Hlavičky 

Když zavoláte službu Translator přes REST, budete muset zajistit, aby každá žádost obsahovala následující záhlaví. Nedělejte si starosti, v následujících oddílech zahrneme hlavičky do vzorového kódu. 

<table width="100%">
  <th width="20%">Hlavičky</th>
  <th>Popis</th>
  <tr>
    <td>Ověřovací hlavičky (y)</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Podívejte se <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">na dostupné možnosti ověřování</a>.</td>
  </tr>
  <tr>
    <td>Typ obsahu</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Určuje typ obsahu datové části.<br/> Přijatá hodnota je <code>application/json; charset=UTF-8</code> .</td>
  </tr>
  <tr>
    <td>Délka obsahu</td>
    <td><em>Požadovaná hlavička žádosti</em><br/>Délka textu žádosti</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td><em>Volitelné</em>.<br/>Identifikátor GUID generovaný klientem pro jednoznačnou identifikaci požadavku. Tuto hlavičku můžete vynechat, pokud zahrnete ID trasování do řetězce dotazu pomocí parametru dotazu s názvem <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="keys-and-endpoints"></a>Keys and endpoints (Klíče a koncové body)

Ukázky na této stránce využívají pevně zakódované klíče a koncové body pro jednoduchost. Nezapomeňte **odebrat klíč z kódu, až budete hotovi**, a **nikdy ho**zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

## <a name="translate-text"></a>Přeložení textu 

Základní operací služby Translator je převod textu. V této části vytvoříte požadavek, který přijímá jeden zdroj ( `from` ) a poskytuje dva výstupy ( `to` ). Pak zkontrolujeme některé parametry, které lze použít k úpravě požadavku i odpovědi. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&from=en&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```


# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```Javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď: 

```JSON
[
    {
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="detect-language"></a>Zjištění jazyka

Pokud víte, že budete potřebovat překlad, ale neznáte jazyk textu, který se pošle službě Translator, můžete použít operaci detekce jazyka. Je k dispozici více než jeden způsob, jak identifikovat zdrojový textový jazyk. V této části se dozvíte, jak používat detekci jazyka pomocí `translate` koncového bodu a `detect` koncového bodu. 

### <a name="detect-source-language-during-translation"></a>Detekovat zdrojový jazyk během překladu

Pokud nezadáte `from` parametr do žádosti o překlad, služba Translator se pokusí zjistit jazyk zdrojového textu. V odpovědi získáte zjištěný jazyk ( `language` ) a hodnocení spolehlivosti ( `score` ). Nejblíže k `score` znamená, `1.0` že dojde ke zvýšení jistoty, že je zjišťování správné.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Output languages are defined as parameters, input language detected.
        string route = "/translate?api-version=3.0&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```


# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";


    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď: 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

### <a name="detect-source-language-without-translation"></a>Detekovat zdrojový jazyk bez překladu

Je možné použít službu Translator k detekci jazyka zdrojového textu bez provádění překladu. K tomu použijete [`/detect`](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) koncový bod. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Just detect language
        string route = "/detect?api-version=3.0";
        string textToLangDetect = "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.";
        object[] body = new object[] { new { Text = textToLangDetect } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/detect?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
         log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Detect {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/detect")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Detect detectRequest = new Detect();
            String response = detectRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/detect',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/detect'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```
---

Při použití `/detect` koncového bodu bude odpověď zahrnovat alternativní zjišťování a pomůže vám zjistit, jestli se pro všechny zjištěné jazyky podporují překlady a převody. Po úspěšném volání by se měla zobrazit následující odpověď: 

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "nl",
                "score": 0.92
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "sk",
                "score": 0.77
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "de",
        "score": 1.0
    }
]
```

## <a name="transliterate-text"></a>Transliterace textu 

Převádění je proces převodu slova nebo fráze ze skriptu (abecedy) z jednoho jazyka na jiný v závislosti na fonetické podobnosti. Například můžete použít převod "สวัสดี" ( `thai` ) na "sawatdi" ( `latn` ). Je k dispozici více než jeden způsob, jak provést překonávání. V této části se dozvíte, jak používat detekci jazyka pomocí `translate` koncového bodu a `transliterate` koncového bodu. 

### <a name="transliterate-during-translation"></a>Přepisy během překladu

Pokud přecházíte do jazyka, který používá jinou abecedu (nebo fonémy) než váš zdroj, možná budete potřebovat překládání. V tomto příkladu přeložíme "Hello" z angličtiny do thajského. Kromě získání překladu v thajštině získáte přeložení přeložené fráze pomocí abecedy latinky.

Chcete-li získat mezihodnotu z `translate` koncového bodu, použijte `toScript` parametr.

> ! ZNAČTE Úplný seznam dostupných jazyků a možností pro přeplňování najdete v tématu [Podpora jazyků](language-support.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Output language defined as parameter, with toScript set to latn
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "th")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "th")
        .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'th',
        'toScript': 'latn'
    },
    data: [{
        'text': 'Hello'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```Python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'th',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Mějte na paměti, že odpověď z `translate` koncového bodu zahrnuje zjištěný zdrojový jazyk s hodnocením spolehlivosti, překladem pomocí abecedy výstupního jazyka a mezičástmi pomocí abecedy latinky.

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "สวัสดี",
                "to": "th",
                "transliteration": {
                    "script": "Latn",
                    "text": "sawatdi"
                }
            }
        ]
    }
]
```

### <a name="transliterate-without-translation"></a>Přepis bez překladu

Pomocí `transliterate` koncového bodu můžete také získat mezihodnotu. Při použití koncového bodu mezijazyku musíte zadat zdrojový jazyk ( `language` ), zdrojový skript/abecedu ( `fromScript` ) a výstupní skript/abeceda ( `toScript` ) jako parametry. V tomto příkladu budeme pro สวัสดี získat přenáší. 

> ! ZNAČTE Úplný seznam dostupných jazyků a možností pro přeplňování najdete v tématu [Podpora jazyků](language-support.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // For a complete list of options, see API reference.
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/transliterate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("language", "th")
    q.Add("fromScript", "thai")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "สวัสดี"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Transliterate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/transliterate")
            .addQueryParameter("api-version", "3.0")
            .addQueryParameter("language", "th")
            .addQueryParameter("fromScript", "thai")
            .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"สวัสดี\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Transliterate transliterateRequest = new Transliterate();
            String response = transliterateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/transliterate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'language': 'th',
        'fromScript': 'thai',
        'toScript': 'latn'
    },
    data: [{
        'text': 'สวัสดี'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/transliterate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'language': 'th',
    'fromScript': 'thai',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'สวัสดี'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Na rozdíl od volání `translate` koncového bodu `transliterate` vrátí pouze `script` výstup a `text` . 

```json
[
    {
        "script": "latn",
        "text": "sawatdi"
    }
]
```

## <a name="get-sentence-length"></a>Získat délku věty

Pomocí služby Translator můžete získat počet znaků pro větu nebo řadu vět. Odpověď se vrátí jako pole s počty znaků pro každou zjištěnou větu. Délku věty můžete získat pomocí `translate` `breaksentence` koncových bodů a.

### <a name="get-sentence-length-during-translation"></a>Získání délky věty během překladu

Můžete získat počty znaků pro zdrojový text i pro výstup překladu pomocí `translate` koncového bodu. Chcete-li vrátit délku věty ( `srcSenLen` a `transSenLen` ), je nutné nastavit `includeSentenceLength` parametr na `True` .

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Include sentence length details.
        string route = "/translate?api-version=3.0&to=es&includeSentenceLength=true";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "es")
    q.Add("includeSentenceLength", "true")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "es")
        .addQueryParameter("includeSentenceLength", "true")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'es',
        'includeSentenceLength': true
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'es',
    'includeSentenceLength': True
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Kromě zjištěného zdrojového jazyka a překladu získáte počty znaků pro každou zjištěnou větu jak pro zdroj ( `srcSentLen` ), tak pro překlad ( `transSentLen` ).

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "sentLen": {
                    "srcSentLen": [
                        44,
                        21,
                        12
                    ],
                    "transSentLen": [
                        48,
                        18,
                        10
                    ]
                },
                "text": "¿Puedes decirme cómo llegar a la estación Penn? ¿No estás seguro? Está bien.",
                "to": "es"
            }
        ]
    }
]
```

### <a name="get-sentence-length-without-translation"></a>Získat délku věty bez překladu

Služba Translator také umožňuje vyžádat délku věty bez překladu pomocí `breaksentence` koncového bodu. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // Only include sentence length details.
        string route = "/breaksentence?api-version=3.0";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/breaksentence?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class BreakSentence {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/breaksentence")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            BreakSentence breakSentenceRequest = new BreakSentence();
            String response = breakSentenceRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/breaksentence',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/breaksentence'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Na rozdíl od volání `translate` koncového bodu `breaksentence` vrátí pouze počty znaků pro zdrojový text v poli s názvem `sentLen` . 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "sentLen": [
            44,
            21,
            12
        ]
    }
]
```

## <a name="dictionary-lookup-alternate-translations"></a>Vyhledávání slovníku (Alternativní překlady)

U koncového bodu můžete získat alternativní překlady slova nebo fráze. Například při překladu slova "žraločí" z `en` na `es` , tento koncový bod vrátí "Tiburón" i "Escualo".

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // See many translation options
        string route = "/dictionary/lookup?api-version=3.0&from=en&to=es";
        string wordToTranslate = "shark";
        object[] body = new object[] { new { Text = wordToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/lookup?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "shark"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryLookup {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/lookup")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryLookup dictionaryLookupRequest = new DictionaryLookup();
            String response = dictionaryLookupRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/dictionary/lookup',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/dictionary/lookup'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Pojďme to rozdělit, protože JSON je složitější než některé další příklady v tomto článku. `translations`Pole obsahuje seznam překladů. Každý objekt v tomto poli obsahuje hodnocení spolehlivosti ( `confidence` ), text optimalizovaný pro zobrazení koncového uživatele ( `displayTarget` ), normalizovaný text ( `normalizedText` ), část řeči ( `posTag` ) a informace o předchozím překladu ( `backTranslations` ). Další informace o odpovědi najdete v tématu [vyhledávání slovníku](reference/v3-0-dictionary-lookup.md) .

```json
[
    {
        "displaySource": "shark",
        "normalizedSource": "shark",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 45,
                        "normalizedText": "shark",
                        "numExamples": 0
                    }
                ],
                "confidence": 0.8182,
                "displayTarget": "tiburón",
                "normalizedTarget": "tiburón",
                "posTag": "OTHER",
                "prefixWord": ""
            },
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 10,
                        "normalizedText": "shark",
                        "numExamples": 1
                    }
                ],
                "confidence": 0.1818,
                "displayTarget": "escualo",
                "normalizedTarget": "escualo",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="dictionary-examples-translations-in-context"></a>Příklady slovníku (překlady v kontextu)

Po provedení vyhledávání slovníku můžete předat zdrojový text a překlad do `dictionary/examples` koncového bodu a získat tak seznam příkladů, které znázorňují oba výrazy v kontextu věty nebo fráze. Sestavení v předchozím příkladu použijete `normalizedText` a `normalizedTarget` z odpovědi vyhledávání slovníku jako `text` a v `translation` uvedeném pořadí. `from`Jsou vyžadovány parametry zdrojového jazyka () a výstupu Target ( `to` ). 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";
    
    static async Task Main(string[] args)
    {
        // See examples of terms in context
        string route = "/dictionary/examples?api-version=3.0&from=en&to=es";
        object[] body = new object[] { new { Text = "Shark",  Translation = "tiburón" } } ;
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

# <a name="go"></a>[Přejít](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/examples?api-version=3.0"
    subscriptionKey := "YOUR_SUBSCRIPTION_KEY"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text        string
        Translation string
    }{
        {
            Text:        "Shark",
            Translation: "tiburón",
        },
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator Text API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryExamples {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/examples")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\", \"Translation\": \"tiburón\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryExamples dictionaryExamplesRequest = new DictionaryExamples();
            String response = dictionaryExamplesRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const uuidv4 = require('uuid/v4');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

axios({
    baseURL: endpoint,
    url: '/dictionary/examples',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark',
        'translation': 'tiburón'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

path = '/dictionary/examples'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark',
    'translation': 'tiburón'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

---

Po úspěšném volání by se měla zobrazit následující odpověď. Další informace o odpovědi najdete v tématu [vyhledávání slovníku](reference/v3-0-dictionary-examples.md) .

```json
[
    {
        "examples": [
            {
                "sourcePrefix": "More than a match for any ",
                "sourceSuffix": ".",
                "sourceTerm": "shark",
                "targetPrefix": "Más que un fósforo para cualquier ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "Same with the mega ",
                "sourceSuffix": ", of course.",
                "sourceTerm": "shark",
                "targetPrefix": "Y con el mega ",
                "targetSuffix": ", por supuesto.",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "A ",
                "sourceSuffix": " ate it.",
                "sourceTerm": "shark",
                "targetPrefix": "Te la ha comido un ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            }
        ],
        "normalizedSource": "shark",
        "normalizedTarget": "tiburón"
    }
]
```

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak rozhraní API počítá znaky.](character-counts.md)
* [Přizpůsobení a zlepšení překladu](customization.md)

## <a name="see-also"></a>Viz také

* [Reference k rozhraní API pro překladatele V3](reference/v3-0-reference.md)
* [Podpora jazyků](language-support.md)