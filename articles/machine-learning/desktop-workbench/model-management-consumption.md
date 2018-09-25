---
title: Azure používání webové služby Správa modelů Machine Learning | Dokumentace Microsoftu
description: Tento dokument popisuje kroky a koncepty součástí využívání webové služby nasazené pomocí správy modelů ve službě Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c01198a78a32c460bd147e1e160358271b80eef5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950617"
---
# <a name="consuming-web-services"></a>Používání webových služeb

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Po nasazení modelu jako webové služby v reálném čase, můžete ho odeslat data a získat předpovědí z nejrůznějších platforem a aplikací. Webové služby v reálném čase zpřístupňuje rozhraní REST API pro získání předpovědi. Může odesílat data do služby web ve formátu jeden nebo více řádky zobrazíte predikcí nejmíň jeden po druhém.

S [služby Azure Machine Learning Web](model-management-service-deploy.md), externí aplikaci synchronně komunikuje s prediktivním modelem tím, že volání HTTP POST na adresu URL služby. Chcete-li volání webové služby, klientská aplikace potřebuje k určení klíče rozhraní API, který je vytvořen při nasazování predikcí a ukládat data požadavku do textu požadavku POST.

> [!NOTE]
> Všimněte si, že klíče rozhraní API jsou k dispozici pouze v režimu nasazení clusteru. Místní webové služby nemají žádné klíče.

## <a name="service-deployment-options"></a>Možnosti nasazení služeb
Azure Machine Learning Web services můžete nasadit clustery založené na cloudu pro produkční a testovací scénáře a místní pracovní stanice pomocí modulu docker. Funkce prediktivního modelu v obou případech zůstává stejná. Nasazení na základě clusteru poskytuje škálovatelné a výkonné řešení založené na služby Azure Container Service, i když je místní nasazení můžete použít pro ladění. 

Rozhraní příkazového řádku Azure Machine Learning a rozhraní API poskytuje pohodlné příkazy pro vytváření a správu výpočetních prostředí pro nasazení služeb pomocí ```az ml env``` možnost. 

## <a name="list-deployed-services-and-images"></a>Výpis nasazení služeb a obrázky
Můžete vytvořit seznam aktuálně nasazená služba a Image Dockeru pomocí rozhraní příkazového řádku ```az ml service list realtime -o table```. Všimněte si, že tento příkaz vždy funguje v rámci aktuálního prostředí compute. Měl by zobrazit služby, které jsou nasazené v prostředí, které není nastavena jako aktuální. Chcete-li nastavit prostředí pomocí ```az ml env set```. 

## <a name="get-service-information"></a>Získat informace o službě
Po úspěšném nasazení webové služby použijte následující příkaz se získat adresu URL služby a další podrobnosti pro volání koncového bodu služby. 

```
az ml service usage realtime -i <web service id>
```

Tento příkaz vypíše adresu URL služby, hlavičky požadované žádosti, adresy URL swaggeru a ukázková data pro volání služby, pokud schéma rozhraní API služby jste zadali při čas nasazení.

Můžete testovat službu přímo z rozhraní příkazového řádku bez vytváření požadavku protokolu HTTP, tak, že zadáte Ukázkový příkaz rozhraní příkazového řádku se vstupními daty:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Získání klíče rozhraní API služby
Chcete-li získat klíč k webové službě, použijte následující příkaz:

```
az ml service keys realtime -i <web service id>
```
Při vytváření požadavku protokolu HTTP, používat klíč v hlavičce autorizace: "Autorizace": "nosiče <key>"

## <a name="get-the-service-swagger-description"></a>Načtení popisu služby Swagger
Pokud byl zadán schématu rozhraní API služby, koncový bod služby by vystavovat dokumentu Swagger za ```http://<ip>/api/v1/service/<service name>/swagger.json```. Dokument Swagger slouží k automatickému generování klienta služby a prozkoumejte očekávaný vstupní data a další podrobnosti o této službě.

## <a name="get-service-logs"></a>Získání protokolů služby
Pochopení chování služby a diagnostikovat problémy, existuje několik způsobů, jak načíst protokoly služby:
- Rozhraní příkazového řádku ```az ml service logs realtime -i <service id>```. Tento příkaz funguje v clusteru a místní režimy.
- Pokud v nasazení bylo povoleno protokolování služby, se do služby AppInsight odesílat protokoly služby. Pomocí příkazu CLI ```az ml service usage realtime -i <service id>``` zobrazí adresu URL služby AppInsight. Všimněte si, že protokoly služby AppInsight se může zpozdit o 2 – 5 minut.
- Protokoly clusteru je možné zobrazit pomocí konzoly Kubernetes, který je připojený při nastavování aktuální prostředí clusteru s ```az ml env set```
- Místní docker protokoly jsou k dispozici prostřednictvím protokoly modulu docker, pokud služba je spuštěna místně.

## <a name="call-the-service-using-c"></a>Volání služby pomocí jazyka C#
Adresa URL služby použijte k odesílání požadavku z konzolové aplikace jazyka C#. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci: 
    * V nabídce klikněte na možnost, Soubor -> Nový -> Projekt
    * V rámci Visual Studio C#, klikněte na tlačítko Windows Desktop třídy a pak vyberte konzolovou aplikaci.
2. Zadejte `MyFirstService` jako název projektu, klikněte na tlačítko OK.
3. V odkazech projektu, nastavte odkazy `System.Net`, a `System.Net.Http`.
4. Klikněte na Nástroje -> Správce balíčků NuGet -> Konzola správce balíčků, potom nainstalovat **Microsoft.AspNet.WebApi.Client** balíčku.
5. Otevřít **Program.cs** souboru a nahraďte kód následujícím kódem:
6. Aktualizace `SERVICE_URL` a `API_KEY` parametry s informacemi z webové služby.
7. Spusťte projekt.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Volání webové služby pomocí Pythonu
Použití Pythonu k odeslat žádost službě webu v reálném čase. 

1. Zkopírujte následující vzorový kód do nového souboru Python.
2. Aktualizujte parametry dat, adresu url a klíč rozhraní API. Místní webové služby odeberte hlavičku "Povolení".
3. Spusťte kód. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, body, headers=headers)
resp.text
```
