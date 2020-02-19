---
title: Monitorování operací a aktivity
titleSuffix: Azure Cognitive Search
description: Povolte protokolování, Získejte metriky aktivity dotazů, využití prostředků a další systémová data ze služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 5846e9516548032595c1ce072d1dae8dcce9d39e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443597"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorování operací a aktivit Azure Kognitivní hledání

Tento článek zavádí monitorování na úrovni služby (prostředku) na úrovni pracovního vytížení (dotazy a indexování) a navrhuje rozhraní pro monitorování přístupu uživatelů.

V rámci spektra použijete kombinaci integrované infrastruktury a základních služeb, jako je Azure Monitor, a také rozhraní API služby, která vrací statistiky, počty a stav. Porozumění rozsahu funkcí vám může přispět k vytvoření smyčky zpětné vazby, abyste mohli řešit problémy, jak se budou objevovat.

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Mnoho služeb, včetně Azure Kognitivní hledání, využívá [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) pro výstrahy, metriky a protokolování diagnostických dat. V případě Azure Kognitivní hledání se integrovaná infrastruktura pro monitorování používá hlavně pro monitorování na úrovni prostředků (stav služby) a [monitorování dotazů](search-monitor-queries.md).

Následující snímek obrazovky vám pomůže najít Azure Monitor funkce na portálu.

+ Karta **monitorování** , která se nachází na hlavní stránce Přehled, ukazuje na první pohled klíčové metriky.
+ **Protokol aktivit**, hned pod přehledem, sestavy o akcích na úrovni prostředku: stav služby a oznámení o požadavcích na klíč rozhraní API.
+ **Monitorování**, dále v seznamu, poskytuje konfigurovatelné výstrahy, metriky a diagnostické protokoly. Můžete je vytvořit, až je budete potřebovat. Po shromáždění a uložení dat můžete zadávat dotazy nebo vizualizovat informace pro přehledy.

![Azure Monitor integrace ve službě vyhledávání](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrace ve službě vyhledávání")

### <a name="precision-of-reported-numbers"></a>Přesnost hlášených čísel

Stránky portálu se aktualizují každých několik minut. V takovém případě mají hodnoty hlášené na portálu přibližnou hodnotu, která vám poskytne obecnou představu o tom, jak dobře systém obsluhuje požadavky. Skutečné metriky, jako například dotazy za sekundu (QPS), mohou být vyšší nebo nižší než číslo zobrazené na stránce.

## <a name="activity-logs-and-service-health"></a>Protokoly aktivit a stav služby

[**Protokol aktivit**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) shromažďuje informace z Azure Resource Manager a oznamuje změny stavu služby. Protokol aktivit můžete monitorovat pro kritické, chybové a varovné podmínky související se stavem služby.

Pro úlohy v rámci služby – jako jsou dotazy, indexování nebo vytváření objektů – uvidíte obecná informační oznámení, jako je *získání klíče správce* a *získání klíčů dotazů* pro každý požadavek, ale ne konkrétní akce samotné. Pro informace o tomto zrnitosti je nutné nakonfigurovat protokolování diagnostiky.

K **protokolu aktivit** můžete přistupovat z levého navigačního podokna nebo z oznámení na panelu příkazů v horním okně nebo na stránce **diagnostikovat a řešit problémy** .

## <a name="monitor-storage"></a>Monitorování úložiště

Stránky se záložkami, které jsou integrované na stránce Přehled, se odhlásí na základě využití prostředků. Tyto informace budou k dispozici hned po zahájení používání služby, bez nutnosti konfigurace a stránka se aktualizuje každých několik minut. 

Pokud dokončujete rozhodnutí o tom, [která úroveň se má použít pro produkční úlohy](search-sku-tier.md), nebo jestli se má [upravit počet aktivních replik a oddílů](search-capacity-planning.md), může vám tyto metriky s těmito rozhodnutími povýšit tím, že vám ukáže, jak rychle se spotřebovávají prostředky a jak dobře aktuální konfigurace zpracovává existující zatížení.

Výstrahy související s úložištěm nejsou aktuálně k dispozici. spotřeba úložiště není agregovaná ani přihlášená do tabulky **AzureMetrics** v Azure monitor. Museli byste vytvořit vlastní řešení, které vysílá oznámení související s prostředky, kde váš kód kontroluje velikost úložiště a zpracovává odpověď. Další informace o metrikách úložiště najdete v tématu [získání statistik služby](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

U vizuálního monitorování na portálu se na kartě **využití** zobrazuje dostupnost prostředků relativně k aktuálním [limitům](search-limits-quotas-capacity.md) , které jsou zavedené vrstvou služby. 

Následující obrázek je pro bezplatnou službu, která je omezené na 3 objekty každého typu a 50 MB úložiště. Základní nebo standardní služba má vyšší limity, a pokud nakročíte počty oddílů, maximální velikost úložiště se provedla poměrně.

![Stav použití vzhledem k omezením úrovně](./media/search-monitor-usage/usage-tab.png
 "Stav použití vzhledem k omezením úrovně")

## <a name="monitor-workloads"></a>Monitorování úloh

Protokolované události obsahují ty, které souvisejí s indexováním a dotazy. Tabulka **AzureDiagnostics** v Log Analytics shromažďuje provozní data týkající se dotazů a indexování.

Většina protokolovaných dat je určena pouze pro operace jen pro čtení. Pro jiné operace Create-Update-Delete nezaznamenané v protokolu můžete zadat dotaz na informace o systému v vyhledávací službě.

| OperationName | Popis |
|---------------|-------------|
| ServiceStats | Tato operace je rutinním voláním metody [Get Service STATISTICS](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), která je volána přímo nebo implicitně k naplnění stránky přehledu portálu při načtení nebo aktualizaci. |
| Dotaz. Search |  Dotazy na požadavky na index najdete v tématu [monitorování dotazů](search-monitor-queries.md) pro informace o protokolovaných dotazech.|
| Indexuje se. index  | Tato operace je voláním pro [Přidání, aktualizaci nebo odstranění dokumentů](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| indexy. Vzorový | Toto je index vytvořený Průvodcem importem dat. |
| Indexery. Create | Vytvořte indexer explicitně nebo implicitně prostřednictvím Průvodce importem dat. |
| Indexery. Get | Vrátí název indexeru pokaždé, když je indexer spuštěn. |
| Indexery. stav | Vrátí stav indexeru pokaždé, když je indexer spuštěn. |
| Zdroje dat. Get | Vrátí název zdroje dat pokaždé, když je spuštěn indexer.|
| Indexy. Get | Při každém spuštění indexeru vrátí název indexu. |

### <a name="kusto-queries-about-workloads"></a>Kusto dotazů na úlohy

Pokud jste povolili protokolování, můžete zadat dotaz na **AzureDiagnostics** , kde najdete seznam operací spuštěných ve vaší službě a kdy. Můžete také korelovat aktivitu a prozkoumat změny v výkonu.

#### <a name="example-list-operations"></a>Příklad: výpis operací 

Vrátí seznam operací a počet každého z nich.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Příklad: korelace operací

Porovnejte požadavky na dotazy s operacemi indexování a vykreslete datové body v grafu s časovým plánem, abyste viděli, že se operace shodují.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Použití rozhraní API pro vyhledávání

REST API Kognitivní hledání Azure i .NET SDK poskytují programový přístup k metrikám služeb, indexům a informacím indexeru a počtům dokumentů.

+ [ZÍSKAT statistiku služby](/rest/api/searchservice/get-service-statistics)
+ [ZÍSKAT statistiku indexu](/rest/api/searchservice/get-index-statistics)
+ [ZÍSKAT počty dokumentů](/rest/api/searchservice/count-documents)
+ [ZÍSKAT stav indexeru](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Sledovat přístup uživatelů

Vzhledem k tomu, že indexy vyhledávání jsou součástí větší klientské aplikace, neexistuje žádná předdefinovaná metodologie pro řízení a monitorování přístupu pro jednotlivé uživatele k indexu. Předpokládá se, že požadavky pocházejí z klientské aplikace, a to buď pro žádosti správce, nebo na dotazy. Operace čtení a zápisu pro správce zahrnují vytváření, aktualizaci a odstraňování objektů napříč celou službou. Operace jen pro čtení jsou dotazy na kolekci dokumentů vymezené na jeden index. 

Jak vidíte v protokolech aktivit, jsou odkazy na volání pomocí klíčů správce nebo klíčů dotazů. Příslušný klíč je součástí požadavků, které pocházejí z kódu klienta. Služba není vybavena pro zpracování tokenů identity nebo zosobnění.

Pokud obchodní požadavky existují pro autorizaci uživatelů, doporučení se integruje s Azure Active Directory. K [vystřihování výsledků hledání](search-security-trimming-for-azure-search-with-aad.md) dokumentů, které by uživatel neměl vidět, můžete použít $Filter a identity uživatelů. 

Neexistuje žádný způsob, jak tyto informace zaprotokolovat odděleně od řetězce dotazu, který obsahuje parametr $filter. Podrobnosti o řetězcích dotazů pro vytváření sestav najdete v tématu [monitorování dotazů](search-monitor-queries.md) .

## <a name="next-steps"></a>Další kroky

Fluency s Azure Monitor jsou nezbytné pro dohled nad jakoukoliv službou Azure, včetně prostředků, jako je Azure Kognitivní hledání. Pokud nejste obeznámeni s Azure Monitor, přečtěte si článek o prostředcích souvisejících s prostředky. Kromě kurzů je vhodným místem pro spuštění následující článek.

> [!div class="nextstepaction"]
> [Monitorování prostředků Azure pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
