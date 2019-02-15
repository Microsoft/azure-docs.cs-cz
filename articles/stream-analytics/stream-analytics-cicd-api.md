---
title: Implementujte CI/CD pro Azure Stream Analytics na hraničních zařízeních IoT pomocí rozhraní REST API
description: Zjistěte, jak implementovat průběžné integrace a nasazení kanálu pro Azure Stream Analytics pomocí rozhraní REST API.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 40beb620e037061b189762a51e3c29d0fd251b27
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268495"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementujte CI/CD pro Stream Analytics na hraničních zařízeních IoT pomocí rozhraní API

Můžete povolit průběžnou integraci a nasazování pro úlohy Azure Stream Analytics pomocí rozhraní REST API. Tento článek obsahuje příklady, na které rozhraní API pro použití a způsob jejich použití. Rozhraní REST API nejsou podporované v Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Volání rozhraní API z různých prostředích

Rozhraní REST API můžete volat z Linux i Windows. Následující příkazy ukazují správnou syntaxi pro volání rozhraní API. Použití konkrétní rozhraní API budou popsané v předchozích částech tohoto článku.

### <a name="linux"></a>Linux

Pro Linux, můžete použít `Curl` nebo `Wget` příkazy:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Pokud používáte Windows, použijte prostředí Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Vytvořit úlohu Azure Stream Analytics na hraničních zařízeních 
 
Chcete-li vytvořit úlohu Stream Analytics, zavolejte metodu PUT pomocí rozhraní API pro Stream Analytics.

|Metoda|Adresa URL požadavku|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**id předplatného**} /resourcegroups/ {**název skupiny prostředků**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**název úlohy**}? api-version = 2017-04-01-preview|
 
Příklad použití příkazu **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Příklad těla žádosti JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Další informace najdete v tématu [dokumentace k rozhraní API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publikování hraničního balíčku 
 
K publikování úlohu Stream Analytics na hraničních zařízeních IoT, zavolejte metodu POST pomocí API publikovat hraniční balíček.

|Metoda|Adresa URL požadavku|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - ve verzi Preview|

Tato asynchronní operace vrátí stav 202, dokud úloha byla úspěšně publikována. Hlavičky location odpovědi obsahuje identifikátor URI použitý k získání stavu procesu. Během procesu volání identifikátoru URI v hlavičce location vrátí stav 202. Až se proces dokončí, identifikátor URI v hlavičce location vrací stav 200. 

Příklad hraniční balíček publikovat pomocí volání **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po provedení volání POST, měli byste očekávat odpověď s prázdným textem zprávy. Vyhledejte adresu URL nachází v HLAVIČCE odpovědi a poznamenejte si ho pro další použití.
 
Příklad adresy URL od záhlaví odpovědi:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Počkejte pár minut před spuštěním následujícího příkazu proveďte volání rozhraní API s adresou URL, který jste našli v HLAVIČCE odpovědi. Pokud nelze získat odpověď 200, opakujte příkaz.
 
Příklad volání rozhraní API se vrátí adresu URL s **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpověď obsahuje informace, které je potřeba přidat do skriptu nasazení Edge. Následující příklady ukazují, co informace, které potřebujete ke shromažďování a kam přidat v nasazení manifestu.
 
Ukázkový text odpovědi po úspěšném publikování:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Ukázka manifestu nasazení: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Po konfiguraci manifest nasazení, přečtěte si [moduly nasazení Azure IoT Edge pomocí Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) pro nasazení.


## <a name="next-steps"></a>Další postup 
 
* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Azure Stream Analytics na hraničních zařízeních IoT kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
