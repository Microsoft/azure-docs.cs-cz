---
title: Sledování operací a aktivit
titleSuffix: Azure Cognitive Search
description: Povolte protokolování, získejte metriky aktivity dotazů, využití prostředků a další systémová data ze služby Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462322"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorování operací a aktivitazure kognitivního vyhledávání

Tento článek zavádí monitorování na úrovni služby (prostředek) na úrovni pracovního vytížení (dotazy a indexování) a navrhuje rámec pro monitorování přístupu uživatelů.

Napříč spektrem použijete kombinaci integrované infrastruktury a základních služeb, jako je Azure Monitor, stejně jako řešení API služeb, která vracejí statistiky, počty a stav. Pochopení rozsahu možností vám může pomoci vytvořit zpětnou vazbu, takže můžete řešit problémy, jakmile se objeví.

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Mnoho služeb, včetně Azure Cognitive Search, využívá [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) pro výstrahy, metriky a protokolování diagnostických dat. Pro Azure Cognitive Search integrovaná infrastruktura monitorování se používá především pro monitorování na úrovni prostředků (stav služby) a [monitorování dotazů](search-monitor-queries.md).

Následující snímek obrazovky vám pomůže najít funkce Azure Monitoru na portálu.

+ **Karta Monitorování,** která se nachází na hlavní stránce přehledu, zobrazuje klíčové metriky na první pohled.
+ **Protokol aktivit**, který je kousek pod položkou Přehled, sestavuje akce na úrovni prostředků: oznámení o stavu služby a požadavcích na klíč rozhraní API.
+ **Monitorování**dále v seznamu poskytuje konfigurovatelné výstrahy, metriky a diagnostické protokoly. Vytvořte je, když je potřebujete. Po shromažďování a ukládání dat můžete vyhledávat nebo vizualizovat informace pro přehledy.

![Integrace Azure Monitoru ve vyhledávací službě](./media/search-monitor-usage/azure-monitor-search.png
 "Integrace Azure Monitoru ve vyhledávací službě")

### <a name="precision-of-reported-numbers"></a>Přesnost hlášených čísel

Portálové stránky se aktualizují každých několik minut. Jako takové jsou čísla uvedená na portálu přibližná a mají poskytnout obecný představu o tom, jak dobře váš systém obsluhuje požadavky. Skutečné metriky, jako jsou dotazy za sekundu (QPS), mohou být vyšší nebo nižší než číslo zobrazené na stránce.

## <a name="activity-logs-and-service-health"></a>Protokoly aktivit a stav služby

[**Protokol aktivit**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) shromažďuje informace ze Správce prostředků Azure a zprávy o změnách stavu služby. Můžete sledovat protokol aktivit pro kritické, chyby a varovné podmínky týkající se stavu služby.

U úkolů v provozu – například dotazů, indexování nebo vytváření objektů – se zobrazí obecná informační oznámení, jako je *získat klíč správce* a klíče získat *dotaz* pro každý požadavek, ale ne konkrétní akce sama. Pro informace o tomto zrnitosti je nutné nakonfigurovat protokolování diagnostiky.

K protokolu **aktivit** můžete přistupovat z levého navigačního podokna nebo z oznámení v horním panelu příkazů okna nebo ze stránky **Diagnostikovat a řešit problémy.**

## <a name="monitor-storage"></a>Monitorování úložiště

Stránky s kartami integrované do sestavy přehledu o využití prostředků. Tyto informace budou k dispozici, jakmile začnete službu používat, bez nutnosti konfigurace a stránka se aktualizuje každých několik minut. 

Pokud dokončujete rozhodnutí o [tom, kterou úroveň použít pro produkční úlohy](search-sku-tier.md), nebo zda [upravit počet aktivních replik a oddílů](search-capacity-planning.md), tyto metriky vám mohou pomoci s těmito rozhodnutími tím, že vám ukáží, jak rychle jsou prostředky spotřebovány a jak dobře aktuální konfigurace zpracovává existující zatížení.

Výstrahy související s úložištěm nejsou v současné době k dispozici. využití úložiště není agregované nebo přihlášen do tabulky **AzureMetrics** v Azure Monitoru. Budete muset [vytvořit vlastní řešení,](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) které vydává oznámení související s prostředky, kde váš kód kontroluje velikost úložiště a zpracovává odpověď. Další informace o metrikách úložiště najdete v [tématu Získání statistiky služeb](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Pro vizuální monitorování na portálu karta **Využití** zobrazuje dostupnost zdrojů vzhledem k [aktuálním limitům](search-limits-quotas-capacity.md) vynuceným úrovní služby. 

Následující obrázek je pro bezplatnou službu, která je omezena na 3 objekty každého typu a 50 MB úložiště. Základní nebo standardní služba má vyšší limity a pokud zvýšíte počet oddílů, maximální úložiště se zvýší proporcionálně.

![Stav využití vzhledem k limitům úrovně](./media/search-monitor-usage/usage-tab.png
 "Stav využití vzhledem k limitům úrovně")

## <a name="monitor-workloads"></a>Sledování úloh

Protokolované události zahrnují ty, které souvisejí s indexováním a dotazy. Tabulka **AzureDiagnostics** v Log Analytics shromažďuje provozní data související s dotazy a indexováním.

Většina protokolovaných dat je určen pro operace jen pro čtení. U jiných operací create-update-delete, které nejsou zachyceny v protokolu, můžete vyhledávací službě vyhledat systémové informace.

| OperationName | Popis |
|---------------|-------------|
| Statistiky služeb | Tato operace je rutinní volání [získat statistiku služby](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), volání přímo nebo implicitně naplnit stránku přehled u portálu při načtení nebo aktualizaci. |
| Dotaz.Hledat |  Požadavky na dotazy proti indexu, viz [Sledování dotazů](search-monitor-queries.md) na informace o protokolovaných dotazech.|
| Indexování.Index  | Tato operace je volání [přidat, aktualizovat nebo odstranit dokumenty](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| Indexy. Prototyp | Toto je index vytvořený průvodcem importem dat. |
| Indexery.Vytvořit | Vytvořte indexer explicitně nebo implicitně pomocí Průvodce importem dat. |
| Indexery.Získejte | Vrátí název indexeru při každém spuštění indexeru. |
| Indexery.Stav | Vrátí stav indexeru při každém spuštění indexeru. |
| Zdroje dat.Získat | Vrátí název zdroje dat při každém spuštění indexeru.|
| Indexy.Získat | Vrátí název indexu při každém spuštění indexeru. |

### <a name="kusto-queries-about-workloads"></a>Kusto dotazy týkající se úloh

Pokud jste povolili protokolování, můžete dotaz **AzureDiagnostics** pro seznam operací, které běžely na vaší službě a kdy. Můžete také korelovat aktivitu a prozkoumat změny výkonu.

#### <a name="example-list-operations"></a>Příklad: Seznam operací 

Vrátí seznam operací a počet každé z nich.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Příklad: Korelační operace

Korelujte požadavek na dotaz s operacemi indexování a vykreslujte datové body v časovém grafu, aby se operace shodovaly.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Použití vyhledávacích api

Rozhraní REST azure cognitive search a sada .NET SDK poskytují programový přístup k metrikám služeb, informacím o indexu a indexeru a počtu dokumentů.

+ [Statistika služby GET](/rest/api/searchservice/get-service-statistics)
+ [Statistika indexu GET](/rest/api/searchservice/get-index-statistics)
+ [GET Počet dokumentů](/rest/api/searchservice/count-documents)
+ [Stav indexeru GET](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Sledování přístupu uživatelů

Vzhledem k tomu, že indexy vyhledávání jsou součástí větší klientské aplikace, neexistuje žádná integrovaná metodika pro řízení nebo monitorování přístupu uživatele k indexu. Předpokládá se, že požadavky pocházejí z klientské aplikace pro požadavky správce nebo dotazu. Operace pro čtení a zápis správce zahrnují vytváření, aktualizaci a odstraňování objektů v celé službě. Operace jen pro čtení jsou dotazy proti kolekci dokumentů, s vymezeným oborem na jeden index. 

V důsledku toho se v protokolech aktivit zobrazí odkazy na volání pomocí klíčů správce nebo klíčů dotazů. Příslušný klíč je součástí požadavků pocházejících z kódu klienta. Služba není vybavena pro zpracování tokenů identity nebo zosobnění.

Pokud existují obchodní požadavky pro autorizaci pro jednotlivé uživatele, doporučení je integrace s Azure Active Directory. Pomocí $filter a identit uživatelů můžete [oříznout výsledky hledání](search-security-trimming-for-azure-search-with-aad.md) dokumentů, které by uživatel neměl vidět. 

Neexistuje žádný způsob, jak protokolovat tyto informace odděleně od řetězce dotazu, který obsahuje parametr $filter. Podrobnosti o řetězcích dotazů vykazování dotazů najdete v tématu [Sledování dotazů.](search-monitor-queries.md)

## <a name="next-steps"></a>Další kroky

Plynulost s Azure Monitorem je nezbytná pro dohled nad jakoukoli službou Azure, včetně prostředků, jako je Azure Cognitive Search. Pokud nejste obeznámeni s Azure Monitor, udělejte si čas na kontrolu článků týkajících se prostředků. Kromě výukových programů je v následujícím článku vhodné začít.

> [!div class="nextstepaction"]
> [Monitorování prostředků Azure pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
