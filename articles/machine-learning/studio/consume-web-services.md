---
title: Jak používat Azure Machine Learning webové služby | Microsoft Docs
description: Po nasazení machine learning služby mohou být využívány RESTFul webové služby, která je k dispozici jako službu v reálném čase požadavků a odpovědí, nebo jako spuštění služby batch.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.openlocfilehash: efb23500d8c718ff1dc7b59c4cfb2b2d5cdfaccb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Jak používat Azure Machine Learning webové služby

Po nasazení Azure Machine Learning prediktivní model jako webovou službu, můžete odeslat data a získat předpovědi rozhraní REST API. Můžete odeslat data v reálném čase nebo v dávkovém režimu.

Můžete najít další informace o tom, jak vytvořit a nasadit Machine Learning webové služby pomocí nástroje Machine Learning Studio tady:

* Kurz týkající se vytvoření experimentu v nástroji Machine Learning Studio, najdete v části [vytvoření prvního experimentu](create-experiment.md).
* Podrobnosti o tom, jak nasadit webovou službu najdete v tématu [nasazení služby Machine Learning webové](publish-a-machine-learning-web-service.md).
* Další informace o Machine Learning obecně naleznete [dokumentace k centru pro Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Přehled
Pomocí Azure Machine Learning webové služby externí aplikace komunikuje se vyhodnocování model Machine Learning pracovního postupu v reálném čase. Volání Machine Learning webové služby vrátí výsledky předpovědi externí aplikací. Chcete-li volání Machine Learning webové služby, předáte klíč rozhraní API, která se vytvoří při nasazení předpovědi. Machine Learning webové služby je založena na REST, možnost populární architektury pro webové projekty programování.

Azure Machine Learning zahrnuje dva typy služeb:

* Požadavků a odpovědí služby (záznamy RR) – s nízkou latencí, vysoce škálovatelná služba, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené z Machine Learning Studio.
* Spuštění služby Batch (BES) – asynchronní služby tohoto skóre a dávky pro datových záznamů.

Další informace o Machine Learning webových služeb najdete v tématu [nasazení služby Machine Learning webové](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Získání klíče autorizace Azure Machine Learning
Když nasadíte experimentu, klíče rozhraní API se generují pro webovou službu. Klíče můžete načíst z několika umístění.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portálu Microsoft Azure Machine Learning webové služby
Přihlaste se k [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net) portálu.

Načíst klíč rozhraní API pro nové Machine Learning webové služby:

1. Na portálu webové služby Azure Machine Learning, klikněte na tlačítko **webové služby** v hlavní nabídce.
2. Klikněte na webovou službu, pro který chcete načíst klíč.
3. V horní nabídce klikněte na tlačítko **spotřebě**.
4. Zkopírujte a uložte **primární klíč**.

Načíst klíč rozhraní API pro Classic Machine Learning webové služby:

1. Na portálu webové služby Azure Machine Learning, klikněte na tlačítko **Classic webové služby** v hlavní nabídce.
2. Klikněte na webovou službu, se kterým pracujete.
3. Klikněte na koncový bod, pro který chcete načíst klíč.
4. V horní nabídce klikněte na tlačítko **spotřebě**.
5. Zkopírujte a uložte **primární klíč**.

### <a name="classic-web-service"></a>Classic webové služby
 Můžete také načíst klíč pro Classic webové služby ze Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. V nástroji Machine Learning Studio, klikněte na tlačítko **webové služby** na levé straně.
2. Klikněte na webovou službu. **Klíč rozhraní API** na **řídicí panel** kartě.

## <a id="connect"></a>Připojení k webové služby Machine Learning
Můžete připojit k službě Machine Learning webové pomocí programovací jazyk, který podporuje žádostí HTTP a odpovědí. Příklady můžete zobrazit v C#, Python a R z Machine Learning webové stránky nápovědy služby.

**Počítač Learning API nápovědy** Machine Learning API nápovědy se vytvoří při nasazení webové služby. V tématu [Azure Machine Learning návod - nasazení webové služby](walkthrough-5-publish-web-service.md).
Machine Learning API nápovědy obsahuje podrobnosti o předpovědi webové služby.

1. Klikněte na webovou službu, se kterým pracujete.
2. Klikněte na koncový bod, pro který chcete zobrazit stránce nápovědy k rozhraní API.
3. V horní nabídce klikněte na tlačítko **spotřebě**.
4. Klikněte na tlačítko **stránku nápovědy rozhraní API** pod buď požadavků a odpovědí nebo Batch Execution koncových bodů.

**Zobrazení Machine Learning API pomoci pro novou webovou službu**

V [Azure Machine Learning webové služby portálu](https://services.azureml.net/):

1. Klikněte na tlačítko **webové služby** v horní nabídce.
2. Klikněte na webovou službu, pro který chcete načíst klíč.

Klikněte na tlačítko **pomocí webové služby** získat identifikátory URI pro požadavek Reposonse a spuštění služby Batch a ukázkový kód v jazyce C#, R a Python.

Klikněte na tlačítko **rozhraní API Swaggeru** získat Swagger základě dokumentace pro rozhraní API volat z zadané identifikátory URI.

### <a name="c-sample"></a>Ukázka C#
Pro připojení k webové služby Machine Learning, použijte **HttpClient** předávání ScoreData. ScoreData obsahuje FeatureVector, n dimenzí vektor číselné funkce představující ScoreData. Ověření ke službě Machine Learning s klíčem rozhraní API.

Pro připojení k Machine Learning webové služby, **Microsoft.AspNet.WebApi.Client** musí být nainstalován balíček NuGet.

**Nainstalovat Microsoft.AspNet.WebApi.Client NuGet v sadě Visual Studio**

1. Publikování datovou sadu stáhnout z UCI: dataset – třída pro dospělé 2 webové služby.
2. Klikněte na **Nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
3. Zvolte **Install-Package Microsoft.AspNet.WebApi.Client**.

**Ke spuštění ukázka kódu**

1. Publikování "Příklad 1: Stáhněte datové sady z UCI: datové sady pro dospělé 2 – třída" experiment, součástí kolekce ukázka Machine Learning.
2. Přiřaďte apiKey klíčem z webové služby. V tématu **získání klíče autorizace Azure Machine Learning** výše.
3. Přiřaďte serviceUri s identifikátor URI požadavku.

**Zde je, jak bude vypadat dokončení požadavku.**
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

### <a name="python-sample"></a>Ukázka Pythonu
Pro připojení k webové služby Machine Learning, použijte **urllib2** knihovny pro jazyk Python 2.X a **urllib.request** knihovny pro jazyk Python 3.X. Předá ScoreData, který obsahuje FeatureVector, n dimenzí vektor číselné funkce představující ScoreData. Ověření ke službě Machine Learning s klíčem rozhraní API.

**Ke spuštění ukázka kódu**

1. Nasadit "Příklad 1: Stáhněte datové sady z UCI: datové sady pro dospělé 2 – třída" experiment, součástí kolekce ukázka Machine Learning.
2. Přiřaďte apiKey klíčem z webové služby. Najdete v článku **získání klíče autorizace Azure Machine Learning** části téměř začátku tohoto článku.
3. Přiřaďte serviceUri s identifikátor URI požadavku.

**Zde je, jak bude vypadat dokončení požadavku.**
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

### <a name="r-sample"></a>Ukázka R

Chcete-li se připojit k webové službě Machine Learning, použijte **RCurl** a **rjson** knihovny a požadavek zpracovat vrácená odpověď JSON. Předá ScoreData, který obsahuje FeatureVector, n dimenzí vektor číselné funkce představující ScoreData. Ověření ke službě Machine Learning s klíčem rozhraní API.

**Zde je, jak bude vypadat dokončení požadavku.**
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

### <a name="javascript-sample"></a>Ukázka JavaScript

Pro připojení k webové službě Machine Learning, použijte **požadavku** balíčku npm ve vašem projektu. Budete také používat `JSON` objekt do formátu váš vstup a analyzovat výsledek. Instalace s `npm install request --save`, nebo přidejte `"request": "*"` k package.json pod `dependencies` a spusťte `npm install`.

**Zde je, jak bude vypadat dokončení požadavku.**
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