---
title: Sledování událostí linuxového clusteru ve službě Azure Service Fabric
description: Zjistěte, jak sledovat události clusteru Service Fabric Linux zápisem události platformy Service Fabric do Syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645748"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Události clusteru Service Fabric Linux v Syslogu

Service Fabric zveřejňuje sadu událostí platformy informovat o důležité aktivity ve vašem clusteru. Úplný seznam událostí, které jsou vystaveny je k dispozici [zde](service-fabric-diagnostics-event-generation-operational.md). Existují různé způsoby, kterými mohou být tyto události spotřebovány. V tomto článku budeme diskutovat o tom, jak nakonfigurovat Service Fabric pro zápis těchto událostí do Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Úvod

Ve verzi 6.4 syslogConsumer byl zaveden k odeslání události platformy Service Fabric do clusterů Syslog pro Linux. Po zapnutí budou události automaticky přetékat do syslogu, který může shromažďovat a odesílat agent log analytics.

Každá událost Syslog má 4 komponenty
* Zařízení
* Identita
* Zpráva
* Severity

SyslogConsumer zapisuje `Local0`všechny události platformy pomocí Facility . Můžete aktualizovat na libovolné platné zařízení změnou konfigurace. Identita používá `ServiceFabric`je . Pole Zpráva obsahuje celou událost serializovanou v JSON, aby mohla být dotazována a spotřebována různými nástroji. 

## <a name="enable-syslogconsumer"></a>Povolit syslogconsumer

Chcete-li povolit SyslogConsumer, je třeba provést upgrade clusteru. Oddíl `fabricSettings` je třeba aktualizovat pomocí následujícího kódu. Poznámka: Tento kód obsahuje pouze oddíly týkající se SyslogConsumer

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

Zde jsou změny, které je třeba vyvolat
1. V části Společné je nový parametr `LinuxStructuredTracesEnabled`s názvem . **To je nutné mít linuxové události strukturovány a serializovány při odeslání do Syslog.**
2. V části Diagnostika byla přidána nová instance ConsumerInstance: SyslogConsumer. To říká platformě, že existuje další spotřebitel událostí. 
3. Nová sekce SyslogConsumer musí `IsEnabled` `true`mít jako . Je nakonfigurován pro automatické použití zařízení Local0. Můžete přepsat přidáním dalšího parametru.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor
Tyto události Syslogu můžete číst v nástroji pro monitorování, jako jsou protokoly Azure Monitor. Pracovní prostor Analýzy protokolů můžete vytvořit pomocí Azure Marketplace pomocí těchto [pokynů]. (.. /azure-monitor/learn/quick-create-workspace.md) Je také potřeba přidat agenta Log Analytics do clusteru, abyste tato data shromažďovali a odesílali do pracovního prostoru. Jedná se o stejného agenta, který slouží ke shromažďování čítačů výkonu. 

1. Přejděte `Advanced Settings` k noži

    ![Nastavení pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klikněte na `Data`.
3. Klikněte na `Syslog`.
4. Konfigurace Local0 jako zařízení ke sledování. Můžete přidat další zařízení, pokud jste jej změnili v fabricSettings

    ![Konfigurace syslogu](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Přejde meze do `Logs` průzkumníka dotazů kliknutím v nabídce prostředku pracovního prostoru a začněte dotazovat

    ![Protokoly pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Můžete dotaz proti `Syslog` tabulce `ServiceFabric` hledá jako ProcessName. Níže uvedený dotaz je příkladem toho, jak analyzovat JSON v události a zobrazit jeho obsah

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Dotaz Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Výše uvedený příklad je událost NodeDown. Úplný seznam událostí si můžete prohlédnout [zde](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Další kroky
* [Nasazení agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) do uzlů ke shromažďování čítačů výkonu a shromažďování statistik a protokolů dockeru pro vaše kontejnery
* Seznamte se s funkcemi [pro vyhledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást protokolů Azure Monitoru
* [Vytvoření vlastních zobrazení v protokolech Azure Monitoru pomocí Návrháře zobrazení](../log-analytics/log-analytics-view-designer.md)
* Odkaz na způsob, jak [Azure Monitor protokoly integrace s Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
