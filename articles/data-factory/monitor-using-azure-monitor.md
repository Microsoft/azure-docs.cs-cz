---
title: Monitorovat pomocí monitorování Azure data Factory | Microsoft Docs
description: Naučte se používat Azure monitorování pro monitorování služby Data Factory kanálů povolením diagnostických protokolů informace z Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 1399455fb727c27e22da8c5525eec87e343d46cc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Monitorovat pomocí monitorování Azure data Factory  
Cloudové aplikace jsou komplexní s mnoha přesunutí částmi. Monitorování poskytuje data a ujistěte se, že vaše aplikace zůstává nahoru a spuštěna v dobrém stavu. Také pomáhá stave vypnout potenciální problémy nebo vyřešit potíže s uplynulou těch, které jsou. Kromě toho můžete data monitorování a získáte přehled o hloubkové o vaší aplikaci. Tato znalostní báze můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah.

Monitorování Azure poskytuje základní úroveň infrastruktura metriky a protokoly pro většina služeb v Microsoft Azure. Podrobnosti najdete v tématu [Přehled monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure diagnostické protokoly jsou protokoly vygenerované prostředek, které poskytují bohatou a často data o operaci prostředku. Objekt pro vytváření dat výstupy diagnostické protokoly v nástroji Sledování Azure. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [monitorování a Správa kanálů v objektu pro vytváření dat version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Diagnostické protokoly

* Uložte je do **účet úložiště** pro auditování nebo ruční kontroly. Můžete zadat dobu uchování (ve dnech) používá nastavení pro diagnostiku.
* Stream, aby **Event Hubs** pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.
* Analyzovat, s **analýzy protokolů**

Můžete použít úložiště účet nebo události rozbočovače obor názvů, který není ve stejném předplatném jako prostředek, který je generování protokoly. Uživatel, který konfiguruje nastavení, musí mít k odpovídající přístup na základě rolí (RBAC) řízení přístupu na oba odběry.

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostické protokoly

### <a name="diagnostic-settings"></a>Nastavení diagnostiky
Diagnostické protokoly pro jiný výpočetní prostředky jsou konfigurováni pomocí nastavení pro diagnostiku. Nastavení diagnostiky pro prostředek řízení:

* Kde diagnostické protokoly jsou odesílány (účet úložiště, Event Hubs nebo analýzy protokolů).
* Kategorie protokolu, které se odesílají.
* Jak dlouho každou kategorii protokolu se uchovávají v účtu úložiště
* Uchování nulový počet dnů znamená, že jsou protokoly v nekonečné smyčce. Hodnota, jinak hodnota může být libovolný počet dnů od 1 do 2147483647.
* Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pouze Event Hubs nebo analýzy protokolů jsou vybrané možnosti), zásady uchovávání informací mít žádný vliv.
* Zásady uchovávání informací jsou použité denní, takže na konci za den (UTC), protokoly dnem, který je teď nad rámec uchovávání se zásada odstraní. Například pokud jste měli zásady uchovávání informací jeden den, od začátku dnešní den protokoly z včerejšek před den by odstraněn.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Povolení diagnostických protokolů přes rozhraní REST API

Vytvořit nebo aktualizovat nastavení diagnostiky REST API služby Azure monitorování

**Požadavek**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Hlavičky**
* Nahraďte `{api-version}` s `2016-09-01`.
* Nahraďte `{resource-id}` s ID prostředku prostředku, pro kterou chcete upravit nastavení pro diagnostiku. Další informace [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-portal.md).
* Nastavte `Content-Type` hlavičky k `application/json`.
* Nastavte hlavičku autorizace webového tokenu JSON, které jste získali od Azure Active Directory. Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/active-directory-authentication-scenarios.md).

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
| StorageAccountId |Řetězec | ID prostředku účtu úložiště, do kterého chcete odesílání diagnostických protokolů |
| serviceBusRuleId |Řetězec | Sběrnice služby pravidlo ID oboru názvů sběrnice služby, ve kterém chcete mít centra událostí vytvořit pro streamování diagnostické protokoly. Pravidlo ID je ve formátu: "{service bus ID prostředku} /authorizationrules/ {název klíče}".|
| ID pracovního prostoru | Komplexní typ | Pole zrna metriky čas a jejich zásady uchovávání informací. V současné době je tato vlastnost prázdná. |
|Průzkumníku metrik| Hodnoty parametru kanálu spustit mají být předány vyvolaná kanálu| Objekt JSON mapování názvy parametrů pro argument hodnoty | 
| Protokoly| Komplexní typ| Název kategorie protokolů diagnostiky pro typ prostředku. Pokud chcete získat seznam kategorií protokolů diagnostiky pro prostředek, nejprve proveďte nastavení pro diagnostiku operaci GET. |
| category| Řetězec| Pole kategorií protokolu a jejich zásady uchovávání informací |
| Časovými úseky | Řetězec | Členitost metriky, které jsou ukládány do formátu ISO 8601 doba trvání. Musí být PT1M (jedné minuty)|
| povoleno| Logická hodnota | Určuje, zda je povoleno kolekci této kategorie metrika nebo protokolu pro tento prostředek|
| retentionPolicy| Komplexní typ| Popisuje zásady uchovávání informací pro kategorii metrika nebo protokolu. Použít pro pouze možnost účet úložiště.|
| dny| Int| Počet dní, které chcete zachovat metriky nebo protokoly. Hodnota 0 uchovává protokoly bez omezení. Použít pro pouze možnost účet úložiště. |

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

Získat informace o nastavení diagnostiky v Azure monitorování REST API

**Požadavek**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Hlavičky**
* Nahraďte `{api-version}` s `2016-09-01`.
* Nahraďte `{resource-id}` s ID prostředku prostředku, pro kterou chcete upravit nastavení pro diagnostiku. Pro další informace o použití skupin prostředků ke správě prostředků Azure.
* Nastavte `Content-Type` hlavičky k `application/json`.
* Nastavte hlavičku autorizace webového tokenu JSON které jste získali od Azure Active Directory. Další informace najdete v části Authenticating požadavků.

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
[Další informace v tomto poli](https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Schéma protokoly a události

### <a name="activity-run-logs-attributes"></a>Aktivita spustit protokoly atributy

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
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Úroveň 4 je vždy případ aktivity při spuštění protokoly. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování určité žádosti klient server | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formátu UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Řetězec| ID aktivity při spuštění | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Řetězec | ID přidružených prostředků pro prostředek objektu pro vytváření dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavením této vlastnosti "ActivityRuns" | `ActivityRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavením této vlastnosti "Informační" | `Informational` |
|operationName| Řetězec |Název aktivitu se stavem. Pokud je ve stavu počáteční prezenčního signálu, je `MyActivity -`. Pokud je ve stavu prezenčního signálu end, je `MyActivity - Succeeded` s konečného stavu | `MyActivity - Succeeded` |
|pipelineName| Řetězec | Název kanálu | `MyPipeline` |
|activityName| Řetězec | Název aktivity | `MyActivity` |
|start| Řetězec | Spuštění aktivity při spuštění v časový interval, formátu UTC | `2017-06-26T20:55:29.5007959Z`|
|konec| Řetězec | Konce aktivity při spuštění v časový interval, formátu UTC. Pokud nebyl ukončen aktivity ještě (protokolů diagnostiky pro spuštění aktivity), výchozí hodnota je `1601-01-01T00:00:00Z` nastavena.  | `2017-06-26T20:55:29.5007959Z` |


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
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Úroveň 4 je případ aktivity při spuštění protokoly. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování určité žádosti klient server | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formátu UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|identifikátor runId| Řetězec| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Řetězec | ID přidružených prostředků pro prostředek objektu pro vytváření dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavením této vlastnosti "PipelineRuns" | `PipelineRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavením této vlastnosti "Informační" | `Informational` |
|operationName| Řetězec |Název kanálu se stavem. "Kanál – bylo úspěšné" s konečného stavu po dokončení spuštění kanálu| `MyPipeline - Succeeded` |
|pipelineName| Řetězec | Název kanálu | `MyPipeline` |
|start| Řetězec | Spuštění aktivity při spuštění v časový interval, formátu UTC | `2017-06-26T20:55:29.5007959Z`|
|konec| Řetězec | Konec aktivity spouští v časový interval, formátu UTC. Pokud nebyl ukončen aktivity ještě (protokolů diagnostiky pro spuštění aktivity), výchozí hodnota je `1601-01-01T00:00:00Z` nastavena.  | `2017-06-26T20:55:29.5007959Z` |
|status| Řetězec | Poslední stav kanálu spustit (úspěšné nebo neúspěšné) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Spustit aktivační událost zaznamená atributy

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
| Úroveň |Řetězec | Úroveň diagnostických protokolů. Nastavte úroveň 4 pro aktivitu spustit protokoly. | `4`  |
| correlationId |Řetězec | Jedinečné ID pro sledování určité žádosti klient server | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Řetězec | Čas události v časový interval, formátu UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|SpuštěcíID| Řetězec| ID spuštění aktivační události | `08587023010602533858661257311` |
|resourceId| Řetězec | ID přidružených prostředků pro prostředek objektu pro vytváření dat | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Řetězec | Kategorie diagnostické protokoly. Nastavením této vlastnosti "PipelineRuns" | `PipelineRuns` |
|úroveň| Řetězec | Úroveň diagnostických protokolů. Nastavením této vlastnosti "Informační" | `Informational` |
|operationName| Řetězec |Název aktivační události s konečného stavu jestli se úspěšně aktivoval. "MyTrigger - bylo úspěšné" Pokud prezenční signál byla úspěšná| `MyTrigger - Succeeded` |
|triggerName| Řetězec | Název aktivační události | `MyTrigger` |
|triggerType| Řetězec | Typ aktivační události (ruční aktivační události nebo plán aktivační události) | `ScheduleTrigger` |
|triggerEvent| Řetězec | Události Aktivační události | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Řetězec | Začátek ještě efektivněji aktivační události v časový interval, formátu UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Řetězec | Konečný stav jestli aktivační událost úspěšně vyvolána (úspěšné nebo neúspěšné) | `Succeeded`|

## <a name="metrics"></a>Metriky

Azure monitorování umožňuje využívat telemetrie a získáte přehled o výkonu a stavu úlohy v Azure. Nejdůležitější typ Azure telemetrická data je metriky (také nazývané čítače výkonu) vysílaných prostředků nejvíce Azure. Monitorování Azure poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

ADFV2 vysílá následující metriky

| **Metrika**           | **Metriky zobrazovaný název**         | **jednotka** | **Typ agregace** | **Popis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Úspěšné metriky spuštění kanálu | Počet    | Celkem                | Celkový počet kanálů spustí úspěšně v rámci časového období minut |
| PipelineFailedRuns   | Metriky spustí kanálu se nezdařilo    | Počet    | Celkem                | Celkový počet kanálů běží v rámci minutu okna se nezdařila    |
| ActiviySucceededRuns | Úspěšné metriky spuštění aktivity | Počet    | Celkem                | Celková aktivita se spustí úspěšně v rámci časového období minut  |
| ActivityFailedRuns   | Spustí metriky aktivity se nezdařilo    | Počet    | Celkem                | Celková aktivita běží v rámci okno minut se nezdařilo     |
| TriggerSucceededRuns | Aktivační událost metriky spustí bylo úspěšné  | Počet    | Celkem                | Celkový aktivační událost spustí úspěšně v rámci časového období minut   |
| TriggerFailedRuns    | Aktivační událost metriky spuštění se nezdařilo     | Počet    | Celkem                | Celkový počet aktivační událost běží v rámci okno minut se nezdařilo      |

Pro přístup k metriky, postupujte podle pokynů v článku – https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="alerts"></a>Výstrahy

Můžete zvýšit výstrahy na podporované metriky v datové továrně. Klikněte na tlačítko **výstrahy** na objektu pro vytváření dat tlačítko **monitorování** stránky.

![Možnost výstrahy](media/monitor-using-azure-monitor/alerts_image1.png)

Tím přejdete **výstrahy** stránky.

![stránka výstrah](media/monitor-using-azure-monitor/alerts_image2.png)

Můžete také přihlásit k portálu Azure a klikněte na tlačítko **monitorování –&gt; výstrahy** k dosažení **výstrahy** stránky přímo.

![Výstrahy v nabídce portálu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Vytvářet výstrahy

1.  Klikněte na tlačítko **+ nové pravidlo výstrahy** vytvořit nové oznámení.

    ![nové pravidlo výstrahy](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definování **výstrahy podmínku**.

    > [!NOTE]
    > Je nutné vybrat **všechny** v **filtrovat podle typu prostředku**.

    ![Podmínka výstrahy, obrazovky 1 ze 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Podmínka výstrahy, obrazovky 2 ze 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Podmínka výstrahy, obrazovce 3 ze 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definování **podrobnosti výstrahy**.

    ![Podrobnosti upozornění](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definování **akce skupiny**.

    ![Akce skupiny, obrazovky 1 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Akce skupiny, obrazovky 2 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Akce skupiny, obrazovky 3 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Akce skupiny, obrazovky 4 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Další postup
V tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md) článku se dozvíte o monitorování a Správa kanálů spuštěním. 