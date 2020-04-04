---
title: Využití webové služby
titleSuffix: ML Studio (classic) - Azure
description: Jakmile se služba strojového učení nasadí ze služby Azure Machine Learning Studio (klasické), webová služba RESTFul se dá využívat buď jako službu odezvy na základě požadavků v reálném čase, nebo jako službu dávkového spuštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: b97fe6e55e2c36b6f101071e702952f529146281
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631660"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Jak využívat webovou službu Azure Machine Learning Studio (klasická)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Jakmile nasadíte prediktivní model Azure Machine Learning Studio (klasický) jako webovou službu, můžete použít rozhraní REST API k jeho odeslání dat a získání předpovědi. Data můžete odesílat v reálném čase nebo v dávkovém režimu.

Další informace o tom, jak vytvořit a nasadit webovou službu Machine Learning pomocí Machine Learning Studio (klasika), najdete zde:

* Návod, jak vytvořit experiment ve Studiu strojového učení (klasika), najdete v [tématu Vytvoření prvního experimentu](create-experiment.md).
* Podrobnosti o nasazení webové služby naleznete v [tématu Deploy a Machine Learning Web service](deploy-a-machine-learning-web-service.md).
* Další informace o strojovém učení obecně naleznete v [Centru dokumentace strojového učení](https://azure.microsoft.com/documentation/services/machine-learning/).



## <a name="overview"></a>Přehled
S webovou službou Azure Machine Learning komunikuje externí aplikace s modelem hodnocení pracovního postupu Machine Learning v reálném čase. Volání webové služby Machine Learning vrátí výsledky předpovědi externí aplikaci. Chcete-li volat webovou službu Machine Learning, předáte klíč rozhraní API, který se vytvoří při nasazení předpověď. Webová služba Machine Learning je založena na REST, oblíbené architektuře pro projekty webového programování.

Azure Machine Learning Studio (klasické) má dva typy služeb:

* Služba odezvy na požadavky (RRS) – služba s nízkou latencí a vysokou škálovatelnou službou, která poskytuje rozhraní pro bezstavové modely vytvořené a nasazené ze studia Machine Learning Studio (klasické).
* Služba dávkového spuštění (BES) – asynchronní služba, která získá skóre dávky pro datové záznamy.

Další informace o webových službách Machine Learning naleznete [v tématu Deploy a Machine Learning Web service](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Získání autorizačního klíče
Při nasazení experimentu jsou pro webovou službu generovány klíče rozhraní API. Klíče můžete načíst z několika umístění.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Z portálu Microsoft Azure Machine Learning Web Services
Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services.](https://services.azureml.net)

Chcete-li načíst klíč rozhraní API pro novou webovou službu Machine Learning:

1. Na portálu Azure Machine Learning Web Services klikněte na **Web Services** v horní nabídce.
2. Klepněte na webovou službu, pro kterou chcete klíč načíst.
3. V horní nabídce klikněte na **Konzumujte**.
4. Zkopírujte a uložte **primární klíč**.

Chcete-li načíst klíč rozhraní API pro webovou službu Classic Machine Learning:

1. Na portálu Azure Machine Learning Web Services klikněte na **klasické webové služby** v horní nabídce.
2. Klepněte na webovou službu, se kterou pracujete.
3. Klikněte na koncový bod, pro který chcete klíč načíst.
4. V horní nabídce klikněte na **Konzumujte**.
5. Zkopírujte a uložte **primární klíč**.

### <a name="classic-web-service"></a>Klasická webová služba
 Můžete také načíst klíč pro klasické webové služby z Machine Learning Studio (classic).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (Classic)
1. V Machine Learning Studio (klasické) klikněte na **WEB SERVICES** vlevo.
2. Klepněte na webovou službu. **Klíč rozhraní API** je na kartě ŘÍDICÍ **PANEL.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Připojení k webové službě Machine Learning
K webové službě Machine Learning se můžete připojit pomocí libovolného programovacího jazyka, který podporuje požadavek a odpověď HTTP. Příklady v jazycích C#, Python a R můžete zobrazit na stránce nápovědy webové služby Machine Learning.

**Nápověda k rozhraní API pro strojové učení** Pomoc rozhraní API strojového učení se vytvoří při nasazení webové služby. Viz [kurz 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
Nápověda rozhraní API strojového učení obsahuje podrobnosti o webové službě předpověď.

1. Klepněte na webovou službu, se kterou pracujete.
2. Klikněte na koncový bod, pro který chcete zobrazit stránku nápovědy rozhraní API.
3. V horní nabídce klikněte na **Konzumujte**.
4. V části Koncové body požadavku a odpovědi nebo Dávkového spuštění klikněte na **stránku nápovědy** rozhraní API.

**Zobrazení nápovědy rozhraní API pro strojové učení pro novou webovou službu**

Na [portálu webových služeb Azure Machine Learning:](https://services.azureml.net/)

1. V horní nabídce klikněte na **WEBOVÉ SLUŽBY.**
2. Klepněte na webovou službu, pro kterou chcete klíč načíst.

Kliknutím na **Použít webovou službu** získáte identifikátory URI pro služby odezvy na požadavky a dávkové spuštění a ukázkový kód v jazycích C#, R a Pythonu.

Kliknutím na **rozhraní API Swagger** získáte dokumentaci založenou na rozhraní API s názvem Swagger ze zadaných identifikátorů URI.

### <a name="c-sample"></a>Ukázka jazyka C#
Chcete-li se připojit k webové službě Machine Learning, použijte **httpclient** předávání ScoreData. ScoreData obsahuje FeatureVector, n-dimenzionální vektor numerických funkcí, který představuje ScoreData. Ověření služby Machine Learning pomocí klíče rozhraní API.

Chcete-li se připojit k webové službě Machine Learning, musí být nainstalován balíček **Microsoft.AspNet.WebApi.Client** NuGet.

**Instalace aplikace Microsoft.AspNet.WebApi.Client NuGet v sadě Visual Studio**

1. Publikujte datovou sadu Stáhnout z webové služby datové sady třídy UCI: Adult 2.
2. Klepněte na **položku Nástroje** > **Aplikace NuGet , konzola** > **správce balíčků správce balíčků**.
3. Zvolte **Instalační balíček Microsoft.AspNet.WebApi.Client**.

**Spuštění ukázky kódu**

1. Publikovat "Ukázka 1: Stáhnout datovou sadu z UCI: Adult 2 třídy datové sady" experiment, který je součástí kolekce vzorků Machine Learning.
2. Přiřaďte apiKey s klíčem z webové služby. Viz **Získání autorizačního klíče** výše.
3. Přiřaďte serviceUri s identifikátorem URI požadavku.

**Zde je to, co kompletní žádost bude vypadat.**
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
Chcete-li se připojit k webové službě Machine Learning, použijte knihovnu **urllib2** pro Python 2.X a **urllib.request** knihovnu pro Python 3.X. Předáte ScoreData, který obsahuje FeatureVector, n-dimenzionální vektor číselných funkcí, které představuje ScoreData. Ověření služby Machine Learning pomocí klíče rozhraní API.

**Spuštění ukázky kódu**

1. Nasazení experimentu "Ukázka 1: Stáhnout datovou sadu z datové sady Třídy Pro dospělé: Dospělí 2", který je součástí kolekce vzorků Machine Learning.
2. Přiřaďte apiKey s klíčem z webové služby. Podívejte se na část **Získat autorizační klíč** poblíž začátku tohoto článku.
3. Přiřaďte serviceUri s identifikátorem URI požadavku.

**Zde je to, co kompletní žádost bude vypadat.**
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

Chcete-li se připojit k webové službě Machine Learning, použijte knihovny **RCurl** a **rjson** k vytvoření požadavku a zpracování vrácené odpovědi JSON. Předáte ScoreData, který obsahuje FeatureVector, n-dimenzionální vektor číselných funkcí, které představuje ScoreData. Ověření služby Machine Learning pomocí klíče rozhraní API.

**Zde je to, co kompletní žádost bude vypadat.**
```r
library("RCurl")
library("rjson")

# Accept TLS/SSL certificates issued by public Certificate Authorities
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

### <a name="javascript-sample"></a>Ukázka javascriptu

Chcete-li se připojit k webové službě Machine Learning, použijte balíček **request** npm v projektu. Objekt použijete `JSON` také k formátování vstupu a analýzy výsledku. Nainstalujte `npm install request --save`pomocí `"request": "*"` aplikace nebo přidejte do `dependencies` souboru package.json pod a spusťte `npm install`.

**Zde je to, co kompletní žádost bude vypadat.**
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
