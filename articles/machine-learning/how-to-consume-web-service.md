---
title: Vytvořit klienta pro model nasazený jako webová služba
titleSuffix: Azure Machine Learning
description: Zjistěte, jak volat koncový bod webové služby, který byl vygenerován při nasazení modelu z Azure Machine Learning. Koncový bod zpřístupňuje rozhraní REST API, které můžete volat k provedení odvození s modelem. Vytvořte klienty pro toto rozhraní API pomocí programovacího jazyka podle vašeho výběru.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 0222b63323c4e546628d790fabb881eba006494e
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383395"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Využití modelu Azure Machine Learning nasazeného jako webová služba
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Nasazení modelu Azure Machine Learning jako webové služby vytvoří koncový bod rozhraní REST API. Můžete odeslat data do tohoto koncového bodu a přijímat předpověď vrácené modelem. V tomto dokumentu se dozvíte, jak vytvořit klienty pro webovou službu pomocí C#, Go, Java a Python.

Webovou službu vytvoříte při nasazení modelu do místního prostředí, instance kontejnerů Azure, služby Azure Kubernetes service nebo pole programovatelných polí brány (FPGA). Identifikátor URI používaný pro přístup k webové službě načtete pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Pokud je ověřování povoleno, můžete také použít sadu SDK k získání ověřovacích klíčů nebo tokenů.

Obecný pracovní postup pro vytvoření klienta, který používá webovou službu strojového učení, je:

1. Pomocí sady SDK získáte informace o připojení.
1. Určete typ dat požadavku používaných modelem.
1. Vytvořte aplikaci, která volá webovou službu.

> [!TIP]
> Příklady v tomto dokumentu jsou ručně vytvořeny bez použití specifikací OpenAPI (Swagger). Pokud jste povolili specifikaci OpenAPI pro vaše nasazení, můžete použít nástroje, jako je [například swagger-codegen](https://github.com/swagger-api/swagger-codegen) k vytvoření klientských knihoven pro vaši službu.

## <a name="connection-information"></a>Informace o připojení

> [!NOTE]
> K získání informací o webové službě použijte stesku Azure Machine Learning SDK. Toto je sada Python SDK. K vytvoření klienta pro službu můžete použít libovolný jazyk.

Třída [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) poskytuje informace, které potřebujete k vytvoření klienta. Následující `Webservice` vlastnosti jsou užitečné pro vytvoření klientské aplikace:

* `auth_enabled`- Pokud je povoleno `True`ověřování pomocí klíče, ; v `False`opačném případě .
* `token_auth_enabled`- Pokud je povoleno `True`ověřování tokenu, ; v `False`opačném případě .
* `scoring_uri`- Adresa REST API.
* `swagger_uri`- Adresa specifikace OpenAPI. Tento identifikátor URI je k dispozici, pokud jste povolili automatické generování schématu. Další informace najdete [v tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

Tyto informace pro nasazené webové služby lze získat třemi způsoby:

* Při nasazení modelu je `Webservice` vrácen objekt s informacemi o službě:

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* Můžete načíst `Webservice.list` seznam nasazených webových služeb pro modely ve vašem pracovním prostoru. Můžete přidat filtry zúžit seznam vrácených informací. Další informace o tom, co lze filtrovat, naleznete v referenční dokumentaci [webservice.list.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py)

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* Pokud znáte název nasazené služby, můžete vytvořit novou `Webservice`instanci a poskytnout pracovní prostor a název služby jako parametry. Nový objekt obsahuje informace o nasazené službě.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

### <a name="secured-web-service"></a>Zabezpečená webová služba

Pokud jste zabezpečili nasazenou webovou službu pomocí certifikátu TLS/SSL, můžete [se](https://en.wikipedia.org/wiki/HTTPS) ke službě připojit pomocí identifikátoru URI pro vyhodnocování nebo naparování. Protokol HTTPS pomáhá zabezpečit komunikaci mezi klientem a webovou službou šifrováním komunikace mezi těmito dvěma. Šifrování používá [zabezpečení transportní vrstvy (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS je někdy stále označován jako *SSL (Secure Sockets L),* což byl předchůdce TLS.

> [!IMPORTANT]
> Webové služby nasazené Azure Machine Learning podporují jenom TLS verze 1.2. Při vytváření klientské aplikace se ujistěte, že tuto verzi podporuje.

Další informace najdete [v tématu Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md).

### <a name="authentication-for-services"></a>Ověřování služeb

Azure Machine Learning poskytuje dva způsoby řízení přístupu k webovým službám.

|Metoda ověřování|Aci|AKS|
|---|---|---|
|Klíč|Ve výchozím nastavení zakázáno| Ve výchozím nastavení povolená|
|Podpisový| Není k dispozici| Ve výchozím nastavení zakázáno |

Při odesílání požadavku na službu, která je zabezpečena klíčem nebo tokenem, použijte hlavičku __Autorizace__ k předání klíče nebo tokenu. Klíč nebo token musí být `Bearer <key-or-token>`formátován jako , kde `<key-or-token>` je hodnota klíče nebo tokenu.

#### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud povolíte ověřování pro nasazení, automaticky vytvoříte ověřovací klíče.

* Ověřování je ve výchozím nastavení povoleno při nasazování do služby Azure Kubernetes.
* Ověřování je ve výchozím nastavení zakázáno při nasazování do instancí kontejneru Azure.

Chcete-li řídit `auth_enabled` ověřování, použijte parametr při vytváření nebo aktualizaci nasazení.

Pokud je ověřování povoleno, `get_keys` můžete pomocí této metody načíst primární a sekundární ověřovací klíč:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)klíč, použijte .

#### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Když povolíte ověřování tokenů pro webovou službu, uživatel musí poskytnout token JWT Azure Machine Learning webové službě, aby k ní měl přístup. 

* Ověřování tokenů je ve výchozím nastavení zakázáno při nasazování do služby Azure Kubernetes.
* Ověřování tokenů není podporováno při nasazování do instancí kontejneru Azure.

Chcete-li řídit ověřování `token_auth_enabled` tokenů, použijte parametr při vytváření nebo aktualizaci nasazení.

Pokud je povoleno ověřování tokenů, můžete použít metodu `get_token` k načtení nosné tokenu a že tokeny vypršení platnosti:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Budete muset požádat o nový token `refresh_by` po uplynutí doby tokenu. 

## <a name="request-data"></a>Údaje o žádosti

Rozhraní REST API očekává, že tělo požadavku bude dokument JSON s následující strukturou:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Struktura dat musí odpovídat tomu, co očekává skript a model hodnocení ve službě. Bodovací skript může změnit data před předáním modelu.

Například model v [Train v příkladu notebooku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) očekává pole 10 čísel. Bodovací skript pro tento příklad vytvoří pole Numpy z požadavku a předá jej modelu. Následující příklad ukazuje data, která tato služba očekává:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
```

Webová služba může přijímat více sad dat v jednom požadavku. Vrátí dokument JSON obsahující pole odpovědí.

### <a name="binary-data"></a>Binární data

Informace o povolení podpory binárních dat ve službě naleznete v [tématu Binární data](how-to-deploy-and-where.md#binary).

> [!TIP]
> Povolení podpory binárních dat probíhá v souboru score.py používaném nasazeným modelem. Z klienta použijte funkci HTTP programovacího jazyka. Například následující úryvek odešle obsah souboru JPG webové službě:
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>Sdílení zdrojů mezi zdroji (CORS)

Informace o povolení podpory CORS ve vaší službě naleznete v [tématu sdílení prostředků mezi zdroji](how-to-deploy-and-where.md#cors).

## <a name="call-the-service-c"></a>Volání služby (C#)

Tento příklad ukazuje, jak pomocí jazyka C# volat webovou službu vytvořenou z příkladu [Train v poznámkovém bloku:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

Vrácené výsledky jsou podobné následujícímu dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Zavolejte službu (Go)

Tento příklad ukazuje, jak použít Go k volání webové služby vytvořené z [train v příkladu poznámkového bloku:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

Vrácené výsledky jsou podobné následujícímu dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Zavolejte službu (Java)

Tento příklad ukazuje, jak používat Jazyk Java k volání webové služby vytvořené z [train v příkladu poznámkového bloku:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

Vrácené výsledky jsou podobné následujícímu dokumentu JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Zavolejte službu (Python)

Tento příklad ukazuje, jak pomocí Pythonu volat webovou službu vytvořenou z [příkladu Train v poznámkovém bloku:](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb)

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Vrácené výsledky jsou podobné následujícímu dokumentu JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Využívání služby v Power BI

Power BI podporuje spotřebu webových služeb Azure Machine Learning a obohacuje data v Power BI o předpovědi. 

Pokud chcete vygenerovat webovou službu, která je podporovaná pro spotřebu v Power BI, musí schéma podporovat formát, který Vyžaduje Power BI. [Přečtěte si, jak vytvořit schéma s podporou Power BI](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#example-entry-script).

Jakmile se webová služba nasadí, bude spotřební z toků dat Power BI. [Zjistěte, jak využívat webovou službu Azure Machine Learning z Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit referenční architekturu pro vyhodnocování Pythonu a modelů hlubokého učení v reálném čase, přejděte do [centra architektury Azure](/azure/architecture/reference-architectures/ai/realtime-scoring-python).
