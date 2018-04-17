---
title: Používání služby webové služby Azure Machine Learning modelu Management | Microsoft Docs
description: Tento dokument popisuje kroky a koncepty součástí využívání webových služeb, které jsou nasazeny pomocí modelu správy v Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 0976e2dca909781ade76c742cc99746e1123307d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="consuming-web-services"></a>Využívání webových služeb
Jakmile nasadíte model jako webovou službu v reálném čase, můžete odeslat data a získat předpovědi z mnoha různých platforem a aplikace. Webovou službu v reálném čase zpřístupňuje rozhraní REST API pro získání předpovědi. K webové službě ve formátu jednoho nebo více řádků získat jeden nebo více předpovědi současně může odesílat data.

Pomocí [Azure Machine Learning webové služby](model-management-service-deploy.md), externí aplikací synchronně komunikuje s prediktivního modelu tím, že volání HTTP POST na adresu URL služby. Chcete-li volání webové služby, klientská aplikace musí určovat klíč rozhraní API, která se vytvoří při nasazení předpovědi a ukládat data požadavku do textu požadavku POST.

Všimněte si, že klíče rozhraní API jsou k dispozici pouze v režimu nasazení clusteru. Místní webové služby není nutné klíče.

## <a name="service-deployment-options"></a>Možnosti nasazení služby
Azure Machine Learning webové služby se dá nasadit clustery založené na cloudu pro produkční a testovací scénáře a místní pracovní stanice pomocí modulu docker. Funkce prediktivního modelu v obou případech zůstává stejná. Nasazení na základě clusteru poskytuje škálovatelné a původce řešení podle kontejneru služby Azure, zatímco místní nasazení lze použít pro ladění. 

Rozhraní příkazového řádku pro Azure Machine Learning a rozhraní API poskytuje pohodlné příkazy pro vytváření a správu výpočetní prostředí pro nasazení služby pomocí ```az ml env``` možnost. 

## <a name="list-deployed-services-and-images"></a>Zobrazit seznam nasazení služeb a obrázků
Můžete vytvořit seznam aktuálně nasazené služby a imagí Dockeru pomocí rozhraní příkazového řádku příkaz ```az ml service list realtime -o table```. Všimněte si, že tento příkaz lze použít vždy v kontextu aktuálního výpočetního prostředí. Služby, které jsou nasazeny nezobrazí v prostředí, které není nastavena jako aktuální. Nastavení použití prostředí ```az ml env set```. 

## <a name="get-service-information"></a>Získat informace o služby
Po úspěšném nasazení webové služby použijte následující příkaz získat adresu URL služby a další podrobnosti pro volání koncový bod služby. 

```
az ml service usage realtime -i <web service id>
```

Tento příkaz vypíše adresu URL služby, hlavičky požadované žádosti, adresy URL swaggeru a vzorová data pro volání služby, pokud byl v době nasazení schématu rozhraní API služby.

Bez skládání požadavku HTTP, zadáním příkazu ukázkové rozhraní příkazového řádku s vstupních dat můžete testovat službu přímo z rozhraní příkazového řádku:

```
az ml service run realtime -i <web service id> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Získat klíč rozhraní API služby
Získat klíč webové služby, použijte následující příkaz:

```
az ml service keys realtime -i <web service id>
```
Při vytváření požadavku HTTP, používá se v hlavičce autorizace: "Autorizace": "nosiče <key>"

## <a name="get-the-service-swagger-description"></a>Získání popisu služby Swagger
Pokud byl schématu rozhraní API služby, koncový bod služby by vystavit dokumentem Swagger v ```http://<ip>/api/v1/service/<service name>/swagger.json```. Dokumentu Swagger slouží k automatickému generování klienta služby a prozkoumat očekávané vstupních dat a další podrobnosti o službě.

## <a name="get-service-logs"></a>Získání protokolů služby
K pochopení chování služby a diagnostice problémů, existuje několik způsobů získat protokoly služby:
- Rozhraní příkazového řádku příkaz ```az ml service logs realtime -i <service id>```. Tento příkaz lze použít v clusteru i místní režimy.
- Pokud v nasazení bylo povoleno protokolování služby, protokoly služby zašle také AppInsight. Rozhraní příkazového řádku příkaz ```az ml service usage realtime -i <service id>``` zobrazuje AppInsight adresu URL. Všimněte si, že protokol AppInsight může zpožděn 2 až 5 minut.
- Protokoly clusteru je možné zobrazit pomocí konzoly Kubernetes, která je připojena při nastavení aktuální prostředí clusteru s ```az ml env set```
- Místní docker protokoly jsou k dispozici prostřednictvím protokoly modulu docker, když je služba spuštěná místně.

## <a name="call-the-service-using-c"></a>Volání služby pomocí jazyka C#
Adresa URL služby použijte k odeslání požadavku z konzolovou aplikaci C#. 

1. V sadě Visual Studio vytvořte novou konzolovou aplikaci: 
    * V nabídce klikněte na možnost, Soubor -> Nový -> projektu
    * V části Visual Studio C#, klikněte na ploše systému Windows – třída a potom vyberte konzolovou aplikaci.
2. Zadejte _MyFirstService_ jako název projektu, klikněte na tlačítko OK.
3. V odkazy na projekt, nastavte odkazy na _System.Net_, a _System.Net.Http_.
4. Klikněte na Nástroje -> Správce balíčků NuGet -> Konzola správce balíčků a potom nainstalovat balíček Microsoft.AspNet.WebApi.Client.
5. Otevřete soubor Program.cs a nahraďte kód následujícím kódem:
6. Aktualizace _SERVICE_URL_ a _API_KEY_ parametry s informacemi z webové služby.
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

## <a name="call-the-web-service-using-python"></a>Volání webové služby používá Python
Poslat žádost o služby webu v reálném čase pomocí Python. 

1. Zkopírujte následující vzorový kód do nového souboru Python.
2. Aktualizujte data, adresy url a api_key parametry. Pro místní webové služby odeberte záhlaví "Povolení".
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
