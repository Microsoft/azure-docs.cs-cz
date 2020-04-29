---
title: Streamování protokolů platformy Azure do centra událostí
description: Naučte se streamovat protokoly prostředků Azure do centra událostí, abyste mohli odesílat data do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658910"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Streamování protokolů platformy Azure do Azure Event Hubs
[Protokoly platforem](platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé.  Tento článek popisuje streamování protokolů platforem na centra událostí, které slouží k odesílání dat do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Co se dá dělat s protokoly platforem odesílanými do centra událostí
Streamování protokolů platforem v Azure do centra událostí, které poskytuje následující funkce:

* **Streamování protokolů na systémy protokolování a telemetrie od jiných výrobců** – Streamujte všechny protokoly vaší platformy do jednoho centra událostí, do kterého se budou zasílat data protokolu kanálu pro Siem nebo nástroj Log Analytics od jiného výrobce.
  
* **Vytvoření vlastní telemetrie a protokolovací platformy** – vysoce škálovatelná povaha pro publikování a odběr centra událostí vám umožní pružně ingestovat protokoly platforem na vlastní platformu teletry. Podrobnosti najdete v tématu [navrhování a změna velikosti platformy telemetrie s globálním škálováním na platformě Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .

* **Zobrazení stavu služby podle streamování dat do Power BI** – pomocí Event Hubs, Stream Analytics a Power BI můžete transformovat diagnostická data na vaše služby Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v tématu [Stream Analytics a Power BI: řídicí panel analýzy v reálném čase pro streamovaná data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

    Následující kód SQL je ukázkový Stream Analytics dotaz, který můžete použít k analýze všech dat protokolu v tabulce Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Požadavky
[Centrum událostí](../../event-hubs/event-hubs-create.md) je potřeba vytvořit, pokud ho ještě nemáte. Pokud již máte nastavení diagnostiky pomocí tohoto oboru názvů Event Hubs, bude toto centrum událostí znovu použito.

Zásady sdíleného přístupu pro obor názvů definují oprávnění, která má mechanismus streamování. Streamování do Event Hubs vyžaduje oprávnění spravovat, odesílat a naslouchat. Zásady sdíleného přístupu můžete vytvořit nebo upravit na Azure Portal na kartě konfigurovat pro svůj obor názvů Event Hubs.

Chcete-li aktualizovat nastavení diagnostiky tak, aby zahrnovalo streamování, musíte mít oprávnění ListKey k tomuto autorizačnímu pravidlu Event Hubs. Obor názvů Event Hubs nemusí být ve stejném předplatném, jako je předplatné, které vysílá protokoly, pokud uživatel, který nastavení nakonfiguruje, má odpovídající přístup RBAC k oběma předplatným a oba odběry jsou ve stejném tenantovi AAD.

## <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky
Odeslání protokolů platformy do centra událostí a dalších cílů vytvořením nastavení diagnostiky pro prostředek Azure. Podrobnosti najdete [v tématu Vytvoření nastavení diagnostiky pro shromáždění protokolů a metrik v Azure](diagnostic-settings.md) .

## <a name="collect-data-from-compute-resources"></a>Shromažďování dat z výpočetních prostředků
Nastavení diagnostiky bude shromažďovat protokoly prostředků pro výpočetní prostředky Azure, jako jsou všechny ostatní prostředky, ale ne jejich hostovaný operační systém nebo úlohy. Chcete-li shromáždit tato data, nainstalujte [agenta Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Využívání dat protokolu z Center událostí
Protokoly platforem z Center událostí se spotřebovávají ve formátu JSON s prvky v následující tabulce.

| Název prvku | Popis |
| --- | --- |
| eviduje |Pole všech událostí protokolu v této datové části. |
| time |Čas, kdy došlo k události. |
| category |Kategorie protokolu pro tuto událost. |
| resourceId |ID prostředku prostředku, který vygeneroval tuto událost |
| operationName |Název operace |
| level |Nepovinný parametr. Označuje úroveň události protokolu. |
| properties |Vlastnosti události Ty se budou lišit pro každou službu Azure, jak [ ]()je popsáno v tématu. |


Následuje ukázka výstupních dat z Event Hubs pro protokol prostředku:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte nastavení diagnostiky pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
* [Streamování Azure Active Directory protokolů pomocí Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

