---
title: Analýza událostí služby Azure Service Fabric pomocí Application Insights | Dokumentace Microsoftu
description: Další informace o vizualizaci a analýzu událostí pomocí služby Application Insights pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: aedbc5925a6e101299170843abef79ef6125eafe
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230416"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Události analýzy a vizualizace s využitím Application Insights

Microsoft Azure Application Insights je rozšiřitelná platforma pro monitorování a diagnostiku aplikací. Obsahuje výkonné analytické a dotazování na nástroj, přizpůsobitelného řídicího panelu a vizualizací a další možnosti, včetně automatické upozorňování. Je doporučená platforma pro monitorování a Diagnostika aplikace Service Fabric a služeb. Tento článek pomůže vyřešit tyto běžné dotazy

* Jak zjistím, co se děje v mé aplikace a služby a shromažďování telemetrických dat
* Jak řešit Moje aplikace, zejména služby komunikaci mezi sebou
* Jak získat metriky o jak Moje služby fungují, například, doba načítání stránek, požadavky http

Účelem tohoto článku je ukazují, jak zkoumat a řešit z v rámci Application Insights. Pokud chcete získat informace tom, jak vytvořit a nakonfigurovat Application Insights s využitím Service Fabric, najdete v tomto [kurzu](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitorování ve službě Application Insights

Application Insights má bohaté mimo prostředí pole pomocí Service Fabric. Na stránce Přehled služby Application Insights poskytuje klíčové informace o službě, jako je například doba odezvy a počet požadavků zpracovaných. Kliknutím na tlačítko "Vyhledat" v horní části uvidíte seznam nedávné žádosti o ve vaší aplikaci. Kromě toho bude moct zobrazit zde neúspěšné žádosti a diagnostikovat, jakým chybám došlo.

![Přehled služby Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

V pravém panelu na předchozím obrázku, existují dva hlavní typy položek v seznamu: požadavky a události. Požadavky jsou volání provedená do aplikace API pomocí požadavku HTTP v tomto případě a události jsou vlastní události, které fungují jako telemetrická data, která můžete přidat libovolné místo v kódu. Můžete podrobněji prozkoumat instrumentace vaší aplikace v [API pro Application Insights pro vlastní události a metriky](../application-insights/app-insights-api-custom-events-metrics.md). Kliknutím na žádost o zobrazí další podrobnosti o jak je znázorněno na následujícím obrázku, včetně dat specifických pro Service Fabric, která se shromažďují v balíčku nuget Application Insights Service Fabric. Tyto informace je užitečná pro řešení potíží a porozumění tomu, co je stav aplikace a všechny tyto informace je prohledávatelná v rámci Application Insights

![Podrobnosti o žádosti Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights má určené zobrazení pro dotazování na data, která se dodává v. Klikněte na tlačítko "Průzkumníka metrik" nahoře na stránce Přehled přejít na portál Application Insights. Tady můžete spouštět dotazy proti vlastní události, které už bylo uvedeno dříve, požadavky, výjimky, čítače výkonu a další metriky pomocí dotazovacího jazyka Kusto. Následující příklad ukazuje všechny požadavky, za poslední 1 hodinu.

![Podrobnosti o žádosti Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Prozkoumat další možnosti portálu služby Application Insights, přejděte na [portálu dokumentace k Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-wad"></a>Konfigurace Application Insights s využitím WAD

>[!NOTE]
>To platí pouze pro clustery s Windows v tuto chvíli.

Existují dva základní způsoby odesílání dat z WAD do služby Azure Application Insights, který můžete vytvořit přidáním jímky Application Insights do konfigurace WAD, jak je uvedeno v [v tomto článku](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Přidat Instrumentační klíč Application Insights při vytváření clusteru na webu Azure portal

![Přidání AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Při vytváření clusteru, pokud je Diagnostika "On", volitelné pole a zadejte kód instrumentace Application Insights se zobrazí. Pokud vám Application Insights sem vložte klíč, jímku Application Insights automaticky nakonfigurované pro vás v šabloně Resource Manageru, který se používá k nasazení clusteru.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Přidání Application Insights jímky do šablony Resource Manageru

V "WadCfg" šablony Resource Manageru přidejte "Sink" zahrnutím následující dvě změny:

1. Přidat konfiguraci jímky přímo po deklaraci sady `DiagnosticMonitorConfiguration` dokončení:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Zahrnout jímce ve `DiagnosticMonitorConfiguration` přidáním následujícího řádku `DiagnosticMonitorConfiguration` z `WadCfg` (bezprostředně před `EtwProviders` jsou deklarovány):

    ```json
    "sinks": "applicationInsights"
    ```

V obou předchozích fragmenty kódu byl použit název "applicationInsights" k popisu jímky. Toto není povinné a jako jímku název je součástí "jímky", můžete nastavit název na libovolný řetězec.

V současné době protokoly z clusteru zobrazí jako **trasy** v log vieweru. Application Insights. Protože většina trasování z platformy je úrovně "Informační", můžete také zvážit změnu konfigurace jímky pouze odeslat protokoly typu "Kritický" nebo "Chyba". To můžete udělat tak, že přidáte "Kanály" do jímky, jak je ukázáno v [v tomto článku](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Pokud používáte nesprávný klíč Application Insights na portálu nebo v šabloně Resource Manageru, budete muset ručně změnit klíč a aktualizaci clusteru a znovu ji nasadíte.

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurace Application Insights s využitím EventFlow

Pokud používáte využitím EventFlow pro agregaci událostí, ujistěte se, že k importu `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`balíček NuGet. Následující kód je nutné v *výstupy* část *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Ujistěte se, že proveďte požadované změny v filtry, jakož i zahrnout všechny ostatní vstupy (spolu s jejich příslušné balíčky NuGet).

## <a name="application-insights-sdk"></a>Application Insights SDK

Doporučuje se použít využitím EventFlow a WAD jako agregace řešení, protože umožňují modulárnější přístup na diagnostiku a sledování, například pokud chcete změnit vaše výstupy z využitím EventFlow, nevyžaduje žádné změny vaší skutečné instrumentace, pouze jednoduchých úprav do konfiguračního souboru. Pokud však rozhodnout investovat do pomocí Application Insights a pravděpodobně nemohou změnit na různé platformy, by měl vypadat do pomocí nové sady SDK služby Application Insights pro agregaci událostí a odesílá je do služby Application Insights. To znamená, že se už nemusíte konfigurovat využitím EventFlow to poslat data do Application Insights, ale místo toho nainstaluje balíček Service Fabric NuGet ApplicationInsight. Najdete podrobnosti o balíček [tady](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[Podpora služby Application Insights pro Mikroslužby a kontejnery](https://azure.microsoft.com/blog/app-insights-microservices/) uvádí některé nové funkce, které se pracuje (stále aktuálně v beta verze), což vám umožní mít bohatší možnosti monitorování out-of-the-box pomocí Application Insights. Patří mezi ně závislost sledování (používá se při vytváření AppMap všech služeb a aplikací v clusteru a komunikace mezi nimi) a lepší korelace trasování z vašich služeb (pomáhá v lepší přesné určení problém v pracovním postupu aplikace nebo služby).

Pokud vyvíjíte v rozhraní .NET a budou pravděpodobně používat některé z programovacích modelů Service Fabric a jsou natolik, jak pomocí Application Insights jako platformu pro vizualizaci a analýzu dat událostí a protokolů, pak doporučujeme přejít přes Application Insights Sady SDK route jako pracovní postup monitorování a diagnostiku. Čtení [to](../application-insights/app-insights-asp-net-more.md) a [to](../application-insights/app-insights-asp-net-trace-logs.md) začít s ke shromáždění a zobrazení protokolů pomocí Application Insights.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Přejděte prostředku Application Insights na webu Azure portal

Po nakonfigurování služby Application Insights jako výstup pro události a protokoly byste začít informace zobrazí v prostředku Application Insights za pár minut. Přejděte do prostředku Application Insights, který se dostanete na řídicí panel prostředku Application Insights. Klikněte na tlačítko **hledání** na hlavním panelu služby Application Insights a zobrazte nejnovější trasování, které se přijaly a bude moct filtrovat projde.

*Průzkumník metrik* je užitečným nástrojem pro vytváření vlastních řídicích panelů na základě metrik, které mohou být vytváření sestav aplikací, služeb a clusteru. Zobrazit [zkoumání metriky v Application Insights](../application-insights/app-insights-metrics-explorer.md) nastavit několik grafů pro sami na základě dat shromažďujete.

Kliknutím na **Analytics** se dostanete na portál Application Insights Analytics, kde můžete zadávat dotazy událostmi a sledováním s větší rozsah a volitelnost. Další informace najdete v [Analytics ve službě Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Další postup

* [Nastavte si upozornění v umělé Inteligenci](../application-insights/app-insights-alerts.md) abyste dostávali oznámení o změny ve výkonu a využití
* [Inteligentní zjišťování ve službě Application Insights](../application-insights/app-insights-proactive-diagnostics.md) provádí proaktivní analýzu telemetrických dat odesílaných do Application Insights upozornit vás na potenciální problémy s výkonem
