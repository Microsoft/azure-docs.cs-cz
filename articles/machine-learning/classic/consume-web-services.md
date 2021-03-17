---
title: 'ML Studio (Classic): využívání webových služeb – Azure'
description: Po nasazení služby Machine Learning z Azure Machine Learning Studio (Classic) je možné webovou službu RESTFul spotřebovat buď jako službu požadavků a odpovědí v reálném čase, nebo jako na službu spuštění služby Batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 0dc49265c0ea799e194e4ac7004b558d8a9d4dd8
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519265"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Jak využívat webovou službu Machine Learning Studio (Classic)

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic).  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic)   ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Po nasazení prediktivního modelu Azure Machine Learning Studio (Classic) jako webové služby můžete použít REST API k odeslání dat IT a získání předpovědi. Data můžete odeslat v reálném čase nebo v dávkovém režimu.

Další informace o tom, jak vytvořit a nasadit webovou službu Machine Learning pomocí Machine Learning Studio (Classic), najdete tady:

* Kurz o tom, jak vytvořit experiment v Machine Learning Studio (Classic), najdete v tématu [Vytvoření prvního experimentu](create-experiment.md).
* Podrobnosti o tom, jak nasadit webovou službu, najdete v tématu [nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md).
* Další informace o Machine Learning obecně najdete v [centru dokumentace Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Přehled
U Azure Machine Learning webové služby komunikuje externí aplikace s modelem bodování Machine Learning pracovního postupu v reálném čase. Volání webové služby Machine Learning vrátí výsledky předpovědi do externí aplikace. Chcete-li provést volání webové služby Machine Learning, předejte klíč rozhraní API, který je vytvořen při nasazení předpovědi. Webová služba Machine Learning je založena na REST, na základě oblíbené architektury pro projekty webového programování.

Azure Machine Learning Studio (Classic) má dva typy služeb:

* Služba Request-Response (RR) – nízká latence, vysoce škálovatelná služba, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené z Machine Learning Studio (Classic).
* Služba batch execution (BES) – asynchronní služba, která vyrovnává dávku datových záznamů.

Další informace o Machine Learning webových služeb najdete v tématu [nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Získat autorizační klíč
Při nasazení experimentu se vygenerují klíče rozhraní API pro webovou službu. Klíče můžete načíst z několika míst.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portálu Microsoft Azure Machine Learning Web Services
Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) .

Načtení klíče rozhraní API pro novou Machine Learning webovou službu:

1. Na portálu Azure Machine Learning Web Services klikněte v horní nabídce na **webové služby** .
2. Klikněte na webovou službu, pro kterou chcete klíč načíst.
3. V horní nabídce klikněte na možnost **spotřebovat**.
4. Zkopírujte a uložte **primární klíč**.

Načtení klíče rozhraní API pro klasický Machine Learning webové služby:

1. Na portálu Azure Machine Learning Web Services klikněte v horní nabídce na **klasické webové služby** .
2. Klikněte na webovou službu, se kterou pracujete.
3. Klikněte na koncový bod, pro který chcete klíč načíst.
4. V horní nabídce klikněte na možnost **spotřebovat**.
5. Zkopírujte a uložte **primární klíč**.

### <a name="classic-web-service"></a>Klasická webová služba
 Můžete také načíst klíč pro klasickou webovou službu z Machine Learning Studio (Classic).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (Classic)
1. V Machine Learning Studio (Classic) klikněte vlevo na **webové služby** .
2. Klikněte na webovou službu. **Klíč rozhraní API** je na kartě **řídicí panel** .

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Připojení k webové službě Machine Learning
K webové službě Machine Learning se můžete připojit pomocí libovolného programovacího jazyka, který podporuje požadavky a odpověď HTTP. Příklady můžete zobrazit v jazycích C#, Python a R na stránce s přehledem Machine Learning webové služby.

**Machine Learning nápovědě k rozhraní API** Při nasazení webové služby se vytvoří Help Machine Learning API. Viz [kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
Nápovědu k rozhraní Machine Learning API obsahuje podrobnosti o předpovědi webové služby.

1. Klikněte na webovou službu, se kterou pracujete.
2. Klikněte na koncový bod, pro který chcete zobrazit stránku s nápovědu k rozhraní API.
3. V horní nabídce klikněte na možnost **spotřebovat**.
4. Klikněte na **stránku s nápovědě k rozhraní API** pod koncovými body spuštění Request-Response nebo dávky.

**Zobrazení Machine Learning nápovědu k rozhraní API pro novou webovou službu**

Na [portálu Azure Machine Learning Web Services](https://services.azureml.net/):

1. V horní nabídce klikněte na **webové služby** .
2. Klikněte na webovou službu, pro kterou chcete klíč načíst.

Kliknutím na možnost **použít webovou službu** získáte identifikátory uri pro Request-Response a dávkové spouštění služby a ukázkový kód v jazycích C#, R a Python.

Kliknutím na **rozhraní Swagger API** získáte dokumentaci na bázi Swagger pro rozhraní API volaná ze zadaných identifikátorů URI.

### <a name="c-sample"></a>Ukázka jazyka C#
Pokud se chcete připojit k webové službě Machine Learning, použijte ScoreData předávání **HttpClient** . ScoreData obsahuje FeatureVector a n-dimenzionální vektor číselných funkcí, které představují ScoreData. Pomocí klíče rozhraní API se ověříte na službu Machine Learning.

Chcete-li se připojit k webové službě Machine Learning, musí být nainstalován balíček NuGet **Microsoft. ASPNET. WebApi. Client** .

**Instalace sady Microsoft. AspNet. WebApi. Client NuGet v aplikaci Visual Studio**

1. Publikujte sadu stažení dat z Ski: webová služba třídy DataSet pro dospělé 2.
2. Klikněte na **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
3. Vyberte **Install-Package Microsoft. ASPNET. WebApi. Client**.

**Spuštění ukázky kódu**

1. Publikování "ukázka 1: stažení datové sady z webu UCI:" sada datových sad pro dospělé 2 "experiment, součást kolekce ukázek Machine Learning.
2. Přiřaďte apiKey k klíči z webové služby. Viz **získat autorizační klíč** výše.
3. Přiřaďte serviceUri k identifikátoru URI žádosti.

**Tady je, jak bude vypadat kompletní požadavek.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Ukázka Pythonu
Chcete-li se připojit k webové službě Machine Learning, použijte knihovnu **urllib2** pro Python 2. x a **urllib. Request** Library for Python 3. x. Předáte ScoreData, který obsahuje FeatureVector, n multidimenzionální vektor číselných funkcí, které představují ScoreData. Pomocí klíče rozhraní API se ověříte na službu Machine Learning.

**Spuštění ukázky kódu**

1. Nasaďte "Sample 1: Stáhněte si sadu dat z Ski:" sada datových sad pro dospělé 2 "experimenty, část kolekce ukázek Machine Learning.
2. Přiřaďte apiKey k klíči z webové služby. Viz část **získání autorizačního klíče** v blízkosti začátku tohoto článku.
3. Přiřaďte serviceUri k identifikátoru URI žádosti.

**Tady je, jak bude vypadat kompletní požadavek.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Ukázka R

Chcete-li se připojit k webové službě Machine Learning, pomocí knihoven **RCurl** a **rjson** proveďte požadavek a ZPRACUJTE vrácenou odpověď JSON. Předáte ScoreData, který obsahuje FeatureVector, n multidimenzionální vektor číselných funkcí, které představují ScoreData. Pomocí klíče rozhraní API se ověříte na službu Machine Learning.

**Tady je, jak bude vypadat kompletní požadavek.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>Ukázka JavaScriptu

Chcete-li se připojit k webové službě Machine Learning, použijte balíček **Request** npm v projektu. `JSON`K formátování vstupu a analýze výsledku použijete také objekt. `npm install request --save` `"request": "*"` V části a spusťte instalaci pomocí nebo přidejte do svého package.js`dependencies` `npm install` .

**Tady je, jak bude vypadat kompletní požadavek.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```