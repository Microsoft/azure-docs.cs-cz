---
title: Monitorování prostředků Azure pomocí Azure Monitor | Microsoft Docs
description: Popisuje, jak shromažďovat a analyzovat data monitorování z prostředků v Azure pomocí Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: e3a8e8b908c9d278654bfe992474811d6a9ddb86
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993708"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorování prostředků Azure s využitím služby Azure Monitor
Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná prostředky Azure a o tom, jak můžete pomocí funkcí Azure Monitor analyzovat tato data a upozorňovat na ně.

> [!IMPORTANT]
> Tento článek se týká všech služeb v Azure, které používají Azure Monitor. Výpočetní prostředky, včetně virtuálních počítačů a App Service, generují stejná data monitorování, ale také obsahují hostovaný operační systém, který může také generovat protokoly a metriky. Podrobnosti o tom, jak tato data shromažďovat a analyzovat, najdete v dokumentaci monitorování pro tyto služby.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Monitor je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích. [Datová platforma Azure monitor](../platform/data-platform.md) shromažďuje data do [protokolů](../platform/data-platform-logs.md) a [metrik](../platform/data-platform-metrics.md) , kde je lze analyzovat společně pomocí kompletní sady nástrojů pro monitorování. Seznamte se s úplnými aplikacemi a službami, které je možné monitorovat pomocí Azure Monitor v tématu [co je monitorované pomocí Azure monitor?](../monitor-reference.md).

Jakmile vytvoříte prostředek Azure, Azure Monitor je povolený a začne shromažďovat metriky a protokoly aktivit, které můžete [Zobrazit a analyzovat v Azure Portal](#monitoring-in-the-azure-portal). V případě některých konfigurací můžete shromáždit další data monitorování a povolit další funkce. Podrobnosti o všech požadavcích na konfiguraci najdete níže v části [monitorování dat](#monitoring-data) .


## <a name="costs-associated-with-monitoring"></a>Náklady spojené s monitorováním
Pro analýzu dat monitorování, která se shromažďují ve výchozím nastavení, se neúčtují žádné náklady. Ta zahrnují následující:

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
Prostředky v Azure generují [protokoly](../platform/data-platform-logs.md) a [metriky](../platform/data-platform-metrics.md) , které jsou uvedené v následujícím diagramu. Konkrétní data, která generují, a veškerá další řešení nebo přehledy, které poskytují, najdete v dokumentaci ke každé službě Azure.

![Přehled](media/monitor-azure-resource/logs-metrics.png)



- [Metriky platforem](../platform/data-platform-metrics.md) – číselné hodnoty, které se automaticky shromažďují v pravidelných intervalech a popisují určitý aspekt prostředku v určitou dobu. 
- [Protokoly prostředků](../platform/platform-logs-overview.md) – poskytují přehled o operacích, které byly provedeny v rámci prostředku Azure (rovina dat), například získání tajného klíče z Key Vault nebo vytvoření žádosti do databáze. Obsah a struktura protokolů prostředků se liší podle typu prostředku a služby Azure.
- [Protokol aktivit](../platform/platform-logs-overview.md) – poskytuje přehled o operacích u jednotlivých prostředků Azure v rámci předplatného zvenčí (rovina správy), například vytvoření nového prostředku nebo spuštění virtuálního počítače. Jedná se o informace o tom, kdo a kdy se u prostředků ve vašem předplatném provádí operace zápisu (PUT, POST, DELETE).


## <a name="configuration-requirements"></a>Požadavky na konfiguraci

### <a name="configure-monitoring"></a>Konfigurace sledování
Některá data monitorování se shromažďují automaticky, ale možná budete muset provést nějakou konfiguraci v závislosti na vašich požadavcích. V níže uvedených informacích najdete konkrétní informace pro každý typ dat monitorování.

- [Metriky platforem](../platform/data-platform-metrics.md) – metriky platformy se shromažďují automaticky do [Azure monitor metrik](../platform/data-platform-metrics.md) bez nutnosti konfigurace. Vytvořte nastavení diagnostiky, které odešle položky do protokolů Azure Monitor nebo je přesměruje mimo Azure.
- [Protokoly prostředků](../platform/platform-logs-overview.md) – protokoly prostředků se automaticky generují pomocí prostředků Azure, ale neshromažďují se bez nastavení diagnostiky.  Vytvořte nastavení diagnostiky, které odešle položky do protokolů Azure Monitor nebo je přesměruje mimo Azure.
- [Protokol aktivit](../platform/platform-logs-overview.md) – protokol aktivit je shromažďován automaticky bez nutnosti konfigurace a může být zobrazen v Azure Portal. Vytvořte nastavení diagnostiky, které se zkopíruje do protokolů Azure Monitor nebo je předají mimo Azure.

### <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Shromažďování dat do protokolů Azure Monitor vyžaduje pracovní prostor Log Analytics. Můžete rychle začít monitorovat službu tím, že vytvoříte nový pracovní prostor, ale v pracovním prostoru, který shromažďuje data z jiných služeb, může dojít k hodnotě. V tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../learn/quick-create-workspace.md) najdete podrobné informace o vytváření pracovního prostoru a [návrhu nasazení Azure Monitorch protokolů](../platform/design-logs-deployment.md) , které vám pomůžou určit nejlepší návrh pracovního prostoru pro vaše požadavky. Pokud používáte existující pracovní prostor ve vaší organizaci, budete potřebovat příslušná oprávnění, jak je popsáno v tématu [Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Nastavení diagnostiky
Nastavení diagnostiky definují, kam se mají odesílat protokoly prostředků a metriky pro konkrétní prostředek. Možné cíle:

- [Log Analytics pracovní prostor](../platform/resource-logs.md#send-to-log-analytics-workspace) , který umožňuje analyzovat data s dalšími daty monitorování shromážděnými pomocí Azure monitor pomocí výkonných dotazů protokolu a také využívat jiné Azure monitor funkce, jako jsou výstrahy protokolu a vizualizace. 
- [Centra událostí](../platform/resource-logs.md#send-to-azure-event-hubs) pro streamování dat do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics. 
- [Účet služby Azure Storage](../platform/resource-logs.md#send-to-azure-storage) , který je vhodný pro audit, statickou analýzu nebo zálohování.

Pomocí postupu v části [Vytvoření nastavení diagnostiky Shromážděte protokoly a metriky platforem v Azure](../platform/diagnostic-settings.md) , abyste mohli vytvořit a spravovat nastavení diagnostiky prostřednictvím Azure Portal. V tématu [Vytvoření nastavení diagnostiky v Azure použijte šablonu správce prostředků](../samples/resource-manager-diagnostic-settings.md) k jejich definování v šabloně a povolení kompletního monitorování prostředku při jeho vytvoření.


## <a name="monitoring-in-the-azure-portal"></a>Monitorování v Azure Portal
 Data monitorování pro většinu prostředků Azure můžete získat z nabídky prostředku v Azure Portal. Díky tomu budete mít přístup k datům jednoho prostředku pomocí standardních Azure Monitorch nástrojů. Některé služby Azure budou poskytovat různé možnosti, takže byste měli v dokumentaci k příslušné službě odkazovat na Další informace. Pomocí nabídky **Azure monitor** můžete analyzovat data ze všech monitorovaných prostředků. 

### <a name="overview"></a>Přehled
Mnoho služeb bude obsahovat data monitorování na stránce s **přehledem** jako rychlý přehled jejich provozu. Tato akce bude typicky založena na podmnožině metrik platforem uložených v Azure Monitorch metrik. Další možnosti monitorování budou obvykle k dispozici v části **monitorování** nabídky služby.

![Stránka Přehled](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Přehledy a řešení 
Některé služby budou poskytovat nástroje nad rámec standardních funkcí Azure Monitor. [Přehledy](./insights-overview.md) poskytují přizpůsobené prostředí pro monitorování založené na Azure monitor datovou platformu a standardní funkce. [Řešení](./solutions.md) poskytují předdefinované logiky monitorování založené na protokolech Azure monitor. 

Pokud má služba Azure Monitor přehled, můžete k ní přistupovat z **monitorování** v nabídce každého prostředku. Přístup ke všem přehledům a řešením z nabídky **Azure monitor** .

![Přehledy v Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metriky
Analyzujte metriky v Azure Portal pomocí [Průzkumníka metrik](../platform/metrics-getting-started.md) , který je k dispozici z položky nabídky **metriky** pro většinu služeb. Tento nástroj umožňuje pracovat s jednotlivými metrikami nebo kombinovat více a identifikovat korelace a trendy. 

- Základy používání Průzkumníka metrik najdete v tématu [Začínáme s Azure Průzkumník metrik](../platform/metrics-getting-started.md) .
- V tématu [Pokročilé funkce služby Azure Průzkumník metrik](../platform/metrics-charts.md) najdete pokročilé funkce Průzkumníka metrik, jako je například použití několika metrik a použití filtrů a rozdělení.

![Průzkumník metrik v Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Protokol aktivit 
Umožňuje zobrazit položky v protokolu aktivit v Azure Portal pomocí počátečního filtru nastaveného na aktuální prostředek. Zkopírujte protokol aktivit do pracovního prostoru Log Analytics, abyste k němu měli přístup, abyste ho mohli použít v protokolových dotazech a sešitech. 

- Podrobnosti o zobrazení protokolu aktivit a načítání položek pomocí různých metod najdete v tématu [zobrazení a načtení událostí protokolu aktivit Azure](../platform/activity-log.md#view-the-activity-log) .
- Konkrétní události, které se protokolují, najdete v dokumentaci ke službě Azure.

![Protokol aktivit](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Protokoly Azure Monitor konsolidují protokoly a metriky z několika služeb a dalších zdrojů dat pro účely analýzy pomocí výkonného dotazovacího nástroje. Jak je popsáno výše, vytvořte nastavení diagnostiky pro shromažďování metrik platforem, protokolu aktivit a protokolů prostředků do pracovního prostoru Log Analytics v Azure Monitor.

[Log Analytics](../log-query/get-started-portal.md) vám umožní pracovat s [dotazy protokolu](../log-query/log-query-overview.md), což je výkonná funkce Azure monitor, která umožňuje provádět pokročilou analýzu dat protokolu pomocí plně funkčního dotazovacího jazyka. Otevřete Log Analytics z **protokolů** v nabídce **monitorování** pro prostředek Azure pro práci s dotazy protokolu pomocí prostředku jako [oboru dotazu](../log-query/scope.md#query-scope). To vám umožní analyzovat data napříč několika tabulkami pouze pro daný prostředek. K získání přístupu k protokolům pro všechny prostředky použijte **protokoly** z nabídky Azure monitor. 

- Kurz týkající se použití dotazovacího jazyka použitého k zápisu dotazů protokolu najdete v tématu Začínáme [s dotazy protokolu v Azure monitor](../log-query/get-started-queries.md) .
- Informace o tom, jak se protokoly o prostředcích shromažďují v Azure Monitor protokolech a podrobnosti o tom, jak k nim přistupovat v dotazu, najdete v tématu [shromáždění protokolů prostředků Azure v pracovním prostoru Log Analytics v Azure monitor](../platform/resource-logs.md#send-to-log-analytics-workspace) .
- Vysvětlení způsobu strukturování dat protokolu prostředků v Azure Monitorch protokolech najdete v části [režim shromažďování](../platform/resource-logs.md#send-to-log-analytics-workspace) .
- Podrobnosti o své tabulce v protokolu Azure Monitor najdete v dokumentaci ke každé službě Azure.

![Log Analytics v Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorování z příkazového řádku
Data monitorování shromážděná z vašeho prostředku můžete získat z příkazového řádku nebo je zahrnout do skriptu pomocí [Azure PowerShell](/powershell/azure/) nebo [rozhraní příkazového řádku Azure](/cli/azure/). 

- Informace o přístupu k datům metrik z CLI najdete v tématu Referenční informace k [metrikám CLI](/cli/azure/monitor/metrics) .
- V tématu Referenční informace o rozhraní příkazového [řádku Log Analytics](/cli/azure/ext/log-analytics/monitor/log-analytics) pro přístup k datům Azure monitor protokolů pomocí dotazu protokolu z CLI.
- V tématu Referenční informace o [metrikách](/powershell/module/azurerm.insights/get-azurermmetric) pro přístup k datům metriky z Azure PowerShell najdete Azure PowerShell.
- V tématu Referenční informace o [dotazech protokolu Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) pro přístup k datům Azure monitor protokolů pomocí dotazu protokolu z Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorování z REST API
Zahrňte data monitorování shromážděná z vašeho prostředku ve vlastní aplikaci pomocí REST API.

- Podrobnosti o přístupu k metrikám z REST API Azure Monitor najdete v tématu [návod pro monitorování Azure REST API](../platform/rest-api-walkthrough.md) .
- Informace o přístupu k datům Azure Monitor protokolů pomocí dotazu protokolu z Azure PowerShell najdete v tématu [Azure Log Analytics REST API](https://dev.loganalytics.io/) .

## <a name="alerts"></a>Výstrahy
[Výstrahy](../platform/alerts-overview.md) proaktivně informují a můžou provádět akce, když se ve vašich datech monitorování zjistí důležité podmínky. Vytvoříte pravidlo upozornění, které definuje cíl výstrahy, podmínky, jestli se má vytvořit výstraha, a všechny akce, které se mají provést v reakci.

Různé druhy dat monitorování se používají pro různé druhy pravidel upozornění.

- [Upozornění protokolu aktivit](../platform/alerts-activity-log.md) – vytvoří výstrahu, když se v protokolu aktivit vytvoří záznam, který odpovídá konkrétním kritériím. To vám umožňuje upozornit například při vytvoření konkrétního typu prostředku nebo v případě, že se změna konfigurace nezdařila.
- [Výstraha metriky](../platform/alerts-metric.md) – vytvoří výstrahu, když hodnota metriky překročí určitou prahovou hodnotu. Výstrahy metriky jsou větší než jiné výstrahy a je možné je automaticky vyřešit při opravě problému.
- [Výstraha na dotaz na protokol](../platform/alerts-log.md) – spustí dotaz protokolu v pravidelných intervalech a vytvoří výstrahu, pokud se najde konkrétní podmínka. To umožňuje provádět složitou analýzu napříč různými sadami dat a.

Pomocí **Upozornění** z nabídky prostředku můžete zobrazit výstrahy a spravovat pravidla výstrah pro daný prostředek. Jenom výstrahy protokolu aktivit a výstrahy metriky používají jako cíl jednotlivé prostředky Azure. Výstrahy dotazování protokolu používají jako cíl Log Analytics pracovní prostor a jsou založené na dotazu, který má přístup k jakýmkoli protokolům uloženým v daném pracovním prostoru. Pomocí nabídky Azure Monitor můžete zobrazit a spravovat výstrahy pro všechny prostředky a pravidla pro výstrahy pro dotaz na správu protokolů.

- Podrobnosti o vytváření pravidel výstrah najdete v článcích o různých typech výstrah výše.
- Podrobnosti o vytvoření skupiny akcí, která vám umožní spravovat odpovědi na výstrahy, najdete v tématu [Vytvoření a Správa skupin akcí v Azure Portal](../platform/action-groups.md) .



## <a name="next-steps"></a>Další kroky

* Podrobnosti o protokolech prostředků pro různé služby Azure najdete v tématu [podporované služby, schémata a kategorie pro protokoly prostředků Azure](../platform/resource-logs-schema.md) .