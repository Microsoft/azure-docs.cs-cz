---
title: Monitorování operací a aktivity
titleSuffix: Azure Cognitive Search
description: Povolte protokolování, Získejte metriky aktivity dotazů, využití prostředků a další systémová data ze služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3f348a5005f28fdd1d02be706ca2742d77368288
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592365"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorování operací a aktivit Azure Kognitivní hledání

Tento článek představuje přehled konceptů a nástrojů monitorování pro Azure Kognitivní hledání. Pro monitorování holistický můžete použít kombinaci integrovaných funkcí a doplňkových služeb, jako je Azure Monitor.

Zcela můžete sledovat následující:

* Služba: stav/dostupnost a změny v konfiguraci služby.
* Storage: používá se a je k dispozici u každého typu obsahu relativního k kvótě povolené pro vrstvu služby.
* Aktivita dotazu: svazek, latence a omezené nebo vyřazené dotazy. Požadavky na protokolované dotazy vyžadují [Azure monitor](#add-azure-monitor).
* Aktivita indexování: vyžaduje [protokolování diagnostiky](#add-azure-monitor) s Azure monitor.

Vyhledávací služba nepodporuje ověřování vázané na uživatele, takže v protokolech nebudou nalezeny žádné informace o identitě.

## <a name="built-in-monitoring"></a>Integrované monitorování

Integrované monitorování označuje aktivity, které jsou protokolovány službou vyhledávání. S výjimkou diagnostiky není pro tuto úroveň monitorování nutná žádná konfigurace.

Azure Kognitivní hledání udržuje interní data v kumulovaných 30denní plán pro vytváření sestav o stavu služby a metrikách dotazů, které můžete najít na portálu nebo prostřednictvím těchto [rozhraní REST API](#monitoring-apis).

Následující snímek obrazovky vám pomůže najít informace o monitorování na portálu. Data budou k dispozici hned po zahájení používání služby. Stránky portálu se aktualizují každých několik minut.

* Karta **monitorování** na hlavní stránce Přehled zobrazuje objem dotazů, latenci a informace o tom, zda je služba pod tlakem.
* **Protokol aktivit** v levém navigačním podokně je připojený k Azure Resource Manager. Sestavy protokolu aktivit jsou založené na akcích, které provádí Správce prostředků: dostupnost služby a stav, změny kapacity (repliky a oddíly) a aktivity týkající se klíčů rozhraní API.
* Nastavení **monitorování** , dále mimo provoz, poskytuje konfigurovatelné výstrahy, metriky a diagnostické protokoly. Můžete je vytvořit, až je budete potřebovat. Po shromáždění a uložení dat můžete zadávat dotazy nebo vizualizovat informace pro přehledy.

![Azure Monitor integrace ve službě vyhledávání](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrace ve službě vyhledávání")

> [!NOTE]
> Vzhledem k tomu, že se stránky portálu aktualizují každých několik minut, jsou uvedená čísla přibližná, což vám poskytne obecnou představu o tom, jak dobře systém obsluhuje požadavky. Skutečné metriky, jako například dotazy za sekundu (QPS), mohou být vyšší nebo nižší než číslo zobrazené na stránce. Pokud přesnost je požadavek, zvažte použití rozhraní API.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>Rozhraní API užitečná pro monitorování

Pomocí následujících rozhraní API můžete načíst stejné informace, jaké jsou k dispozici na kartách monitorování a použití na portálu.

* [ZÍSKAT statistiku služby](/rest/api/searchservice/get-service-statistics)
* [ZÍSKAT statistiku indexu](/rest/api/searchservice/get-index-statistics)
* [ZÍSKAT počty dokumentů](/rest/api/searchservice/count-documents)
* [ZÍSKAT stav indexeru](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Protokoly aktivit a stav služby

Stránka [**Protokol aktivit**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) na portálu shromažďuje informace z Azure Resource Manager a sestavy o změnách stavu služby. Protokol aktivit můžete monitorovat pro kritické, chybové a varovné podmínky související se stavem služby.

Mezi běžné položky patří odkazy na klíče rozhraní API – obecná informační oznámení, jako je *získání klíče správce* a *získání klíčů dotazů*. Tyto aktivity označují požadavky, které byly provedeny pomocí klíče správce (vytváření nebo odstraňování objektů) nebo klíče dotazu, ale nezobrazují samotný požadavek. Pro informace o tomto zrnitosti je nutné nakonfigurovat protokolování diagnostiky.

K **protokolu aktivit** můžete přistupovat z levého navigačního podokna nebo z oznámení na panelu příkazů v horním okně nebo na stránce **diagnostikovat a řešit problémy** .

### <a name="monitor-storage-in-the-usage-tab"></a>Monitorování úložiště na kartě využití

U vizuálního monitorování na portálu se na kartě **využití** zobrazuje dostupnost prostředků relativně k aktuálním [limitům](search-limits-quotas-capacity.md) , které jsou zavedené vrstvou služby. Pokud dokončujete rozhodnutí o tom, [která úroveň se má použít pro produkční úlohy](search-sku-tier.md), nebo jestli se má [upravit počet aktivních replik a oddílů](search-capacity-planning.md), může vám tyto metriky s těmito rozhodnutími povýšit tím, že vám ukáže, jak rychle se spotřebovávají prostředky a jak dobře aktuální konfigurace zpracovává existující zatížení.

Následující obrázek je pro bezplatnou službu, která je omezené na 3 objekty každého typu a 50 MB úložiště. Základní nebo standardní služba má vyšší limity, a pokud nakročíte počty oddílů, maximální velikost úložiště se provedla poměrně.

![Stav použití vzhledem k omezením úrovně](./media/search-monitor-usage/usage-tab.png
 "Stav použití vzhledem k omezením úrovně")

> [!NOTE]
> Výstrahy související s úložištěm nejsou aktuálně k dispozici. spotřeba úložiště není agregovaná ani přihlášená do tabulky **AzureMetrics** v Azure monitor. Chcete-li získat výstrahy úložiště, budete muset [vytvořit vlastní řešení](../azure-monitor/insights/solutions.md) , které vysílá oznámení související s prostředky, kde váš kód kontroluje velikost úložiště a zpracovává odpověď.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Monitorování doplňku pomocí Azure Monitor

Řada služeb, včetně Azure Kognitivní hledání, se integruje s [Azure monitor](../azure-monitor/index.yml) pro další výstrahy, metriky a diagnostická data protokolování. 

[Povolte protokolování diagnostiky](search-monitor-logs.md) pro vyhledávací službu, pokud chcete řídit shromažďování a ukládání dat. Události zaznamenané v Azure Monitor jsou uloženy v tabulce **AzureDiagnostics** a tvoří provozní data související s dotazy a indexováním.

Azure Monitor poskytuje několik možností úložiště a vaše volba určí, jak můžete data spotřebovat:

* Pokud chcete [vizualizovat data protokolu](search-monitor-logs-powerbi.md) v sestavě Power BI, vyberte úložiště objektů BLOB v Azure.
* Vyberte možnost Log Analytics, pokud chcete prozkoumat data prostřednictvím dotazů Kusto.

Azure Monitor má svou vlastní fakturační strukturu a diagnostické protokoly, na které se odkazuje v této části, mají přidružené náklady. Další informace najdete v tématu [využití a odhadované náklady v Azure monitor](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Sledovat přístup uživatelů

Vzhledem k tomu, že indexy vyhledávání jsou součástí větší klientské aplikace, neexistuje žádná předdefinovaná metodologie pro řízení a monitorování přístupu pro jednotlivé uživatele k indexu. Předpokládá se, že požadavky pocházejí z klientské aplikace, a to buď pro žádosti správce, nebo na dotazy. Operace čtení a zápisu pro správce zahrnují vytváření, aktualizaci a odstraňování objektů napříč celou službou. Operace jen pro čtení jsou dotazy na kolekci dokumentů vymezené na jeden index. 

Jak vidíte v protokolech aktivit, jsou odkazy na volání pomocí klíčů správce nebo klíčů dotazů. Příslušný klíč je součástí požadavků, které pocházejí z kódu klienta. Služba není vybavena pro zpracování tokenů identity nebo zosobnění.

Pokud obchodní požadavky existují pro autorizaci uživatelů, doporučení se integruje s Azure Active Directory. K [vystřihování výsledků hledání](search-security-trimming-for-azure-search-with-aad.md) dokumentů, které by uživatel neměl vidět, můžete použít $Filter a identity uživatelů. 

Neexistuje žádný způsob, jak tyto informace zaprotokolovat odděleně od řetězce dotazu, který obsahuje parametr $filter. Podrobnosti o řetězcích dotazů pro vytváření sestav najdete v tématu [monitorování dotazů](search-monitor-queries.md) .

## <a name="next-steps"></a>Další kroky

Fluency s Azure Monitor jsou nezbytné pro dohled nad jakoukoliv službou Azure, včetně prostředků, jako je Azure Kognitivní hledání. Pokud nejste obeznámeni s Azure Monitor, přečtěte si článek o prostředcích souvisejících s prostředky. Kromě kurzů je vhodným místem pro spuštění následující článek.

> [!div class="nextstepaction"]
> [Monitorování prostředků Azure s využitím služby Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)