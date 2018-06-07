---
title: Azure Service Fabric diagnostikovat běžné scénáře | Microsoft Docs
description: Zjistěte, jak řešit běžné scénáře s Azure Service Fabric
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
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654997"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostikovat běžné scénáře s Service Fabric

Tento článek ukazuje běžných scénářů, které mají uživatelé došlo v oblasti monitorování a Diagnostika pomocí Service Fabric. Scénáře uvedené zahrnují všechny vrstvy 3 service fabric: aplikace, clusteru a infrastrukturu. Každé řešení používá Application Insights a analýzy protokolů Azure nástrojů pro monitorování, k dokončení jednotlivé scénáře. Kroky v každé řešení uživatelům úvodní informace o tom, jak použít Application Insights a analýzy protokolů v kontextu Service Fabric.

## <a name="prerequisites-and-recommendations"></a>Požadavky a doporučení

Řešení v tomto článku se použít následující nástroje. Doporučujeme, že abyste měli tyto sady nahoru a konfiguraci:

* [Application Insights s Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Povolit Azure Diagnostics v clusteru](service-fabric-diagnostics-event-aggregation-wad.md)
* [Nastavit pracovní prostor Log Analytics OMS](service-fabric-diagnostics-oms-setup.md)
* [Agent OMS sledovat čítače výkonu](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak lze zobrazit neošetřených výjimek v mé aplikaci?

1. Přejděte do zdroje Application Insights nakonfigurovaný s vaší aplikace.
2. Klikněte na *vyhledávání* v levé horní části. Klikněte na panelu další filtr.

    ![Přehled AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobrazí se velké množství typů událostí (trasování, požadavků, vlastních událostí). Zvolte "Výjimky" jako filtr.

    ![Seznam filtrů AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Kliknutím na výjimku v seznamu, můžete zobrazit další podrobnosti, včetně kontext služby, pokud používáte služby Fabric Application Insights SDK.

    ![Výjimka AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak zobrazím které HTTP volání se používají v mé služby?

1. V rámci stejné prostředku Application Insights můžete filtrovat podle "požadavky" místo výjimky a zobrazit všechny požadavky vytvořené
2. Pokud používáte služby Fabric Application Insights SDK, uvidíte vizuální reprezentace vašich služeb připojené k sobě a počet bylo úspěšné a neúspěšné požadavky. Na levé straně klikněte na tlačítko "Aplikace Map"

    ![Okno aplikace mapy AI](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI aplikace mapy](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Další informace o aplikaci mapy, přejděte [dokumentaci aplikace mapy](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak lze vytvořit výstrahu, kdy uzlu přestane fungovat

1. Uzel události se sledují váš Cluster Service Fabric. Přejděte na služby prostředků infrastruktury Analytics řešení prostředků s názvem **ServiceFabric(NameofResourceGroup)**
2. Klikněte na graf v dolní části okna s názvem "Souhrn"

    ![Řešení OMS](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Zde máte mnoho grafy a dlaždice zobrazení různé metriky. Klikněte na některý z grafů a jeho se dostanete na hledání protokolů. Sem se můžete dotazovat pro všechny události clusteru nebo čítače výkonu.
4. Zadejte následující dotaz. Tato událost ID se nacházejí v [odkaz události uzlu](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Klikněte na tlačítko "Nové pravidlo výstrahy" v horní části a nyní můžete kdykoli na tento dotaz dorazí založené na události, obdržíte výstrahu v zvolenou metodu komunikace.

    ![Nová výstraha OMS](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak můžete I Generovat výstrahy z odvolání upgradu aplikace?

1. Na stejném okně hledání protokolů jako před zadejte následující dotaz pro upgrade odvolání. Tato událost ID se nacházejí v rámci [odkaz událostí aplikace](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Klikněte na tlačítko "Nové pravidlo výstrahy" v horní části a nyní můžete kdykoli na tento dotaz dorazí založené na události, zobrazí se výstraha.

## <a name="how-do-i-see-container-metrics"></a>Jak zobrazit metriky kontejneru?

Ve stejném zobrazení všech grafů zobrazí se některé dlaždice pro výkon kontejnerů. Budete potřebovat agenta OMS a [řešením pro monitorování kontejneru](service-fabric-diagnostics-oms-containers.md) pro tyto dlaždice k naplnění.

![Metriky kontejneru OMS](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>K nástroji telemetrie z **uvnitř** vašeho kontejneru, budete muset přidat [balíček nuget Application Insights pro kontejnery](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak můžete monitorovat čítače výkonu?

1. Po přidání agenta OMS do clusteru, je nutné přidat konkrétních čítačích výkonu, které chcete sledovat. Přejděte na stránku pracovní prostor OMS na portálu – ze stránky na řešení, je na kartě pracovního prostoru v levé nabídce.

    ![Karta pracovní prostor OMS](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Jakmile jste na stránce pracovním prostoru, klikněte na "Pokročilé nastavení" ve stejném levé nabídce.

    ![OMS Upřesnit nastavení](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klikněte na Data > čítačů výkonu systému Windows (Data > Linux čítače výkonu pro počítače se systémem Linux) spustit shromažďování specifické čítače z uzlů prostřednictvím agenta OMS. Zde jsou příklady formát pro čítače pro přidání

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    V rychlé spuštění, VotingData a VotingWeb jsou názvy procesů používá, tak bude vypadat sledování tyto čítače

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Čítače výkonu OMS](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. To bude umožňují zobrazit, jak je vaše infrastruktura zpracování vašich úloh a nastavte příslušné výstrahy na základě využití prostředků. Například – můžete nastavit upozornění, pokud celkové využití procesoru přejde více než 90 % nebo nižší než 5 %. Název čítače, které se používají pro tento je "% času procesoru." Může to uděláte tak, že vytvoříte pravidlo výstrahy pro následující dotaz:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak mohu sledovat výkon Moje Reliable Services a aktéři?

Pro sledování výkonu spolehlivé služby nebo kteří ve svých aplikacích, měli byste přidat také čítače objektu Actor Service Fabric, metoda objektu Actor, služby a služby metodu. Tyto čítače můžete přidat podobně jako výše uvedené scénáře, zde jsou příklady spolehlivá služba a objektu actor čítače výkonu chcete-li přidat v OMS

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Zkontrolujte tyto odkazy pro úplný seznam čítačů výkonu pro spolehlivé [služby](service-fabric-reliable-serviceremoting-diagnostics.md) a [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Další postup

* [Nastavení výstrah v AI](../application-insights/app-insights-alerts.md) oznámení o změnách v výkonu a využití
* [Inteligentní detekce ve službě Application Insights](../application-insights/app-insights-proactive-diagnostics.md) provede proaktivní analýzu telemetrie odesílána AI varovat před potenciálním problémům s výkonem
* Další informace o analýzy protokolů OMS [výstrahy](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a diagnostiky.
* Pro místní clusterů nabízí OMS brány (dál server Proxy protokolu HTTP), který slouží k odesílání dat do OMS. Další informace o v [propojíte počítače bez připojení k Internetu pomocí brány OMS OMS](../log-analytics/log-analytics-oms-gateway.md)
* Získat familiarized s [vyhledávání a dotazování protokolu](../log-analytics/log-analytics-log-searches.md) funkcím poskytovaným jako součást analýzy protokolů
* Získat podrobnější přehled analýzy protokolů a co nabízí, přečtěte si [co je Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
