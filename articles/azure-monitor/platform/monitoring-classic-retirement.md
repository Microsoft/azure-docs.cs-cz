---
title: Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování
description: Přehled o vyřazení klasické služby monitorování a funkci, dříve uvedené na webu Azure portal v části upozornění (klasická). Klasické upozorňování a monitorování zahrnuje klasického upozornění metrik pro prostředky Azure, klasického upozornění metrik pro službu Application Insights, upozornění classic webového testu pro službu Application Insights classic vlastních metrik založených na upozornění služby Application Insights a Klasický model výstrahy pro Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5e882bf0a9b7cbc8a3e96c0184cc1ad53893e989
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999216"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování

Azure Monitor teď stal jednotné plnohodnotných sledování služby, které teď podporuje v prostředků; "Jedné výstrahy" a "jedna metrika. Další informace najdete v tématu naše [blogový příspěvek na nový Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové Azure, monitorování a upozorňování platformy je sestavený Build bude rychlejší a chytřejší vytváření a rozšiřitelné – uchování kroku s rostoucí expanse cloud computingu a v řádku s filozofií inteligentního cloudu společnosti Microsoft. 

S novou Azure monitorování a upozorňování platformy na místě můžeme vyřazení "klasickém", monitorování a upozorňování platformu – hostované v rámci *zobrazit upozornění classic* části upozornění v Azure, **přestanou dne 2019**.

 ![Upozornění Classic na webu Azure portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme vám začít a znovu ho vytvořte upozornění na nové platformě. Pro zákazníky, kteří mají velký počet výstrah můžeme pracují na poskytování automatizovaný způsob, jak přesunout stávající klasických upozornění na nový systém upozornění bez přerušení nebo přidat náklady.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Sjednocené metrik a výstrah ve službě Application Insights

Novější metriky Platforma Azure Monitor se teď power monitorování odesílaných ze služby Application Insights. Tento přesun znamená, že Application Insights se zapojit do skupiny akcí, umožňuje mnohem více možností než jenom předchozí volání e-mail a webhook. Výstrahy teď můžete vyvolat hlasových hovorů, Azure Functions, Logic Apps, SMS a nástroji ITSM, jako jsou ServiceNow a Runbooků služby Automation. S téměř v reálném čase monitorování a upozorňování, novou platformu Application Insights uživatelům umožňuje využívat stejné technologie zajišťující provoz monitorování v rámci dalších prostředků Azure a podporu monitorování produktů společnosti Microsoft.

Nová jednotná monitorování a upozorňování pro službu Application Insights bude zahrnovat:

- **Application Insights platformy metriky** – které obsahuje oblíbené předem připravené metriky z produktu Application Insights. Další informace najdete v článku o používání [platformy metriky pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test dostupnosti Application Insights a Web** – které poskytuje možnost vyhodnotit rychlost reakce a dostupnost webové aplikace nebo serveru. Další informace najdete v článku o používání [testy dostupnosti a výstrah pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application Insights vlastní metriky** – umožňuje definovat a generovat vlastní metriky pro monitorování a výstrah. Další informace najdete v článku o používání [vlastní metriky pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomálie selhání Application Insights (součást inteligentního zjišťování)** – což automaticky upozorní téměř v reálném čase, zda neobvyklý nárůst míry neúspěšných žádostí HTTP a volání závislostí prostředích vašich webových aplikací. Anomálie selhání Insights (součást inteligentního zjišťování) aplikace jako součást nové monitorování Azure, bude brzy k dispozici a budeme aktualizovat tento dokument s odkazy na další iteraci jako to je zavést v nadcházejících měsících.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Sjednocené metriky a výstrahy pro ostatní prostředky Azure

Od března 2018 byly nové generace upozorňování a monitorování s multidimenzionálním pro prostředky Azure ve skupině dostupnosti. Novější platformu pro metriky a výstrahy je teď rychlejší při použití možnosti téměř v reálném čase. Důležitější je novější upozornění metriky platformy poskytují větší členitost jako novější platforma zahrnuje možnost dimenzí, které vám umožní k nařezání a filtr na kombinaci konkrétní hodnoty, podmínky nebo operaci. Stejně jako všechna upozornění v nové monitorování Azure jsou novějších upozornění metrik více extensible s použitím ActionGroups – povolení oznámení rozšířit nad rámec e-mailu nebo webhooku do služby SMS, hlas, funkce Azure Functions, sady Automation Runbook a další.
Jsou k dispozici jako novější metriky pro prostředky Azure:

- **Azure Monitor standardních metrik platformy** – které obsahuje oblíbené předvyplněný metriky z různých služeb Azure a produkty. Další informace najdete v článku na [podporované metriky pro monitorování Azure](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) a [podporují upozornění metrik na Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor vlastní metriky** – které poskytuje metriky z uživatelské základě zdrojů, včetně agenta diagnostiky Azure. Další informace najdete v článku na [vlastní metriky ve službě Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Pomocí vlastních metrik, můžete také publikovat metrik shromážděných [agenta Windows Azure Diagnostics](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) a [InfluxData Telegraf agenta](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasických monitorování a upozorňování platformy

Jak bylo uvedeno dříve, classic monitorování a výstrah z aktuálně použitelné platformy [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal se vyřadí z provozu v odesílaných měsíců vzhledem, byla nahrazena novější systém.
Starší classic monitorování a upozorňování se vyřadí z provozu 30. června 2019; včetně uzavření související rozhraní API, rozhraní Azure portal a služby v ní. Konkrétně přestanou používat tyto funkce:

- Starší (classic) metrik a výstrah pro prostředky Azure jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal; přístupná jako [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) platformy a vlastní metriky pro Application Insights jako upozornění na ně jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) z webu Azure portal a dostupné jako [microsoft.insights/ alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) anomálie selhání výstraha aktuálně k dispozici jako [inteligentního zjišťování v Application Insights,](../../application-insights/app-insights-proactive-diagnostics.md) na webu Azure Portal; nakonfigurovaná upozornění ukazuje [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portál

Všechny klasické monitorování a upozorňování systémy včetně odpovídající [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [rozhraní příkazového řádku](../../azure-monitor/platform/alerts-classic-portal.md), [stránka na webu Azure portal](../../azure-monitor/platform/alerts-classic-portal.md)a [ Prostředek šablony](../../azure-monitor/platform/alerts-enable-template.md) zůstane až do konce dne 2019 použitelné. 

Na konci dne. 2019 ve službě Azure Monitor:

- Klasické služby monitorování a oznamování bude vyřazeno a již není k dispozici pro vytvoření nového pravidla upozornění
- Pravidla výstrah, které dál existovat v upozornění (klasická) nad rámec 2019 dne bude i nadále spouštět a aktivují upozornění, ale nebudou k dispozici pro úpravy.
- Od července 2019 se všechna pravidla upozornění v klasickém monitoring a alerting budou automaticky migrovat od Microsoftu na ekvivalentní na nové platformě Azure monitor. Proces bude bezproblémové bez výpadku a bez ztráty monitorování pokrytí budou mít zákazníci.

Brzy budeme poskytovat nástroje, aby bylo možné odpojit migrovat vaše upozornění z [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) z webu Azure portal a nová upozornění Azure. Všechna pravidla nakonfigurovaných ve výstrahách (classic), které se migrují na nový Azure Monitor, zůstanou a nebudou účtovat. Migrované pravidla upozornění classic také nenese žádné poplatky pro odesílání nabízených oznámení prostřednictvím e-mailu, webhooku nebo aplikaci LogicApp. Použití novější oznámení nebo akce typů (jako je SMS, hlasovým hovorem, integraci ITSM atd.) bude však fakturovatelné, zda je přidána do migrovaných nebo nové výstrahy. Další informace najdete v tématu [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Kromě toho bude fakturovatelné pod zahrnuty následující [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Všechny nové (nemigrované) pravidlo upozornění vytvořená nad rámec volné jednotky, na nové platformě Azure Monitor
- Žádná data ingestují a ponecháte déle než službou Azure Monitor zahrnuté volné jednotky
- Žádné více testů webové testy spustit v Application Insights
- Jakékoli vlastní metriky, které jsou uloženy nad rámec zahrnuté ve službě Azure Monitor volné jednotky

Tento článek bude průběžně aktualizované se odkazy & Podrobnosti týkající se nové Azure monitoring a alerting funkce a také dostupnost nástroje, které pomáhají uživatelům při přijetí nové platformě Azure Monitor.


## <a name="next-steps"></a>Další postup

* Další informace o [nový jednotný Azure Monitor](../../azure-monitor/overview.md).
* Další informace o novém [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
