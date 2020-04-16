---
title: Monitorování datových továren pomocí Azure Monitoru
description: Zjistěte, jak pomocí Azure Monitoru monitorovat kanály /Azure Data Factory povolením diagnostických protokolů s informacemi z Datové továrny.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419455"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Výstrahy a monitorování datových továren pomocí Azure Monitoru

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudové aplikace jsou složité a mají mnoho pohyblivých částí. Monitory poskytují data, která pomáhají zajistit, aby vaše aplikace zůstaly v provozu v pořádku. Monitory vám také pomohou vyhnout se potenciálním problémům a řešit problémy z minulosti.

Data monitorování můžete použít k získání podrobného přehledu o vašich aplikacích. Tyto znalosti vám pomohou zlepšit výkon aplikací a udržovatelnost. Pomáhá také automatizovat akce, které jinak vyžadují ruční zásah.

Azure Monitor poskytuje metriky a protokoly infrastruktury základní úrovně pro většinu služeb Azure. Diagnostické protokoly Azure jsou emitovány prostředek a poskytují bohaté, časté údaje o provozu tohoto prostředku. A Azure Data Factory zapisuje diagnostické protokoly v monitoru.

Podrobnosti najdete v [tématu Azure Monitor přehled](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Uchovávání dat Azure Data Factory

Data Factory ukládá data spuštění kanálu pouze 45 dní. Pokud chcete tato data uchovávat delší dobu, použijte monitor. Pomocí nástroje Sledování můžete směrovat diagnostické protokoly pro analýzu. Můžete je také uchovávat v účtu úložiště, abyste měli informace z výroby po zvolenou dobu trvání.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

* Uložte diagnostické protokoly do účtu úložiště pro auditování nebo ruční kontrolu. Pomocí nastavení diagnostiky můžete určit dobu uchovávání ve dnech.
* Streamujte protokoly do Azure Event Hubs. Protokoly se stanou vstupem do partnerské služby nebo do vlastního analytického řešení, jako je Power BI.
* Analyzujte protokoly pomocí analýzy protokolů.

Můžete použít účet úložiště nebo obor názvů centra událostí, který není v předplatném prostředku, který vydává protokoly. Uživatel, který konfiguruje nastavení musí mít odpovídající řízení přístupu na základě rolí (RBAC) přístup k oběma předplatným.

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

### <a name="diagnostic-settings"></a>Nastavení diagnostiky

Pomocí diagnostických nastavení nakonfigurujte diagnostické protokoly pro nevýpočetní prostředky. Nastavení ovládacího prvku prostředků mají následující funkce:

* Určují, kam jsou odesílány diagnostické protokoly. Mezi příklady patří účet úložiště Azure, centrum událostí Azure nebo protokoly monitorování.
* Určují, které kategorie protokolu jsou odesílány.
* Určují, jak dlouho by měla být každá kategorie protokolu uchovávána v účtu úložiště.
* Nastavení doby uchovávání na 0 dní znamená, že se protokoly uchovávají trvale. V opačném případě může být hodnota libovolný počet dní od 1 do 2,147,483,647.
* Pokud jsou nastaveny zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázáno, zásady uchovávání informací nemají žádný vliv. K této podmínce může například dojít, když jsou vybrány pouze centra událostí nebo protokoly monitorování.
* Zásady uchovávání informací jsou použity za den. Hranice mezi dny nastane o půlnoci koordinovaný světový čas (UTC). Na konci dne protokoly z dnů, které jsou mimo zásady uchovávání informací jsou odstraněny. Například pokud máte zásady uchovávání informací jeden den, na začátku dnešního dne protokoly z předevčírem jsou odstraněny.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Povolení diagnostických protokolů prostřednictvím rozhraní AZURE Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Vytvoření nebo aktualizace nastavení diagnostiky v rozhraní REST API monitoru

##### <a name="request"></a>Žádost

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Hlavičky

* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku, pro který chcete upravit nastavení diagnostiky. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Nastavte `Content-Type` záhlaví `application/json`na .
* Nastavte hlavičku autorizace na webový token JSON, který jste získali z Azure Active Directory (Azure AD). Další informace naleznete [v tématu Ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Tělo

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Vlastnost | Typ | Popis |
| --- | --- | --- |
| **storageAccountId** |Řetězec | ID prostředku účtu úložiště, do kterého chcete odeslat diagnostické protokoly. |
| **serviceBusRuleId** |Řetězec | ID pravidla sběrnice oboru názvů service-bus, ve kterém chcete mít centra událostí vytvořená pro datové diagnostické protokoly. ID pravidla má `{service bus resource ID}/authorizationrules/{key name}`formát .|
| **workspaceId** | Komplexní typ | Pole metriky čas zrna a jejich zásady uchovávání informací. Hodnota této vlastnosti je prázdná. |
|**Metriky**| Hodnoty parametrů kanálu, který má být předán do vyvolání kanálu| A JSON objekt, který mapuje názvy parametrů na hodnoty argumentů. |
| **Protokoly**| Komplexní typ| Název kategorie diagnostického protokolu pro typ prostředku. Chcete-li získat seznam kategorií diagnostických protokolů pro prostředek, proveďte operaci nastavení diagnostiky GET. |
| **Kategorie**| Řetězec| Pole kategorií protokolu a jejich zásady uchovávání informací. |
| **timeGrain** | Řetězec | Členitost metriky, které jsou zachyceny ve formátu iso 8601 doba trvání. Hodnota vlastnosti `PT1M`musí být , která určuje jednu minutu. |
| **Povoleno**| Logická hodnota | Určuje, zda je pro tento prostředek povolena kolekce metriky nebo kategorie protokolu. |
| **retenčníZásady**| Komplexní typ| Popisuje zásady uchovávání informací pro kategorii metriky nebo protokolu. Tato vlastnost se používá pouze pro účty úložiště. |
|**Dní**| Int| Počet dní, po které mají být metriky nebo protokoly uchovány. Pokud je hodnota vlastnosti 0, protokoly jsou uchovávány navždy. Tato vlastnost se používá pouze pro účty úložiště. |

##### <a name="response"></a>Odpověď

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Získání informací o nastavení diagnostiky v rozhraní REST API monitoru

##### <a name="request"></a>Žádost

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Hlavičky

* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku, pro který chcete upravit nastavení diagnostiky. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Nastavte `Content-Type` záhlaví `application/json`na .
* Nastavte hlavičku autorizace na webový token JSON, který jste získali z Azure AD. Další informace naleznete [v tématu Ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Odpověď

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
Další informace naleznete [v tématu Diagnostic Settings](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schéma protokolů a událostí

### <a name="monitor-schema"></a>Schéma monitoru

#### <a name="activity-run-log-attributes"></a>Atributy protokolu spuštění aktivity

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Úroveň** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivity nastavte hodnotu vlastnosti na hodnotu 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Čas** | Řetězec | Čas události ve formátu `YYYY-MM-DDTHH:MM:SS.00000Z`UTC časového rozpětí . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Řetězec| ID spuštění aktivity. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Řetězec| ID spuštění kanálu. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Řetězec | ID přidružené k prostředku datové továrny. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorie**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu `ActivityRuns`vlastnosti na . | `ActivityRuns` |
|**Úrovni**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu `Informational`vlastnosti na . | `Informational` |
|**název_operace**| Řetězec | Název aktivity s jejím stavem. Pokud je aktivita počáteční prezenční `MyActivity -`signál, hodnota vlastnosti je . Pokud je aktivita koncový prezenční `MyActivity - Succeeded`signál, hodnota vlastnosti je . | `MyActivity - Succeeded` |
|**název_kanálu**| Řetězec | Název kanálu. | `MyPipeline` |
|**Název aktivity activityName**| Řetězec | Název aktivity. | `MyActivity` |
|**Spustit**| Řetězec | Čas zahájení aktivity běží ve formátu Timespan UTC. | `2017-06-26T20:55:29.5007959Z`|
|**Konec**| Řetězec | Koncový čas aktivity běží ve formátu Timespan UTC. Pokud diagnostický protokol ukazuje, že aktivita byla spuštěna, `1601-01-01T00:00:00Z`ale ještě neskončila, hodnota vlastnosti je . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributy protokolu spuštěného kanálem

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Úroveň** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivity nastavte hodnotu vlastnosti na hodnotu 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Čas** | Řetězec | Čas události ve formátu `YYYY-MM-DDTHH:MM:SS.00000Z`UTC časového rozpětí . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Řetězec| ID spuštění kanálu. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| Řetězec | ID přidružené k prostředku datové továrny. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorie**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu `PipelineRuns`vlastnosti na . | `PipelineRuns` |
|**Úrovni**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu `Informational`vlastnosti na . | `Informational` |
|**název_operace**| Řetězec | Název kanálu spolu s jeho stavem. Po dokončení spuštění kanálu je `Pipeline - Succeeded`hodnota vlastnosti . | `MyPipeline - Succeeded`. |
|**název_kanálu**| Řetězec | Název kanálu. | `MyPipeline` |
|**Spustit**| Řetězec | Čas zahájení aktivity běží ve formátu Timespan UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**Konec**| Řetězec | Koncový čas aktivity běží ve formátu Timespan UTC. Pokud diagnostický protokol ukazuje, že aktivita byla spuštěna, ale ještě neskončila, hodnota vlastnosti je `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**Stav**| Řetězec | Konečný stav spuštění kanálu. Možné hodnoty `Succeeded` vlastností jsou a `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Atributy protokolu s pouštěm

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Vlastnost | Typ | Popis | Příklad |
| --- | --- | --- | --- |
| **Úroveň** |Řetězec | Úroveň diagnostických protokolů. Pro protokoly spuštění aktivity nastavte hodnotu vlastnosti na hodnotu 4. | `4` |
| **correlationId** |Řetězec | Jedinečné ID pro sledování konkrétního požadavku. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **Čas** | Řetězec | Čas události ve formátu `YYYY-MM-DDTHH:MM:SS.00000Z`UTC časového rozpětí . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Řetězec| ID spuštění aktivační události. | `08587023010602533858661257311` |
|**Resourceid**| Řetězec | ID přidružené k prostředku datové továrny. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategorie**| Řetězec | Kategorie diagnostických protokolů. Nastavte hodnotu `PipelineRuns`vlastnosti na . | `PipelineRuns` |
|**Úrovni**| Řetězec | Úroveň diagnostických protokolů. Nastavte hodnotu `Informational`vlastnosti na . | `Informational` |
|**název_operace**| Řetězec | Název aktivační události s jeho konečný stav, který označuje, zda aktivační událost úspěšně aktivována. Pokud byl prezenční signál `MyTrigger - Succeeded`úspěšný, hodnota vlastnosti je . | `MyTrigger - Succeeded` |
|**název_spoušť**| Řetězec | Název aktivační události. | `MyTrigger` |
|**triggerType**| Řetězec | Typ aktivační události. Možné hodnoty `Manual Trigger` vlastností jsou a `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| Řetězec | Událost aktivační události. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Spustit**| Řetězec | Čas spuštění spuštění aktivační události ve formátu UTC časového období. | `2017-06-26T20:55:29.5007959Z`|
|**Stav**| Řetězec | Konečný stav zobrazující, zda aktivační událost úspěšně aktivována. Možné hodnoty `Succeeded` vlastností jsou a `Failed`. | `Succeeded`|

### <a name="log-analytics-schema"></a>Schéma analýzy protokolů

Log Analytics dědí schéma z Monitor s následujícími výjimkami:

* První písmeno v každém názvu sloupce je velkými písmeny. Například název sloupce "correlationId" v monitoru je "CorrelationId" v Log Analytics.
* Není tu žádný sloupec "Úroveň".
* Dynamický sloupec "vlastnosti" je zachován jako následující dynamický typ objektu blob JSON.

    | Sloupec Azure Monitor | Sloupec Log Analytics | Typ |
    | --- | --- | --- |
    | $.properties. Vlastnosti uživatele | Vlastnosti uživatele | Dynamická |
    | $.properties. Poznámky | Poznámky | Dynamická |
    | $.properties. Vstupní | Vstup | Dynamická |
    | $.properties. Výstup | Výstup | Dynamická |
    | $.properties. Error.errorCode | ErrorCode | int |
    | $.properties. Chyba.zpráva | Errormessage | řetězec |
    | $.properties. Chyba | Chyba | Dynamická |
    | $.properties. Předchůdci | Předchůdci | Dynamická |
    | $.properties. Parametry | Parametry | Dynamická |
    | $.properties. Parametry systému | Parametry systému | Dynamická |
    | $.properties. Tagy | Značky | Dynamická |
    
## <a name="metrics"></a>Metriky

Pomocí monitoru můžete získat přehled o výkonu a stavu vašich úloh Azure. Nejdůležitějším typem dat monitorování je metrika, která se také nazývá čítač výkonu. Metriky jsou emitovány většinou prostředků Azure. Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

Azure Data Factory verze 2 vydává následující metriky.

| **Metrika**           | **Grafický název metriky**         | **Jednotka** | **Typ agregace** | **Popis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Úspěšné metriky spuštění kanálu | Počet    | Celkem                | Celkový počet spuštění kanálu, který byl úspěšný v rámci minuty. |
| Spuštění pipelinefailedruns   | Metriky spuštění neúspěšného kanálu    | Počet    | Celkem                | Celkový počet spuštění kanálu, které se nezdařilo v rámci minuty.    |
| Úspěšné spuštění aktivity | Úspěšná aktivita spustí metriky | Počet    | Celkem                | Celkový počet spuštění aktivity, která byla úspěšná v rámci minuty.  |
| ActivityFailedRuns   | Neúspěšná aktivita spustí metriky    | Počet    | Celkem                | Celkový počet spuštění aktivity, které se nezdařilo v rámci minuty.     |
| TriggerSucceededRuns | Úspěšné aktivační události spustí metriky  | Počet    | Celkem                | Celkový počet spuštění aktivační události, které byly úspěšné v rámci minutového okna.   |
| Spuštění spouštění    | Metriky spuštění aktivační události se nezdařilo.     | Počet    | Celkem                | Celkový počet spuštění aktivační události, které se nezdařilo v rámci minuty.      |

Chcete-li získat přístup k metrikám, vyplňte pokyny v [datové platformě Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Jsou emitovány pouze dokončené, aktivované aktivity a události spuštění kanálu. Probíhá a spuštění izolovaného prostoru/ladění **nejsou** emitovány. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorování metrik Datové továrny pomocí Azure Monitoru

Pomocí integrace Data Factory s monitorem můžete směrovat data do monitoru. Tato integrace je užitečná v následujících scénářích:

* Chcete psát složité dotazy na bohatou sadu metrik, které je publikovány Data Factory na monitor. Můžete vytvořit vlastní výstrahy na tyto dotazy prostřednictvím monitoru.

* Chcete sledovat napříč datovými továrnami. Data z více továren dat můžete směrovat do jednoho pracovního prostoru monitoru.

Sedmiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurace nastavení diagnostiky a pracovního prostoru

Vytvořte nebo přidejte diagnostická nastavení pro vaši datovou továrnu.

1. Na portálu přejděte na Monitor. Vyberte **Nastavení** > **nastavení diagnostiky**.

1. Vyberte datovou továrnu, pro kterou chcete nastavit diagnostické nastavení.

1. Pokud ve vybrané datové továrně neexistují žádná nastavení, budete vyzváni k vytvoření nastavení. Vyberte **možnost Zapnout diagnostiku**.

   ![Vytvoření diagnostického nastavení, pokud neexistují žádná nastavení](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Pokud existují existující nastavení v datové továrně, zobrazí se seznam nastavení, která jsou již nakonfigurována v datové továrně. Vyberte **Přidat diagnostické nastavení**.

   ![Přidání diagnostického nastavení, pokud existují nastavení](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Pojmenujte své nastavení, vyberte **Odeslat do log Analytics**a pak vyberte pracovní prostor z **pracovního prostoru Analýzy protokolů**.

    ![Pojmenujte nastavení a vyberte pracovní prostor pro analýzu protokolů](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Vyberte **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro tuto datovou továrnu. Diagnostické protokoly jsou přenášeny datovým proudem do tohoto pracovního prostoru, jakmile jsou generována nová data událostí. Až 15 minut může uplynout mezi při vyzařování události a když se zobrazí v Log Analytics.

* V režimu _specifickém pro prostředky_ diagnostické protokoly z Azure Data Factory toku do _tabulek ADFPipelineRun_, _ADFTriggerRun_a _ADFActivityRun_
* V režimu _Azure-Diagnostics_ se diagnostické protokoly přenášejí do tabulky _AzureDiagnostics_.

> [!NOTE]
> Vzhledem k tomu, že tabulka protokolu Azure nemůže mít více než 500 sloupců, důrazně doporučujeme vybrat režim specifické pro prostředky. Další informace naleznete v [tématu Log Analytics Known Limitations](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalace Azure Data Factory Analytics z Azure Marketplace

![Přejděte na "Azure Marketplace", zadejte "Filtr Analytics" a vyberte "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Podrobnosti o "Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Vyberte **Vytvořit** a pak vyberte nastavení **pracovního prostoru OMS** a **OMS .**

![Vytvoření nového řešení](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorování metrik datové továrny

Instalace Azure Data Factory Analytics vytvoří výchozí sadu zobrazení tak, aby byly povoleny následující metriky:

- Spuštění adf - 1) Spuštění kanálu podle datové továrny
 
- ADF runs- 2) Aktivita běží podle data factory

- Spuštění adf - 3) Spuštění aktivační události podle datové továrny

- Chyby ADF - 1) Top 10 potrubní chyby podle data factory

- Chyby ADF - 2) Top 10 aktivit běží podle data factory

- Chyby ADF - 3) Top 10 Trigger Chyby podle data Factory

- Statistika ADF - 1) Aktivita běží podle typu

- Statistika ADF - 2) Spuštění aktivační události podle typu

- Statistika ADF - 3) Maximální doba trvání spuštění kanálu

![Okno se zvýrazněnými sešity (preview) a AzureDataFactoryAnalytics](media/data-factory-monitor-oms/monitor-oms-image6.png)

Můžete vizualizovat předchozí metriky, podívat se na dotazy za těmito metrikami, upravit dotazy, vytvořit výstrahy a provést další akce.

![Grafické znázornění potrubí běží podle data factory"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Preview) odesílá diagnostické protokoly do cílových tabulek _specifických pro prostředky._ Můžete psát dotazy proti následujícím tabulkám: _ADFPipelineRun_, _ADFTriggerRun_a _ADFActivityRun_.

## <a name="alerts"></a>Výstrahy

Přihlaste se na portál Azure a vyberte **výstrahy** > **monitorování** a vytvořte výstrahy.

![Upozornění v nabídce portálu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Vytvořit výstrahy

1. Chcete-li vytvořit novou výstrahu, vyberte **možnost + nové pravidlo výstrahy.**

    ![Nové pravidlo výstrahy](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definujte podmínku výstrahy.

    > [!NOTE]
    > Nezapomeňte vybrat **možnost Vše** v rozevíracím seznamu **Filtr podle typu prostředku.**

    !["Definovat výstražnou podmínku" > "Vybrat cíl", který otevře podokno Vybrat prostředek ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definovat výstražnou podmínku" >" Přidat kritéria", která otevře podokno Konfigurovat logiku signálu](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Podokno Konfigurovat typ signálu](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definujte podrobnosti výstrahy.

    ![Podrobnosti upozornění](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definujte skupinu akcí.

    ![Vytvoření pravidla se zvýrazněnou možností "Skupina nové akce"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Vytvoření nové skupiny akcí](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurace e-mailu, SMS, push a hlasu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definování skupiny akcí](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Další kroky
[Programové sledování a správa kanálů](monitor-programmatically.md)
