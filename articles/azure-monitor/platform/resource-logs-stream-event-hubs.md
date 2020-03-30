---
title: Streamování protokolů platformy Azure do centra událostí
description: Zjistěte, jak streamovat protokoly prostředků Azure do centra událostí a odesílat data do externích systémů, jako jsou siemi třetích stran a další řešení pro analýzu protokolů.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658910"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Streamování protokolů platformy Azure do Azure Event Hubs
[Protokoly platformy](platform-logs-overview.md) v Azure, včetně protokolu aktivit Azure a protokolů prostředků, poskytují podrobné diagnostické a auditující informace pro prostředky Azure a platformu Azure, na které závisí.  Tento článek popisuje protokoly platformy streamování do centra událostí k odesílání dat do externích systémů, jako jsou siem třetích stran a další řešení analýzy protokolů.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Co můžete dělat s protokoly platformy odeslanými do centra událostí
Streamujte protokoly platformy v Azure do centra událostí, abyste zajistili následující funkce:

* **Streamujte protokoly do systémů protokolování a telemetrie třetích stran** – streamujte všechny protokoly platformy do jednoho centra událostí a můžete data protokolu do kanálu do nástroje SIEM nebo log analytics třetí strany.
  
* **Vytvořte vlastní telemetrickou a protokolovací platformu** – vysoce škálovatelná povaha publikování a odběru centra událostí umožňuje pružně ingestovat protokoly platformy do vlastní teletry platformy. Podrobnosti najdete [v tématu Navrhování a dimenzování globální platformy telemetrie v azure event hubech.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

* **Zobrazení stavu služeb pomocí streamování dat do Power BI** – Použijte centra událostí, Stream Analytics a Power BI a transformujte diagnostická data na přehledy služeb Azure téměř v reálném čase. Podrobnosti o tomto řešení najdete v [tématech Stream Analytics a Power BI: Řídicí panel analýzy v reálném čase pro streamování dat.](../../stream-analytics/stream-analytics-power-bi-dashboard.md)

    Následující kód SQL je ukázkový dotaz Stream Analytics, který můžete použít k analýze všech dat protokolu do tabulky Power BI:
    
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
Centrum událostí musíte [vytvořit,](../../event-hubs/event-hubs-create.md) pokud ho ještě nemáte. Pokud již máte diagnostické nastavení pomocí tohoto oboru názvů Event Hubs, bude toto centrum událostí znovu použito.

Zásady sdíleného přístupu pro obor názvů definují oprávnění, která má mechanismus streamování. Streamování do centra událostí vyžaduje oprávnění Ke správě, odesílání a naslouchání. Zásady sdíleného přístupu můžete vytvořit nebo upravit na webu Azure Portal na kartě Konfigurace pro obor názvů Event Hubs.

Chcete-li aktualizovat nastavení diagnostiky tak, aby zahrnovalo streamování, musíte mít oprávnění ListKey k tomuto autorizačnímu pravidlu centra událostí. Obor názvů Event Hubs nemusí být ve stejném předplatném jako předplatné, které vyzařuje protokoly, pokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným a obě předplatná jsou ve stejném tenantovi AAD.

## <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení
Odešlete protokoly platformy do centra událostí a dalších cílů vytvořením diagnostického nastavení pro prostředek Azure. Podrobnosti najdete [v tématu Vytvoření diagnostického nastavení pro shromažďování protokolů a metrik v Azure.](diagnostic-settings.md)

## <a name="collect-data-from-compute-resources"></a>Shromažďování dat z výpočetních prostředků
Diagnostická nastavení budou shromažďovat protokoly prostředků pro výpočetní prostředky Azure, jako je jakýkoli jiný prostředek, ale ne jejich hostovaný operační systém nebo úlohy. Chcete-li shromažďovat tato data, nainstalujte [agenta Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Využití dat protokolu z centra událostí
Protokoly platformy z rozbočovačů událostí jsou spotřebovány ve formátu JSON s prvky v následující tabulce.

| Název prvku | Popis |
| --- | --- |
| Záznamy |Pole všech událostí protokolu v této datové části. |
| time |Čas, kdy k události došlo. |
| category |Kategorie protokolu pro tuto událost. |
| resourceId |ID prostředku, který vygeneroval tuto událost. |
| operationName |Název operace. |
| level |Nepovinný parametr. Označuje úroveň události protokolu. |
| properties |Vlastnosti události. Ty se budou lišit pro každou [ ]()službu Azure, jak je popsáno v . |


Následují ukázková výstupní data z centra událostí pro protokol prostředků:

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

* [Další informace o protokolech prostředků](platform-logs-overview.md).
* [Vytvořte diagnostické nastavení pro shromažďování protokolů a metrik v Azure](diagnostic-settings.md).
* [Streamujte protokoly služby Azure Active Directory pomocí nástroje Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Začínáme s event huby](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

