---
title: Monitorování datových továren pomocí Azure Monitor | Microsoft Docs
description: Naučte se používat Azure Monitor k monitorování kanálů Data Factory povolením diagnostických protokolů s informacemi z Azure Data Factory.
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
ms.openlocfilehash: 0614de8bbb1429c84bf5f2e55c1765f3e4863f3a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141130"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Výstrahy a monitorování datových továren pomocí Azure Monitor
Cloudové aplikace jsou komplexní s mnoha pohybujícími se částmi. Monitorování poskytuje data, která zajistí, že vaše aplikace zůstane v dobrém stavu. Také vám pomůže nastavovat potenciální problémy nebo řešit potíže s předchozími. Kromě toho můžete k získání podrobných přehledů o vaší aplikaci použít data monitorování. Tato znalostní báze vám může pomoci zvýšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Azure Monitor poskytuje metriky a protokoly infrastruktury základní úrovně pro většinu služeb v Microsoft Azure. Podrobnosti najdete v tématu [monitorování – přehled](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Diagnostické protokoly Azure jsou protokoly emitované prostředkem, který poskytuje bohatou a častou data o provozu daného prostředku. Data Factory vytvoří výstupy diagnostických protokolů v Azure Monitor.

## <a name="persist-data-factory-data"></a>Zachovat data Data Factory
Data Factory ukládá pouze data spuštění kanálu po dobu 45 dnů. Pokud chcete zachovat data o běhu kanálu po dobu více než 45 dnů, pomocí Azure Monitor, nemůžete směrovat protokoly diagnostiky jenom pro analýzu, můžete je zachovat v účtu úložiště, abyste měli k dispozici informace o výrobě po dobu trvání výběru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

* Uložte je do **účtu úložiště** pro auditování nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí nastavení diagnostiky.
* Streamujte je **Event Hubs** k ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je například Power BI.
* Analyzovat je pomocí **Log Analytics**

Můžete použít účet úložiště nebo obor názvů centra událostí, který není ve stejném předplatném jako prostředek, který vysílá protokoly. Uživatel, který nakonfiguruje toto nastavení, musí mít přístup k oběma předplatným odpovídajícímu řízení přístupu na základě role (RBAC).

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

### <a name="diagnostic-settings"></a>Nastavení diagnostiky
Diagnostické protokoly pro prostředky, které nejsou výpočetními prostředky, se konfigurují pomocí nastavení diagnostiky. Nastavení diagnostiky pro ovládací prvek prostředku:

* Kde jsou odesílány diagnostické protokoly (účet úložiště, Event Hubs nebo protokoly Azure Monitor).
* Které kategorie protokolů jsou odesílány.
* Jak dlouho by se měly uchovávat jednotlivé kategorie protokolů v účtu úložiště.
* Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů mezi 1 a 2147483647.
* Pokud jsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázané (například jsou vybrané jenom Event Hubs nebo Azure Monitor protokoly), zásady uchovávání dat nemají žádný vliv.
* Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Povolení diagnostických protokolů přes rozhraní REST API

Vytvořit nebo aktualizovat nastavení diagnostiky v Azure Monitor REST API

**Požadavek**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Záhlaví**
* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku prostředku, pro který chcete upravit nastavení diagnostiky. Další informace o [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Nastavte hlavičku na `application/json`. `Content-Type`
* Nastavte autorizační hlavičku na webový token JSON, který získáte z Azure Active Directory. Další informace najdete v tématu [ověřování požadavků](../active-directory/develop/authentication-scenarios.md).

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
| storageAccountId |String | ID prostředku účtu úložiště, na který chcete odesílat diagnostické protokoly |
| serviceBusRuleId |String | ID pravidla služby Service Bus oboru názvů služby Service Bus, ve kterém chcete mít Event Hubs vytvořené pro streamování diagnostických protokolů. ID pravidla má formát: {Service Bus Resource ID}/authorizationrules/{Key Name}.|
| ID pracovního prostoru | Komplexní typ | Pole časových období metriky a jejich zásady uchovávání. V tuto chvíli je tato vlastnost prázdná. |
|metrics| Hodnoty parametrů běhu kanálu, které se mají předat vyvolanému kanálu| Názvy parametrů mapování objektů JSON na hodnoty argumentů |
| logs| Komplexní typ| Název kategorie diagnostického protokolu pro typ prostředku. Chcete-li získat seznam kategorií diagnostického protokolu pro určitý prostředek, proveďte nejprve operaci získat nastavení diagnostiky. |
| category| String| Pole kategorií protokolů a jejich zásady uchovávání informací |
| timeGrain | String | Členitost metrik, která je zachycena ve formátu ISO 8601 Duration. Musí být PT1M (jedna minuta)|
| enabled| Logická hodnota | Určuje, zda je pro tento prostředek povolena kolekce této metriky nebo kategorie protokolu.|
| retentionPolicy| Komplexní typ| Popisuje zásady uchovávání informací pro kategorii metrik nebo protokolů. Používá se jenom pro účet úložiště.|
| days| Int| Počet dní, po které se mají zachovat metriky nebo protokoly Hodnota 0 uchová protokoly po neomezenou dobu. Používá se jenom pro účet úložiště. |

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

Získat informace o nastavení diagnostiky v Azure Monitor REST API

**Požadavek**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Záhlaví**
* Nahraďte `{api-version}` za `2016-09-01` (Jak velká může být moje znalostní báze?).
* Nahraďte `{resource-id}` ID prostředku prostředku, pro který chcete upravit nastavení diagnostiky. Další informace o použití skupin prostředků ke správě prostředků Azure.
* Nastavte hlavičku na `application/json`. `Content-Type`
* Nastavte hlavičkou autorizace na JSON Web Token, které získáte z Azure Active Directory. Další informace najdete v tématu ověřování požadavků.

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
[Další informace najdete tady.](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schéma protokolů & události

### <a name="azure-monitor-schema"></a>Azure Monitor schéma

#### <a name="activity-run-logs-attributes"></a>Atributy protokolů spuštění aktivit

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
| Level |String | Úroveň diagnostických protokolů. Úroveň 4 je vždy případ pro protokoly spuštění aktivit. | `4`  |
| correlationId |String | Jedinečné ID pro sledování konkrétního požadavku na konec | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Čas události ve formátu TimeSpan, UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| ID spuštění aktivity | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | ID přidruženého prostředku pro prostředek datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategorie diagnostických protokolů. Nastavte tuto vlastnost na "ActivityRuns". | `ActivityRuns` |
|level| String | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na informativní. | `Informational` |
|operationName| String |Název aktivity se stavem. Pokud je stav spouštěcí prezenční signál, je `MyActivity -`. Pokud je stav koncového prezenčního signálu, jedná `MyActivity - Succeeded` se o konečný stav. | `MyActivity - Succeeded` |
|pipelineName| String | Název kanálu | `MyPipeline` |
|Název aktivity ActivityName| String | Název aktivity | `MyActivity` |
|Spuštění| String | Spuštění aktivity ve formátu TimeSpan, UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Konec aktivity spuštěný ve formátu TimeSpan, UTC. Pokud aktivita ještě neskončila (diagnostický protokol pro aktivitu začíná), `1601-01-01T00:00:00Z` je nastavena výchozí hodnota.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Atributy protokolů spuštění kanálu

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
| Level |String | Úroveň diagnostických protokolů. Úroveň 4 je případ pro protokoly spuštění aktivit. | `4`  |
| correlationId |String | Jedinečné ID pro sledování konkrétního požadavku na konec | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Čas události ve formátu TimeSpan, UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| ID spuštění kanálu | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | ID přidruženého prostředku pro prostředek datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategorie diagnostických protokolů. Nastavte tuto vlastnost na "PipelineRuns". | `PipelineRuns` |
|level| String | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na informativní. | `Informational` |
|operationName| String |Název kanálu se stavem. Kanál – úspěch s konečným stavem při dokončení běhu kanálu| `MyPipeline - Succeeded` |
|pipelineName| String | Název kanálu | `MyPipeline` |
|Spuštění| String | Spuštění aktivity ve formátu TimeSpan, UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Konec aktivity běží ve formátu TimeSpan, UTC. Pokud aktivita ještě neskončila (diagnostický protokol pro aktivitu začíná), `1601-01-01T00:00:00Z` je nastavena výchozí hodnota.  | `2017-06-26T20:55:29.5007959Z` |
|status| String | Konečný stav spuštění kanálu (úspěšné nebo neúspěšné) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Aktivační události spuštění atributů protokolů

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
| Level |String | Úroveň diagnostických protokolů. Nastavte na úroveň 4 pro protokoly spuštění aktivit. | `4`  |
| correlationId |String | Jedinečné ID pro sledování konkrétního požadavku na konec | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | Čas události ve formátu TimeSpan, UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| ID spuštění triggeru | `08587023010602533858661257311` |
|resourceId| String | ID přidruženého prostředku pro prostředek datové továrny | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | Kategorie diagnostických protokolů. Nastavte tuto vlastnost na "PipelineRuns". | `PipelineRuns` |
|level| String | Úroveň diagnostických protokolů. Nastavte tuto vlastnost na informativní. | `Informational` |
|operationName| String |Název triggeru s konečným stavem bez ohledu na to, jestli se úspěšně vyvolal "MyTrigger – úspěšné", pokud byl prezenční signál úspěšný| `MyTrigger - Succeeded` |
|triggerName| String | Název triggeru | `MyTrigger` |
|triggerType| String | Typ triggeru (manuální aktivační událost nebo aktivační událost plánovače) | `ScheduleTrigger` |
|triggerEvent| String | Událost triggeru | `ScheduleTime - 2017-07-06T01:50:25Z` |
|Spuštění| String | Spuštění triggeru triggeru ve formátu TimeSpan, UTC | `2017-06-26T20:55:29.5007959Z`|
|status| String | Konečný stav, zda se aktivační událost úspěšně aktivovala (úspěch nebo neúspěch) | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schéma

Log Analytics dědí schéma z Azure Monitor s následujícími výjimkami:

* První písmeno v každém názvu sloupce bude velkými písmeny, pro *ID korelace* v Azure monitor bude *ID korelace* v Log Analytics.
* *Úroveň* sloupce se vynechá.
* *Vlastnosti* dynamického sloupce budou zachovány jako následující dynamický typ objektu BLOB JSON:

    | Azure Monitor sloupec | Log Analytics sloupec | type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamické |
    | $. Properties. Anotac | Anotac | Dynamické |
    | $. Properties. Vstup | Vstup | Dynamické |
    | $. Properties. Výkonem | Výstup | Dynamické |
    | $. Properties. Chyba. errorCode | Kód chyby | int |
    | $. Properties. Chyba. zpráva | Chybová | string |
    | $. Properties. Chyba | Chyba | Dynamické |
    | $. Properties. Předchůdci | Předchůdci | Dynamické |
    | $. Properties. Ukazatelů | Parametry | Dynamické |
    | $. Properties. Třídy SystemParameters | Třídy SystemParameters | Dynamické |
    | $. Properties. Značky | Tags | Dynamické |
    
## <a name="metrics"></a>Metriky

Azure Monitor vám umožňuje využívat telemetrii, abyste získali přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitější typ dat telemetrie Azure jsou metriky (označované taky jako čítače výkonu) vydávané většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

ADFV2 emituje následující metriky:

| **Metrika**           | **Zobrazovaný název metriky**         | **Jednotce** | **Typ agregace** | **Popis**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Úspěšné metriky spuštění kanálu | Count    | Celkem                | Celkový počet úspěšných spuštění kanálů v minutovém okně |
| PipelineFailedRuns   | Neúspěšná metrika spuštění kanálu    | Count    | Celkem                | Celkový počet spuštění kanálů se nezdařil během minutového okna.    |
| ActivitySucceededRuns | Úspěšná aktivita spustí metriky | Count    | Celkem                | Celkový počet úspěšných spuštění aktivit do minutového okna  |
| ActivityFailedRuns   | Neúspěšná aktivita spustí metriky    | Count    | Celkem                | Celkový počet spuštění aktivit se nezdařil během minutového okna.     |
| TriggerSucceededRuns | Úspěšná aktivační událost spustí metriky  | Count    | Celkem                | Celkový počet úspěšných spuštění aktivačních událostí během minutového okna   |
| TriggerFailedRuns    | Neúspěšná aktivační událost spustí metriky     | Count    | Celkem                | Celkový počet spuštění triggeru se nezdařil do minutového okna.      |

Pokud chcete získat přístup k metrikám, postupujte podle pokynů v [Azure monitor datovou platformu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorování Data Factory metriky pomocí Azure Monitor

Pomocí Azure Data Factory integrace s Azure Monitor můžete směrovat data do Azure Monitor. Tato integrace je užitečná v následujících scénářích:

1.  Chcete zapisovat komplexní dotazy na bohatou sadu metrik, která je publikována nástrojem Data Factory k Azure Monitor. Můžete také vytvořit vlastní výstrahy na těchto dotazech prostřednictvím Azure Monitor.

2.  Chcete monitorovat napříč datovými továrnami. Data z několika datových továrn můžete směrovat do jednoho Azure Monitor pracovního prostoru.

Pokud chcete tuto funkci seznámit a předvedení této funkce, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurovat nastavení diagnostiky a pracovní prostor

Povolte nastavení diagnostiky pro datovou továrnu.

1. Na portálu přejděte na Azure Monitor a v nabídce **Nastavení** klikněte na **nastavení diagnostiky** .

2. Vyberte objekt pro vytváření dat, pro který chcete nastavit nastavení diagnostiky.
    
3. Pokud v datové továrně, kterou jste vybrali, neexistuje žádné nastavení, zobrazí se výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Pokud v datové továrně existují nastavení, zobrazí se seznam nastavení již nakonfigurovaných v této datové továrně. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Dejte nastavení název a zaškrtněte políčko pro **odeslání do Log Analytics**a pak vyberte pracovní prostor Log Analytics.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Klikněte na **Uložit**.

Po chvíli se nové nastavení zobrazí v seznamu nastavení pro tuto datovou továrnu a diagnostické protokoly se do tohoto pracovního prostoru streamují ihned po vygenerování nových dat události. Po vygenerování události a jejím zobrazení v Log Analytics může trvat až 15 minut.

> [!NOTE]
> Vzhledem k explicitnímu omezení všech daných tabulek protokolu Azure, které neobsahují více než 500 sloupců, **důrazně doporučujeme použít režim specifický pro prostředky**. Další informace najdete v tématu [Log Analytics známá omezení](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalace Azure Data Factory Analytics z Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klikněte na **vytvořit** a vyberte pracovní prostor a nastavení pracovního prostoru.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorovat Data Factory metriky

Instalace **Azure Data Factory Analytics** vytvoří výchozí sadu zobrazení, která umožňuje následující metriky:

- Spuštění ADF – 1) spuštění kanálu pomocí Data Factory

- Spuštění ADF – 2) spuštění aktivit pomocí Data Factory

- Spuštění ADF – 3) spuštění triggeru pomocí Data Factory

- Chyby ADF – 1) horní 10 chyb kanálu podle Data Factory

- Chyby ADF – 2) prvních 10 spuštění aktivit pomocí Data Factory

- Chyby ADF – 3) hlavních 10 chyb triggerů podle Data Factory

- Statistika ADF – 1) spuštění aktivit podle typu

- Statistika ADF – 2) spuštění triggeru podle typu

- Statistika ADF – 3) maximální doba běhu kanálu

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Můžete vizualizovat výše uvedené metriky, zobrazit dotazy za těmito metrikami, upravit dotazy, vytvořit výstrahy a tak dále.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Upozornění

Přihlaste se k Azure Portal a > kliknutím na monitorovat**výstrahy** vytvořte upozornění.

![Výstrahy v nabídce portálu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Vytvořit výstrahy

1.  Kliknutím na **+ nové pravidlo výstrahy** vytvořte novou výstrahu.

    ![Nové pravidlo upozornění](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definujte **podmínku upozornění**.

    > [!NOTE]
    > Ujistěte se, že jste vybrali možnost **vše** v poli **filtrovat podle typu prostředku**.

    ![Podmínka upozornění, obrazovka 1 ze 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Podmínka upozornění, obrazovka 2 ze 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Podmínka upozornění, obrazovka 3 ze 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Zadejte **Podrobnosti výstrahy**.

    ![Podrobnosti upozornění](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definujte **skupinu akcí**.

    ![Skupina akcí, obrazovka 1 ze 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Skupina akcí, obrazovka 2 ze 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Skupina akcí, obrazovka 3 ze 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Skupina akcí, obrazovka 4 ze 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů pomocí kódu najdete v článku [programové monitorování a Správa kanálů](monitor-programmatically.md) .
