---
title: Použití rozhraní REST API k provedení CI/CD pro Azure Stream Analytics na IoT Edge
description: Naučte se implementovat kanál průběžné integrace a nasazování pro Azure Stream Analytics pomocí rozhraní REST API.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 3c3f776ad0996fa0b7422f0fca2d899a35e853d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016129"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementace CI/CD pro Stream Analytics na IoT Edge pomocí rozhraní API

Můžete povolit průběžnou integraci a nasazování pro úlohy Azure Stream Analytics pomocí rozhraní REST API. Tento článek popisuje příklady použití rozhraní API a jejich použití. Rozhraní REST API nejsou v Azure Cloud Shell podporovaná.

## <a name="call-apis-from-different-environments"></a>Volání rozhraní API z různých prostředí

Rozhraní REST API je možné volat jak z Linux, tak z Windows. Následující příkazy ukazují správnou syntaxi pro volání rozhraní API. Konkrétní využití rozhraní API bude popsáno v dalších částech tohoto článku.

### <a name="linux"></a>Linux

Pro Linux můžete použít `Curl` `Wget` příkazy nebo:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Pro Windows použijte PowerShell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Vytvoření úlohy ASA na hraničních zařízeních 
 
Chcete-li vytvořit úlohu Stream Analytics, zavolejte metodu PUT pomocí rozhraní Stream Analytics API.

|Metoda|Adresa URL požadavku|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Příklad příkazu využívajícího **kudrlinkou**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Příklad textu žádosti ve formátu JSON:

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
 
Další informace najdete v [dokumentaci k rozhraní API](/rest/api/streamanalytics/).  
 
## <a name="publish-edge-package"></a>Publikovat hraniční balíček 
 
Pokud chcete publikovat úlohu Stream Analytics na IoT Edge, zavolejte metodu POST pomocí rozhraní API pro publikování balíčku Edge.

|Metoda|Adresa URL požadavku|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Tato asynchronní operace vrátí stav 202 až do úspěšného publikování úlohy. Hlavička odpovědi umístění obsahuje identifikátor URI, který se používá k získání stavu procesu. Při běhu procesu vrátí volání identifikátoru URI v hlavičce umístění stav 202. Po dokončení procesu vrátí identifikátor URI v hlavičce umístění stav 200. 

Příklad balíčku Edge s voláním pro publikování pomocí objektu **kudrlinkou**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po provedení NÁSLEDNÉho volání byste měli očekávat odpověď s prázdným tělem. Vyhledejte adresu URL, která se nachází v HLAVIČCE odpovědi, a zaznamenejte ji pro další použití.
 
Příklad adresy URL z HLAVIČKY odpovědi:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Počkejte jednu až dvě minuty, než spustíte následující příkaz, aby bylo volání rozhraní API s adresou URL, kterou jste nalezli v HLAVIČCE odpovědi. Pokud neobdržíte odpověď 200, zkuste příkaz zopakovat.
 
Příklad volání rozhraní API s vrácenou adresou URL s **kudrlinkou**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpověď obsahuje informace, které je třeba přidat do skriptu nasazení Edge. Níže uvedené příklady obsahují informace, které potřebujete shromažďovat a kam je přidat v manifestu nasazení.
 
Ukázka textu odpovědi po úspěšném publikování:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
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

Po konfiguraci manifestu nasazení si přečtěte téma [nasazení Azure IoT Edge modulů pomocí Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) pro nasazení.


## <a name="next-steps"></a>Další kroky 
 
* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz k ASA v IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)