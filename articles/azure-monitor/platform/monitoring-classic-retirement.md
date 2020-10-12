---
title: Aktualizace klasického upozorňování & monitorování v Azure Monitor
description: Popis vyřazení klasických monitorovacích služeb a funkcí, které se dříve zobrazovaly v Azure Portal v části výstrahy (Classic).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 720a0e25f3486f32ffed897e54033fd4b68dace4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87848121"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Jednotné upozorňování & monitorování v Azure Monitor nahrazuje klasické výstrahy & monitorování

Azure Monitor se teď stala sjednocenou úplnou službou pro monitorování zásobníku, která teď v rámci prostředků podporuje jednu metriku a jedno upozornění. Další informace najdete v našem [blogovém příspěvku o novém Azure monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové platformy pro monitorování a upozorňování Azure jsou sestavené tak, aby byly rychlejší, inteligentnější a rozšiřitelné, a přitom dodržíte rostoucí expansei cloud computingu a online pomocí inteligentního cloudového filozofie Microsoftu.

Díky nové platformě monitorování a upozorňování Azure se vyřadí klasické výstrahy v Azure Monitor, i když jsou stále v omezeném rozsahu použití pro prostředky, které ještě nepodporují nové výstrahy. Datum vyřazení těchto výstrah bylo ještě rozšířeno. Nové datum bude brzy oznámeno pro migraci zbývajících výstrah, [Azure Government Cloud](../../azure-government/documentation-government-welcome.md)a [Azure Čína 21Vianet](https://docs.azure.cn/).

 ![Klasická výstraha v Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme začít a znovu vytvořit upozornění v nové platformě.

> [!IMPORTANT]
> Pravidla pro klasické výstrahy vytvořená v protokolu aktivit nebudou zastaralá ani migrována. Všechna pravidla pro klasické výstrahy vytvořená v protokolu aktivit jsou k dispozici a budou se používat jako z nových Azure Monitor – výstrahy. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu aktivit pomocí Azure monitor](./alerts-activity-log.md). Podobně jsou k dispozici výstrahy na Service Health, které jsou k dispozici v nové Service Health oddílu. Podrobnosti najdete v tématu [výstrahy týkající se oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Sjednocení metrik a výstrah v Application Insights

Azure Monitor novější metriky teď budou monitorovat napájení od Application Insights. Tento přesun znamená, že Application Insights se budou zapojovat do skupin akcí a umožňují mnohem více možností než jenom předchozí volání e-mailu a Webhooku. Výstrahy teď můžou aktivovat hlasové hovory, Azure Functions, Logic Apps, SMS a nástroje ITSM jako Runbooky ServiceNow a Automation. Díky monitorování a upozorňování v reálném čase umožňuje nová platforma Application Insights uživatelům využívat stejné technologie jako monitorování v jiných prostředcích Azure a podporuje monitorování produktů Microsoftu.

Nové sjednocené monitorování a upozorňování na Application Insights bude zahrnovat:

- **Application Insights metriky platforem** , které poskytují populární předem připravené metriky z Application Insights produktu. Další informace najdete v tomto článku o používání [metrik platforem pro Application Insights v nových Azure monitor](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights dostupnost a webový test** , což vám umožní vyhodnotit odezvu a dostupnost vaší webové aplikace nebo serveru. Další informace najdete v tomto článku o použití [testů dostupnosti a výstrah pro Application Insights v nových Azure monitor](../app/monitor-web-app-availability.md).
- **Application Insights vlastní metriky** – díky čemuž můžete definovat a generovat vlastní metriky pro monitorování a výstrahy. Další informace najdete v tomto článku o použití [vlastní metriky pro Application Insights pro nové Azure monitor](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights anomálií selhání (součást inteligentní detekce)** – což vás automaticky upozorní téměř v reálném čase, pokud se ve vaší webové aplikaci nejedná o abnormální nárůst frekvence neúspěšných požadavků HTTP nebo volání závislostí. Další informace najdete v tomto článku o používání [inteligentní detekce – anomálie selhání](../app/proactive-failure-diagnostics.md).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Sjednocení metrik a upozornění pro jiné prostředky Azure

Od března 2018 byly k dispozici další generace výstrah a multidimenzionální monitorování prostředků Azure. Teď je novější metrika a upozorňování rychlejší díky možnostem téměř v reálném čase. Důležitější je, že novější výstrahy na platformě metrik poskytují větší členitost, protože novější platforma zahrnuje možnost dimenzí, což vám umožňuje rozdělit a filtrovat na konkrétní kombinaci hodnot, podmínku nebo operaci. Stejně jako všechny výstrahy v novém Azure Monitor jsou novější výstrahy metrik více rozšiřitelné s využitím ActionGroups – umožňuje oznámení rozšiřovat mimo e-mail nebo Webhook do služby SMS, hlasu, funkce Azure Functions, sady Automation Runbook a dalších funkcí. Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](./alerts-metric.md).
Novější metriky pro prostředky Azure jsou k dispozici jako:

- **Azure monitor standardní metriky platforem** – což nabízí populární předem vyplněnou metriku z různých služeb a produktů Azure. Další informace najdete v tomto článku o [podporovaných metrikách na Azure monitor](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) a [podpoře upozornění na metriky v Azure monitor](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure monitor vlastní metriky** , které poskytují metriky ze zdrojů řízených uživatelem včetně agenta Azure Diagnostics. Další informace najdete v tomto článku o [vlastních metrikách v Azure monitor](./metrics-custom-overview.md). Pomocí vlastních metrik můžete také publikovat metriky shromážděné agentem [Windows Azure Diagnostics agentem](./collect-custom-metrics-guestos-resource-manager-vm.md) a [agentem InfluxData telegraf](./collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasického monitorování a platformy pro výstrahy

Jak bylo uvedeno dříve, jsou vyřazení starší klasické monitorování a upozorňování. včetně uzavírání souvisejících rozhraní API, Azure Portal rozhraní a služeb v něm, i když ještě omezené použití pro prostředky, které ještě nepodporují nové výstrahy. Konkrétně tyto funkce budou zastaralé:

- Starší (Classic) metriky a výstrahy pro prostředky Azure, které jsou aktuálně dostupné prostřednictvím Azure Portal v [části výstrahy (Classic)](./alerts-classic.overview.md) . dostupné jako prostředek [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)
- Starší (Classic) platforma a vlastní metriky pro Application Insights a také výstrahy, jak jsou aktuálně dostupné prostřednictvím [části výstrahy (Classic)](./alerts-classic.overview.md) Azure Portal a přístupné jako prostředek [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)
- Starší (Classic) upozornění na anomálii v současnosti je aktuálně k dispozici jako [inteligentní zjišťování v rámci Application Insights](../app/proactive-diagnostics.md) Azure Portal; s nakonfigurovanými výstrahami zobrazenými v [části výstrahy (klasické)](./alerts-classic.overview.md) Azure Portal

To znamená, že:

- Služba Classic monitorování a výstrahy bude vyřazena a nebude již k dispozici pro vytváření nových pravidel upozornění.
- Všechna pravidla výstrahy, která dál existují v upozorněních (Classic), budou i nadále spouštět a vyvolávat oznámení.
- Pravidla výstrah v klasickém monitorování & upozorňování, které je možné migrovat, automaticky přesune společnost Microsoft na jejich ekvivalent na nové platformě Azure monitor v fázích s několika týdny. Proces bude bezproblémové bez výpadků a zákazníci nebudou mít žádné ztráty v pokrytí monitorování.
- Pravidla upozornění migrovaná na nové platformy výstrah budou poskytovat pokrytí monitorování jako dříve, ale budou moci spustit oznámení s novými datovými částmi. Veškerá e-mailová adresa, koncový bod Webhooku nebo propojení Logic Apps přidružená k pravidlu klasické výstrahy se přenese do migrace, ale nemusí fungovat správně, protože datová část výstrahy bude v nové platformě odlišná.
- Některá [klasická pravidla výstrah, která se nedají automaticky migrovat](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) a vyžadují ruční akci od uživatelů, se budou dál spouštět.

> [!IMPORTANT]
> Microsoft Azure monitorování má v nástroji fáze zahrnuté [do nejbližší migrace](alerts-using-migration-tool.md) svých klasických pravidel upozornění na novou platformu. A spustit ho vynutí všechna klasická pravidla výstrah, která stále existují a můžou být migrována. Zákazníci budou muset zajistit, aby datová část pravidla pro automatizaci Classic byla přizpůsobená, aby zpracovávala novou datovou část z [sjednocených metrik a výstrah v Application Insights](#unified-metrics-and-alerts-in-application-insights) nebo [sjednocených metrik a výstrah pro jiné prostředky Azure](#unified-metrics-and-alerts-for-other-azure-resources), po migraci klasických pravidel upozornění. Další informace najdete v tématu [Příprava pro migraci pravidla upozornění Classic](alerts-prepare-migration.md) .

Tento článek se průběžně aktualizuje pomocí odkazů & podrobnostmi týkajícími se nových funkcí upozorňování služby Azure Monitoring & a dostupnosti nástrojů pro pomoc uživatelům při přijímání nové Azure Monitor platformy.

## <a name="pricing-for-migrated-alert-rules"></a>Ceny pro migrovaná pravidla výstrah

Zavádíme Nástroj pro migraci, který vám umožní migrovat [výstrahy Azure monitor Classic](./alerts-classic.overview.md) na nové prostředí výstrah. Migrovaná pravidla výstrah a odpovídající migrované skupiny akcí (e-mail, Webhook nebo LogicApp) zůstanou zadarmo. Funkce, které jste používali s klasickými výstrahami včetně možnosti upravit prahovou hodnotu, typ agregace a členitosti agregace, budou nadále k dispozici zdarma s vaším pravidlem upozornění na migraci. Pokud však pravidlo migrované výstrahy upravíte tak, aby používalo některou z nových funkcí a oznámení z nové platformy pro upozornění, budou platit odpovídající poplatky. Další informace o cenách pro pravidla a oznámení výstrah najdete v tématu [Azure monitor ceny](https://azure.microsoft.com/pricing/details/monitor/).

Následují příklady případů, kdy se vám za pravidlo výstrahy účtují poplatky:

- Jakékoli nové (nemigrováno) pravidlo výstrahy vytvořené nad rámec volných jednotek na nové platformě Azure Monitor
- Všechna data ingestovaná a zachovaná mimo volné jednotky, kterou zahrnuje Azure Monitor
- Všechny webové testy multi-test spouštěné Application Insights
- Všechny vlastní metriky uložené mimo volné jednotky, které jsou součástí Azure Monitor
- Všechna migrovaná pravidla výstrah, která jsou upravena pro použití novějších funkcí metriky, jako je frekvence, více prostředků nebo dimenzí, [dynamické prahové hodnoty](alerts-dynamic-thresholds.md), změna prostředku nebo signálu atd.
- Všechny migrované skupiny akcí, které jsou upraveny pro použití novějších oznámení, nebo typů akcí, jako je SMS, hlasový hovor nebo integrace ITSM.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [novými sjednocenými Azure monitor](../overview.md).
* Přečtěte si další informace o nových [výstrahách Azure](./alerts-overview.md).

