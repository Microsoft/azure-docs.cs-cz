---
title: Přehled Azure Monitor | Microsoft Docs
description: Přehled služeb a funkcí Microsoftu, které přispívají k úplné strategii monitorování aplikací a služeb Azure
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 005068c8e81adb9a79a4e6dc7e86a9bfb39902a1
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088625"
---
# <a name="azure-monitor-overview"></a>Přehled služby Azure Monitor

Azure Monitor maximalizuje dostupnost a výkon vašich aplikací a služeb tím, že poskytuje ucelené řešení pro shromažďování, analýzu a vykonávání telemetrie z vašich cloudových a místních prostředí. Pomůže vám při zjišťování stavu vašich aplikací a proaktivně identifikuje problémy, které je ovlivňují, a prostředky, na kterých jsou závislé.

Jedním z příkladů toho, co můžete s Azure Monitor udělat, je:

- Vyhledá a diagnostikuje problémy napříč aplikacemi a závislostmi pomocí [Application Insights](app/app-insights-overview.md).
- Korelujte problémy infrastruktury s [Azure monitor pro virtuální počítače](insights/vminsights-overview.md) a [Azure monitor pro kontejnery](insights/container-insights-overview.md).
- Přejděte k datům monitorování pomocí [Log Analytics](log-query/log-query-overview.md) pro řešení potíží a hloubkovou diagnostiku.
- Podpora operací se škálováním pomocí [inteligentních výstrah](platform/alerts-smartgroups-overview.md) a [automatizovaných akcí](platform/alerts-action-rules.md).
- Vytvářejte vizualizace pomocí [řídicích panelů](learn/tutorial-logs-dashboards.md) a [sešitů](platform/workbooks-overview.md)Azure.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Přehled

Následující diagram poskytuje celkový přehled služby Azure Monitor. Uprostřed diagramu jsou úložiště dat pro metriky a protokoly, které jsou dvěma základními typy dat, které používá Azure Monitor. Vlevo jsou [zdroje dat monitorování](platform/data-sources.md) , která naplňují tato [úložiště dat](platform/data-platform.md). Napravo jsou různé funkce, které Azure Monitor provádí s těmito shromážděnými daty, jako jsou například analýzy, upozorňování a streamování do externích systémů.

![Přehled služby Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Monitorovaná datová platforma

Všechna data shromážděná Azure Monitor se vejdou do jednoho ze dvou základních typů, [metrik a protokolů](platform/data-platform.md). [Metriky](platform/data-platform-metrics.md) jsou číselné hodnoty, které popisují určitý aspekt systému v určitém časovém okamžiku. Jsou odlehčené a schopné podporovat scénáře téměř v reálném čase. [Protokoly](platform/data-platform-logs.md) obsahují různé druhy dat uspořádané do záznamů s různými sadami vlastností pro každý typ. Telemetrie, jako jsou události a trasování, se kromě údajů o výkonu ukládají jako protokoly, aby se mohla kombinovat pro účely analýzy.

V případě mnoha prostředků Azure uvidíte data shromážděná Azure Monitor přímo na stránce s přehledem v Azure Portal. Podívejte se na libovolný virtuální počítač a zobrazí se několik grafů, které zobrazují metriky výkonu. Kliknutím na kterýkoli z grafů otevřete data v [Průzkumníku metrik](platform/metrics-charts.md) v Azure Portal, což umožňuje v průběhu času seřadí hodnoty více metrik.  Grafy můžete interaktivně zobrazit nebo připnout na řídicí panel, abyste je viděli s ostatními vizualizacemi.

![Diagram zobrazuje data metrik do Průzkumník metrik, která se mají použít ve vizualizacích.](media/overview/metrics.png)

Data protokolu shromážděná pomocí Azure Monitor lze analyzovat pomocí [dotazů](log-query/log-query-overview.md) pro rychlé načítání, konsolidaci a analýzu shromážděných dat.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](./log-query/log-query-overview.md) v Azure Portal a pak buď přímo analyzovat data pomocí různých nástrojů, nebo ukládat dotazy pro použití s [vizualizacemi](visualizations.md) nebo [pravidly výstrah](platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) , kterou používá služba Azure Průzkumník dat, která je vhodná pro jednoduché dotazy protokolů, ale zahrnuje i pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk můžete rychle zjistit pomocí [více lekcí](log-query/get-started-queries.md).  Pro uživatele, kteří již znají [SQL](log-query/sql-cheatsheet.md) a [Splunk](log-query/splunk-cheatsheet.md), jsou k dispozici konkrétní pokyny.

![Diagram znázorňuje data protokolů, která se přenášejí do Log Analytics pro účely analýzy.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jaká data shromažďuje služba Azure Monitor?

Azure Monitor může shromažďovat data z různých zdrojů. Monitorování dat pro aplikace si můžete představit ve vrstvách od vaší aplikace, libovolného operačního systému a služeb, na které daná aplikace spoléhá, až po samotnou platformu. Azure Monitor shromažďuje data z každé z následujících vrstev:

- **Data monitorování aplikací**: údaje o výkonu a funkcích kódu, který jste napsali, bez ohledu na jeho platformu.
- **Data monitorování hostovaného operačního**systému: data o operačním systému, na kterém je vaše aplikace spuštěná. Může běžet v Azure, v jiném cloudu nebo místně. 
- **Monitorování dat prostředku Azure:** Data týkající se provozu prostředku Azure.
- **Data monitorování předplatného Azure**: data o provozu a správě předplatného Azure a také údaje o stavu a provozu samotného Azure. 
- **Data monitorování tenanta Azure**: data o provozu služeb Azure na úrovni tenanta, jako je Azure Active Directory.

Azure Monitor začne shromažďovat data, jakmile vytvoříte předplatné Azure a začnete přidávat prostředky, jako jsou virtuální počítače a webové aplikace.  Záznamy [protokolů aktivit](platform/platform-logs-overview.md) při vytváření nebo úpravách prostředků [Metriky](platform/data-platform.md) vás informují o tom, jak prostředek pracuje, a prostředky, které je náročné. 

Data, která shromažďujete, rozšíříte do skutečné operace prostředků tím, že [povolíte diagnostiku](platform/platform-logs-overview.md) a [přidáte agenta](platform/agent-windows.md) pro výpočetní prostředky. Tím se shromáždí telemetrie pro interní operaci prostředku a umožní vám nakonfigurovat různé [zdroje dat](platform/agent-data-sources.md) pro shromažďování protokolů a metrik z hostovaného operačního systému Windows a Linux. 

Povolte monitorování pro aplikaci [App Services](app/azure-web-apps.md) nebo virtuální počítač [a aplikaci sady škálování virtuálního počítače](app/azure-vm-vmss-apps.md), aby bylo možné Application Insights shromažďovat podrobné informace o vaší aplikaci včetně zobrazení stránek, žádostí o aplikace a výjimek. Dále ověřte dostupnost aplikace konfigurací [testu dostupnosti](app/monitor-web-app-availability.md) pro simulaci provozu uživatelů.

### <a name="custom-sources"></a>Vlastní zdroje

Azure Monitor může shromažďovat data protokolu z libovolného klienta REST pomocí [rozhraní API kolekce dat](platform/data-collector-api.md). Díky tomu můžete vytvářet vlastní scénáře monitorování a rozšíříte monitorování na prostředky, které nezveřejňují telemetrii prostřednictvím jiných zdrojů.

## <a name="insights"></a>Insights
Data monitorování jsou užitečná jenom v případě, že může zvýšit vaši viditelnost provozu výpočetního prostředí. Azure Monitor obsahuje několik funkcí a nástrojů, které poskytují cenné přehledy o aplikacích a dalších prostředcích, na kterých jsou závislé. [Monitorování řešení](insights/solutions.md) a funkcí, jako jsou [Application Insights](app/app-insights-overview.md) a [Azure monitor pro kontejnery](insights/container-insights-overview.md) , poskytují podrobné přehledy o různých aspektech aplikace a konkrétních služeb Azure. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoruje dostupnost, výkon a využití webových aplikací bez ohledu na to, jestli jsou hostované v cloudu nebo místně. Využívá výkonnou platformu pro analýzu dat v Azure Monitor k poskytování podrobných přehledů o operacích vaší aplikace a k diagnostice chyb, aniž by bylo třeba čekat na jejich nahlášení uživateli. Application Insights obsahuje body připojení k nejrůznějším vývojářským nástrojům a integruje se se sadou Visual Studio, aby podporovala procesy DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
[Azure monitor for Containers](insights/container-insights-overview.md) je funkce navržená tak, aby sledovala výkon úloh kontejneru nasazených ve spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Poskytuje přehled o výkonu díky shromažďování metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se tyto metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux.

![Stav kontejneru](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure monitor pro virtuální počítače](insights/vminsights-overview.md) monitoruje vaše virtuální počítače Azure ve velkém měřítku tím, že analyzuje výkon a stav virtuálních počítačů s Windows a Linux, včetně jejich různých procesů a vzájemně propojených závislostí s dalšími prostředky a externími procesy. Řešení zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače hostované místně nebo jiným poskytovatelem cloudu.  


![Přehledy virtuálních počítačů](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Řešení monitorování
[Řešení monitorování](insights/solutions.md) v Azure monitor jsou zabalené sady logiky, které poskytují přehledy pro konkrétní aplikaci nebo službu. Obsahují logiku pro shromažďování dat monitorování pro aplikaci nebo službu, [dotazy](log-query/log-query-overview.md) k analýze těchto dat a [zobrazení](./platform/view-designer.md) pro vizualizaci. Řešení pro monitorování jsou [dostupná od Microsoftu](./monitor-reference.md) a partnerů, aby poskytovaly monitorování pro různé služby Azure a další aplikace.

![Řešení monitorování](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reakce na kritické situace
Kromě toho, že vám umožní interaktivně analyzovat data monitorování, efektivní řešení monitorování musí být schopné aktivně reagovat na kritické podmínky zjištěné v datech, která shromažďuje. Může to odeslat text nebo e-mail správci zodpovědnému za prošetření problému. Nebo můžete spustit automatizovaný proces, který se pokusí opravit chybový stav.


### <a name="alerts"></a>Výstrahy
[Výstrahy v Azure monitor](platform/alerts-overview.md) proaktivně upozorňují na kritické podmínky a potenciálně se snaží provést nápravná opatření. Pravidla výstrah založená na metrikách poskytují výstrahy téměř v reálném čase na základě číselných hodnot, zatímco pravidla založená na protokolech umožňují složitou logiku napříč daty z více zdrojů.

Pravidla výstrah v Azure Monitor používají [skupiny akcí](platform/action-groups.md), které obsahují jedinečné sady příjemců a akcí, které je možné sdílet v rámci více pravidel. V závislosti na vašich požadavcích můžou skupiny akcí provádět takové akce jako s použitím webhooků, aby výstrahy spouštěly externí akce nebo prováděly integraci s nástroji ITSM.

![Snímek obrazovky zobrazuje výstrahy v Azure Monitor se závažnostmi, celkovými výstrahami a dalšími informacemi.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatické škálování
Automatické škálování umožňuje mít ke zpracování zatížení vaší aplikace správné množství prostředků, na kterých běží. Umožňuje vytvořit pravidla, která používají metriky shromážděná Azure Monitor k určení, kdy se mají automaticky přidávat prostředky, které se budou zvyšovat zatížením, a také ušetřit peníze odebráním nečinných prostředků. Zadáte minimální a maximální počet instancí a logiku, kdy chcete zvýšit nebo snížit prostředky.

![Diagram znázorňuje automatické škálování s několika servery na řádku s názvem procesor čas > 80% a dva servery s označením minimální, tři servery jako aktuální kapacitu a pět jako maximum.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Vizualizace dat monitorování
[Vizualizace](visualizations.md) , jako jsou grafy a tabulky, jsou efektivní nástroje pro shrnutí dat monitorování a jejich prezentaci různým cílovým skupinám. Azure Monitor má své vlastní funkce pro vizualizaci dat monitorování a využívá další služby Azure pro jejich publikování do různých cílových skupin.

### <a name="dashboards"></a>Řídicí panely
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) umožňují kombinovat různé druhy dat, včetně obou metrik a protokolů, do jednoho podokna v [Azure Portal](https://portal.azure.com). Volitelně můžete řídicí panel sdílet s dalšími uživateli Azure. Prvky v rámci Azure Monitor lze přidat do řídicího panelu Azure kromě výstupu jakýchkoli dotazů protokolu nebo grafu metrik. Můžete například vytvořit řídicí panel, který kombinuje dlaždice, které zobrazují graf metrik, tabulku protokolů aktivit, graf využití z Application Insights a výstup dotazu protokolu.

![Snímek obrazovky ukazuje řídicí panel Azure, který zahrnuje dlaždice aplikace a zabezpečení spolu s dalšími přizpůsobitelnými informacemi.](media/overview/dashboard.png)

### <a name="views"></a>Zobrazení
[Zobrazení](./platform/view-designer.md) vizuálně prezentují data protokolu v Azure monitor.  Každé zobrazení obsahuje jednu dlaždici, která se podrobněji rozpíná na kombinaci vizualizací, jako jsou například pruhové a spojnicové grafy, včetně shrnutí důležitých dat.  Mezi řešení monitorování patří zobrazení, která shrnují data pro konkrétní aplikaci, a můžete si vytvořit vlastní zobrazení, která budou prezentovat data z jakéhokoli dotazu protokolu. Podobně jako jiné prvky v Azure Monitor mohou být zobrazení přidána do řídicích panelů Azure.

![Snímek obrazovky zobrazuje dlaždici pro řešení monitorování kontejnerů a podrobné zobrazení, které se otevře, pokud vyberete dlaždici.](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) je služba obchodní analýzy, která poskytuje interaktivní vizualizace napříč různými zdroji dat a je účinným prostředkem k zpřístupnění dat ostatním v rámci vaší organizace i mimo ni. Power BI můžete nakonfigurovat tak, aby [automaticky importovala data protokolu z Azure monitor](./platform/powerbi.md) a využila tyto další vizualizace.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrace a export dat
Často budete mít nutnost integrovat Azure Monitor s jinými systémy a vytvářet vlastní řešení, která používají data monitorování. Další služby Azure spolupracují s Azure Monitor k zajištění této integrace.

### <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](../event-hubs/index.yml) je streamovaná platforma a služba pro příjem událostí, která umožňuje transformovat a ukládat data pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkování/ukládání. Pomocí Event Hubs můžete [streamovat Azure monitor data](platform/stream-monitoring-data-event-hubs.md) do partnerských nástrojů pro Siem a monitorování.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) je služba, která umožňuje automatizovat úlohy a obchodní procesy pomocí pracovních postupů, které se integrují s různými systémy a službami. K dispozici jsou aktivity, které čtou a zapisují metriky a protokoly v Azure Monitor, což umožňuje sestavovat pracovní postupy integrující s nejrůznějšími jinými systémy.


### <a name="api"></a>Rozhraní API
K dispozici je více rozhraní API pro čtení a zápis metrik a protokolů do a z Azure Monitor Kromě přístupu k vygenerovaným výstrahám. Můžete také nakonfigurovat a načíst výstrahy. Získáte tak v podstatě neomezené možnosti pro vytváření vlastních řešení, která se integrují s Azure Monitor.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace:

* [Metriky a protokoly](platform/data-platform.md) pro data shromažďovaná nástrojem Azure monitor.
* [Zdroje dat](platform/data-sources.md) pro způsob, jakým jednotlivé komponenty vaší aplikace odesílají telemetrii.
* [Dotazy protokolu](log-query/log-query-overview.md) pro analýzu shromážděných dat
* [Osvědčené postupy](/azure/architecture/best-practices/monitoring) pro monitorování cloudových aplikací a služeb.
