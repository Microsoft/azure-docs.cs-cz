---
title: Přehled Azure Monitor | Microsoft Docs
description: Přehled služeb a funkcí Microsoftu, které přispívají k úplné strategii monitorování aplikací a služeb Azure
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 7c48311612d48ef616e5b4c0eefaaa0ae7bb2e84
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451275"
---
# <a name="azure-monitor-overview"></a>Přehled služby Azure Monitor

Azure Monitor vám pomůže maximalizovat dostupnost a výkon vašich aplikací a služeb. Poskytuje komplexní řešení pro shromažďování, analýzu a provoz telemetrie z vašeho cloudového a místního prostředí. Tyto informace vám pomohou pochopit, jak vaše aplikace provádí a aktivně identifikují problémy, které mají vliv na problémy, a prostředky, na kterých jsou závislé.

Jedním z příkladů toho, co můžete s Azure Monitor udělat, je:

- Vyhledá a diagnostikuje problémy napříč aplikacemi a závislostmi pomocí [Application Insights](app/app-insights-overview.md).
- Korelujte problémy infrastruktury s [Azure monitor pro virtuální počítače](insights/vminsights-overview.md) a [Azure monitor pro kontejnery](insights/container-insights-overview.md).
- Přejděte k datům monitorování pomocí [Log Analytics](log-query/log-query-overview.md) pro řešení potíží a hloubkovou diagnostiku.
- Podpora operací se škálováním pomocí [inteligentních výstrah](platform/alerts-smartgroups-overview.md) a [automatizovaných akcí](platform/alerts-action-rules.md).
- Vytvářejte vizualizace pomocí [řídicích panelů](learn/tutorial-logs-dashboards.md) a [sešitů](platform/workbooks-overview.md)Azure.
- Shromažďovat data z [monitorovaných prostředků](./monitor-reference.md) pomocí [Azure monitor metrik](./platform/data-platform-metrics.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Přehled

Následující diagram poskytuje celkový přehled služby Azure Monitor. Uprostřed diagramu jsou úložiště dat pro metriky a protokoly, které jsou dvěma základními typy dat, které používá Azure Monitor. Vlevo jsou [zdroje dat monitorování](platform/data-sources.md) , která naplňují tato [úložiště dat](platform/data-platform.md). Napravo jsou různé funkce, které Azure Monitor s těmito shromážděnými daty provádět. To zahrnuje takové akce jako analýzy, upozorňování a streamování do externích systémů.

![Přehled služby Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Monitorovaná datová platforma

Všechna data shromážděná Azure Monitor se vejdou do jednoho ze dvou základních typů, [metrik a protokolů](platform/data-platform.md). [Metriky](platform/data-platform-metrics.md) jsou číselné hodnoty, které popisují určitý aspekt systému v určitém časovém okamžiku. Jsou odlehčené a schopné podporovat scénáře téměř v reálném čase. [Protokoly](platform/data-platform-logs.md) obsahují různé druhy dat uspořádané do záznamů s různými sadami vlastností pro každý typ. Telemetrie, jako jsou události a trasování, se kromě údajů o výkonu ukládají jako protokoly, aby se mohla kombinovat pro účely analýzy.

V případě mnoha prostředků Azure uvidíte data shromážděná Azure Monitor přímo na stránce s přehledem v Azure Portal. Podívejte se na libovolný virtuální počítač a zobrazí se několik grafů, které zobrazují metriky výkonu. Kliknutím na kterýkoli z grafů otevřete data v [Průzkumníku metrik](platform/metrics-charts.md) v Azure Portal, což umožňuje v průběhu času seřadí hodnoty více metrik.  Grafy můžete interaktivně zobrazit nebo připnout na řídicí panel, abyste je viděli s ostatními vizualizacemi.

![Diagram zobrazuje data metrik do Průzkumník metrik, která se mají použít ve vizualizacích.](media/overview/metrics.png)

Data protokolu shromážděná pomocí Azure Monitor lze analyzovat pomocí [dotazů](log-query/log-query-overview.md) pro rychlé načítání, konsolidaci a analýzu shromážděných dat.  Můžete vytvářet a testovat dotazy pomocí [Log Analytics](./log-query/log-query-overview.md) v Azure Portal. Data pak můžete buď přímo analyzovat pomocí různých nástrojů, nebo ukládat dotazy pro použití s [vizualizacemi](visualizations.md) nebo [pravidly upozornění](platform/alerts-overview.md).

Azure Monitor používá verzi [dotazovacího jazyka Kusto](/azure/kusto/query/) , která je vhodná pro jednoduché dotazy protokolů, ale zahrnuje i pokročilé funkce, jako jsou agregace, spojení a inteligentní analýzy. Dotazovací jazyk můžete rychle zjistit pomocí [více lekcí](log-query/get-started-queries.md).  Pro uživatele, kteří již znají [SQL](log-query/sql-cheatsheet.md) a [Splunk](log-query/splunk-cheatsheet.md), jsou k dispozici konkrétní pokyny.

![Diagram znázorňuje data protokolů, která se přenášejí do Log Analytics pro účely analýzy.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jaká data shromažďuje služba Azure Monitor?

Azure Monitor může shromažďovat data z [nejrůznějších zdrojů](monitor-reference.md). Rozsah z vaší aplikace, jakýkoli operační systém a služby, na kterých spoléhá, na platformu přímo. Azure Monitor shromažďuje data z každé z následujících vrstev:

- **Data monitorování aplikací**: údaje o výkonu a funkcích kódu, který jste napsali, bez ohledu na jeho platformu.
- **Data monitorování hostovaného operačního** systému: data o operačním systému, na kterém je vaše aplikace spuštěná. Může běžet v Azure, v jiném cloudu nebo místně. 
- **Monitorování dat prostředku Azure:** Data týkající se provozu prostředku Azure.
- **Data monitorování předplatného Azure**: data o provozu a správě předplatného Azure a také údaje o stavu a provozu samotného Azure. 
- **Data monitorování tenanta Azure**: data o provozu služeb Azure na úrovni tenanta, jako je Azure Active Directory.

Azure Monitor začne shromažďovat data, jakmile vytvoříte předplatné Azure a začnete přidávat prostředky, jako jsou virtuální počítače a webové aplikace.  Záznamy [protokolů aktivit](platform/platform-logs-overview.md) při vytváření nebo úpravách prostředků [Metriky](platform/data-platform.md) vás informují o tom, jak prostředek pracuje, a prostředky, které je náročné. 

[Povolte diagnostiku](platform/platform-logs-overview.md) , abyste rozšířili shromažďovaná data do interní operace prostředků.  [Přidejte agenta](platform/agents-overview.md) pro výpočetní prostředky pro shromažďování telemetrie ze svých hostovaných operačních systémů. 

Povolte monitorování vaší aplikace pomocí [Application Insights](app/app-insights-overview.md) ke shromažďování podrobných informací, včetně zobrazení stránky, žádostí o aplikace a výjimek. Dále ověřte dostupnost aplikace konfigurací [testu dostupnosti](app/monitor-web-app-availability.md) pro simulaci provozu uživatelů.

### <a name="custom-sources"></a>Vlastní zdroje

Azure Monitor může shromažďovat data protokolu z libovolného klienta REST pomocí [rozhraní API kolekce dat](platform/data-collector-api.md). Díky tomu můžete vytvářet vlastní scénáře monitorování a rozšíříte monitorování na prostředky, které nezveřejňují telemetrii prostřednictvím jiných zdrojů.

## <a name="insights"></a>Insights
Data monitorování jsou užitečná jenom v případě, že může zvýšit vaši viditelnost provozu výpočetního prostředí. [Přehledy](monitor-reference.md#insights-and-core-solutions) poskytují přizpůsobené možnosti monitorování pro konkrétní služby Azure. Vyžadují minimální konfiguraci a zvyšují přehled o fungování důležitých prostředků.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) monitoruje dostupnost, výkon a využití webových aplikací bez ohledu na to, jestli jsou hostované v cloudu nebo místně. Využívá výkonnou platformu pro analýzu dat v Azure Monitor a poskytuje podrobné přehledy o operacích vaší aplikace. Umožňuje diagnostikovat chyby bez čekání na uživatele, kteří je nahlásí. Application Insights obsahuje body připojení k nejrůznějším vývojářským nástrojům a integruje se se sadou Visual Studio, aby podporovala procesy DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery
[Azure monitor pro kontejnery](insights/container-insights-overview.md) monitoruje výkon úloh kontejnerů, které jsou nasazené na spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Poskytuje přehled o výkonu shromažďováním metrik z řadičů, uzlů a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik. Shromažďují se také protokoly kontejnerů.  Po povolení monitorování z clusterů Kubernetes se tyto metriky a protokoly automaticky shromažďují pomocí kontejnerové verze Log Analytics agenta pro Linux.

![Stav kontejneru](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor pro virtuální počítače
[Azure monitor pro virtuální počítače](insights/vminsights-overview.md) monitoruje vaše virtuální počítače Azure ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linux a identifikuje jejich různé procesy a propojené závislosti na externích procesech. Řešení zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače hostované místně nebo jiným poskytovatelem cloudu.  


![Přehledy virtuálních počítačů](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Reakce na kritické situace
Kromě toho, že vám umožní interaktivně analyzovat data monitorování, efektivní řešení monitorování musí být schopné aktivně reagovat na kritické podmínky zjištěné v datech, která shromažďuje. Může to odeslat text nebo e-mail správci zodpovědnému za prošetření problému. Nebo můžete spustit automatizovaný proces, který se pokusí opravit chybový stav.


### <a name="alerts"></a>Výstrahy
[Výstrahy v Azure monitor](platform/alerts-overview.md) proaktivně upozorňují na kritické podmínky a potenciálně se snaží provést nápravná opatření. Pravidla výstrah založená na metrikách poskytují výstrahy téměř v reálném čase na základě číselných hodnot. Pravidla založená na protokolech umožňují složitou logiku napříč daty z více zdrojů.

Pravidla výstrah v Azure Monitor používají [skupiny akcí](platform/action-groups.md), které obsahují jedinečné sady příjemců a akcí, které je možné sdílet v rámci více pravidel. V závislosti na vašich požadavcích můžou skupiny akcí provádět takové akce jako s použitím webhooků, aby výstrahy spouštěly externí akce nebo prováděly integraci s nástroji ITSM.

![Snímek obrazovky zobrazuje výstrahy v Azure Monitor se závažnostmi, celkovými výstrahami a dalšími informacemi.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatické škálování
Automatické škálování umožňuje mít ke zpracování zatížení vaší aplikace správné množství prostředků, na kterých běží. Vytvořte pravidla, která používají metriky shromažďované nástrojem Azure Monitor k určení, kdy se mají při zvýšení zátěže automaticky přidávat prostředky. Odstraněním prostředků, které jsou nečinné, ušetříte peníze. Zadáte minimální a maximální počet instancí a logiku, kdy chcete zvýšit nebo snížit prostředky.

![Diagram znázorňuje automatické škálování s několika servery na řádku s názvem procesor čas > 80% a dva servery s označením minimální, tři servery jako aktuální kapacitu a pět jako maximum.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Vizualizace dat monitorování
[Vizualizace](visualizations.md) , jako jsou grafy a tabulky, jsou efektivní nástroje pro shrnutí dat monitorování a jejich prezentaci různým cílovým skupinám. Azure Monitor má své vlastní funkce pro vizualizaci dat monitorování a využívá další služby Azure pro jejich publikování do různých cílových skupin.

### <a name="dashboards"></a>Řídicí panely
[Řídicí panely Azure](../azure-portal/azure-portal-dashboards.md) umožňují kombinovat různé druhy dat do jednoho podokna v [Azure Portal](https://portal.azure.com). Volitelně můžete řídicí panel sdílet s dalšími uživateli Azure. Do řídicího panelu Azure přidejte výstup všech dotazů protokolu nebo grafu metrik. Můžete například vytvořit řídicí panel, který kombinuje dlaždice, které zobrazují graf metrik, tabulku protokolů aktivit, graf využití z Application Insights a výstup dotazu protokolu.

![Snímek obrazovky ukazuje řídicí panel Azure, který zahrnuje dlaždice aplikace a zabezpečení spolu s dalšími přizpůsobitelnými informacemi.](media/overview/dashboard.png)

### <a name="workbooks"></a>Sešity
Pracovní [sešity](platform/workbooks-overview.md) poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v Azure Portal. Umožňují využívat více zdrojů dat v rámci Azure a kombinovat je do sjednocených interaktivních prostředí. Používejte sešity poskytované s přehledy nebo si vytvořte svoje vlastní z předdefinovaných šablon.


![Příklad sešitů](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) je služba obchodní analýzy, která poskytuje interaktivní vizualizace napříč různými zdroji dat. Je to efektivní způsob, jak data zpřístupnit ostatním v rámci vaší organizace i mimo ni. Power BI můžete nakonfigurovat tak, aby [automaticky importovala data protokolu z Azure monitor](./platform/powerbi.md) a využila tyto další vizualizace.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrace a export dat
Často budete mít nutnost integrovat Azure Monitor s jinými systémy a vytvářet vlastní řešení, která používají data monitorování. Další služby Azure spolupracují s Azure Monitor k zajištění této integrace.

### <a name="event-hub"></a>Centrum událostí
[Azure Event Hubs](../event-hubs/index.yml) je platforma pro přenos dat a služba pro přijímání událostí. Může transformovat a ukládat data pomocí zprostředkovatele analýz v reálném čase nebo adaptérů pro dávkování/ukládání. Pomocí Event Hubs můžete [streamovat Azure monitor data](platform/stream-monitoring-data-event-hubs.md) do partnerských nástrojů pro Siem a monitorování.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) je služba, která umožňuje automatizovat úlohy a obchodní procesy pomocí pracovních postupů, které se integrují s různými systémy a službami. K dispozici jsou aktivity, které čtou a zapisují metriky a protokoly v Azure Monitor. Díky tomu můžete vytvářet pracovní postupy, které jsou integrovány s různými systémy.


### <a name="api"></a>Rozhraní API
K dispozici je více rozhraní API pro čtení a zápis metrik a protokolů do a z Azure Monitor Kromě přístupu k vygenerovaným výstrahám. Můžete také nakonfigurovat a načíst výstrahy. Získáte tak v podstatě neomezené možnosti pro vytváření vlastních řešení, která se integrují s Azure Monitor.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace:

* [Metriky a protokoly](platform/data-platform.md) pro data shromažďovaná nástrojem Azure monitor.
* [Zdroje dat](platform/data-sources.md) pro způsob, jakým jednotlivé komponenty vaší aplikace odesílají telemetrii.
* [Dotazy protokolu](log-query/log-query-overview.md) pro analýzu shromážděných dat
* [Osvědčené postupy](/azure/architecture/best-practices/monitoring) pro monitorování cloudových aplikací a služeb.
