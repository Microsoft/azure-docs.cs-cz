---
title: Přehled Azure Monitoru | Dokumenty společnosti Microsoft
description: Přehled služeb a funkcí Microsoftu, které přispívají k úplné strategii monitorování aplikací a služeb Azure
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cfdd0beac7d257a424d327df71602b4612c3da3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536849"
---
# <a name="azure-monitor-overview"></a>Přehled služby Azure Monitor

Azure Monitor maximalizuje dostupnost a výkon vašich aplikací a služeb tím, že poskytuje komplexní řešení pro shromažďování, analýzu a působení na telemetrii z vašeho cloudu a místních prostředí. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé.

Mezi několik příkladů, co můžete s Azure Monitorem dělat, patří:

- Zjišťujte a diagnostikujte problémy napříč aplikacemi a závislostmi pomocí [application insights](app/app-insights-overview.md).
- Korelujte problémy s infrastrukturou s [Azure Monitor pro virtuální počítače](insights/vminsights-overview.md) a Azure Monitor pro [kontejnery](insights/container-insights-overview.md).
- Procházet data monitorování pomocí [analýzy protokolů](log-query/log-query-overview.md) pro řešení potíží a hloubkovou diagnostiku.
- Podpora operací ve velkém měřítku pomocí [inteligentních výstrah](platform/alerts-smartgroups-overview.md) a [automatizovaných akcí](platform/alerts-action-rules.md).
- Vytvářejte vizualizace pomocí [řídicích panelů](learn/tutorial-logs-dashboards.md) Azure a [sešitů](app/usage-workbooks.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Přehled

Následující diagram poskytuje podrobný přehled služby Azure Monitor. Ve středu diagramu jsou úložiště dat pro metriky a protokoly, které jsou dva základní typy využití dat azure monitoru. Vlevo jsou zdroje dat monitorování, které [naplňují](platform/data-sources.md) tato [úložiště dat](platform/data-platform.md). Vpravo jsou různé funkce, které Azure Monitor provádí s těmito shromážděnými daty, jako je analýza, upozorňování a streamování do externích systémů.

![Přehled služby Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Monitorovací datová platforma

Všechna data shromážděná službou Azure Monitor se vejdou do jednoho ze dvou základních typů, [metrik a protokolů](platform/data-platform.md). [Metriky](platform/data-platform-metrics.md) jsou číselné hodnoty, které popisují některé aspekty systému v určitém okamžiku v čase. Jsou lehké a schopné podporovat scénáře téměř v reálném čase. [Protokoly](platform/data-platform-logs.md) obsahují různé druhy dat uspořádaných do záznamů s různými sadami vlastností pro každý typ. Telemetrie, jako jsou události a trasování jsou uloženy jako protokoly kromě dat o výkonu tak, aby všechny lze kombinovat pro analýzu.

U mnoha prostředků Azure uvidíte data shromážděná službou Azure Monitor přímo na jejich stránce Přehled na webu Azure Portal. Podívejte se například na jakýkoli virtuální počítač a uvidíte několik grafů zobrazujících metriky výkonu. Kliknutím na některý z grafů otevřete data v [průzkumníku metrik](platform/metrics-charts.md) na webu Azure Portal, který vám umožní vymapovat hodnoty více metrik v čase.  Grafy můžete zobrazit interaktivně nebo je připnout na řídicí panel a zobrazit je pomocí jiných vizualizací.

![Metriky](media/overview/metrics.png)

Data protokolu shromážděná službou Azure Monitor lze analyzovat pomocí [dotazů](log-query/log-query-overview.md) pro rychlé načtení, konsolidaci a analýzu shromážděných dat.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](log-query/portals.md) na webu Azure Portal a pak buď přímo analyzovat data pomocí těchto nástrojů nebo uložit dotazy pro použití s [vizualizacemi](visualizations.md) nebo [pravidly výstrah](platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) používanou aplikací Azure Data Explorer, která je vhodná pro jednoduché dotazy protokolu, ale také zahrnuje pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk se můžete rychle naučit pomocí [několika lekcí](log-query/get-started-queries.md).  Pro uživatele, kteří již znají [SQL](log-query/sql-cheatsheet.md) a [Splunk](log-query/splunk-cheatsheet.md), jsou k dispozici konkrétní pokyny.

![Protokoly](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jaká data služba Azure Monitor shromažďuje?

Azure Monitor může shromažďovat data z různých zdrojů. Monitorování dat pro aplikace si můžete představit ve vrstvách od vaší aplikace, libovolného operačního systému a služeb, na které daná aplikace spoléhá, až po samotnou platformu. Azure Monitor shromažďuje data z každé z následujících vrstev:

- **Data monitorování aplikací**: Údaje o výkonu a funkčnosti kódu, který jste napsali, bez ohledu na jeho platformu.
- **Data monitorování hostovaného operačního**systému : Data o operačním systému, na kterém je aplikace spuštěna. Může běžet v Azure, v jiném cloudu nebo místně. 
- **Data monitorování prostředků Azure**: Data o provozu prostředku Azure.
- **Data monitorování předplatného Azure**: Data o provozu a správě předplatného Azure, stejně jako data o stavu a provozu samotného Azure. 
- **Data monitorování tenanta Azure**: Data o provozu služeb Azure na úrovni tenanta, jako je Azure Active Directory.

Azure Monitor začne shromažďovat data, jakmile vytvoříte předplatné Azure a začnete přidávat prostředky, jako jsou virtuální počítače a webové aplikace.  [Protokoly aktivit zaznamenávají,](platform/platform-logs-overview.md) kdy jsou prostředky vytvořeny nebo změněny. [Metriky](platform/data-platform.md) vám řeknou, jak si prostředek vede a jaké prostředky spotřebovává. 

Rozšiřte data, která shromažďujete, do skutečného provozu prostředků [povolením diagnostiky](platform/platform-logs-overview.md) a [přidáním agenta](platform/agent-windows.md) k výpočtu prostředků. To bude shromažďovat telemetrii pro interní provoz prostředku a umožňují konfigurovat různé [zdroje dat](platform/agent-data-sources.md) pro shromažďování protokolů a metrik z hostovaného operačního systému Windows a Linux. 

Povolte monitorování [pro aplikaci služby App Services](app/azure-web-apps.md) nebo [aplikaci škálovací sady virtuálních zařízení a virtuálních strojů](app/azure-vm-vmss-apps.md), abyste umožnili application insights shromažďovat podrobné informace o vaší aplikaci, včetně zobrazení stránek, požadavků aplikací a výjimek. Dále ověřte dostupnost aplikace konfigurací [testu dostupnosti](app/monitor-web-app-availability.md) pro simulaci provozu uživatelů.

### <a name="custom-sources"></a>Vlastní zdroje

Azure Monitor můžete shromažďovat data protokolu z libovolného [klienta](platform/data-collector-api.md)REST pomocí rozhraní API shromažďování dat . To umožňuje vytvářet vlastní scénáře monitorování a rozšířit monitorování na prostředky, které nezveřejňují telemetrii prostřednictvím jiných zdrojů.

## <a name="insights"></a>Insights
Data monitorování jsou užitečná pouze v případě, že mohou zvýšit viditelnost provozu výpočetního prostředí. Azure Monitor obsahuje několik funkcí a nástrojů, které poskytují cenné informace o vašich aplikacích a dalších prostředcích, na kterých závisí. Řešení a funkce [monitorování,](insights/solutions.md) jako jsou [Application Insights](app/app-insights-overview.md) a Azure Monitor [pro kontejnery,](insights/container-insights-overview.md) poskytují podrobný přehled o různých aspektech vaší aplikace a konkrétních služeb Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoruje dostupnost, výkon a využití webových aplikací bez ohledu na to, jestli jsou hostované v cloudu nebo místně. Využívá výkonnou platformu pro analýzu dat ve službě Azure Monitor, která vám poskytne podrobný přehled o operacích vaší aplikace a diagnostikuje chyby, aniž by čekala, až je uživatel nahlásí. Application Insights obsahuje body připojení k různým vývojovým nástrojům a integruje se s Visual Studio pro podporu procesů DevOps.

![Přehledy aplikací](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
[Azure Monitor pro kontejnery](insights/container-insights-overview.md) je funkce určená ke sledování výkonu úloh kontejnerů nasazených do spravovaných clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Poskytuje viditelnost výkonu shromažďováním metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metriky. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se tyto metriky a protokoly automaticky shromažďují prostřednictvím kontejnerizované verze agenta Log Analytics pro Linux.

![Stav kontejneru](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure Monitor pro virtuální počítače](insights/vminsights-overview.md) monitoruje vaše virtuální počítače Azure (VM) ve velkém měřítku analýzou výkonu a stavu vašich virtuálních počítačů s Windows a Linuxem, včetně jejich různých procesů a propojených závislostí na jiných prostředcích a externích procesech. Řešení zahrnuje podporu pro monitorování výkonu a závislostí aplikací pro virtuální servery hostované místně nebo jiného poskytovatele cloudu.  


![Přehledy virtuálních virtuálních mispozi](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Řešení monitorování
[Monitorování řešení](insights/solutions.md) ve službě Azure Monitor jsou zabalené sady logiky, které poskytují přehledy pro konkrétní aplikaci nebo službu. Patří mezi ně logika pro shromažďování dat monitorování pro aplikaci nebo službu, [dotazy](log-query/log-query-overview.md) k analýze těchto dat a [zobrazení](../log-analytics/log-analytics-view-designer.md) pro vizualizaci. Řešení monitorování jsou [k dispozici od Microsoftu](insights/solutions-inventory.md) a partnerů, kteří poskytují monitorování pro různé služby Azure a další aplikace.

![Řešení monitorování](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reakce na kritické situace
Kromě toho, že vám umožní interaktivně analyzovat data monitorování, musí být efektivní řešení monitorování schopno aktivně reagovat na kritické podmínky zjištěné v datech, která shromažďuje. Může se jedná o odeslání textové zprávy nebo e-mailu správci odpovědnému za vyšetřování problému. Nebo můžete spustit automatizovaný proces, který se pokusí opravit chybový stav.


### <a name="alerts"></a>Výstrahy
[Výstrahy v Azure Monitor](platform/alerts-overview.md) proaktivně vás upozorní na kritické podmínky a potenciálně se pokusí přijmout nápravná opatření. Pravidla výstrah založená na metrikách poskytují téměř upozornění v reálném čase na základě číselných hodnot, zatímco pravidla založená na protokolech umožňují složitou logiku napříč daty z více zdrojů.

Pravidla výstrah ve službě Azure Monitor používají [skupiny akcí](platform/action-groups.md), které obsahují jedinečné sady příjemců a akce, které lze sdílet mezi více pravidly. Na základě vašich požadavků mohou skupiny akcí provádět takové akce, jako je použití webhooků k tomu, aby výstrahy spustily externí akce nebo aby se integrovaly s nástroji ITSM.

![Výstrahy](media/overview/alerts.png)

### <a name="autoscale"></a>Automatické škálování
Automatické škálování umožňuje mít správné množství prostředků spuštěných pro zpracování zatížení vaší aplikace. Umožňuje vytvářet pravidla, která používají metriky shromážděné službou Azure Monitor k určení, kdy se mají automaticky přidávat prostředky ke zpracování zvýšení zatížení a také ušetřit peníze odebráním prostředků, které jsou nečinné. Zadáte minimální a maximální počet instancí a logiku pro kdy zvýšit nebo snížit prostředky.

![Automatické škálování](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Vizualizace dat monitorování
[Vizualizace,](visualizations.md) jako jsou grafy a tabulky, jsou efektivní nástroje pro shrnutí dat monitorování a jejich prezentaci různým cílovým skupinám. Azure Monitor má své vlastní funkce pro vizualizaci dat monitorování a využívá další služby Azure pro publikování na různých cílových skupin.

### <a name="dashboards"></a>Řídicí panely
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) umožňují kombinovat různé druhy dat, včetně metrik a protokolů, do jednoho podokna na [webu Azure Portal](https://portal.azure.com). Můžete volitelně sdílet řídicí panel s ostatními uživateli Azure. Prvky v rámci Azure Monitor u dát přidándo řídicího panelu Azure kromě výstupu libovolného dotazu protokolu nebo metriky grafu. Můžete například vytvořit řídicí panel, který kombinuje dlaždice, které zobrazují graf metrik, tabulku protokolů aktivit, graf využití z Application Insights a výstup dotazu protokolu.

![Řídicí panel](media/overview/dashboard.png)

### <a name="views"></a>Zobrazení
[Zobrazení](../log-analytics/log-analytics-view-designer.md) vizuálně prezentovat data protokolu v Azure Monitoru.  Každé zobrazení obsahuje jednu dlaždici, která kromě seznamů shrnujících důležitá data přecváká na kombinaci vizualizací, jako jsou pruhové a spojnicové grafy.  Monitorování řešení zahrnují zobrazení, která sumarizují data pro konkrétní aplikaci a můžete vytvořit vlastní zobrazení prezentovat data z libovolného dotazu protokolu. Stejně jako ostatní prvky ve službě Azure Monitor lze zobrazení přidat do řídicích panelů Azure.

![Zobrazení](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) je služba obchodní analýzy, která poskytuje interaktivní vizualizace napříč různými zdroji dat a je účinným prostředkem zpřístupnění dat ostatním uživatelům ve vaší organizaci i mimo ni. Power BI můžete nakonfigurovat tak, aby [automaticky importoval data protokolu z Azure Monitoru,](../log-analytics/log-analytics-powerbi.md) abyste mohli využít těchto dalších vizualizací.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrace a export dat
Často budete mít požadavek na integraci Azure Monitoru s jinými systémy a vytváření vlastních řešení, která používají vaše data monitorování. Ostatní služby Azure spolupracují s Azure Monitorem a poskytují tuto integraci.

### <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) je streamovací platforma a služba pro ingestování událostí, která může transformovat a ukládat data pomocí libovolného poskytovatele analýzy v reálném čase nebo dávkových a úložných adaptérů. Pomocí centra událostí [můžete streamovat data Azure Monitor](platform/stream-monitoring-data-event-hubs.md) u partnerského SIEM a nástrojů pro monitorování.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) je služba, která umožňuje automatizovat úkoly a obchodní procesy pomocí pracovních postupů, které se integrují s různými systémy a službami. Aktivity jsou k dispozici, které čtení a zápis metriky a protokoly v Azure Monitoru, který umožňuje vytvářet pracovní postupy integrace s řadou jiných systémů.


### <a name="api"></a>rozhraní API
Více rozhraní API jsou k dispozici pro čtení a zápis metriky a protokoly do a z Azure Monitor kromě přístupu k generované výstrahy. Můžete také konfigurovat a načítat výstrahy. To vám poskytuje v podstatě neomezené možnosti vytváření vlastních řešení, která se integrují s Azure Monitorem.

## <a name="next-steps"></a>Další kroky
Další informace:

* [Metriky a protokoly](platform/data-platform.md) pro data shromážděná službou Azure Monitor.
* [Zdroje dat](platform/data-sources.md) pro způsob, jakým různé součásti aplikace odesílají telemetrii.
* [Protokolovat dotazy](log-query/log-query-overview.md) pro analýzu shromážděných dat.
* [Doporučené postupy](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) pro monitorování cloudových aplikací a služeb.
