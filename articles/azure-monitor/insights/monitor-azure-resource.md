---
title: Monitorování prostředků Azure pomocí Azure Monitor | Microsoft Docs
description: Popisuje, jak shromažďovat a analyzovat data monitorování z prostředků v Azure pomocí Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 8ba50e5b55e31c6542e81879068bf231918ce1b0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244886"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorování prostředků Azure pomocí Azure Monitor
Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná prostředky Azure a o tom, jak můžete pomocí funkcí Azure Monitor analyzovat tato data a upozorňovat na ně.

> [!IMPORTANT]
> Tento článek se týká všech služeb v Azure, které používají Azure Monitor. Výpočetní prostředky, včetně virtuálních počítačů a App Service, generují stejná data monitorování, ale také obsahují hostovaný operační systém, který může také generovat protokoly a metriky. Podrobnosti o tom, jak tato data shromažďovat a analyzovat, najdete v dokumentaci monitorování pro tyto služby.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Monitor je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích. [Datová platforma Azure monitor](../platform/data-platform.md) shromažďuje data do [protokolů](../platform/data-platform-logs.md) a [metrik](../platform/data-platform-metrics.md) , kde je lze analyzovat společně pomocí kompletní sady monitorovacích nástrojů, jak je popsáno v následujících částech.

- [Co se dá dělat s Azure Monitor metrikami?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Co se dá dělat s protokoly Azure Monitor?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Jakmile vytvoříte prostředek Azure, Azure Monitor je povolený a začne shromažďovat metriky a protokoly aktivit, které můžete [Zobrazit a analyzovat v Azure Portal](#monitoring-in-the-azure-portal). V případě některých konfigurací můžete shromáždit další data monitorování a povolit další funkce. Podrobnosti o všech požadavcích na konfiguraci najdete níže v části [monitorování dat](#monitoring-data) .


## <a name="costs-associated-with-monitoring"></a>Náklady spojené s monitorováním
Pro analýzu dat monitorování, která se shromažďují ve výchozím nastavení, se neúčtují žádné náklady. To zahrnuje následující:

- Shromažďování metrik platforem a jejich analýza pomocí Průzkumníka metrik.
- Shromažďování protokolů aktivit a jejich analýza v Azure Portal.
- Vytváří se pravidlo upozornění protokolu aktivit.

Pro shromažďování a exportování protokolů a metrik nejsou k dispozici žádné Azure Monitor náklady, ale k cíli můžou být přidružené i související náklady:

- Náklady spojené s přijímáním a uchováváním dat při shromažďování protokolů a metrik v pracovním prostoru Log Analytics. Viz [ceny Azure Monitor Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Náklady spojené s úložištěm dat při shromažďování protokolů a metrik do účtu služby Azure Storage. Další informace najdete v tématu [Azure Storage ceny pro úložiště objektů BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Náklady spojené s datovým centrem centra událostí při předávání protokolů a metrik do Azure Event Hubs. Viz [ceny za Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

K následujícím akcím mohou být spojeny Azure Monitor náklady. Viz [ceny Azure monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Spuštění dotazu protokolu.
- Vytváří se pravidlo upozornění na metriku nebo dotaz na protokol.
- Odesílání oznámení z pravidla výstrahy.
- Přístup k metrikám prostřednictvím rozhraní API.

## <a name="monitoring-data"></a>Data monitorování
Prostředky v Azure generují [protokoly](../platform/data-platform-logs.md) a [metriky](../platform/data-platform-metrics.md) zobrazené v následujícím diagramu. Konkrétní data, která generují, a veškerá další řešení nebo přehledy, které poskytují, najdete v dokumentaci ke každé službě Azure.

![Přehled](media/monitor-azure-resource/logs-metrics.png)



- [Metriky platforem](../platform/data-platform-metrics.md) – číselné hodnoty, které se automaticky shromažďují v pravidelných intervalech a popisují určitý aspekt prostředku v určitou dobu. 
- [Protokoly prostředků](../platform/resource-logs-overview.md) – poskytují přehled o operacích, které byly provedeny v rámci prostředku Azure (rovina dat), například získání tajného klíče z Key Vault nebo vytvoření žádosti do databáze. Obsah a struktura protokolů prostředků se liší podle typu prostředku a služby Azure.
- [Protokol aktivit](../platform/activity-logs-overview.md) – poskytuje přehled o operacích u jednotlivých prostředků Azure v rámci předplatného zvenčí (rovina správy), například vytvoření nového prostředku nebo spuštění virtuálního počítače. Jedná se o informace o tom, kdo a kdy se u prostředků ve vašem předplatném provádí operace zápisu (PUT, POST, DELETE).


## <a name="configuration-requirements"></a>Požadavky na konfiguraci
Některá data monitorování se shromažďují automaticky, ale možná budete muset provést nějakou konfiguraci v závislosti na vašich požadavcích. V níže uvedených informacích najdete konkrétní informace pro každý typ dat monitorování.

- [Metriky platforem](../platform/data-platform-metrics.md) – metriky platformy se shromažďují automaticky do [Azure monitor metrik](../platform/data-platform-metrics.md) bez nutnosti konfigurace. Vytvořte nastavení diagnostiky, které odešle položky do protokolů Azure Monitor nebo je přesměruje mimo Azure.
- [Protokoly prostředků](../platform/resource-logs-overview.md) – protokoly prostředků se automaticky generují pomocí prostředků Azure, ale neshromažďují se bez nastavení diagnostiky.  Vytvořte nastavení diagnostiky, které odešle položky do protokolů Azure Monitor nebo je přesměruje mimo Azure.
- [Protokol aktivit](../platform/activity-logs-overview.md) – protokol aktivit je shromažďován automaticky bez nutnosti konfigurace a může být zobrazen v Azure Portal. Vytvořte nastavení diagnostiky, které se zkopíruje do protokolů Azure Monitor nebo je předají mimo Azure.


## <a name="diagnostic-settings"></a>Nastavení diagnostiky
Nastavení diagnostiky definují, kam se mají odesílat protokoly prostředků a metriky pro konkrétní prostředek. Možné cíle:

- [Log Analytics pracovní prostor](../platform/resource-logs-collect-workspace.md) , který umožňuje analyzovat data s dalšími daty monitorování shromážděnými pomocí Azure monitor pomocí výkonných dotazů protokolu a také využívat jiné Azure monitor funkce, jako jsou výstrahy protokolu a vizualizace. 
- [Centra událostí](../platform/resource-logs-stream-event-hubs.md) pro streamování dat do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics. 
- [Účet služby Azure Storage](../platform/resource-logs-collect-storage.md) , který je vhodný pro audit, statickou analýzu nebo zálohování.

Pomocí postupu v části [Vytvoření nastavení diagnostiky Shromážděte protokoly a metriky platforem v Azure](../platform/diagnostic-settings.md) , abyste mohli vytvořit a spravovat nastavení diagnostiky prostřednictvím Azure Portal. Informace najdete v tématu [Vytvoření nastavení diagnostiky v Azure pomocí šablony Správce prostředků](../platform/diagnostic-settings-template.md) k jejich definování v šabloně a povolení kompletního monitorování prostředku při jeho vytvoření.


## <a name="monitoring-in-the-azure-portal"></a>Monitorování v Azure Portal
 Data monitorování pro většinu prostředků Azure můžete získat z nabídky prostředku v Azure Portal. Díky tomu budete mít přístup k datům jednoho prostředku pomocí standardních Azure Monitorch nástrojů. Některé služby Azure budou poskytovat různé možnosti, takže byste měli v dokumentaci k příslušné službě odkazovat na Další informace. Pomocí nabídky **Azure monitor** můžete analyzovat data ze všech monitorovaných prostředků. 

Mnoho služeb bude obsahovat data monitorování na stránce s **přehledem** jako rychlý přehled jejich provozu. Tato akce bude typicky založena na podmnožině metrik platforem uložených v Azure Monitorch metrik. Další možnosti monitorování budou obvykle k dispozici v části **monitorování** služeb. nabídce.

![Stránka Přehled](media/monitor-azure-resource/overview-page.png)

## <a name="analyzing-metrics"></a>Analýza metrik
Analyzujte jednotlivé metriky a korelujte několik metrik a Identifikujte korelace a trendy pomocí [Průzkumníka metrik](../platform/metrics-getting-started.md). Některé služby poskytují přizpůsobené prostředí pro práci s metrikami při otevření **metrik** z nabídky prostředků.

- Základy používání Průzkumníka metrik najdete v tématu [Začínáme s Azure Průzkumník metrik](../platform/metrics-getting-started.md) .
- V tématu [Pokročilé funkce služby Azure Průzkumník metrik](../platform/metrics-charts.md) najdete pokročilé funkce Průzkumníka metrik, jako je například použití několika metrik a použití filtrů a rozdělení.

![Metriky](media/monitor-azure-resource/metrics.png)


## <a name="analyzing-logs"></a>Analýza protokolů

### <a name="activity-log"></a>Protokol aktivit 
Umožňuje zobrazit položky v protokolu aktivit v Azure Portal pomocí počátečního filtru nastaveného na aktuální prostředek. Zkopírujte protokol aktivit do pracovního prostoru Log Analytics, abyste k němu měli přístup, abyste ho mohli použít v protokolových dotazech a sešitech. 

- Podrobnosti o zobrazení protokolu aktivit a načítání položek pomocí různých metod najdete v tématu [zobrazení a načtení událostí protokolu aktivit Azure](../platform/activity-log-view.md) .
- Konkrétní události, které se protokolují, najdete v dokumentaci ke službě Azure.

![Protokol aktivit](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Protokoly Azure Monitor konsolidují protokoly a metriky z několika služeb a dalších zdrojů dat pro účely analýzy pomocí výkonného dotazovacího nástroje. Jak je popsáno výše, vytvořte nastavení diagnostiky pro shromažďování metrik platforem, protokolu aktivit a protokolů prostředků do pracovního prostoru Log Analytics v Azure Monitor.

[Log Analytics](../log-query/get-started-portal.md) vám umožní pracovat s [dotazy protokolu](../log-query/log-query-overview.md), což je výkonná funkce Azure monitor, která umožňuje provádět pokročilou analýzu dat protokolu pomocí plně funkčního dotazovacího jazyka. Otevřete Log Analytics z **protokolů** v nabídce **monitorování** pro prostředek Azure pro práci s dotazy protokolu pomocí prostředku jako [oboru dotazu](../log-query/scope.md#query-scope). To vám umožní analyzovat data napříč několika tabulkami pouze pro daný prostředek. K získání přístupu k protokolům pro všechny prostředky použijte **protokoly** z nabídky Azure monitor. 

- Návod k použití Log Analytics k zápisu dotazu a práci s výsledky najdete [v tématu Začínáme s Log Analytics v Azure monitor](../log-query/get-started-portal.md) .
- Kurz týkající se použití dotazovacího jazyka použitého k zápisu dotazů protokolu najdete v tématu Začínáme [s dotazy protokolu v Azure monitor](../log-query/get-started-queries.md) .
- Informace o tom, jak se protokoly o prostředcích shromažďují v Azure Monitor protokolech a podrobnosti o tom, jak k nim přistupovat v dotazu, najdete v tématu [shromáždění protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure monitor](../platform/resource-logs-collect-workspace.md) .
- Vysvětlení způsobu strukturování dat protokolu prostředků v Azure Monitorch protokolech najdete v části [režim shromažďování](../platform/resource-logs-collect-workspace.md#collection-mode) .
- Podrobnosti o své tabulce v protokolu Azure Monitor najdete v dokumentaci ke každé službě Azure.

![Protokoly](media/monitor-azure-resource/logs.png)


## <a name="alerts"></a>Výstrahy
[Výstrahy](../platform/alerts-overview.md) proaktivně informují a můžou provádět akce, když se ve vašich datech monitorování zjistí důležité podmínky. Vytvoříte pravidlo upozornění, které definuje cíl výstrahy, podmínky, jestli se má vytvořit výstraha, a všechny akce, které se mají provést v reakci.

Různé druhy dat monitorování se používají pro různé druhy pravidel upozornění.

- [Upozornění protokolu aktivit](../platform/alerts-activity-log.md) – vytvoří výstrahu, když se v protokolu aktivit vytvoří záznam, který odpovídá konkrétním kritériím. To vám umožňuje upozornit například při vytvoření konkrétního typu prostředku nebo v případě, že se změna konfigurace nezdařila.
- [Výstraha metriky](../platform/alerts-metric.md) – vytvoří výstrahu, když hodnota metriky překročí určitou prahovou hodnotu. Výstrahy metriky jsou větší než jiné výstrahy a je možné je automaticky vyřešit při opravě problému.
- [Výstraha na dotaz na protokol](../platform/alerts-log.md) – spustí dotaz protokolu v pravidelných intervalech a vytvoří výstrahu, pokud se najde konkrétní podmínka. To umožňuje provádět složitou analýzu napříč různými sadami dat a.

Pomocí **Upozornění** z nabídky prostředku můžete zobrazit výstrahy a spravovat pravidla výstrah pro daný prostředek. Jenom výstrahy protokolu aktivit a výstrahy metriky používají jako cíl jednotlivé prostředky Azure. Výstrahy dotazování protokolu používají jako cíl Log Analytics pracovní prostor a jsou založené na dotazu, který má přístup k jakýmkoli protokolům uloženým v daném pracovním prostoru. Pomocí nabídky Azure Monitor můžete zobrazit a spravovat výstrahy pro všechny prostředky a pravidla pro výstrahy pro dotaz na správu protokolů.

- Podrobnosti o vytváření pravidel výstrah najdete v článcích o různých typech výstrah výše.
- Podrobnosti o vytvoření skupiny akcí, která vám umožní spravovat odpovědi na výstrahy, najdete v tématu [Vytvoření a Správa skupin akcí v Azure Portal](../platform/action-groups.md) .

## <a name="insights-and-solutions"></a>Přehledy a řešení 
Některé služby budou poskytovat nástroje nad rámec standardních funkcí Azure Monitor. [Řešení](../insights/solutions.md) poskytují předdefinované logiky monitorování, které jsou postavené na standardních funkcích Azure monitor. [Přehledy](../insights/insights-overview.md) poskytují přizpůsobené prostředí pro monitorování založené na Azure monitor datovou platformu a standardní funkce.

Pokud je v prostředku dostupný přehled, můžete k němu **přistupovat z přehledu** v nabídce prostředků. Přístup ke všem přehledům a řešením z nabídky Azure Monitor.

- V dokumentaci monitorování pro každou službu zjistíte, jestli jsou dostupné nějaké poznatky nebo řešení.

![Poznatky](media/monitor-azure-resource/insights.png)

## <a name="next-steps"></a>Další kroky

* Podrobnosti o protokolech prostředků pro různé služby Azure najdete v tématu [podporované služby, schémata a kategorie pro diagnostické protokoly Azure](../platform/diagnostic-logs-schema.md) .  
