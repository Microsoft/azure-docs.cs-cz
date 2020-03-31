---
title: Aktualizace klasického monitorování & výstrah v Azure Monitoru
description: Popis vyřazení klasických monitorovacích služeb a funkcí, které se dříve zobrazovaly na webu Azure Portal v části Výstrahy (klasické).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659471"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Jednotné monitorování & monitorování ve službě Azure Monitor nahrazuje klasické & monitorování

Azure Monitor se teď stala jednotnou službou monitorování celého zásobníku, která teď podporuje "jednu metriku" a jednu výstrahu napříč prostředky. Další informace najdete v našem [příspěvku blogu na novém Azure Monitoru](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové platformy azure monitoringu a upozorňování byly navrženy tak, aby byly rychlejší, chytřejší a rozšiřitelné – držely krok s rostoucí rozlohou cloud computingu a byly v souladu s filozofií Inteligentního cloudu Microsoftu. 

S novou platformou azure monitorování a upozorňování na místě, budeme vyřazení "klasické" monitorování a upozorňování platformy - hostované v rámci *zobrazení klasické výstrahy* části výstrah Azure, **bude zastaralé do srpna 2019 ve veřejných cloudech Azure**. [Cloud Azure Government](../../azure-government/documentation-government-welcome.md) a [Azure China 21Vianet](https://docs.azure.cn/) nebudou ovlivněny.

> [!NOTE]
> Kvůli zpoždění při zavádění migračního nástroje bylo datum odchodu do důchodu pro klasickou [výstrahu prodlouženo na 31.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

 ![Klasická výstraha na webu Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme vám začít a znovu vytvářet upozornění v nové platformě. Pro zákazníky, kteří mají velký počet výstrah, zavádíme ve fázích , což je [nástroj pro dobrovolnou migraci,](alerts-using-migration-tool.md) který přesouvá stávající klasické výstrahy do nového systému výstrah bez přerušení nebo [dalších](alerts-understand-migration.md#rollout-phases)nákladů.

> [!IMPORTANT]
> Klasická pravidla výstrah vytvořená v protokolu aktivit nebudou zastaralá ani migrovaná. Všechna klasická pravidla výstrah vytvořená v protokolu aktivit jsou přístupná a používána tak, jak je z nového monitoru Azure – výstrahy. Další informace najdete [v tématu Vytváření, zobrazení a správa výstrah protokolu aktivit pomocí azure monitoru](../../azure-monitor/platform/alerts-activity-log.md). Podobně výstrahy na stav služby lze přistupovat a používat jako-je z nové části stav služby. Podrobnosti naleznete v [tématu upozornění na oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Jednotné metriky a výstrahy v přehledech aplikací

Novější metrická platforma Azure Monitoru teď bude moc monitorování pocházející z Application Insights. Tento krok znamená, že Application Insights se připojí k akčním skupinám, což umožňuje mnohem více možností než jen předchozí e-mail a volání webhooku. Výstrahy teď můžou spouštět hlasové hovory, funkce Azure, logic aplikace, SMS a nástroje ITSM, jako jsou ServiceNow a Automatizační sady Runbook. Díky monitorování a upozorňování téměř v reálném čase umožňuje nová platforma uživatelům Application Insights využívat stejnou technologii, která podporuje monitorování napříč jinými prostředky Azure a podporuje monitorování produktů Microsoftu.

Nové jednotné monitorování a upozorňování pro přehledy aplikací bude zahrnovat:

- **Metriky platformy Application Insights –** které poskytují oblíbené předem vytvořené metriky z produktu Application Insights. Další informace najdete v tomto článku o použití [metriky platformy pro přehledy aplikací na nové Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Dostupnost přehledů aplikací a webový test** , který vám poskytuje možnost posoudit odezvu a dostupnost webové aplikace nebo serveru. Další informace naleznete v tomto článku o použití [testů dostupnosti a výstrah pro přehledy aplikací na novém Azure Monitoru](../../azure-monitor/app/monitor-web-app-availability.md).
- **Vlastní metriky Application Insights** – které vám umožní definovat a vyzařovat vlastní metriky pro monitorování a výstrahy. Další informace najdete v tomto článku o použití [vlastní metriky pro přehledy aplikací na novém Azure Monitoru](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomálie selhání přehledů aplikací (součást inteligentní detekce)** – která vás automaticky upozorní téměř v reálném čase, pokud vaše webová aplikace dojde k abnormálnímu nárůstu rychlosti neúspěšných požadavků HTTP nebo volání závislostí. Další informace naleznete v tomto článku o používání [inteligentní detekce - anomálie selhání](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Jednotné metriky a výstrahy pro jiné prostředky Azure

Od března 2018 je k dispozici nová generace výstrah a vícerozměrného monitorování prostředků Azure. Nyní je novější metrická platforma a upozorňování rychlejší s funkcemi téměř v reálném čase. Ještě důležitější je, že novější upozornění platformy metriky poskytují více rozlišovací schopnost, protože novější platforma obsahuje možnost dimenzí, které umožňují řez a filtrovat na konkrétní kombinaci hodnot, podmínku nebo operaci. Stejně jako všechny výstrahy v novém Azure Monitoru, novější metriky výstrahy jsou rozšiřitelnější s použitím ActionGroups – umožňuje oznámení rozšířit mimo e-mail nebo webhookna SMS, hlas, Azure funkce, automatizace Runbook a další. Další informace najdete [v tématu Vytváření, zobrazení a správa upozornění na metriky pomocí Azure Monitoru](../../azure-monitor/platform/alerts-metric.md).
Novější metriky pro prostředky Azure jsou k dispozici jako:

- **Metriky platformy Azure Monitor Standard** – které poskytují oblíbené předem vyplněné metriky z různých služeb a produktů Azure. Další informace najdete v tomto článku o [podporovaných metrik na Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) a [výstrahy metrik podpory na Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Vlastní metriky Azure Monitor** – který poskytuje metriky z uživatelem řízených zdrojů, včetně agenta Diagnostika Azure. Další informace najdete v tomto článku o [vlastní metriky v Azure Monitoru](../../azure-monitor/platform/metrics-custom-overview.md). Pomocí vlastních metrik můžete také publikovat metriky shromážděné [agentem Diagnostiky Windows Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) a [agentem InfluxData Telegraf](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasické monitorovací a upozorňující platformy

Jak již bylo uvedeno dříve, klasická platforma monitorování a upozorňování, která je aktuálně použitelná z [části Výstrahy (klasické)](../../azure-monitor/platform/alerts-classic.overview.md) na webu Azure Portal, bude v nadcházejících měsících vyřazena, protože byla nahrazena novějším systémem.
Starší klasické monitorování a upozorňování bude vyřazeno 31. včetně uzavření souvisejících rozhraní API, rozhraní portálu Azure a služeb v něm. Konkrétně tyto funkce budou zastaralé:

- Starší (klasické) metriky a výstrahy pro prostředky Azure, které jsou momentálně dostupné prostřednictvím [části Výstrahy (klasické)](../../azure-monitor/platform/alerts-classic.overview.md) na webu Azure Portal; přístupný jako prostředek [Microsoft.Insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Starší (klasická) platforma a vlastní metriky pro Application Insights, stejně jako upozornění na ně jako aktuálně dostupné prostřednictvím [výstrah (klasické) části](../../azure-monitor/platform/alerts-classic.overview.md) webu Azure Portal a přístupné jako [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) zdroj
- Starší (klasické) poruchy anomálie výstrahy aktuálně k dispozici jako [inteligentní zjišťování uvnitř application insights](../../azure-monitor/app/proactive-diagnostics.md) na webu Azure Portal; s výstrahami nakonfigurovanými v [části Výstrahy (klasické)](../../azure-monitor/platform/alerts-classic.overview.md) na webu Azure Portal

Všechny klasické monitorování a upozorňování systémy, včetně odpovídající [rozhraní API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [stránka portálu Azure](../../azure-monitor/platform/alerts-classic-portal.md)a [šablona prostředků](../../azure-monitor/platform/alerts-enable-template.md) zůstane použitelná až do konce srpna 2019. 

Na konci srpna 2019 ve službě Azure Monitor:

- Služba klasického monitorování a výstrah bude vyřazena a již nebude k dispozici pro vytvoření nových pravidel výstrah.
- Všechna pravidla výstrah, která v výstrahách (klasickém) přetrvávají i po srpnu 2019, budou nadále spouštět a zapalovat oznámení, ale nebudou k dispozici pro úpravy.
- Od září 2019 budou pravidla výstrah v klasickém monitorování & výstrahám, která lze migrovat, automaticky přesunuta Microsoftem na jejich ekvivalent v nové platformě Azure monitoru ve fázích trvajících několik týdnů. Proces bude bezproblémový bez prostojů a zákazníci nebudou mít žádné ztráty v monitorování pokrytí.
- Pravidla výstrah migrovaná na novou platformu výstrah budou poskytovat monitorování jako dříve, ale smí oznámení s novými datovými zatíženími. Všechny e-mailové adresy, koncový bod webhooku nebo odkaz na aplikaci logiky spojené s klasickým pravidlem výstrahy se budou přenášet při migraci, ale nemusí se chovat správně, protože datová část výstrahy se bude lišit v nové platformě.
- Některá [klasická pravidla výstrah, která nelze automaticky migrovat](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) a vyžadují ruční akci od uživatelů, budou nadále spuštěna až do června 2020.

> [!IMPORTANT]
> Microsoft Azure Monitor zavedla ve fázích [nástroj dobrovolně migrovat](alerts-using-migration-tool.md) jejich klasická pravidla výstrah na novou platformu brzy. A spusťte jej silou pro všechna klasická pravidla výstrah, která stále existují a mohou být migrována, od září 2019. Zákazníci budou muset zajistit, aby automatizace spotřebovávající klasickou datovou část pravidel výstrah byla přizpůsobena tak, aby zpracovávala novou datovou část z [jednotných metrik a výstrah v přehledech aplikací](#unified-metrics-and-alerts-in-application-insights) nebo [sjednocených metrik a výstrah pro jiné prostředky Azure](#unified-metrics-and-alerts-for-other-azure-resources), po migraci klasických pravidel výstrah. Další informace naleznete v [tématu PŘÍPRAVA na klasickou migraci pravidel výstrahy.](alerts-prepare-migration.md)

Tento článek bude průběžně aktualizován odkazy & podrobnosti týkající se nové funkce monitorování Azure & upozornění funkce, stejně jako dostupnost nástrojů, které pomáhají uživatelům při přijímání nové platformy Azure Monitor.

## <a name="pricing-for-migrated-alert-rules"></a>Ceny pro pravidla přenesených výstrah

Zavádíme nástroj pro migraci, který vám pomůže migrovat [klasické výstrahy](../../azure-monitor/platform/alerts-classic.overview.md) Azure Monitoru do nového prostředí výstrah. Migrovaná pravidla výstrah a odpovídající skupiny přenesených akcí (e-mail, webhook nebo LogicApp) zůstanou zdarma. Funkce, které jste měli s klasickými výstrahami, včetně možnosti upravit prahovou hodnotu, typ agregace a rozlišovací schopnost agregace, budou i nadále k dispozici zdarma s pravidlem migrované výstrahy. Pokud však upravíte pravidlo migrované výstrahy tak, aby používalo některou z nových funkcí platformy výstrah, oznámení nebo typů akcí, bude účtován odpovídající poplatek. Další informace o cenách pravidel výstrah a oznámení najdete v [tématu Azure Monitor Ceny](https://azure.microsoft.com/pricing/details/monitor/).

Následují příklady případů, kdy vám bude účtován poplatek za pravidlo výstrahy:

- Jakékoli nové (nemigrované) pravidlo výstrahy vytvořené mimo volné jednotky na nové platformě Azure Monitor
- Všechna data přijatá a uchovávaná mimo volné jednotky zahrnuté v Azure Monitoru
- Všechny webové testy s více testy testů prováděné application insights
- Všechny vlastní metriky uložené nad rámec volných jednotek zahrnutých v Azure Monitoru
- Všechna migrovaná pravidla výstrah, která jsou upravena tak, aby používala novější funkce upozornění na metriky, jako je frekvence, více zdrojů/dimenzí, [dynamické prahové hodnoty](alerts-dynamic-thresholds.md), změna zdroje/signálu a tak dále.
- Všechny migrované skupiny akcí, které jsou upraveny tak, aby používaly novější oznámení nebo typy akcí, jako je SMS, hlasový hovor nebo integrace ITSM.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [novém jednotném Azure Monitoru](../../azure-monitor/overview.md).
* Další informace o nových [výstrahách Azure](../../azure-monitor/platform/alerts-overview.md).
