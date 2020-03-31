---
title: Azure Service Fabric diagnostikovat běžné scénáře
description: Přečtěte si o řešení potíží s běžnými scénáři monitorování a diagnostiky v rámci aplikací Azure Service Fabric.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906944"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostika běžných scénářů pomocí service fabric

Tento článek ilustruje běžné scénáře, se kterými se uživatelé setkali v oblasti monitorování a diagnostiky pomocí service fabric. Prezentované scénáře pokrývají všechny 3 vrstvy prostředků infrastruktury služeb: aplikace, cluster a infrastruktura. Každé řešení používá application insights a Azure Monitor protokoly, nástroje pro monitorování Azure, k dokončení každého scénáře. Kroky v každém řešení poskytují uživatelům úvod o tom, jak používat application insights a Azure Monitor protokoly v kontextu Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Požadavky a doporučení

Řešení v tomto článku budou používat následující nástroje. Doporučujeme, abyste měli tyto nastavené a nakonfigurované:

* [Přehledy aplikací s service fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Povolení diagnostiky Azure ve vašem clusteru](service-fabric-diagnostics-event-aggregation-wad.md)
* [Nastavení pracovního prostoru Analýzy protokolů](service-fabric-diagnostics-oms-setup.md)
* [Agent log Analytics pro sledování čítačů výkonu](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak lze v aplikaci zobrazit neošetřené výjimky?

1. Přejděte na prostředek Application Insights, se kterým je vaše aplikace nakonfigurovaná.
2. Klikněte na *Hledat* v levém horním rohu. Pak klepněte na filtr na dalším panelu.

    ![Přehled ai](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Uvidíte spoustu typů událostí (trasování, požadavky, vlastní události). Jako filtr zvolte "Výjimka".

    ![Seznam filtrů AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Kliknutím na výjimku v seznamu, můžete se podívat na další podrobnosti, včetně kontextu služby, pokud používáte Service Fabric Application Insights SDK.

    ![Výjimka AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Jak lze zobrazit, která volání HTTP se používají v mých službách?

1. Ve stejném prostředku Application Insights můžete filtrovat na "požadavky" namísto výjimek a zobrazit všechny požadavky
2. Pokud používáte service fabric Application Insights SDK, můžete zobrazit vizuální reprezentaci služeb vzájemně propojených a počet úspěšných a neúspěšných požadavků. Na levé straně klikněte na "Mapa aplikace"

    ![Mapa aplikací AI App Blade](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Další informace o mapě aplikace naleznete v [dokumentaci k mapě aplikací](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Jak vytvořím výstrahu při pádu uzlu

1. Události uzlu jsou sledovány clusterem Service Fabric. Přejděte na prostředek řešení Service Fabric Analytics s názvem **ServiceFabric(NameofResourceGroup)**
2. Klikněte na graf na spodní straně čepele s názvem "Shrnutí"

    ![Řešení protokolů Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Zde máte mnoho grafů a dlaždic zobrazujících různé metriky. Klikněte na jeden z grafů a to vás zavede do log search. Zde můžete dotazovat na všechny události clusteru nebo čítače výkonu.
4. Zadejte následující dotaz. Tato ID událostí se nacházejí v [odkazu na události uzlu.](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klikněte na "Nové pravidlo výstrahy" nahoře a nyní kdykoli událost dorazí na základě tohoto dotazu, obdržíte upozornění ve zvoleném způsobu komunikace.

    ![Azure Monitor protokoluje nové výstrahy](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak mohu být upozorněni na vrácení upgradu aplikace?

1. Ve stejném okně hledání protokolu jako předtím zadejte následující dotaz pro vrácení upgradu. Tato ID událostí se nacházejí pod [odkazem na události aplikace.](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klikněte na "Nové pravidlo výstrahy" nahoře a nyní kdykoli událost dorazí na základě tohoto dotazu, obdržíte upozornění.

## <a name="how-do-i-see-container-metrics"></a>Jak se mi zobrazí metriky kontejneru?

Ve stejném zobrazení se všemi grafy, uvidíte některé dlaždice pro výkon kontejnerů. Potřebujete agenta analýzy protokolů a [monitorování kontejnerů řešení](service-fabric-diagnostics-oms-containers.md) pro tyto dlaždice naplnit.

![Metriky kontejneru analýzy protokolů](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Chcete-li instrumentovat telemetrii **zevnitř** kontejneru, budete muset přidat [nugetový balíček Application Insights pro kontejnery](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak lze sledovat čítače výkonu?

1. Po přidání agenta Analýzy protokolů do clusteru je třeba přidat konkrétní čítače výkonu, které chcete sledovat. Přejděte na stránku pracovního prostoru Analýzy protokolů na portálu – na stránce řešení je karta pracovního prostoru v levé nabídce.

    ![Karta Pracovní prostor Analýzy protokolů](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Jakmile jste na stránce pracovního prostoru, klikněte na "Upřesnit nastavení" ve stejné levé nabídce.

    ![Upřesnit nastavení analýzy protokolů](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kliknutím na data > čítače výkonu systému Windows (Data > Linux Performance Counters for Linux) můžete začít shromažďovat konkrétní čítače z uzlů prostřednictvím agenta Log Analytics. Zde jsou příklady formátu čítačů, které chcete přidat

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     V rychlém startu, VotingData a VotingWeb jsou názvy procesů používaných, takže sledování těchto čítačů bude vypadat

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Čítače Perf analýzy protokolů](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. To vám umožní zjistit, jak vaše infrastruktura zpracovává vaše úlohy, a nastavit relevantní výstrahy na základě využití prostředků. Například – můžete nastavit výstrahu, pokud celkové využití procesoru přesahuje 90 % nebo méně než 5 %. Název čítače, který byste pro toto použili, je "% času procesoru". Můžete to provést vytvořením pravidla výstrahy pro následující dotaz:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Jak mohu sledovat výkon svých spolehlivých služeb a herců?

Chcete-li sledovat výkon spolehlivé služby nebo objekty actor ve vašich aplikacích, měli byste shromažďovat také čítače actor, actor actor, service a service method. Zde jsou příklady spolehlivých čítačů výkonu služeb a aktérů, které lze shromažďovat

>[!NOTE]
>Čítače výkonu Service Fabric nelze shromažďovat agenta Analýzy protokolů aktuálně, ale mohou být shromažďovány [jinými diagnostickými řešeními.](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Zkontrolujte, zda tyto odkazy pro úplný seznam čítačů výkonu na spolehlivé [služby](service-fabric-reliable-serviceremoting-diagnostics.md) a [aktéry](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>Další kroky

* [Vyhledat běžné chyby aktivace balíčku kódu](./service-fabric-diagnostics-code-package-errors.md)
* [Nastavení upozornění v umělou a inici,](../azure-monitor/app/alerts.md) která mají být upozorňována na změny výkonu nebo využití
* [Inteligentní detekce v Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrie odesílané do AI, aby vás varovala před možnými problémy s výkonem
* Další informace o protokolech Azure Monitor [upozorňující](../log-analytics/log-analytics-alerts.md) chytat při zjišťování a diagnostice.
* Pro místní clustery protokoly Azure Monitor nabízí bránu (HTTP forward proxy), který lze použít k odesílání dat do protokolů Azure Monitor. Další informace o tom načtete v [části Připojení počítačů bez přístupu k internetu k protokolům Azure Monitoru pomocí brány Log Analytics](../azure-monitor/platform/gateway.md)
* Seznamte se s funkcemi [pro vyhledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást protokolů Azure Monitoru
* Získejte podrobnější přehled protokolů Azure Monitoru a co nabízí, přečtěte si [článek Co jsou protokoly Azure Monitoru?](../operations-management-suite/operations-management-suite-overview.md)
