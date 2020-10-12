---
title: Azure Service Fabric – diagnostikování běžných scénářů
description: Přečtěte si o řešení běžných scénářů monitorování a diagnostiky v rámci aplikací Azure Service Fabric.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: f92bc02082d8bcd9d917f05c93d3da413f772cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257728"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostika běžných scénářů pomocí Service Fabric

Tento článek popisuje běžné scénáře, se kterými se uživatelé setkali v oblasti monitorování a diagnostiky s Service Fabric. Uvedené scénáře zahrnují všechny 3 vrstvy Service Fabric: aplikace, cluster a infrastruktura. Každé řešení používá protokoly Application Insights a Azure Monitor, nástroje pro monitorování Azure k dokončení jednotlivých scénářů. Kroky v jednotlivých řešeních dávají uživatelům úvodní informace o tom, jak používat protokoly Application Insights a Azure Monitor v kontextu Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Požadavky a doporučení

Řešení v tomto článku budou používat následující nástroje. Doporučujeme, abyste nastavili a nakonfigurovali:

* [Application Insights s Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Povolení Azure Diagnostics v clusteru](service-fabric-diagnostics-event-aggregation-wad.md)
* [Nastavení pracovního prostoru Log Analytics](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics agenta pro sledování čítačů výkonu](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Jak můžu v aplikaci zobrazit neošetřené výjimky?

1. Přejděte na prostředek Application Insights, pomocí kterého je vaše aplikace nakonfigurovaná.
2. V levém horním rohu klikněte na *Hledat* . Pak klikněte na tlačítko filtr na dalším panelu.

    ![Přehled AI](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Zobrazí se spousta typů událostí (trasování, požadavky, vlastní události). Jako filtr vyberte "Exception".

    ![Seznam filtru AI](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Kliknutím na výjimku v seznamu můžete zobrazit další podrobnosti, včetně kontextu služby, pokud používáte sadu Service Fabric Application Insights SDK.

    ![Výjimka AI](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Návody zobrazit, která volání HTTP se ve svých službách používají?

1. Ve stejném Application Insights prostředku můžete místo výjimek a zobrazení všech požadavků vytvořit filtrování požadavků.
2. Pokud používáte sadu SDK Service Fabric Application Insights, uvidíte vizuální znázornění vašich služeb, které jsou připojené k druhému, a počet úspěšných a neúspěšných žádostí. Na levé straně klikněte na mapa aplikace.

    ![](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png)Mapa aplikace AI okno Mapa aplikace AI ![](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Další informace o mapě aplikace najdete v [dokumentaci k mapě aplikací](../azure-monitor/app/app-map.md) .

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Návody vytvořit výstrahu, když se uzel ukončí

1. Události uzlu jsou sledovány Service Fabricm clusterem. Přejděte do prostředku řešení Service Fabric Analytics s názvem **ServiceFabric (NameofResourceGroup)** .
2. V dolní části okna s názvem Summary klikněte na graf.

    ![Řešení protokolu Azure Monitor](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Tady máte spoustu grafů a dlaždic znázorňující různé metriky. Klikněte na jeden z grafů a přejdete k prohledávání protokolu. Tady se můžete dotazovat na všechny události clusteru nebo čítače výkonu.
4. Zadejte následující dotaz. Tato ID událostí se nacházejí v [odkazu na události uzlu](service-fabric-diagnostics-event-generation-operational.md#application-events) .

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. V horní části klikněte na nové pravidlo upozornění a teď na základě tohoto dotazu dostanou událost, že se vám zobrazí výstraha ve zvolené metodě komunikace.

    ![Azure Monitor zaprotokolovat nové upozornění](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Jak se dá upozornit na vrácení zpět upgradu aplikace?

1. Ve stejném okně prohledávání protokolu jako předtím zadejte následující dotaz pro vrácení zpět se systémem upgradu. Tato ID událostí se nacházejí v části [referenční informace o událostech aplikace](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. V horní části klikněte na nové pravidlo upozornění a teď na základě tohoto dotazu dostanou událost, že se vám zobrazí výstraha.

## <a name="how-do-i-see-container-metrics"></a>Návody viz metriky kontejnerů?

Ve stejném zobrazení se všemi grafy se zobrazí některé dlaždice pro výkon kontejnerů. K naplnění těchto dlaždic potřebujete Log Analytics agenta a [řešení pro monitorování kontejnerů](service-fabric-diagnostics-oms-containers.md) .

![Log Analytics metriky kontejnerů](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>K instrumentaci telemetrie **zevnitř** vašeho kontejneru budete muset přidat [balíček NuGet Application Insights pro kontejnery](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Jak můžu monitorovat čítače výkonu?

1. Po přidání agenta Log Analytics do clusteru je nutné přidat konkrétní čítače výkonu, které chcete sledovat. Přejděte na stránku Log Analytics pracovního prostoru na portálu – na stránce řešení na kartě pracovní prostor se nachází v levé nabídce.

    ![Karta pracovní prostor Log Analytics](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Až budete na stránce pracovního prostoru, klikněte v levé nabídce na Upřesnit nastavení.

    ![Log Analytics Upřesnit nastavení](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kliknutím na data > čítače výkonu systému Windows (čítače výkonu data > Linux pro počítače se systémem Linux) zahájíte shromažďování konkrétních čítačů z uzlů pomocí agenta Log Analytics. Tady jsou příklady formátu pro přidání čítačů.

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     V rychlém startu jsou VotingData a VotingWeb používané názvy procesů, takže sledování těchto čítačů by vypadalo takto.

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Čítače výkonu Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. To vám umožní zjistit, jak vaše infrastruktura zpracovává vaše úlohy, a nastavit relevantní výstrahy na základě využití prostředků. Například můžete chtít nastavit výstrahu, pokud celkové využití procesoru překročí 90% nebo pod 5%. Název čítače, který byste použili, je% času procesoru. To můžete provést vytvořením pravidla výstrahy pro následující dotaz:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Návody sledovat výkon mých Reliable Services a Actorů?

Chcete-li sledovat výkon Reliable Services nebo objektů actor ve vašich aplikacích, měli byste také shromažďovat i čítače Service Fabric Actor, metoda Actor, Service a Service Method. Tady jsou příklady čítačů výkonu spolehlivých služeb a objektů Actor, které se mají shromáždit.

>[!NOTE]
>Agentem Log Analytics momentálně nelze shromáždit Service Fabric čítače výkonu, ale mohou být shromažďována [jinými diagnostickými řešeními](service-fabric-diagnostics-partners.md) .

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

V těchto odkazech najdete úplný seznam čítačů výkonu pro spolehlivé [služby](service-fabric-reliable-serviceremoting-diagnostics.md) a [objekty Actors](service-fabric-reliable-actors-diagnostics.md) .

## <a name="next-steps"></a>Další kroky

* [Vyhledat společné chyby při aktivaci balíčku kódu](./service-fabric-diagnostics-code-package-errors.md)
* [V AI nastavte výstrahy](../azure-monitor/platform/alerts-log.md) na změny výkonu nebo využití.
* [Inteligentní zjišťování v Application Insights](../azure-monitor/app/proactive-diagnostics.md) provádí proaktivní analýzu telemetrie, která se POSÍLÁ do AI a upozorňuje na potenciální problémy s výkonem.
* Přečtěte si další informace o tom, Azure Monitor protokoly [upozorňují](../azure-monitor/platform/alerts-overview.md) na pomoc při detekci a diagnostice.
* U místních clusterů nabízí Azure Monitor protokoly bránu (proxy server HTTP, která se dá použít k odesílání dat do protokolů Azure Monitor. Přečtěte si další informace o tom, jak v [počítačích připojit počítače bez přístupu k internetu Azure monitor protokoly pomocí Log Analytics brány](../azure-monitor/platform/gateway.md) .
* Seznámení s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/log-query/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor
* Získejte podrobnější přehled o Azure Monitor protokolů a o tom, co nabízí, najdete v článku [co jsou protokoly Azure monitor?](../azure-monitor/overview.md)
