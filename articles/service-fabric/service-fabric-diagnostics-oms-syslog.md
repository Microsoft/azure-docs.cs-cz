---
title: Monitorování události clusteru Linux v Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o monitorování události clusteru Linux z protokolu Syslog
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118374"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Události clusteru Service Fabric s Linuxem v procesu Syslog

Service Fabric poskytuje sadu událostí platformy, které informují o důležitých aktivit ve vašem clusteru. Úplný seznam událostí, které jsou vystaveny je k dispozici [tady](service-fabric-diagnostics-event-generation-operational.md). Existují různé způsoby, pomocí kterého můžete využívat tyto události. V tomto článku budeme diskutovat o tom, jak nakonfigurovat Service Fabric zapsat tyto události do protokolu Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Úvod

6\.4 verze SyslogConsumer zavedl k odeslání události platformy Service Fabric s protokolem Syslog pro Linuxové clustery. Po zapnutí událostí bude automaticky směrovat do Syslog, které můžete shromažďovat a odesílaných agenta Log Analytics.

Každá událost procesu Syslog má 4 komponenty
* Zařízení
* Identita
* Message
* Severity

SyslogConsumer zapisuje všechny události platformy pomocí zařízení `Local0`. Jakékoli platné zařízení můžete aktualizovat změnou konfigurace config. Je identita používaná `ServiceFabric`. Pole zprávy obsahuje celou událost serializovanou ve formátu JSON tak, aby se jde dotazovat a používané celou řadu nástrojů. 

## <a name="enable-syslogconsumer"></a>Povolit SyslogConsumer

Povolit SyslogConsumer, budete muset provést upgrade vašeho clusteru. `fabricSettings` Části je potřeba aktualizovat následujícím kódem. Všimněte si, že tento kód obsahuje jenom části týkající se SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Tady jsou změny provádět volání
1. V části společné je nový parametr s názvem `LinuxStructuredTracesEnabled`. **To se vyžaduje pro Linux se události strukturované a serializovat, když je odeslána s protokolem Syslog.**
2. V části Diagnostika nové ConsumerInstance: SyslogConsumer se přidala. Platforma říká, že existuje jiný příjemce událostí. 
3. Nový oddíl SyslogConsumer musí mít `IsEnabled` jako `true`. To je nakonfigurován pro použití zařízení Local0 automaticky. Toto můžete přepsat tak, že přidáte jiný parametr.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor
Můžete číst tyto události protokolu Syslog v monitorovacího nástroje, jako jsou protokoly Azure monitoru. Pracovní prostor Log Analytics můžete vytvořit pomocí webu Azure Marketplace pomocí těchto [pokynů]. (.. / azure-monitor/learn/quick-create-workspace.md) budete také muset přidat agenta Log Analytics ke svému clusteru pro shromažďování a odesílání dat do pracovního prostoru. Toto je stejný agent, kterého používá ke shromažďování čítačů výkonu. 

1. Přejděte `Advanced Settings` okno

    ![Nastavení pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klikněte na `Data`.
3. Klikněte na `Syslog`.
4. Nakonfigurujte Local0 jako zařízení ke sledování. Pokud jste změnili v nastavení fabricSettings můžete přidat další zařízení

    ![Konfigurovat Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Přejděte do Průzkumníka dotazů kliknutím `Logs` v nabídce prostředku pracovního prostoru ke spuštění dotazu

    ![Protokoly pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Můžete dotazovat `Syslog` tabulky hledáte `ServiceFabric` jako název procesu. Dotaz níže je příklad toho, jak k parsování formátu JSON v události a zobrazení jeho obsahu

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Dotaz Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

V příkladu výše je NodeDown události. Můžete zobrazit úplný seznam událostí [tady](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Další postup
* [Nasadit agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) na svých uzlech shromažďování čítačů výkonu a shromáždit statistiky dockeru a protokoly pro vaše kontejnery
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízí jako součást protokoly Azure monitoru
* [Použít návrháře zobrazení pro vytváření vlastních zobrazení v protokoly Azure monitoru](../log-analytics/log-analytics-view-designer.md)
* Referenční informace o [protokoly Azure monitoru integrace se Syslogem](../log-analytics/log-analytics-data-sources-syslog.md).
