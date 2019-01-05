---
title: Diagnostikovat běžné scénáře Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak řešit běžné scénáře pomocí Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: 700295c94428021445f6cbbd84175046d57b9147
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054890"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostikovat běžné scénáře s využitím Service Fabric

Tento článek ukazuje běžné situace, které uživatelé došlo v oblasti monitorování a diagnostiku, s využitím Service Fabric. Scénáře uvedené zahrnují všechny vrstvy 3 service Fabric: Aplikace, clusteru a infrastruktury. Každé řešení používá Application Insights a Log Analytics, Azure nástrojů pro monitorování, dokončete každý scénář. Kroky v jednotlivých řešení uživatelům úvodní informace o tom, jak používat Application Insights a Log Analytics v rámci Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Požadavky a doporučení

Řešení v tomto článku se bude používat následující nástroje. Doporučujeme, že abyste měli tyto sady nahoru a nakonfigurované:

* [Application Insights s využitím Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Povolení diagnostiky Azure na svém clusteru](service-fabric-diagnostics-event-aggregation-wad.md)
* [Nastavit pracovní prostor Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Protokolu agenta Analytics ke sledování čítače výkonu](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak lze zobrazit neošetřené výjimky v mé aplikaci?

1. Přejděte do prostředku Application Insights, který má nakonfigurovanou aplikaci.
2. Klikněte na *hledání* vlevo nahoře. Pak klikněte na filtr na panelu Další.

    ![Přehled AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobrazí se velké množství typy událostí (trasování, požadavky, vlastních událostí). Zvolte možnost "Výjimka" jako filtr.

    ![Seznam filtrů AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Když kliknete na výjimku v seznamu, můžete si prohlédnout podrobnosti, včetně kontext služby, pokud použijete Service Fabric Application Insights SDK.

    ![Výjimka AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak zobrazím které HTTP volání se používají v mých služeb?

1. V stejný prostředek Application Insights můžete filtrovat podle "požadavky" místo výjimek a zobrazit všechny žádosti
2. Pokud používáte sadu Application Insights SDK pro Service Fabric, se zobrazí vizuální reprezentace vašich služeb připojení mezi sebou a počet bylo úspěšné a neúspěšné požadavky. Na levé straně klikněte na tlačítko "Mapa aplikace"

    ![Mapa aplikace AI okno](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Mapa aplikace AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Další informace o mapy aplikace najdete [dokumentaci Mapa aplikace](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak vytvořím oznámení, když se uzel ocitne mimo provoz

1. Uzel události jsou sledovány ve vašem clusteru Service Fabric. Přejděte k prostředku řešení analýza služby Service Fabric s názvem **ServiceFabric(NameofResourceGroup)**
2. Klikněte na graf v dolní části okna s názvem "Přehled"

    ![Řešení log Analytics](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Tady máte mnoho grafů a zobrazení různých metrik dlaždice. Klikněte na jednotlivé grafy a je zkratkou k prohledávání protokolů. Tady můžete zadat dotaz na všechny události clusteru nebo čítače výkonu.
4. Zadejte následující dotaz. Tyto události jsou součástí [uzel události – referenční informace](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Klikněte na tlačítko "Nové pravidlo výstrahy" v horní části a nyní kdykoli události dorazí na tento dotaz na základě, obdržíte výstrahu zvolenou metodu komunikace.

    ![Nová upozornění log Analytics](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak lze I upozorněni vrácení upgradu zpět aplikaci?

1. Na stejné okno prohledávání protokolu jako před zadejte následující dotaz pro vrácení upgradu zpět. Tyto události jsou nalezené pod [aplikace události – referenční informace](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Klikněte na tlačítko "Nové pravidlo výstrahy" v horní části a nyní kdykoli události dorazí na tento dotaz na základě, zobrazí se upozornění.

## <a name="how-do-i-see-container-metrics"></a>Jak můžu zobrazit metriky kontejnerů?

Ve stejném zobrazení se všechny grafy zobrazí se některé dlaždice pro výkon své kontejnery. Je třeba agenta Log Analytics a [řešení pro monitorování kontejnerů](service-fabric-diagnostics-oms-containers.md) pro tyto dlaždice k naplnění.

![Metriky kontejnerů log Analytics](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>K instrumentaci telemetrii z **uvnitř** vašeho kontejneru, budete muset přidat [balíček nuget Application Insights pro kontejnery](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak můžu monitorovat čítače výkonu?

1. Po přidání agenta Log Analytics pro váš cluster, musíte přidat specifické čítače výkonu, které chcete sledovat. Přejděte na stránku pracovního prostoru Log Analytics na portálu – na stránce řešení, je karta pracovního prostoru v levé nabídce.

    ![Karta pracovního prostoru log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Jakmile budete na stránce pracovním prostoru, klikněte na "pokročilé"nastavení v nabídce vlevo na stejné.

    ![Upřesňující nastavení log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klikněte na Data > čítače výkonu Windows (Data > Linuxovými čítači výkonu pro počítače s Linuxem) spustíte shromažďování specifické čítače aplikací z uzly pomocí agenta Log Analytics. Tady jsou příklady formát pro čítače, které chcete přidat

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    V tomto rychlém startu, VotingData a VotingWeb jsou názvy procesů používá, takže tyto čítače pro sledování může vypadat třeba

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Čítače výkonu služby log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. To umožní zobrazit, jak je infrastrukturu zpracování vašich úloh a nastavte příslušné výstrahy na základě využití prostředků. Například – můžete nastavit výstrahu v případě více než 90 % nebo méně než 5 % celkové využití procesoru v aplikaci. Název čítače, které můžete využít k tomu je "% Processor Time". Může to provedete tak, že vytvoříte pravidlo upozornění pro následující dotaz:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak mohu sledovat výkon modelu Reliable Services a objekty actor?

Pro sledování výkonu modelu Reliable Services nebo Actors ve svých aplikacích, měli byste přidat také čítače Service Fabric Actor, metoda objektu Actor, služby a metody služby. Tyto čítače můžete přidat podobným způsobem jako výše uvedeného scénáře, tady jsou příklady spolehlivé služby a objekt actor čítačů výkonu přidat v Log Analytics:

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Zkontrolujte tyto odkazy pro úplný seznam čítačů výkonu na spolehlivé [služby](service-fabric-reliable-serviceremoting-diagnostics.md) a [objektů actor](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Další postup

* [Nastavte si upozornění v umělé Inteligenci](../azure-monitor/app/alerts.md) abyste dostávali oznámení o změny ve výkonu a využití
* [Inteligentní zjišťování ve službě Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrických dat odesílaných do AI upozornit vás na potenciální problémy s výkonem
* Další informace o službě Log Analytics [upozorňování](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika.
* Log Analytics nabízí místními clustery brány (dopředu proxy server HTTP), který slouží k odesílání dat do Log Analytics. Další informace o, že v [počítače bez připojení k Internetu připojení ke službě Log Analytics pomocí brány Log Analytics](../azure-monitor/platform/gateway.md)
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízená v rámci služby Log Analytics
* Získejte podrobnější přehled o Log Analytics a navíc nabízejí, přečtěte si [co je služba Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
