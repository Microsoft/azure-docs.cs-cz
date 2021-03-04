---
title: Začínáme s překladem dokumentů
description: Jak vytvořit službu překladu dokumentů pomocí programovacích jazyků a jazyků C#, jazyků, Java, Node.js nebo Python a platforem
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 886889ef9a42e358fca22a9d86955a23c5419dfa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738153"
---
# <a name="get-started-with-document-translation-preview"></a>Začínáme s překladem dokumentů (Preview)

 V tomto článku se naučíte používat překlad dokumentů s metodami REST API HTTP. Překlad dokumentů je cloudová funkce služby [Azure Translator](../translator-info-overview.md) .  Rozhraní API pro překlad dokumentů umožňuje překlad celých dokumentů při zachování struktury zdrojového dokumentu a formátování textu.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* Aktivní [**účet Azure**](https://azure.microsoft.com/free/cognitive-services/).  Pokud ho nemáte, můžete si [**vytvořit bezplatný účet**](https://azure.microsoft.com/free/).

* Prostředek služby [**Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) **(nejedná se** o prostředek Cognitive Services). 

* [**Účet úložiště objektů BLOB v Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Veškerý přístup k Azure Storage probíhá prostřednictvím účtu úložiště.

* Formulář dokončeného [**překladu dokumentů (Preview)**](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-riVR3Xj0tOnIRdZOALbM9UOEE4UVdFQVBRQVBWWDBRQUM3WjYxUEpUTC4u) , který umožní vašemu předplatnému Azure používat novou funkci překladu dokumentů.

> [!NOTE]
> Překlad dokumentu se v současné době podporuje jenom v prostředku překladatele (s jednou službou), **nikoli** v Cognitive Services (Multi-Service) prostředku.

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Získání vlastního názvu domény a klíče předplatného

> [!IMPORTANT]
>
> * Koncový bod, který se nachází na vašich Azure Portal _klíčů prostředků a koncových_ bodech globálního překladatele, nemůžete použít `api.cognitive.microsofttranslator.com` k převedení požadavků HTTP na překlad dokumentů.
> * **Všechny požadavky rozhraní API na službu překladu dokumentů vyžadují vlastní koncový bod domény**.

### <a name="what-is-the-custom-domain-endpoint"></a>Jaký je vlastní koncový bod domény? 

Vlastní koncový bod domény je adresa URL formátovaná pomocí názvu prostředku, názvu hostitele a podadresářů překladatele:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Hledání vlastního názvu domény

Parametr **název-zdroje** (označovaný také jako *vlastní název domény*) je hodnota, kterou jste zadali do pole **název** při vytváření prostředku překladatele.

:::image type="content" source="../media/instance-details.png" alt-text="Obrázek pole Azure Portal, vytvořit prostředek, okamžité podrobnosti a název":::

### <a name="get-your-subscription-key"></a>Získat klíč předplatného

Požadavky na službu translatoru vyžadují pro ověření přístupu klíč určený jen pro čtení.

1. Pokud jste vytvořili nový prostředek, potom po jeho nasazení vyberte **Přejít k prostředku**. Pokud máte existující prostředek překladu dokumentů, přejděte přímo na stránku prostředku.
1. V levé kolejnici v části *Správa prostředků* vyberte **klíče a koncový bod**.
1. Zkopírujte a vložte svůj klíč předplatného do vhodného umístění, jako je například *Poznámkový blok společnosti Microsoft*.
1. Vložíte ho do kódu níže, abyste ověřili požadavek na službu překladu dokumentů.

:::image type="content" source="../media/translator-keys.png" alt-text="Obrázek pole získat klíč předplatného v Azure Portal.":::

## <a name="create-your-azure-blob-storage-containers"></a>Vytvoření kontejnerů úložiště objektů BLOB v Azure

V [**účtu služby Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) budete muset [**vytvořit kontejnery**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) pro zdrojové, cílové a volitelné soubory glosáře.

* **Zdrojový kontejner** Do tohoto kontejneru se nahrávají soubory pro překlad (povinné).
* **Cílový kontejner** Do tohoto kontejneru se budou ukládat přeložené soubory (povinné).  
* **Kontejner glosáře**. Do tohoto kontejneru se nahrávají soubory glosáře (volitelné).  

*Viz* **vytvoření přístupových tokenů SAS pro překlad dokumentu**

`sourceUrl` `targetUrl` Nepovinné `glossaryUrl` musí zahrnovat token sdíleného přístupového podpisu (SAS), připojený jako řetězec dotazu. Token se dá přiřadit ke kontejneru nebo konkrétním objektům blob.

* **Zdrojový** kontejner nebo objekt BLOB musí mít určený přístup **pro čtení** a **seznam** .
* **Cílový** kontejner nebo objekt BLOB musí mít určený přístup pro **zápis** a přístup k **seznamu** .
* Váš kontejner **glosáře** nebo objekt BLOB musí mít určený přístup  **pro čtení** a **seznam** .

> [!TIP]
>
> * Pokud překládáte **více** souborů (objektů BLOB) v rámci operace, **delegovat přístup SAS na úrovni kontejneru**.  
> * Pokud v rámci operace překládáte **jeden** soubor (objekt BLOB), **pověřit přístup SAS na úrovni objektu BLOB**.  
>

## <a name="set-up-your-coding-platform"></a>Nastavení kódovací platformy

### <a name="c"></a>[C#](#tab/csharp)

* Vytvoření nového projektu
* Nahraďte Program.cs kódem jazyka C# zobrazeným níže.
* Nastavte koncový bod. klíč předplatného a hodnoty URL kontejneru v Program.cs.
* K zpracování dat JSON přidejte [Newtonsoft.Jsv balíčku pomocí rozhraní .NET CLI](https://www.nuget.org/packages/Newtonsoft.Json/).
* Spusťte program z adresáře projektu.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Vytvořte nový projekt Node.js.
* Nainstalujte knihovnu Axios pomocí nástroje `npm i axios` .
* Zkopírujte do projektu vložte kód níže.
* Nastavte hodnoty pro koncový bod, klíč předplatného a adresu URL kontejneru.
* Spustíte program.

### <a name="python"></a>[Python](#tab/python)  

* Vytvoření nového projektu
* Zkopírujte a vložte kód z jedné z ukázek do projektu.
* Nastavte hodnoty pro koncový bod, klíč předplatného a adresu URL kontejneru.
* Spustíte program. Příklad: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Vytvořte pracovní adresář pro váš projekt. Například:

```powershell
mkdir sample-project
```

* V adresáři projektu vytvořte následující strukturu podadresáře:  

  src</br>
&emsp; └ Main</br>
&emsp;&emsp;&emsp;└ Java

```powershell
mkdir -p src/main/java/
```

**Poznámka**: zdrojové soubory Java (například _Sample. Java_) živě v src/Main/**Java**.

* V kořenovém adresáři (například *ukázkový projekt*) inicializujte projekt pomocí Gradle:

```powershell
gradle init --type basic
```

* Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

* Aktualizujte `build.gradle.kts`  soubor. Mějte na paměti, že budete muset aktualizovat v `mainClassName` závislosti na ukázce:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Vytvořte soubor Java v adresáři **Java** a zkopírujte nebo vložte kód ze zadané ukázky. Nezapomeňte přidat klíč předplatného a koncový bod.

* **Sestavte a spusťte ukázku z kořenového adresáře**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Přejít](#tab/go)  

* Vytvořte nový projekt přejít.
* Přidejte níže uvedený kód.
* Nastavte hodnoty pro koncový bod, klíč předplatného a adresu URL kontejneru.
* Uložte soubor s příponou .go.
* Na počítači s nainstalovaným jazykem Go otevřete příkazové okno.
* Sestavte soubor, například: ' jít příklad sestavení-Code. přejít '.
* Spusťte soubor, například: "ukázkový kód".

 ---

## <a name="make-document-translation-requests"></a>Vytváření požadavků na překlad dokumentů

Požadavek na překlad dokumentu Batch se odešle do koncového bodu služby translatoru prostřednictvím žádosti POST. V případě úspěchu vrátí metoda POST `202 Accepted`  kód odpovědi a služba vytvoří požadavek Batch.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Každá žádost o rozhraní API pro překladatele dokumentu obsahuje následující hlavičky:

|Hlavička protokolu HTTP|Popis|
|---|--|
|Ocp-Apim-Subscription-Key|**Požadováno**: hodnota je klíč předplatného Azure pro váš překladatel nebo Cognitive Services prostředek.|
|Typ obsahu|**Požadováno**: Určuje typ obsahu datové části. Přijaté hodnoty jsou Application/JSON nebo charset = UTF-8.|
|Délka obsahu|**Požadováno**: délka textu požadavku.|

### <a name="post-request-body-properties"></a>Vlastnosti textu žádosti POST

* Tělo žádosti POST je objekt JSON s názvem `inputs` .
* `inputs`Objekt obsahuje jak `sourceURL` `targetURL` adresy kontejnerů, tak i adresy kontejnerů pro zdrojové a cílové jazykové páry a může volitelně obsahovat `glossaryURL` adresu kontejneru.
* `prefix`Pole a `suffix` (volitelné) slouží k filtrování dokumentů v kontejneru, včetně složek.
* `glossaries`Při překladu dokumentu se použije hodnota pro pole (volitelné).
* `targetUrl`Pro každý cílový jazyk musí být jedinečný.

>[!NOTE]
> Pokud soubor se stejným názvem již v cíli existuje, bude přepsán.

## <a name="post-a-translation-request"></a>ODESLÁNÍ žádosti o překlad

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>Text žádosti POST bez volitelného glossaryURLu

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>Vystavení textu žádosti s volitelným glossaryURL

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

> [!IMPORTANT]
>
> Pro ukázky kódu níže může být nutné aktualizovat následující pole v závislosti na operaci:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (ID úlohy)
>>
> Místo, kde má být nalezena `id` hodnota:
> * Úlohu můžete najít `id`  v `Operation-Location`  hodnotě adresy URL hlavičky odpovědi metody post. Posledním parametrem adresy URL je úloha operace **`id`** .  
> * Můžete také použít požadavek GET Jobs k načtení úlohy `id`  pro operaci překladu dokumentu.
>
> Pro níže uvedené ukázky kódu budete mít pevný kód a koncový bod, kde je uvedeno. Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit.  
>
> Způsoby, jak bezpečně ukládat a přistupovat k vašim přihlašovacím údajům, najdete v tématu [zabezpečení Azure Cognitive Services](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) .

## <a name="_post-document-translation_-request"></a>_Odeslat požadavek na posunutí dokumentu_

Odešle službě překladu požadavek na překlad dokumentu Batch.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Přejít](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_"></a>_ZÍSKAT formáty souborů_ 

Načte seznam podporovaných formátů souborů. V případě úspěchu vrátí tato metoda `200 OK` kód odpovědi.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Přejít](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_"></a>_ZÍSKAT stav úlohy_ 

Získejte aktuální stav pro jednu úlohu a souhrn všech úloh v žádosti o překlad dokumentu. V případě úspěchu vrátí tato metoda `200 OK` kód odpovědi.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Přejít](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_"></a>_ZÍSKAT stav dokumentu_

### <a name="brief-overview"></a>Stručný přehled

Načte stav konkrétního dokumentu v požadavku na překlad dokumentu. V případě úspěchu vrátí tato metoda `200 OK` kód odpovědi.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Přejít](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_"></a>_Odstranit úlohu_ 

### <a name="brief-overview"></a>Stručný přehled

Zruší aktuálně zpracovávanou úlohu nebo úlohu zařazenou do fronty. Zruší se pouze dokumenty, pro které se překlad nezahájil.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Přejít](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Omezení obsahu

Následující tabulka uvádí omezení pro data, která se odesílají do překladu dokumentů.

|Atribut | Omezení|
|---|---|
|Velikost dokumentu| ≤ 40 MB |
|Celkový počet souborů|≤ 1000 |
|Celková velikost obsahu v dávce | ≤ 250 MB|
|Počet cílových jazyků v dávce| ≤ 10 |
|Velikost souboru překladu paměti| ≤ 10 MB|

> [!NOTE]
> Výše uvedené limity obsahu se mohou změnit před veřejným vydáním.

## <a name="learn-more"></a>Další informace

* [Reference k rozhraní API pro překladatele V3](../reference/v3-0-reference.md)
* [Podpora jazyků](../language-support.md)
* [Předplatná v Azure API Management](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření přizpůsobeného jazykového systému pomocí vlastního překladatele](../custom-translator/overview.md)
>
>