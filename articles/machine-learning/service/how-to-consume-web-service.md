---
title: Zacházení s nasazením webových služeb - Azure Machine Learning
description: Informace o využívání webové služby vytvořit a nasadit model ve službě Azure Machine Learning. Model ve službě Azure Machine Learning nasazení vytvoří webové služby, který zpřístupňuje rozhraní REST API. Můžete vytvořit klienty pro toto rozhraní API pomocí programovacím jazyce podle vašeho výběru. V tomto dokumentu se dozvíte, jak pro přístup k rozhraní API pomocí Pythonu a C#.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: larryfr
ms.date: 10/30/2018
ms.openlocfilehash: 2af954f14f7113a1f6214bf5e9235933312bbf02
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51347674"
---
# <a name="how-to-consume-an-azure-ml-model-deployed-as-a-web-service"></a>Jak využívat model ve službě Azure ML nasadit jako webovou službu

Rozhraní REST API Azure Machine Learning modelu jako webové služby vytvoří. Můžete odesílat data do tohoto rozhraní API a přijímat předpovědi vrácený modelu. V tomto dokumentu, zjistěte, jak vytvořit klientů pro webovou službu pomocí C#, Go, Java nebo Python.

Webová služba se vytvoří při nasazení image do Azure Container Instance, Azure Kubernetes Service nebo Project Brainwave (pole programmable gate Array). Vytváření imagí z registrované modely a soubory vyhodnocení. Identifikátor URI použitý pro přístup k webové službě se dá načíst pomocí [SDK služby Azure Machine Learning](https://docs.microsoft.com/en-us/python/api/overview/azure/ml/intro?view=azure-ml-py). Pokud je povolené ověřování, můžete také použít sady SDK získat ověřovací klíče.

Obecný pracovní postup při vytváření klienta, který používá webové služby ML je:

1. Použití sady SDK k získání informací o připojení
1. Určit typ používá model dat požadavku
1. Vytvoření aplikace, která volá webové služby

## <a name="connection-information"></a>Informace o připojení

> [!NOTE]
> Sada SDK Azure Machine Learning slouží k získání informací o webové služby. Toto je Python SDK. Když se používá k načtení informací o webových službách, můžou používat jakýkoli jazyk se vytvořit klienta pro službu.

Informace o připojení webové služby můžete načíst pomocí sady SDK služby Azure Machine Learning. [Azureml.core.Webservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) třída poskytuje informace potřebné k vytvoření klienta. Následující `Webservice` vlastnosti, které jsou užitečné při vytváření klientské aplikace:

* `auth_enabled` – Pokud je povoleno ověřování, `True`; v opačném případě `False`.
* `scoring_uri` Adresa – rozhraní REST API.

Existují tři způsoby pro načtení těchto informací pro nasazené webové služby:

* Při nasazení modelu, `Webservice` je vrácen objekt s informace o službě:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* Můžete použít `Webservice.list` k načtení seznamu nasazené webové služby pro modely v pracovním prostoru. Můžete přidat filtry k zúžení seznamu vrácených informací. Další informace o tom, dají se filtrovat v, najdete v článku [Webservice.list](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#list) referenční dokumentaci.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Pokud znáte název v nasazované službě, můžete vytvořit novou instanci třídy `Webservice` a zadejte název pracovního prostoru a služby jako parametry. Nový objekt obsahuje informace o nasazené služby.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-key"></a>Ověřovací klíč

Ověřovací klíče se vytvoří automaticky při nasazení je povolené ověřování.

* Ověřování je __ve výchozím nastavení povolené__ při nasazování do __Azure Kubernetes Service__.
* Ověřování je __ve výchozím nastavení zakázané__ při nasazování do __Azure container Instances__.

Pokud chcete nastavit ověřování, použijte `auth_enabled` parametr, když se vytváří nebo aktualizuje nasazení.

Pokud je ověřování zapnuté, můžete použít `get_keys` metody k získání primární a sekundární ověřovací klíč:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud je potřeba znovu vygenerovat klíč, použijte [ `service.regen_key` ](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#regen-key).

## <a name="request-data"></a>Data žádosti

Rozhraní REST API očekává, že text žádosti jako dokument JSON s následující strukturou:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> Strukturu dat musí odpovídat jaké hodnoticí skript a modelu v expect služby. Hodnoticí skript může upravit data před předáním do modelu.

Například modelu v [trénování v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) příklad očekává, že pole 10 čísel. Hodnoticí skript v tomto příkladu vytvoří pole Numpy z požadavku a předává je do modelu. Následující příklad ukazuje data, která očekává, že tato služba:

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

Webová služba může přijmout víc kopií dat v jedné žádosti. Vrátí dokument JSON obsahující pole s odpovědí.

## <a name="call-the-service-c"></a>Volání služby (C#)

Tento příklad ukazuje, jak používat C# k volání webové služby vytvořené z [trénování v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) příkladu:

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
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

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

Vrácené výsledky jsou podobné následující dokument JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Volání služby (Přejít)

Tento příklad ukazuje použití jazyka Go k volání webové služby vytvořené z [trénování v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) příkladu:

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
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

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

Vrácené výsledky jsou podobné následující dokument JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Volání služby (Java)

Tento příklad ukazuje použití Javy k volání webové služby vytvořené z [trénování v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) příkladu:

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
        // If using authentication, replace with the auth key
        String key = "<your key>";
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

Vrácené výsledky jsou podobné následující dokument JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Volání služby (Python)

Tento příklad ukazuje použití Pythonu k volání webové služby vytvořené z [trénování v rámci poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/01.train-within-notebook) příkladu:

```python
import requests
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

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
headers = { 'Content-Type':'application/json' }
# If authentication is enabled, set the authorization header
headers['Authorization']=f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers = headers)
print(resp.text)

```

Vrácené výsledky jsou podobné následující dokument JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit klienta pro nasazenou model, zjistěte, jak [nasazení modelu do zařízení IoT Edge](how-to-deploy-to-iot.md).