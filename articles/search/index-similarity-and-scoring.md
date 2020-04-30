---
title: Přehled podobnosti a bodování
titleSuffix: Azure Cognitive Search
description: Vysvětluje koncepty podobnosti a bodování a to, co vývojář může udělat pro přizpůsobení výsledků bodování.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254119"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Podobnost a bodování v Azure Kognitivní hledání

Bodování označuje výpočet skóre vyhledávání pro každou položku vrácenou ve výsledcích vyhledávání pro fulltextové vyhledávací dotazy. Skóre je indikátorem relevance položek v kontextu aktuální operace vyhledávání. Čím vyšší je skóre, tím je daná položka relevantnější. Ve výsledcích hledání jsou položky seřazeny od vysoké po nejnižší na základě skóre hledání vypočítaného pro každou položku. 

Ve výchozím nastavení se v odpovědi vrátí Top 50, ale můžete použít parametr **$Top** k vrácení menšího nebo většího počtu položek (až 1000 v jedné odpovědi) a **$Skip** k získání další sady výsledků.

Skóre hledání je vypočítáno na základě statistických vlastností dat a dotazu. Azure Kognitivní hledání vyhledá dokumenty, které se shodují na hledaných termínech (v závislosti na [searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)), a upřednostňuje dokumenty, které obsahují mnoho instancí hledaného termínu. Skóre hledání bude ještě vyšší, pokud je v indexu dat zřídka, ale v dokumentu běžné. Základem pro tento přístup k výpočetním významům je známý jako *TF-IDF nebo* četnost termínů – inverzní frekvence dokumentů.

Hodnoty skóre hledání je možné opakovat v rámci sady výsledků dotazu. Pokud má více přístupů stejné skóre hledání, řazení stejných položek skóre není definováno a není stabilní. Spusťte dotaz znovu a můžete se podívat na pozici posunu položek, zejména pokud používáte bezplatnou službu nebo fakturovatelnou službu s více replikami. Vzhledem k tomu, že se dvě položky shodují se stejným skóre, neexistuje žádná záruka, která se zobrazí jako první.

Pokud chcete přerušit vazbu mezi opakujícími se výsledky, můžete přidat klauzuli **$OrderBy** do prvního pořadí podle skóre a pak seřadit podle jiného pole, které lze seřadit (například `$orderby=search.score() desc,Rating desc`). Další informace najdete v tématu [$OrderBy](https://docs.microsoft.com/azure/search/search-query-odata-orderby).

> [!NOTE]
> Symbol `@search.score = 1.00` označuje sadu výsledků bez hodnocení nebo Neseřazený výsledek. Skóre je rovnoměrné napříč všemi výsledky. Pokud je formulář dotazu přibližné vyhledávání, zástupné dotazy nebo výrazy regulárního výrazu nebo výraz **$Filter** , dojde k neskóre výsledků. 

## <a name="scoring-profiles"></a>Profily skórování

Můžete přizpůsobit způsob řazení různých polí definováním vlastního *profilu vyhodnocování*. Profily vyhodnocování poskytují větší kontrolu nad hodnocením položek ve výsledcích vyhledávání. Například můžete chtít zvýšit množství položek na základě jejich potenciálních výnosů, zvýšit úroveň nových položek nebo možná zvýšit množství položek, které byly v inventáři příliš dlouho. 

Profil vyhodnocování je součástí definice indexu, která se skládá z vážených polí, funkcí a parametrů. Další informace o definování jednoho najdete v tématu [profily vyhodnocování](index-add-scoring-profiles.md).

## <a name="scoring-statistics"></a>Statistiky bodování

Z důvodu škálovatelnosti Azure Kognitivní hledání distribuuje každý index vodorovně prostřednictvím procesu horizontálního dělení, což znamená, že části indexu jsou fyzicky oddělené.

Ve výchozím nastavení se skóre dokumentu počítá na základě statistických vlastností dat *v rámci horizontálních oddílů*. Tento přístup obecně není problémem pro velké corpusy dat a poskytuje lepší výkon než výpočet skóre na základě informací v rámci všech horizontálních oddílů. To, že použití této optimalizace výkonu může způsobit, že se dva velmi podobné dokumenty (nebo dokonce identické dokumenty) ukončí s různou závažností, pokud končí v různých horizontálních oddílů.

Pokud upřednostňujete výpočet skóre na základě statistických vlastností ve všech horizontálních oddílů, můžete to udělat přidáním *scoringStatistics = Global* jako [parametru dotazu](https://docs.microsoft.com/rest/api/searchservice/search-documents) (nebo přidat *"scoringStatistics": "Global"* jako parametr těla [žádosti o dotaz](https://docs.microsoft.com/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> Pro `scoringStatistics` parametr je vyžadován klíč rozhraní API pro správu.

## <a name="similarity-ranking-algorithms"></a>Algoritmy řazení podobnosti

Azure Kognitivní hledání podporuje dva různé algoritmy řazení podobných podobností: *klasický algoritmus podobnosti* a oficiální implementace algoritmu *BM25 Okapi* (v současnosti ve verzi Preview). Klasický algoritmus podobnosti je výchozí algoritmus, ale od 15. července se všechny nové služby vytvořené po tomto datu použijí pomocí nového algoritmu BM25. Bude to jediný algoritmus, který je k dispozici pro nové služby.

Prozatím můžete určit, který algoritmus řazení podobnosti byste chtěli použít. Další informace najdete v tématu o [algoritmu řazení](index-ranking-similarity.md).

## <a name="watch-this-video"></a>Podívejte se na toto video

V tomto 16bitovém videu Raouf Merouche software inženýr vysvětluje proces indexování, dotazování a vytváření profilů vyhodnocování. Poskytuje vám dobrou představu o tom, co v digestoři probíhá při indexování a načítání dokumentů.

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2-3 minut pokrývá indexování: zpracování textu a lexikální analýzu.
+ 3-4 minut pokrývá indexování: obrácené indexy.
+ dotazování na 4-6 minut: načítání a hodnocení.
+ 7-16 minut pokrývá profily vyhodnocování.

## <a name="see-also"></a>Viz také

 Reference k [profilům vyhodnocování](index-add-scoring-profiles.md) [REST API](https://docs.microsoft.com/rest/api/searchservice/)   
 [Rozhraní API pro hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Sada Azure Kognitivní hledání .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
