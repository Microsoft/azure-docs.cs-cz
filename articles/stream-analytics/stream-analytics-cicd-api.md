---
title: Použití rest API k provádění CI/CD pro Azure Stream Analytics na IoT Edge
description: Zjistěte, jak implementovat kanál průběžné integrace a nasazení pro Azure Stream Analytics pomocí rest API.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 328ca7cd2c6f76095c8334ae6fdb4aa75fbb867d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292008"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementace CI/CD pro Analýzu streamů na IoT Edge pomocí API

Můžete povolit průběžnou integraci a nasazení pro úlohy Azure Stream Analytics pomocí rest API. Tento článek obsahuje příklady, na kterých api použít a jak je používat. REST API nejsou podporovány na Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Volání api z různých prostředí

REST API lze volat z Linuxu i Windows. Následující příkazy ukazují správnou syntaxi pro volání rozhraní API. Konkrétní využití rozhraní API bude uvedeno v dalších částech tohoto článku.

### <a name="linux"></a>Linux

Pro Linux můžete `Curl` použít `Wget` nebo příkazy:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Pro Windows použijte Powershell: 

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
 
## <a name="create-an-asa-job-on-edge"></a>Vytvoření úlohy ASA na okraji 
 
Chcete-li vytvořit úlohu Stream Analytics, zavolejte metodu PUT pomocí rozhraní API Stream Analytics.

|Metoda|Adresa URL požadavku|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Příklad příkazu pomocí **zvlnění**:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Příklad těla požadavku v JSON:

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
 
Další informace naleznete v [dokumentaci k rozhraní API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publikovat balíček Edge 
 
Chcete-li publikovat úlohu Stream Analytics na IoT Edge, zavolejte metodu POST pomocí rozhraní API pro publikování balíčku Edge.

|Metoda|Adresa URL požadavku|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Tato asynchronní operace vrátí stav 202, dokud nebude úloha úspěšně publikována. Hlavička odpovědi na umístění obsahuje identifikátor URI použitý k získání stavu procesu. Při spuštění procesu volání identifikátoru URI v záhlaví umístění vrátí stav 202. Po dokončení procesu uri v záhlaví umístění vrátí stav 200. 

Příklad volání publikování balíčku Edge pomocí **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Po volání POST byste měli očekávat odpověď s prázdným tělem. Vyhledejte adresu URL umístěnou v hlavě odpovědi a zaznamenejte ji pro další použití.
 
Příklad adresy URL z head of response:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
A Počkejte jednu až dvě minuty před spuštěním následující příkaz provést volání rozhraní API s adresou URL, které jste našli v HEAD odpovědi. Opakujte příkaz, pokud nedostanete odpověď 200.
 
Příklad volání rozhraní API s vrácenou adresou URL s **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Odpověď obsahuje informace, které je třeba přidat do skriptu nasazení Edge. Níže uvedené příklady ukazují, jaké informace je třeba shromáždit a kde je přidat do manifestu nasazení.
 
Ukázkové tělo odpovědi po úspěšném publikování:

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

Po konfiguraci manifestu nasazení, najdete [na nasazení modulů Azure IoT Edge s Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) pro nasazení.


## <a name="next-steps"></a>Další kroky 
 
* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [ASA na IoT Edge kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů Visual Studia](stream-analytics-tools-for-visual-studio-edge-jobs.md)
