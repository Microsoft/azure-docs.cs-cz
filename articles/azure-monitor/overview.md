---
title: Přehled služby Azure Monitor | Dokumentace Microsoftu
description: Přehled služeb a funkcí Microsoftu, které přispívají k úplné strategii monitorování aplikací a služeb Azure
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: bwren
ms.openlocfilehash: 160078787d28f43ef800d25c8b50fb316b38944f
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436173"
---
# <a name="azure-monitor-overview"></a>Přehled služby Azure Monitor

Azure Monitor maximalizuje dostupnost a výkon vašich aplikací, zajišťuje komplexní řešení pro shromažďování, analýzu a funguje na telemetrická data z vašeho cloudu a místních prostředích. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Přehled
Následující obrázek poskytuje souhrnný přehled monitorování Azure. V centru diagramu se úložiště dat pro metriky a protokoly, které jsou dva základní druhy dat používá Azure Monitor. Na levé straně jsou [zdrojů, která shromažďují telemetrická data z různých monitorovat prostředky](platform/data-sources.md) a naplňte jimi [úložišť dat](platform/data-collection.md). Na pravé straně jsou různé funkce, které Azure Monitor provádí s Tato shromážděná data, jako je například analýza, upozorňování a streamování do externích systémů.


![Přehled služby Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Monitorování datové platformy
Všechna data shromážděná službou Azure Monitor zapadá do jednoho ze dvou základních typů, [metriky a protokoly](platform/data-collection.md). [Metriky](platform/data-collection.md#metrics) jsou číselné hodnoty, které popisují některé aspekty systému v určitém místě v čase. Jsou jednoduché a schopný zajistit podporu téměř v reálném čase scénáře. [Protokoly](platform/data-collection.md#logs) obsahovat různé druhy dat uspořádaných do s různými sadami vlastností pro jednotlivé typy záznamů. Telemetrická data, jako je například událostmi a sledováním jsou uloženy jako protokoly kromě na data výkonu tak, aby se všechny zkombinovat pro analýzu.

Pro řadu prostředků Azure uvidíte data shromážděná službou Azure Monitor přímo v jejich stránce Přehled na webu Azure Portal. Podíváme se na jakýkoli virtuální počítač například a zobrazí se vám několik grafů zobrazení metrik výkonu. Klikněte na libovolné grafy k otevírání těchto dat v [Průzkumník metrik](../azure-monitor/platform/metrics-charts.md) na webu Azure Portal, která umožňuje graf hodnoty několika metrik v čase.  Můžete zobrazit grafy interaktivně nebo je připnout na řídicí panel k zobrazení se ostatní vizualizace.

![Metriky](media/overview/metrics.png)

<<<<<<< HEAD protokolu data shromážděná službou Azure Monitor můžete analyzovat pomocí [dotazy](log-query/log-query-overview.md) , která rychle načíst, konsolidovat a analyzovat shromážděná data.  Můžete vytvářet a testovat pomocí [protokolu analytics](../log-analytics/log-analytics-log-search-portals.md) webu Azure portal a pak buď přímo analyzovat data s využitím těchto nástrojů nebo ukládat dotazy pro použití s [vizualizace](visualizations.md) nebo [upozornění pravidla](../monitoring-and-diagnostics/monitoring-overview-alerts.md).
=== Protokolu data shromážděná službou Azure Monitor se ukládají ve službě Log Analytics, která zahrnuje [Bohatý dotazovací jazyk](../azure-monitor/log-query/log-query-overview.md) rychle načíst, konsolidovat a analyzovat shromážděná data.  Můžete vytvářet a testovat pomocí [stránce Log Analytics](../azure-monitor/log-query/portals.md) webu Azure portal a pak buď přímo analyzovat data s využitím těchto nástrojů nebo ukládat dotazy pro použití s [vizualizace](visualizations.md) nebo [ pravidla výstrah](../azure-monitor/platform/alerts-overview.md).
>>>>>>> 98b6f1655e9ada6d4b6a408ada0f1e4c9ed727d2

Azure Monitor používá verzi [Průzkumník dat dotazovací jazyk](/azure/kusto/query/) , který je vhodný pro jednoduchá protokolu dotazů, ale zahrnuje také pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Můžete rychle naučit jazyk dotazu pomocí [více lekce](log-query/get-started-queries.md).  Pro uživatele, kteří již znají [SQL](log-query/sql-cheatsheet.md) a [Splunk](log-query/splunk-cheatsheet.md), jsou k dispozici konkrétní pokyny.

![Logs](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jaká data shromažďuje nástroj Azure Monitor?
Azure Monitor může shromažďovat data z různých zdrojů. Můžete si představit sledování dat pro vaše aplikace na úrovních, od aplikace, libovolný operační systém a služeb, které spoléhá na, na platformě, jako takové. Azure Monitor provádí sběr dat ze všech následujících úrovních:

- **Monitorování dat aplikací**: Data o výkonu a funkce kódu, který jste napsali, bez ohledu na jeho platformu.
- **Data monitorování hostovaného operačního systému**: Data o operačním systému, na kterém je aplikace spuštěna. To může být spuštěn v Azure, jiného cloudu nebo místně. 
- **Data monitorování prostředků Azure**: Data o provozu prostředku Azure.
- **Předplatné Azure, data monitorování**: Data o provozu a správy předplatného Azure, jakož i informace o stavu a provoz Azure samotný. 
- **Data monitorování klienta Azure**: Data o provozu služeb Azure úrovni klienta, jako je Azure Active Directory.

Jakmile vytvoříte předplatné Azure a začněte přidávat prostředky, jako jsou virtuální počítače a webové aplikace, spustí monitorování Azure, shromažďování dat.  [Protokoly aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zaznamenat při vytvoření nebo úpravě prostředky. [Metriky](../monitoring-and-diagnostics/monitoring-overview-metrics.md) zjistíte, jaký je výkon prostředku a prostředky, které je spotřebovává. 

Roztáhnout svá data ukládáte do aktuální operace prostředky podle [povolení diagnostiky](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a [Přidání agenta](platform/agent-windows.md) k výpočetním prostředkům. To bude shromažďovat telemetrii pro interní operace prostředku a umožňují konfigurovat různé [zdroje dat](platform/agent-data-sources.md) shromažďovat protokoly a metriky z hostovaného operačního systému Windows a Linux. 

[Přidat Instrumentační balíček do vaší aplikace](../application-insights/app-insights-azure-web-apps.md)povolte Application Insights shromažďovat podrobné informace o vaší aplikaci, včetně zobrazení stránek, žádosti o aplikace a výjimky. Dále ověřte dostupnost vaší aplikace tím, že nakonfigurujete [test dostupnosti](../application-insights/app-insights-monitor-web-app-availability.md) můžete simulovat provoz uživatelů.

### <a name="custom-sources"></a>Vlastní zdroje
Azure Monitor může shromažďovat data protokolu z jakéhokoli klienta REST pomocí [rozhraní API kolekce dat](../azure-monitor/platform/data-collector-api.md). To umožňuje vytvářet vlastní scénáře monitorování a rozšiřování monitorování na prostředky, které Nezveřejňují telemetrická data prostřednictvím dalších zdrojů.



## <a name="insights"></a>Insights
Sledování dat je pouze užitečné, pokud zvýšíte svůj vhled do provozu vaše výpočetní prostředí. Azure Monitor obsahuje několik funkcí a nástrojů, které poskytují cenné přehledy o vašich aplikací a další prostředky, které jsou závislé. [Řešení monitorování](insights/solutions.md) a funkce, jako [Application Insights](../application-insights/app-insights-overview.md) a přehledy o kontejnerech poskytnout podrobný přehled o různých aspektech vaší aplikace a konkrétních služeb Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](../application-insights/app-insights-overview.md) Monitoruje dostupnost, výkon a využití webových aplikací, ať už jsou hostované v cloudu nebo místně. Využívá výkonnou datovou platformu pro analýzy ve službě Azure Monitor vám poskytnout podrobný přehled o operacích vaší aplikace a diagnostikovat chyby bez čekání na jejich nahlášení uživatelem. Application Insights obsahuje spojovací body s celou řadu nástrojů pro vývoj a integruje se sadou Visual Studio pro podporu vašich procesů DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
Azure Monitor pro kontejnery je funkce určené k monitorování výkonu kontejner úloh nasazených do spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Poskytuje přehled o výkonu shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Když povolíte monitorování z clusterů Kubernetes, tyto metriky a protokoly se automaticky shromažďují, můžete prostřednictvím kontejnerizovaných verzi agenta Log Analytics pro Linux.

![Stav kontejneru](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
Azure insights monitorování virtuálního počítače monitoruje virtuální počítače Azure (VM) ve velkém měřítku prostřednictvím analýzy výkonu a stavu Windows a virtuální počítače s Linuxem, včetně jejich různé procesy a propojených záviset na jiných prostředcích a externí proces. Toto řešení zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače hostované v místním nebo jiného zprostředkovatele cloudu.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Řešení monitorování
[Řešení monitorování](insights/solutions.md) ve službě Azure Monitor jsou zabalené sady logik, které poskytují přehledy pro konkrétní aplikaci nebo službě. Obsahují logiku pro shromažďování dat monitorování pro aplikaci nebo službě, [dotazy](log-query/log-query-overview.md) pro analýzy dat, a [zobrazení](../log-analytics/log-analytics-view-designer.md) pro vizualizaci. Řešení pro monitorování [od Microsoftu k dispozici](insights/solutions-inventory.md) a partnery nabízí monitorování pro různé služby Azure a další aplikace.

![Řešení monitorování](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reakce na kritická situacích
Mimo možnosti k interaktivní analýze dat monitorování, musí být schopné proaktivně reagovat na kritické ujednání identifikovat data, která shromažďuje efektivní řešení monitorování. To může odesílat textové nebo e-mailu do správce zodpovědným za vyšetřování problém. Nebo může spustit automatizovaný proces, který se pokusí opravit chybovou podmínku.


### <a name="alerts"></a>Výstrahy
[Výstrahy ve službě Azure Monitor](../azure-monitor/platform/alerts-overview.md) zajistit aktivní upozorňování kritického stavu a potenciálně se pokusí provést opravné akce. Pravidla upozornění na základě metrik poskytuje téměř reálném čase výstrahy podle číselných hodnot, zatímco pravidla založená na protokolech umožňují komplexní logiku napříč daty z více zdrojů.

Pravidla, která používá Azure Monitor výstrah [skupiny akcí](../azure-monitor/platform/action-groups.md), které obsahují jedinečné sady příjemců a akce, které mohou být sdíleny napříč více pravidel. V závislosti na vašich požadavcích, skupiny akcí lze provádět činnosti, jako mají výstrahy spustit externí akce nebo integrují s vašimi nástroji ITSM pomocí webhooků.

![Výstrahy](media/overview/alerts.png)

### <a name="autoscale"></a>Automatické škálování
Automatické škálování umožňuje mít správného množství prostředků systémem pro zpracování zátěže ve své aplikaci. Umožňuje vytvořit pravidla, která pomocí metrik shromážděných službou Azure Monitor k určení, kdy se mají automaticky přidávat prostředky zpracovat nárůst zatížení a také prostředky, které jsou sedí ušetříte peníze nečinnosti. Můžete zadat minimální a maximální počet instancí a logiku, kdy chcete zvýšit nebo snížit prostředky.

![Automatické škálování](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Vizualizace dat monitorování
[Vizualizace](visualizations.md) jako grafy a tabulky jsou účinné nástroje pro shrnutí dat monitorování a prezentace na různé skupiny uživatelů. Azure Monitor má svůj vlastní funkce pro vizualizaci dat monitorování a využívá další služby Azure pro publikování na různé skupiny uživatelů.

### <a name="dashboards"></a>Řídicí panely
[Řídicích panelů Azure](../azure-portal/azure-portal-dashboards.md) umožňují kombinovat různé typy dat, včetně metrik a protokolů, do jediného v [webu Azure portal](https://portal.azure.com). Řídicí panel můžete volitelně sdílet s ostatními uživateli Azure. Elementy v celém Azure Monitor můžete přidat na řídicí panel Azure kromě výstup u kteréhokoli grafu dotazu nebo metriky protokolu. Můžete například vytvořit řídicí panel, který kombinuje dlaždice, které zobrazují graf metrik, tabulky protokolů aktivit, graf využití ze služby Application Insights a výstup protokolu dotazu.

![Řídicí panel](media/overview/dashboard.png)

### <a name="views"></a>Zobrazení
[Zobrazení](../log-analytics/log-analytics-view-designer.md) vizuálně reprezentují data protokolu ve službě Azure Monitor.  Každé zobrazení obsahuje jednu dlaždici, která operací na kombinaci vizualizací, jako jsou pruhové a spojnicové grafy kromě seznamy se souhrnem důležitých data.  Řešení monitorování zahrnují zobrazení se souhrnem dat pro konkrétní aplikaci a můžete vytvořit vlastní zobrazení pro reprezentaci dat z libovolného dotazu protokolu. Jako další elementy ve službě Azure Monitor zobrazení jde přidat na řídicí panely Azure.

![Zobrazení](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) je služba pro obchodní analýzy, která poskytuje interaktivní vizualizace na různých zdrojů dat a efektivní způsob zpřístupnění dat pro ostatní uvnitř a vně organizace. Power BI, můžete nakonfigurovat [automaticky importovat data protokolů ze služby Azure Monitor](../log-analytics/log-analytics-powerbi.md) využívat tyto další vizualizace.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrace a export dat
Často budete mít tento požadavek pro integraci s jinými systémy pro monitorování Azure a k vytvoření vlastních řešení, které používají data monitorování. Další služby Azure fungují s Azure Monitor k poskytování tato integrační.

### <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) je streamování platformy a události služba příjmu, dají se transformovat a ukládat data pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů dávkování/úložišť. Použití služby Event Hubs do [Streamovat data protokolů ze služby Azure Monitor](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) partnerovi SIEM a monitorovací nástroje.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) je služba, která umožňuje automatizovat úlohy a obchodních procesů pomocí pracovních postupů, které se integrují s různými systémy a službami. Aktivity jsou k dispozici, čtení a zápis metrik a protokolů ve službě Azure Monitor, které umožňuje vytvářet pracovní postupy integrace s celou řadu jiných systémů.

![Aplikace logiky](platform/media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="api"></a>Rozhraní API
Několik rozhraní API jsou dostupná pro čtení a zápis metriky a protokoly z Azure monitoru a kromě přístupu vygenerované výstrahy. Můžete také nakonfigurovat a načíst výstrahy. To vám poskytne v podstatě neomezené možnosti, jak vytvářet vlastní řešení, které se integrují se službou Azure Monitor.

## <a name="next-steps"></a>Další postup
Další informace:

* [Metriky a protokoly](platform/data-collection.md) pro data shromážděná službou Azure Monitor.
* [Zdroje dat](platform/data-sources.md) pro jak různých komponent vaší aplikace odesílat telemetrická data.
* [Protokolovat dotazy](log-query/log-query-overview.md) analyzovat shromážděná data.
