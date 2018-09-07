---
title: K monitorování datových továren používat Azure Monitor | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure Monitor k monitorování kanálů Data Factory povolením diagnostické protokoly s informacemi ze služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2018
ms.author: shlo
ms.openlocfilehash: d0f36551fb06e04b50af464bac6953dda64c6202
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054144"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Výstrahy a monitorování datové továrny pomocí Azure monitoru
Cloudové aplikace jsou složité s řadu pohyblivých částí. Monitorování poskytuje data k zajištění, že systém zůstane vaší aplikace v provozu a spuštěná v dobrém stavu. Také pomáhá stave vypnout potenciální problémy a řešení potíží s poslední těch, které jsou. Kromě toho můžete data monitorování získat podrobný přehled o vaší aplikaci. Tyto znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Platforma Azure Monitor poskytuje základní úroveň infrastruktura metriky a protokoly pro většinu služeb Microsoft Azure. Podrobnosti najdete v tématu [Přehled monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Diagnostické protokoly Azure jsou protokoly generované prostředek, které poskytují bohatě vybaveným a časté informace o fungování tohoto prostředku. Data Factory uloží diagnostické protokoly ve službě Azure Monitor.

## <a name="persist-data-factory-data"></a>Uchování dat Data Factory
Objekt pro vytváření dat se uchovávají pouze spuštění kanálu dat pro 45 dní. Pokud chcete zachovat spuštění kanálu dat pro více než 45 dnů, používat Azure Monitor, nemůžete provádět směrování pouze diagnostických protokolů pro analýzy, je možné zachovat do účtu úložiště a tak budete mít objekt pro vytváření informace po dobu trvání podle vašeho výběru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

* Uložte je do **účtu úložiště** pro auditování nebo ruční kontrolu. Můžete zadat dobu uchování (ve dnech), pomocí nastavení diagnostiky.
* Stream je **Event Hubs** za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.
* Analyzovat pomocí **Log Analytics**

Můžete použít úložiště účtu nebo událostí centra oboru názvů, který není ve stejném předplatném jako prostředek, který je vysílá protokoly. Uživatel, který konfiguruje nastavení musí mít přístup odpovídající přístup na základě rolí (RBAC) ovládacího prvku k oběma předplatným.

## <a name="set-up-diagnostic-logs"></a>Nastavili odesílání diagnostických protokolů

### <a name="diagnostic-settings"></a>Nastavení diagnostiky
Diagnostické protokoly pro prostředky rozsáhlých výpočetních prostředků jsou nakonfigurováni pomocí nastavení diagnostiky. Nastavení diagnostiky pro ovládací prvek zdroje:

* Diagnostické protokoly se odešle (účet úložiště, Event Hubs, a/nebo Log Analytics).
* Kategorie protokolu, které se odesílají.
* Jak dlouho se každá kategorie protokolu uchovávat v účtu úložiště
* Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů mezi 1 a 2147483647.
* Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pouze služby Event Hubs nebo Log Analytics jsou vybrané možnosti), zásady uchovávání informací nemají žádný vliv.
* Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Povolení diagnostických protokolů přes rozhraní REST API

Vytvoří nebo aktualizuje nastavení diagnostiky v rozhraní REST API služby Azure Monitor

**Požadavek**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Hlavičky**
* Nahraďte `{api-version}` s `2016-09-01`.
* Nahraďte `{resource-id}` s ID prostředku, pro kterou chcete upravit nastavení diagnostiky prostředku. Další informace [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-portal.md).
* Nastavte `Content-Type` záhlaví `application/json`.
* Nastavte hlavičku autorizace JSON web token, který můžete získat ze služby Azure Active Directory. Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

**Text**
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
| StorageAccountId |Řetězec | ID prostředku účtu úložiště, do které chcete posílat diagnostické protokoly |
| serviceBusRuleId |Řetězec | Identifikátor služby Service bus pravidlo oboru názvů služby Service bus ve kterém by měl mít vytvořené pro streamování diagnostických protokolů služby Event Hubs. Toto pravidlo je ID ve formátu: "{služby Service bus ID prostředku} /authorizationrules/ {název klíče}".|
| ID pracovního prostoru | Komplexní typ | Pole zrna metriku času a jejich zásady uchovávání informací. V současné době je tato vlastnost prázdná. |
|Průzkumníku metrik| Hodnoty parametrů, které se mají předat vyvolaný kanál spuštění kanálu| Mapování názvů parametrů hodnot argumentů v objektu JSON |
| Protokoly| Komplexní typ| Název kategorie diagnostických protokolů pro typ prostředku. Pokud chcete získat seznam kategorií protokol diagnostiky pro prostředek, nejprve proveďte operaci NAČÍST nastavení diagnostiky. |
| category| Řetězec| Pole kategorie protokolu a jejich zásady uchovávání informací |
| timeGrain | Řetězec | Členitost metriky, které jsou zachyceny ve formátu ISO 8601 doby trvání. Musí být PT1M (jednu minutu)|
| povoleno| Logická hodnota | Určuje, zda je povoleno shromažďování této kategorie metriky nebo protokolu pro tento prostředek|
| parametru retentionPolicy.| Komplexní typ| Popisuje zásady uchovávání informací pro kategorii metrika nebo protokolu. Používá se pro pouze možnost účtu úložiště.|
| dny| Int| Počet dnů uchování metriky nebo protokoly. Hodnota 0 protokoly uchovává po neomezenou dobu. Používá se pro pouze možnost účtu úložiště. |

**Odpověď**

200 OK


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

Získejte informace o nastavení diagnostiky v rozhraní REST API služby Azure Monitor

**Požadavek**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Hlavičky**
* Nahraďte `{api-version}` s `2016-09-01`.
* Nahraďte `{resource-id}` s ID prostředku, pro kterou chcete upravit nastavení diagnostiky prostředku. Pro další informace o použití skupin prostředků ke správě prostředků Azure.
* Nastavte `Content-Type` záhlaví `application/json`.
* Nastavte hlavičku autorizace webového tokenu JSON, který získáte ze služby Azure Active Directory. Další informace najdete v tématu věnovaném ověřování požadavků.

**Odpověď**

200 OK

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
[Další informace v tomto poli](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schéma protokolů a událostí

### <a name="activity-run-logs-attributes"></a>Aktivita spuštění zaznamená atributy

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
   "properties:"
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

| Vlastnost | Typ | Popis | Příklad: |
| --- | --- | --- | --- |
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Úroveň 4 vždy platí pro spuštění protokoly aktivit. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování konkrétní žádost o ukončení až do konce | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formát UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Řetězec| ID spuštění aktivit | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Řetězec | ID přidruženého prostředku pro objekt pro vytváření zdrojů dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavte tuto vlastnost na "ActivityRuns" | `ActivityRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na "Informační" | `Informational` |
|operationName| Řetězec |Název aktivitu se stavem. Pokud je ve stavu spuštění prezenčního signálu, je `MyActivity -`. Pokud je stav ukončení prezenčního signálu, je `MyActivity - Succeeded` s konečného stavu | `MyActivity - Succeeded` |
|pipelineName| Řetězec | Název kanálu | `MyPipeline` |
|Název aktivity activityName| Řetězec | Název aktivity | `MyActivity` |
|start| Řetězec | Spuštění aktivity při spuštění v časový interval, formát UTC | `2017-06-26T20:55:29.5007959Z`|
|konec| Řetězec | Ukončení aktivity spustit v časový interval, formátu UTC. Pokud aktivita nebyla skončila, ale (diagnostický protokol spuštění aktivity), výchozí hodnotu `1601-01-01T00:00:00Z` nastavena.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Atributy protokoly spuštění kanálu

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

| Vlastnost | Typ | Popis | Příklad: |
| --- | --- | --- | --- |
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Úroveň 4 platí pro spuštění protokoly aktivit. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování konkrétní žádost o ukončení až do konce | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formát UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Řetězec | ID přidruženého prostředku pro objekt pro vytváření zdrojů dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavte tuto vlastnost na "PipelineRuns" | `PipelineRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na "Informační" | `Informational` |
|operationName| Řetězec |Název kanálu se stavem. "Kanál – bylo úspěšné" s konečný stav po dokončení spuštění kanálu| `MyPipeline - Succeeded` |
|pipelineName| Řetězec | Název kanálu | `MyPipeline` |
|start| Řetězec | Spuštění aktivity při spuštění v časový interval, formát UTC | `2017-06-26T20:55:29.5007959Z`|
|konec| Řetězec | Konec aktivita se spustí v časový interval, formátu UTC. Pokud aktivita nebyla skončila, ale (diagnostický protokol spuštění aktivity), výchozí hodnotu `1601-01-01T00:00:00Z` nastavena.  | `2017-06-26T20:55:29.5007959Z` |
|status| Řetězec | Konečný stav kanálu spuštěním (Succeeded nebo Failed) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Spuštění triggeru protokoly atributy

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

| Vlastnost | Typ | Popis | Příklad: |
| --- | --- | --- | --- |
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Nastavení úrovně 4 pro spuštění protokoly aktivit. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování konkrétní žádost o ukončení až do konce | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formát UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|SpuštěcíID| Řetězec| ID spuštění aktivační události | `08587023010602533858661257311` |
|resourceId| Řetězec | ID přidruženého prostředku pro objekt pro vytváření zdrojů dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavte tuto vlastnost na "PipelineRuns" | `PipelineRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na "Informační" | `Informational` |
|operationName| Řetězec |Název triggeru s konečného stavu, jestli se úspěšně aktivoval. "MyTrigger - bylo úspěšné" Pokud se úspěšně dokončil prezenčního signálu| `MyTrigger - Succeeded` |
|Název aktivační události| Řetězec | Název triggeru | `MyTrigger` |
|triggerType| Řetězec | Typ aktivační události (ruční aktivační události nebo aktivační událost plánovače) | `ScheduleTrigger` |
|triggerEvent| Řetězec | Události triggeru | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Řetězec | Spuštění triggeru fire v časový interval, formát UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Řetězec | Konečný stav určuje, zda trigger úspěšně spustí (Succeeded nebo Failed) | `Succeeded`|

## <a name="metrics"></a>Metriky

Azure Monitor umožňuje využívat telemetrii s cílem získat přehled o výkonu a stavu vašich úloh v Azure. Metriky (také nazývané čítače výkonu) většinu služeb Azure prostředky emitovány je nejdůležitější typu Azure telemetrická data. Platforma Azure Monitor poskytuje několik způsobů, jak konfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

ADFV2 generuje následující metriky

| **Metrika**           | **Metriky zobrazovaný název**         | **Jednotka** | **Typ agregace** | **Popis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Úspěšné metriky spuštění kanálu | Počet    | Celkem                | Celkový počet kanálů úspěšných běhů v rámci minut časového období |
| PipelineFailedRuns   | Se nezdařilo metrika spuštění kanálu    | Počet    | Celkem                | Celkový počet kanálů neúspěšných běhů v rámci minut časového období    |
| ActivitySucceededRuns | Úspěšné běhy metriky aktivity | Počet    | Celkem                | Spuštění bylo úspěšné. Celkový počet aktivit v rámci minut časového období  |
| ActivityFailedRuns   | Metriky aktivity spuštění se nezdařilo    | Počet    | Celkem                | Celková aktivita neúspěšných běhů v rámci minut časového období     |
| TriggerSucceededRuns | Aktivační událost metriky spuštění bylo úspěšné  | Počet    | Celkem                | Celkový počet aktivační událost spouští v rámci minut časového období bylo úspěšné.   |
| TriggerFailedRuns    | Se nezdařilo metrika spuštění aktivační události     | Počet    | Celkem                | Celkový počet aktivační událost spouští v rámci minut časového období se nezdařilo      |

Pro přístup k metriky, postupujte podle pokynů v článku – https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Metriky objektů pro vytváření dat monitorování službou Azure Monitor

Integrace služby Azure Data Factory můžete použít prostřednictvím služby Azure Monitor pro směrování dat do Azure monitoru. Tato integrace je užitečná v následujících scénářích:

1.  Chcete vytváření složitých dotazů na celou řadu metrik, jenž je publikována serverem služby Data Factory do Azure monitoru. Můžete také vytvořit vlastní oznámení na tyto dotazy prostřednictvím služby Azure Monitor.

2.  Chcete sledovat v rámci datové továrny. Směrovat data z několika továren s dat do jednoho pracovního prostoru Azure Monitor.

Pro zavedení sedm po minutách a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurace nastavení diagnostiky a pracovního prostoru

Povolení diagnostických nastavení pro službu data factory.

1.  Vyberte **Azure Monitor** -> **nastavení diagnostiky** -> vyberte služby data factory -> zapnout na diagnostiky.

    ![monitorování oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Zadejte nastavení diagnostiky, včetně konfigurace pracovního prostoru.

    ![monitorování oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Nainstalovat Azure Data Factory Analytics z Azure Marketplace

![monitorování oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitorování oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klikněte na tlačítko **vytvořit** a vyberte pracovní prostor a pracovní prostor nastavení.

![monitorování oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Metriky objektů pro vytváření dat monitorování

Instalace **Azure Data Factory Analytics** vytvoří výchozí sadu zobrazení, která umožňuje následující metriky:

- Spuštění kanálu ADF spuštění-1) pomocí služby Data Factory

- Spuštění aktivit spuštění ADF-2) pomocí služby Data Factory

- Spuštění aktivační události spuštění ADF-3) pomocí služby Data Factory

- Chyby kanálu ADF chyby-1) prvních 10 službou Data Factory

- Spuštění aktivit prvních 10 ADF chyby-2) pomocí služby Data Factory

- Chyby ADF-3) prvních 10 aktivační událost chyby službou Data Factory

- Spuštění aktivit ADF statistiky-1) podle typu

- Statistiky ADF-2) spuštění aktivační události podle typu

- Spuštění kanálu ADF statistiky – 3) maximální doba trvání

![monitorování oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitorování oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Můžete vizualizovat metriky výše, podívejte se na dotazy za tyto metriky, upravit dotazy, vytvářet výstrahy a tak dále.

![monitorování oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Výstrahy

Může vyvolat upozornění na podporované metriky ve službě Data Factory. Klikněte na tlačítko **výstrahy** tlačítko v datové továrně **monitorování** stránky.

![Možnost výstrahy](media/monitor-using-azure-monitor/alerts_image1.png)

Tím přejdete **výstrahy** stránky.

![Stránky s upozorněními](media/monitor-using-azure-monitor/alerts_image2.png)

Můžete také přihlásit k webu Azure portal a klikněte na tlačítko **monitorování –&gt; výstrahy** kontaktovat **výstrahy** přímo stránku.

![Výstrahy v nabídce portálu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Vytvořit výstrahy

1.  Klikněte na tlačítko **+ nové pravidlo upozornění** vytvořit nové oznámení.

    ![Nové pravidlo upozornění](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definovat **upozornění podmínku**.

    > [!NOTE]
    > Je nutné vybrat **všechny** v **filtrovat podle typu prostředku**.

    ![Podmínka výstrahy, obrazovky: 1 z 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Podmínka výstrahy, obrazovky 2 ze 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Podmínka výstrahy, obrazovky 3 ze 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definovat **podrobnosti výstrahy**.

    ![Podrobnosti upozornění](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definovat **skupiny akcí**.

    ![Skupina akcí obrazovky 1 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Skupina akcí obrazovky 2 ze 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Skupina akcí obrazovky 3 ze 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Skupina akcí obrazovky 4 ze 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Další postup
Zobrazit [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md) článku se dozvíte o monitorování a Správa kanálů spuštěním.
