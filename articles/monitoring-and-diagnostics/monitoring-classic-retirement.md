---
title: Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování
description: Přehled o vyřazení klasické služby monitorování a funkci, dříve uvedené na webu Azure portal v části upozornění (klasická). Klasické upozorňování a monitorování zahrnuje klasického upozornění metrik pro prostředky Azure, klasického upozornění metrik pro službu Application Insights, upozornění classic webového testu pro službu Application Insights classic vlastních metrik založených na upozornění služby Application Insights a Klasický model výstrahy pro Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 35869e0a345299f2b0724f01201154fc8d090e4f
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975375"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování

Azure Monitor se teď stal jednotné plnohodnotných monitorovací službu, která teď podporuje "Jeden metrik" a "Jedné výstrahy" v prostředcích; Další informace najdete v tématu naše [blogový příspěvek na nový Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové Azure, monitorování a upozorňování platformy je sestavený Build bude rychlejší a chytřejší vytváření a rozšiřitelné – uchování kroku s rostoucí expanse cloud computingu a v řádku s filozofií inteligentního cloudu společnosti Microsoft. 

S novou Azure monitorování a upozorňování platformy na místě můžeme vyřazení "klasickém", monitorování a upozorňování platformu – hostované v rámci *zobrazit upozornění classic* části upozornění v Azure, se nebude používat roku 2019. června.

 ![Upozornění Classic na webu Azure portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme vám začít a znovu ho vytvořte upozornění na nové platformě. Pro zákazníky, kteří mají velký počet výstrah můžeme pracují na poskytování automatizovaný způsob, jak přesunout stávající klasických upozornění na nový systém upozornění bez přerušení nebo přidat náklady.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Sjednocené metrik a výstrah ve službě Application Insights

Novější metriky Platforma Azure Monitor se teď power monitorování odesílaných ze služby Application Insights. Tento přesun znamená, že Application Insights se zapojit do skupiny akcí, umožňuje mnohem více možností než jenom předchozí volání e-mail a webhook. Výstrahy teď můžete vyvolat hlasových hovorů, Azure Functions, Logic Apps, SMS a nástroji ITSM, jako jsou ServiceNow a Runbooků služby Automation. S téměř v reálném čase monitorování a upozorňování, novou platformu Application Insights uživatelům umožňuje využívat stejné technologie zajišťující provoz monitorování v rámci dalších prostředků Azure a podporu monitorování produktů společnosti Microsoft.

Nová jednotná monitorování a upozorňování pro službu Application Insights bude zahrnovat:

- **Application Insights platformy metriky** – které obsahuje oblíbené předem připravené metriky z produktu Application Insights. Další informace najdete v článku o používání [platformy metriky pro službu Application Insights na nový Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test dostupnosti Application Insights a Web** – které poskytuje možnost vyhodnotit rychlost reakce a dostupnost webové aplikace nebo serveru. Další informace najdete v článku o používání [testy dostupnosti a výstrah pro službu Application Insights na nový Azure Monitor](../application-insights/app-insights-monitor-web-app-availability.md).
- **Application Insights vlastní metriky** – umožňuje definovat a generovat vlastní metriky pro monitorování a výstrah. Další informace najdete v článku o používání [vlastní metriky pro službu Application Insights na nový Azure Monitor](../application-insights/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomálie selhání Application Insights (součást inteligentního zjišťování)** – což automaticky upozorní téměř v reálném čase, zda neobvyklý nárůst míry neúspěšných žádostí HTTP a volání závislostí prostředích vašich webových aplikací. Anomálie selhání Insights (součást inteligentního zjišťování) aplikace jako součást nové monitorování Azure, bude brzy k dispozici a budeme aktualizovat tento dokument s odkazy na další iteraci jako to je zavést v nadcházejících měsících.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Sjednocené metriky a výstrahy pro ostatní prostředky Azure

Od března 2018 byly nové generace upozorňování a monitorování s multidimenzionálním pro prostředky Azure ve skupině dostupnosti. Novější platformu pro metriky a výstrahy je teď rychlejší při použití možnosti téměř v reálném čase. Důležitější je novější upozornění metriky platformy poskytují větší členitost jako novější platforma zahrnuje možnost dimenzí, které vám umožní k nařezání a filtr na kombinaci konkrétní hodnoty, podmínky nebo operaci. Stejně jako všechna upozornění v nové monitorování Azure jsou novějších upozornění metrik více extensible s použitím ActionGroups – povolení oznámení rozšířit nad rámec e-mailu nebo webhooku do služby SMS, hlas, funkce Azure Functions, sady Automation Runbook a další.
Jsou k dispozici jako novější metriky pro prostředky Azure:

- **Azure Monitor standardních metrik platformy** – které obsahuje oblíbené předvyplněný metriky z různých služeb Azure a produkty. Další informace najdete v článku na [podporované metriky pro monitorování Azure](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) a [podporují upozornění metrik na Azure Monitor](alert-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor vlastní metriky** – které poskytuje metriky z uživatelské základě zdrojů, včetně agenta diagnostiky Azure. Další informace najdete v článku na [vlastní metriky ve službě Azure Monitor](metrics-custom-overview.md). Pomocí vlastních metrik, můžete také publikovat metrik shromážděných [agenta Windows Azure Diagnostics](metrics-store-custom-guestos-resource-manager-vm.md) a [InfluxData Telegraf agenta](metrics-store-custom-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasických monitorování a upozorňování platformy

Jak bylo uvedeno dříve, classic monitorování a výstrah z aktuálně použitelné platformy [upozornění (klasická) části](monitoring-overview-alerts-classic.md) Azure portal se vyřadí z provozu v odesílaných měsíců vzhledem, byla nahrazena novější systém.
Starší classic monitorování a upozorňování se vyřadí z provozu 30. června 2019; včetně uzavření související rozhraní API, rozhraní Azure portal a služby v ní. Konkrétně přestanou používat tyto funkce:

- Starší (classic) metrik a výstrah pro prostředky Azure jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](monitoring-overview-alerts-classic.md) Azure portal; přístupná jako [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) platformy a vlastní metriky pro Application Insights jako upozornění na ně jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](monitoring-overview-alerts-classic.md) z webu Azure portal a dostupné jako [microsoft.insights/ alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) anomálie selhání výstraha aktuálně k dispozici jako [inteligentního zjišťování v Application Insights,](../application-insights/app-insights-proactive-diagnostics.md) na webu Azure Portal; nakonfigurovaná upozornění ukazuje [upozornění (klasická) části](monitoring-overview-alerts-classic.md) Azure portál

Všechny klasické monitorování a upozorňování systémy včetně odpovídající [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](insights-alerts-powershell.md), [rozhraní příkazového řádku](insights-alerts-command-line-interface.md), [stránka na webu Azure portal](alert-metric-classic.md)a [ Prostředek šablony](monitoring-enable-alerts-using-template.md) zůstane až do června 2019 použitelné. Po tomto datu classic monitorování a výstrahy služby budou vyřazený a už nebude k dispozici pro použití; Při jakékoli upozornění pravidla, která nadále existovat v upozornění (klasická) nad rámec 2019 dne bude dál spustit, ale není k dispozici pro úpravy.

Všechny výstrahy zbývajících v klasickém monitoring a alerting platformy nad rámec 2019 dne, bude automaticky migrovat od Microsoftu na ekvivalentní na nové platformě Azure monitor v červenci 2019. Proces bude bezproblémové bez jakýchkoli prostojů a ujistěte se, že zákazníci mají bez ztráty monitorování pokrytí.

Brzy budeme poskytovat nástroje, aby bylo možné odpojit migrovat vaše upozornění z [upozornění (klasická) části](monitoring-overview-alerts-classic.md) z webu Azure portal a nová upozornění Azure. Všechna pravidla nakonfigurovaných ve výstrahách (classic), které se migrují na nový Azure Monitor, zůstanou a nebudou účtovat. Migrované pravidla upozornění classic také nenese žádné poplatky pro odesílání nabízených oznámení prostřednictvím e-mailu, webhooku nebo aplikaci LogicApp. Použití novější oznámení nebo akce typů (jako je SMS, hlasovým hovorem, integraci ITSM atd.) bude však fakturovatelné, zda je přidána do migrovaných nebo nové výstrahy. Další informace najdete v tématu [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Kromě toho bude fakturovatelné pod zahrnuty následující [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Všechny nové (nemigrované) pravidlo upozornění vytvořená nad rámec volné jednotky, na nové platformě Azure Monitor
- Žádná data ingestují a ponecháte déle než službou Azure Monitor zahrnuté volné jednotky
- Žádné více testů webové testy spustit v Application Insights
- Jakékoli vlastní metriky, které jsou uloženy nad rámec zahrnuté ve službě Azure Monitor volné jednotky

Tento článek bude průběžně aktualizované se odkazy & Podrobnosti týkající se nové Azure monitoring a alerting funkce a také dostupnost nástroje, které pomáhají uživatelům při přijetí nové platformě Azure Monitor.


## <a name="next-steps"></a>Další postup

* Další informace o [nový jednotný Azure Monitor](../azure-monitor/overview.md).
* Další informace o novém [Azure Alerts](monitoring-overview-alerts.md).
