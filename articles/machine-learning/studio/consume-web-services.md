---
title: Používání webové služby – Azure Machine Learning Studio | Dokumentace Microsoftu
description: Po nasazení služby machine learning service z Azure Machine Learning Studio RESTFul webová služba může zpracovat buď jako službu v reálném čase žádost odpověď, nebo jako služba batch execution.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.openlocfilehash: 7f1950afc8e84f0bfeba7d51aa68ee126db38e2e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075127"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-web-service"></a>Způsob využívání webové služby Azure Machine Learning Studio

Po nasazení Azure Machine Learning prediktivní model jako webovou službu můžete použít rozhraní REST API k odeslání dat a získání předpovědi. Můžete odeslat data v reálném čase nebo v dávkovém režimu.

Můžete najít další informace o tom, jak vytvořit a nasadit služby Machine Learning Web pomocí Machine Learning Studio tady:

* Kurz o tom, jak vytvořit nový experiment v Machine Learning Studio, najdete v tématu [vytvoření prvního experimentu](create-experiment.md).
* Podrobnosti o tom, jak nasadit webovou službu, najdete v článku [nasazení služby Machine Learning Web](publish-a-machine-learning-web-service.md).
* Další informace o službě Machine Learning, přejděte [centru dokumentace Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Přehled
Se službou Azure Machine Learning Web externí aplikace komunikuje s hodnoticí modelem Machine Learning pracovních postupů v reálném čase. Volání služby Web Microsoft Machine Learning vrací do externí aplikace predikované výsledky. Volání služby Machine Learning Web, předáním klíče rozhraní API, který je vytvořen při nasazování predikcí. Služba Web Microsoft Machine Learning je založená na REST, možnost Oblíbené architektuře programátorských projektů na webu.

Azure Machine Learning zahrnuje dva typy služeb:

* Služba Request-Response (RRS) – má nízkou latenci, vysoce škálovatelná služba, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené z nástroje Machine Learning Studio.
* Služba batch Execution (BES) – asynchronní služba pro vyhodnocování dávek datových záznamů.

Další informace o službě Machine Learning Web services najdete v tématu [nasazení služby Machine Learning Web](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Získání autorizačního klíče Azure Machine Learning
Při nasazení experimentu, vygenerují se klíče rozhraní API pro webovou službu. Načtení klíčů v několika umístěních.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portálu Microsoft Azure Machine Learning Web Services
Přihlaste se k [Microsoft Azure Machine Learning Web Services](https://services.azureml.net) portálu.

Načíst klíč rozhraní API pro službu Web Microsoft Learning nový počítač:

1. Na portálu Azure Machine Learning Web Services, klikněte na tlačítko **webových služeb** horní nabídce.
2. Klikněte na webovou službu, pro které chcete načíst klíč.
3. V horní nabídce klikněte na tlačítko **spotřebovat**.
4. Zkopírujte a uložte **primární klíč**.

Načíst klíč rozhraní API pro službu Web Microsoft Learning klasické počítače:

1. Na portálu Azure Machine Learning Web Services, klikněte na tlačítko **klasické webové služby** horní nabídce.
2. Klikněte na webovou službu, se kterým pracujete.
3. Klikněte na koncový bod, pro které chcete načíst klíč.
4. V horní nabídce klikněte na tlačítko **spotřebovat**.
5. Zkopírujte a uložte **primární klíč**.

### <a name="classic-web-service"></a>Klasické webové služby
 V nástroji Machine Learning Studio můžete také načíst klíč pro klasickou webovou službou.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. V nástroji Machine Learning Studio, klikněte na tlačítko **webových služeb** na levé straně.
2. Klikněte na webovou službu. **Klíč rozhraní API** nachází **řídicí panel** kartu.

## <a id="connect"></a>Připojení ke službě Machine Learning Web
Můžete připojit ke službě Machine Learning Web použitím libovolného programovacího jazyka, který podporuje HTTP požadavku a odpovědi. Můžete zobrazit příklady v C#, Pythonu a r. od stránce nápovědy služby Machine Learning Web.

**Machine Learning API nápovědy** nápovědy Machine Learning API se vytvoří při nasazení webové služby. Zobrazit [Azure Machine Learning názorný postup nasazení webové služby](walkthrough-5-publish-web-service.md).
Machine Learning API nápovědy obsahuje podrobnosti o predikcí webové služby.

1. Klikněte na webovou službu, se kterým pracujete.
2. Klikněte na koncový bod, pro kterou chcete zobrazit stránce nápovědy k API.
3. V horní nabídce klikněte na tlačítko **spotřebovat**.
4. Klikněte na tlačítko **stránku nápovědy API** v části koncové body typu žádost-odpověď nebo spuštění dávky.

**Zobrazit rozhraní API pro Machine Learning nápovědy pro novou webovou službu**

V [Azure Machine Learning Web Services portálu](https://services.azureml.net/):

1. Klikněte na tlačítko **webových služeb** v horní nabídce.
2. Klikněte na webovou službu, pro které chcete načíst klíč.

Klikněte na tlačítko **použijte webovou službu** zobrazíte identifikátory URI pro požadavek Reposonse a spuštění služby Batch a ukázkový kód v C#, R a Python.

Klikněte na tlačítko **rozhraní API Swaggeru** na získání Swaggeru podle dokumentace pro rozhraní API volat ze zadaných identifikátorů URI.

### <a name="c-sample"></a>Ukázka v jazyce C#
Pro připojení ke službě Machine Learning Web, použijte **HttpClient** předávání ScoreData. ScoreData obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

Připojení ke službě Machine Learning Web, **Microsoft.AspNet.WebApi.Client** musí být nainstalován balíček NuGet.

**Nainstalovat Microsoft.AspNet.WebApi.Client NuGet v sadě Visual Studio**

1. Publikování datové sady ke stažení z UCI: Třída dataset dospělé 2 webové služby.
2. Klikněte na **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
3. Zvolte **Install-Package Microsoft.AspNet.WebApi.Client**.

**Ke spuštění ukázky kódu**

1. Publikování "Příklad 1: stáhnout datovou sadu z UCI: Třída dataset Adult 2" experiment, součástí kolekce ukázky Machine learningu.
2. Přiřaďte apiKey klíčem z webové služby. Zobrazit **získání autorizačního klíče Azure Machine Learning** výše.
3. Přiřaďte identifikátorem URI služby s identifikátorem URI žádosti.

**Tady je úplný požadavek bude vypadat.**
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
                // if you are calling this code from the UI thread of an ASP.Net application.
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

                    // Print the headers - they include the requert ID and the timestamp,
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
Pro připojení ke službě Machine Learning Web, použijte **urllib2** knihovny pro Python 2.X a **urllib.request** knihovny pro Python 3.X. Předá ScoreData, který obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

**Ke spuštění ukázky kódu**

1. Nasazení "Příklad 1: stáhnout datovou sadu z UCI: Třída dataset Adult 2" experiment, součástí kolekce ukázky Machine learningu.
2. Přiřaďte apiKey klíčem z webové služby. Zobrazit **získání autorizačního klíče Azure Machine Learning** v začátku části tohoto článku.
3. Přiřaďte identifikátorem URI služby s identifikátorem URI žádosti.

**Tady je úplný požadavek bude vypadat.**
```python
import urllib2 # urllib.request for Python 3.X
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

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Ukázky jazyka R

Chcete-li se připojit k webové službě Machine Learning, použijte **RCurl** a **rjson** knihovny k odeslání požadavku a zpracování vrácená odpověď JSON. Předá ScoreData, který obsahuje FeatureVector, n rozměrný vektor číselné funkce, která představuje ScoreData. Ověření ve službě Machine Learning pomocí klíče rozhraní API.

**Tady je úplný požadavek bude vypadat.**
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

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Ukázky jazyka JavaScript

Chcete-li se připojit k webové službě Machine Learning, použijte **požadavek** balíčku npm ve vašem projektu. Budete taky používat `JSON` objekt pro formátování svůj vstup a výsledek analyzovat. Nainstalovat s použitím `npm install request --save`, nebo přidejte `"request": "*"` do souboru package.json ve skupinovém rámečku `dependencies` a spusťte `npm install`.

**Tady je úplný požadavek bude vypadat.**
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