---
title: Spotřebovat webovou službu
titleSuffix: ML Studio (classic) - Azure
description: Po nasazení služby Machine Learning z Azure Machine Learning Studio (Classic) je možné webovou službu RESTFul spotřebovat buď jako službu požadavků a odpovědí v reálném čase, nebo jako na službu spuštění služby Batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: de395f7167f0ab7d7c6429c14d5efce46831b576
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218241"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Jak využívat webovou službu Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Po nasazení prediktivního modelu Azure Machine Learning Studio (Classic) jako webové služby můžete použít REST API k odeslání dat IT a získání předpovědi. Můžete odeslat data v reálném čase nebo v dávkovém režimu.

Další informace o tom, jak vytvořit a nasadit webovou službu Machine Learning pomocí Machine Learning Studio (Classic), najdete tady:

* Kurz o tom, jak vytvořit experiment v Machine Learning Studio (Classic), najdete v tématu [Vytvoření prvního experimentu](create-experiment.md).
* Podrobnosti o tom, jak nasadit webovou službu, najdete v tématu [nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md).
* Další informace o Machine Learning obecně najdete v [centru dokumentace Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Přehled
Se službou Azure Machine Learning Web externí aplikace komunikuje s hodnoticí modelem Machine Learning pracovních postupů v reálném čase. Volání služby Web Microsoft Machine Learning vrací do externí aplikace predikované výsledky. Volání služby Machine Learning Web, předáním klíče rozhraní API, který je vytvořen při nasazování predikcí. Služba Web Microsoft Machine Learning je založená na REST, možnost Oblíbené architektuře programátorských projektů na webu.

Azure Machine Learning Studio (Classic) má dva typy služeb:

* Služba Request-response (RR) – nízká latence, vysoce škálovatelná služba, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené z Machine Learning Studio (Classic).
* Služba batch Execution (BES) – asynchronní služba pro vyhodnocování dávek datových záznamů.

Další informace o Machine Learning webových služeb najdete v tématu [nasazení webové služby Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Získat autorizační klíč
Při nasazení experimentu, vygenerují se klíče rozhraní API pro webovou službu. Načtení klíčů v několika umístěních.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portálu Microsoft Azure Machine Learning Web Services
Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) .

Načíst klíč rozhraní API pro službu Web Microsoft Learning nový počítač:

1. Na portálu Azure Machine Learning Web Services klikněte v horní nabídce na **webové služby** .
2. Klikněte na webovou službu, pro které chcete načíst klíč.
3. V horní nabídce klikněte na možnost **spotřebovat**.
4. Zkopírujte a uložte **primární klíč**.

Načíst klíč rozhraní API pro službu Web Microsoft Learning klasické počítače:

1. Na portálu Azure Machine Learning Web Services klikněte v horní nabídce na **klasické webové služby** .
2. Klikněte na webovou službu, se kterým pracujete.
3. Klikněte na koncový bod, pro které chcete načíst klíč.
4. V horní nabídce klikněte na možnost **spotřebovat**.
5. Zkopírujte a uložte **primární klíč**.

### <a name="classic-web-service"></a>Klasické webové služby
 Můžete také načíst klíč pro klasickou webovou službu z Machine Learning Studio (Classic).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (Classic)
1. V Machine Learning Studio (Classic) klikněte vlevo na **webové služby** .
2. Klikněte na webovou službu. **Klíč rozhraní API** je na kartě **řídicí panel** .

## <a id="connect"></a>Připojení k webové službě Machine Learning
Můžete připojit ke službě Machine Learning Web použitím libovolného programovacího jazyka, který podporuje HTTP požadavku a odpovědi. Můžete zobrazit příklady v C#, Pythonu a r. od stránce nápovědy služby Machine Learning Web.

**Machine Learning nápovědě k rozhraní API** Při nasazení webové služby se vytvoří Help Machine Learning API. Viz [kurz 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
Machine Learning API nápovědy obsahuje podrobnosti o predikcí webové služby.

1. Klikněte na webovou službu, se kterým pracujete.
2. Klikněte na koncový bod, pro kterou chcete zobrazit stránce nápovědy k API.
3. V horní nabídce klikněte na možnost **spotřebovat**.
4. Klikněte na **stránku s nápovědě k rozhraní API** pod koncovými body pro požadavek a odpověď na zpracování dávky.

**Zobrazení Machine Learning nápovědu k rozhraní API pro novou webovou službu**

Na [portálu Azure Machine Learning Web Services](https://services.azureml.net/):

1. V horní nabídce klikněte na **webové služby** .
2. Klikněte na webovou službu, pro které chcete načíst klíč.

Kliknutím na možnost **použít webovou službu** získáte identifikátory URI pro služby požadavku a odpovědi na zpracování dávky a ukázkový kód v C#, R a Pythonu.

Kliknutím na **rozhraní Swagger API** získáte dokumentaci na bázi Swagger pro rozhraní API volaná ze zadaných identifikátorů URI.

### <a name="c-sample"></a>Ukázka v jazyce C#
Pokud se chcete připojit k webové službě Machine Learning, použijte ScoreData předávání **HttpClient** . ScoreData obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

Chcete-li se připojit k webové službě Machine Learning, musí být nainstalován balíček NuGet **Microsoft. ASPNET. WebApi. Client** .

**Instalace sady Microsoft. AspNet. WebApi. Client NuGet v aplikaci Visual Studio**

1. Publikování datové sady ke stažení z UCI: Třída dataset dospělé 2 webové služby.
2. Klikněte na **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.
3. Vyberte **Install-Package Microsoft. ASPNET. WebApi. Client**.

**Spuštění ukázky kódu**

1. Publikování "Příklad 1: stáhnout datovou sadu z UCI: Třída dataset Adult 2" experiment, součástí kolekce ukázky Machine learningu.
2. Přiřaďte apiKey klíčem z webové služby. Viz **získat autorizační klíč** výše.
3. Přiřaďte identifikátorem URI služby s identifikátorem URI žádosti.

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

### <a name="python-sample"></a>Ukázky Pythonu
Chcete-li se připojit k webové službě Machine Learning, použijte knihovnu **urllib2** pro Python 2. x a **urllib. Request** Library for Python 3. x. Předá ScoreData, který obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

**Spuštění ukázky kódu**

1. Nasazení "Příklad 1: stáhnout datovou sadu z UCI: Třída dataset Adult 2" experiment, součástí kolekce ukázky Machine learningu.
2. Přiřaďte apiKey klíčem z webové služby. Viz část **získání autorizačního klíče** v blízkosti začátku tohoto článku.
3. Přiřaďte identifikátorem URI služby s identifikátorem URI žádosti.

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

### <a name="r-sample"></a>Ukázky jazyka R

Chcete-li se připojit k webové službě Machine Learning, pomocí knihoven **RCurl** a **rjson** proveďte požadavek a ZPRACUJTE vrácenou odpověď JSON. Předá ScoreData, který obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

**Tady je, jak bude vypadat kompletní požadavek.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Ukázky jazyka JavaScript

Chcete-li se připojit k webové službě Machine Learning, použijte balíček **Request** npm v projektu. K formátování vstupu a analýze výsledku použijete také objekt `JSON`. Nainstalujte pomocí `npm install request --save`nebo do balíčku. JSON přidejte `"request": "*"` v části `dependencies` a spusťte `npm install`.

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
