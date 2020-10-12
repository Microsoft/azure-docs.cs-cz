---
title: Monitorování událostí clusteru se systémem Linux v Azure Service Fabric
description: Naučte se monitorovat události clusteru Service Fabric Linux tak, že zapíšete Service Fabric události platformy do protokolu syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: fe31c6fdca3651bfe56e798b30d50c9f047c680b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258640"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Události clusteru Service Fabric Linux v protokolu syslog

Service Fabric zveřejňuje sadu událostí platformy, které informují o důležité aktivitě v clusteru. Úplný seznam událostí, které jsou zpřístupněny, je k dispozici [zde](service-fabric-diagnostics-event-generation-operational.md). Existuje mnoho způsobů, jak lze tyto události spotřebovat. V tomto článku se podíváme, jak nakonfigurovat Service Fabric pro zápis těchto událostí do protokolu syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Úvod

Ve verzi 6,4 byla představena SyslogConsumer, aby odesílala události Service Fabric platformy do protokolu syslog pro clustery Linux. Po zapnutí budou události automaticky převedeny do protokolu syslog, který je možné shromažďovat a odesílat pomocí agenta Log Analytics.

Každá událost syslog má 4 součásti
* Vybavení
* Identita
* Zpráva
* Závažnost

SyslogConsumer zapisuje všechny události platforem pomocí zařízení `Local0` . Změnou konfigurace konfigurace můžete aktualizovat na jakékoli platné zařízení. Použitá identita je `ServiceFabric` . Pole zpráva obsahuje celou událost serializovanou ve formátu JSON, aby mohla být dotazována a spotřebována řadou nástrojů. 

## <a name="enable-syslogconsumer"></a>Povolit SyslogConsumer

Pokud chcete SyslogConsumer povolit, musíte provést upgrade clusteru. `fabricSettings`Oddíl je třeba aktualizovat pomocí následujícího kódu. Všimněte si, že tento kód obsahuje jenom oddíly týkající se SyslogConsumer.

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

Tady jsou změny, které se mají zavolat.
1. V části Common se používá nový parametr `LinuxStructuredTracesEnabled` . **To je nutné, aby při odeslání do protokolu syslog byly strukturované a serializované události systému Linux.**
2. V části Diagnostika byl přidán nový ConsumerInstance: SyslogConsumer. To oznamuje platformě další příjemce událostí. 
3. Nový oddíl SyslogConsumer musí mít `IsEnabled` jako `true` . Je nakonfigurovaná tak, aby se zařízení Local0 automaticky používalo. To můžete přepsat přidáním dalšího parametru.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrace protokolů Azure Monitor
Tyto události syslog můžete číst v monitorovacím nástroji, jako jsou protokoly Azure Monitor. Pracovní prostor Log Analytics můžete vytvořit pomocí Azure Marketplace pomocí těchto [pokynů]. (.. /Azure-monitor/Learn/Quick-Create-Workspace.MD) je také potřeba přidat agenta Log Analytics do clusteru, aby bylo možné tato data shromažďovat a odesílat do pracovního prostoru. To je stejný agent, který se používá ke shromažďování čítačů výkonu. 

1. Přejít `Advanced Settings` na okno

    ![Nastavení pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Klikněte na `Data`.
3. Klikněte na `Syslog`.
4. Nakonfigurujte Local0 jako zařízení, které se má sledovat. Pokud jste změnili v fabricSettings, můžete přidat další zařízení.

    ![Konfigurace protokolu syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Přejděte do Průzkumníku dotazů kliknutím `Logs` do nabídky prostředku v pracovním prostoru a spusťte dotazování.

    ![Protokoly pracovního prostoru](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Můžete zadávat dotazy na tabulku, která je v `Syslog` `ServiceFabric` podobě procesu. Níže uvedený dotaz ukazuje, jak analyzovat JSON v události a zobrazit jeho obsah.

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Dotaz syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

Výše uvedený příklad je NodeDown události. Úplný seznam událostí můžete zobrazit [zde](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Další kroky
* [Nasaďte agenta Log Analytics](service-fabric-diagnostics-oms-agent.md) do svých uzlů, abyste mohli shromažďovat čítače výkonu a shromažďovat statistiky a protokoly Docker pro vaše kontejnery.
* Seznámení s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/log-query/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor
* [Použití návrháře zobrazení k vytváření vlastních zobrazení v protokolech Azure Monitor](../azure-monitor/platform/view-designer.md)
* Referenční informace o tom, jak [Azure monitor protokoly integrace s protokolem SYSLOG](../azure-monitor/platform/data-sources-syslog.md).
