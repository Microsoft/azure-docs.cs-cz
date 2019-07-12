---
title: Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování
description: Přehled o vyřazení klasické služby monitorování a funkci, dříve uvedené na webu Azure portal v části upozornění (klasická). Klasické upozorňování a monitorování zahrnuje klasického upozornění metrik pro prostředky Azure, klasického upozornění metrik pro službu Application Insights, upozornění classic webového testu pro službu Application Insights classic vlastních metrik založených na upozornění služby Application Insights a Klasický model výstrahy pro Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 28dba8af059e24040abb254dd4ecd8e336d8221d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594287"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Sjednocené upozorňování a monitorování ve službě Azure Monitor nahradí classic upozorňování a monitorování

Azure Monitor teď stal jednotné plnohodnotných sledování služby, které teď podporuje v prostředků; "Jedné výstrahy" a "jedna metrika. Další informace najdete v tématu naše [blogový příspěvek na nový Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nové Azure, monitorování a upozorňování platformy je sestavený Build bude rychlejší a chytřejší vytváření a rozšiřitelné – uchování kroku s rostoucí expanse cloud computingu a v řádku s filozofií inteligentního cloudu společnosti Microsoft. 

S novou Azure monitorování a upozorňování platformy na místě můžeme vyřazení "klasickém", monitorování a upozorňování platformu – hostované v rámci *zobrazit upozornění classic* části upozornění v Azure, **se přestanou používat podle 2019 dne na Azure veřejných cloudů**. [Azure Government cloud](../../azure-government/documentation-government-welcome.md) a [Azure China 21Vianet](https://docs.azure.cn/) tyto zásady neovlivní.

> [!NOTE]
> Datum vyřazení pro migraci klasických upozornění byla z důvodu zpoždění při uvádění nástroj pro migraci [rozšířená tak, aby 31. srpna 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) od původně oznámené data 30. června 2019.

 ![Upozornění Classic na webu Azure portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Doporučujeme vám začít a znovu ho vytvořte upozornění na nové platformě. Pro zákazníky, kteří mají velký počet výstrah, jsme [postupné ve fázích](alerts-understand-migration.md#rollout-phases), [dobrovolné migrační nástroj](alerts-using-migration-tool.md) přesunout stávající klasických upozornění na nový systém upozornění bez přerušení a náklady.

> [!IMPORTANT]
> Klasické pravidla upozornění vytvořená v protokolu aktivit nebude zastaralé nebo migrovat. Jde přistupovat a používat jako všechny klasické pravidla upozornění vytvořená na protokol aktivit – je nová Azure monitor – výstrahy. Další informace najdete v tématu [vytvoření, zobrazení a správa pomocí Azure monitoru upozornění protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md). Podobně můžete přistupovat a použít jako upozornění na stav služby-je z části nový stav služby. Podrobnosti najdete v tématu [výstrahy týkající se oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Sjednocené metrik a výstrah ve službě Application Insights

Novější metriky Platforma Azure Monitor se teď power monitorování odesílaných ze služby Application Insights. Tento přesun znamená, že Application Insights se zapojit do skupiny akcí, umožňuje mnohem více možností než jenom předchozí volání e-mail a webhook. Výstrahy teď můžete vyvolat hlasových hovorů, Azure Functions, Logic Apps, SMS a nástroji ITSM, jako jsou ServiceNow a Runbooků služby Automation. S téměř v reálném čase monitorování a upozorňování, novou platformu Application Insights uživatelům umožňuje využívat stejné technologie zajišťující provoz monitorování v rámci dalších prostředků Azure a podporu monitorování produktů společnosti Microsoft.

Nová jednotná monitorování a upozorňování pro službu Application Insights bude zahrnovat:

- **Application Insights platformy metriky** – které obsahuje oblíbené předem připravené metriky z produktu Application Insights. Další informace najdete v článku o používání [platformy metriky pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test dostupnosti Application Insights a Web** – které poskytuje možnost vyhodnotit rychlost reakce a dostupnost webové aplikace nebo serveru. Další informace najdete v článku o používání [testy dostupnosti a výstrah pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application Insights vlastní metriky** – umožňuje definovat a generovat vlastní metriky pro monitorování a výstrah. Další informace najdete v článku o používání [vlastní metriky pro službu Application Insights na nový Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomálie selhání Application Insights (součást inteligentního zjišťování)** – což automaticky upozorní téměř v reálném čase, zda neobvyklý nárůst míry neúspěšných žádostí HTTP a volání závislostí prostředích vašich webových aplikací. Další informace najdete v článku o používání [inteligentní zjišťování – anomálie selhání](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Sjednocené metrik a výstrah pro ostatní prostředky Azure

Od března 2018 byly nové generace upozorňování a monitorování s multidimenzionálním pro prostředky Azure ve skupině dostupnosti. Novější platformu pro metriky a výstrahy je teď rychlejší při použití možnosti téměř v reálném čase. Důležitější je novější upozornění metriky platformy poskytují větší členitost jako novější platforma zahrnuje možnost dimenzí, které vám umožní k nařezání a filtr na kombinaci konkrétní hodnoty, podmínky nebo operaci. Stejně jako všechna upozornění v nové monitorování Azure jsou novějších upozornění metrik více extensible s použitím ActionGroups – povolení oznámení rozšířit nad rámec e-mailu nebo webhooku do služby SMS, hlas, funkce Azure Functions, sady Automation Runbook a další. Další informace najdete v tématu [vytvoření, zobrazení a Správa upozornění na metriku pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).
Jsou k dispozici jako novější metriky pro prostředky Azure:

- **Azure Monitor standardních metrik platformy** – které obsahuje oblíbené předvyplněný metriky z různých služeb Azure a produkty. Další informace najdete v článku na [podporované metriky pro monitorování Azure](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) a [podporují upozornění metrik na Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor vlastní metriky** – které poskytuje metriky z uživatelské základě zdrojů, včetně agenta diagnostiky Azure. Další informace najdete v článku na [vlastní metriky ve službě Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Pomocí vlastních metrik, můžete také publikovat metrik shromážděných [agenta Windows Azure Diagnostics](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) a [InfluxData Telegraf agenta](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Vyřazení klasických monitorování a upozorňování platformy

Jak bylo uvedeno dříve, classic monitorování a výstrah z aktuálně použitelné platformy [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal se vyřadí z provozu v odesílaných měsíců vzhledem, byla nahrazena novější systém.
Starší classic monitorování a upozorňování skončí 31. srpna 2019; včetně uzavření související rozhraní API, rozhraní Azure portal a služby v ní. Konkrétně přestanou používat tyto funkce:

- Starší (classic) metrik a výstrah pro prostředky Azure jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal; přístupná jako [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) platformy a vlastní metriky pro Application Insights jako upozornění na ně jako aktuálně k dispozici prostřednictvím [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) z webu Azure portal a dostupné jako [microsoft.insights/ alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) prostředků
- Starší (classic) anomálie selhání výstraha aktuálně k dispozici jako [inteligentního zjišťování v Application Insights,](../../azure-monitor/app/proactive-diagnostics.md) na webu Azure Portal; nakonfigurovaná upozornění ukazuje [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) Azure portál

Všechny klasické monitorování a upozorňování systémy včetně odpovídající [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [rozhraní příkazového řádku](../../azure-monitor/platform/alerts-classic-portal.md), [stránka na webu Azure portal](../../azure-monitor/platform/alerts-classic-portal.md)a [ Prostředek šablony](../../azure-monitor/platform/alerts-enable-template.md) zůstane až do konce dne 2019 použitelné. 

Na konci dne. 2019 ve službě Azure Monitor:

- Klasické služby monitorování a oznamování bude vyřazeno a již není k dispozici pro vytvoření nového pravidla upozornění.
- Pravidla výstrah, které dál existovat v upozornění (klasická) nad rámec 2019. srpna bude i nadále spouštět a aktivují upozornění, ale nebudou k dispozici pro úpravy.
- Od září 2019, pravidla výstrah ve službě classic monitoring a alerting, které se dají migrovat, bude automaticky přesunut microsoftem na ekvivalentní na nové platformě Azure monitor ve fázích pokrývající několik týdnů. Proces bude bezproblémové bez výpadku a bez ztráty monitorování pokrytí budou mít zákazníci.
- Upozornění pravidla migrovány do nového oznámení platformy bude poskytovat monitorování pokrytí jako předtím, ale se aktivuje upozornění s datovými částmi nové. Všechny e-mailovou adresu, koncový bod webhooku nebo odkaz na aplikaci logiky přidružené klasické pravidlo upozornění přenesena při migraci, ale nemusí správně fungovat jako datová část oznámení se bude lišit v nové platformy.
- Některé [classic pravidel upozornění, které se nedají migrovat automaticky](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) a vyžadují ruční zásah uživatele bude pokračovat až do roku 2020 dne.

> [!IMPORTANT]
> Microsoft Azure Monitor se vrátila ve fázích [nástroj pro migraci dobrovolně](alerts-using-migration-tool.md) classic pravidla výstrah k nové platformy brzy. A spusťte ho vynutí pro všechny klasické pravidla výstrah, které stále existuje a je možné migrovat, od září 2019. Zákazníci musí zajistit náročné datové části klasické pravidlo upozornění je přizpůsobena zpracovat nové datové části ze služby automation [Unified metrik a výstrah ve službě Application Insights](#unified-metrics-and-alerts-in-application-insights) nebo [Unified metriky a upozornění na jiné službě Azure prostředky](#unified-metrics-and-alerts-for-other-azure-resources), po migraci klasického pravidel upozornění. Další informace najdete v tématu [Příprava na migraci klasické pravidlo upozornění](alerts-prepare-migration.md)

Zavádíme nástroj pro migraci a umožňuje tak dobrovolně migrovat vaše upozornění z [upozornění (klasická) části](../../azure-monitor/platform/alerts-classic.overview.md) z webu Azure portal a nová upozornění Azure. Všechna pravidla nakonfigurovaných ve výstrahách (classic), které se migrují na nový Azure Monitor, zůstanou a nebudou účtovat. Migrované pravidla upozornění classic také nenese žádné poplatky pro odesílání nabízených oznámení prostřednictvím e-mailu, webhooku nebo aplikaci LogicApp. Použití novější oznámení nebo akce typů (jako je SMS, hlasovým hovorem, integraci ITSM atd.) bude však fakturovatelné, zda je přidána do migrovaných nebo nové výstrahy. Další informace najdete v tématu [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Kromě toho bude fakturovatelné pod zahrnuty následující [ceny služby Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Všechny nové (nemigrované) pravidlo upozornění vytvořená nad rámec volné jednotky, na nové platformě Azure Monitor
- Žádná data ingestují a ponecháte déle než službou Azure Monitor zahrnuté volné jednotky
- Žádné více testů webové testy spustit v Application Insights
- Jakékoli vlastní metriky, které jsou uloženy nad rámec zahrnuté ve službě Azure Monitor volné jednotky

Tento článek bude průběžně aktualizován s odkazy & Podrobnosti týkající se nové Azure monitoring a alerting funkce a také dostupnost nástroje a pomáhají uživatelům při přijetí nové platformě Azure Monitor.


## <a name="next-steps"></a>Další postup

* Další informace o [nový jednotný Azure Monitor](../../azure-monitor/overview.md).
* Další informace o novém [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
