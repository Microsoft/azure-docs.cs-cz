---
title: Přehled podobnosti a bodování
titleSuffix: Azure Cognitive Search
description: Vysvětluje koncepty podobnosti a bodování a to, co vývojář může udělat pro přizpůsobení výsledků bodování.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 72243f896b2cf7dbab61a42514bee634da28d4c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676320"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Podobnost a bodování v Azure Kognitivní hledání

Tento článek popisuje dva algoritmy řazení podobnosti v Azure Kognitivní hledání. Obsahuje taky dvě související funkce: *profily vyhodnocování* (kritéria pro úpravu skóre hledání) a parametr *featuresMode* (rozbalením skóre hledání zobrazíte další podrobnosti). 

Třetí algoritmus pro nové sémantické přeřazení je v současné době ve verzi Public Preview. Další informace najdete v článku [Přehled sémantického hledání](semantic-search-overview.md).

## <a name="similarity-ranking-algorithms"></a>Algoritmy řazení podobnosti

Azure Kognitivní hledání podporuje dva algoritmy řazení podle podobnosti.

| Algoritmus | Skóre | Dostupnost |
|-----------|-------|--------------|
| ClassicSimilarity | @search.score | Používá se pro všechny služby vyhledávání až do 15. července 2020. |
| BM25Similarity | @search.score | Používá se všemi službami vyhledávání vytvořenými po 15. červenci. Starší služby, které používají klasický standardně, se můžou [přihlásit k BM25](index-ranking-similarity.md). |

Klasické i BM25 jsou funkce načítání TF-IDF, které používají frekvenci termínu (TF) a inverzní hustota dokumentů (IDF) jako proměnné k výpočtu skóre relevance pro jednotlivé páry dokumentů a dotazů, které se pak používají pro hodnocení, které jsou v konceptuálním tvaru jako klasické, BM25 přebírá svůj kořen v pravděpodobnostních informacích, aby se na něm vylepšil. BM25 nabízí také pokročilé možnosti vlastního nastavení, jako je například umožnění, aby se uživatel rozhodl, jak se skóre relevance škáluje s termínem četnosti shodných podmínek.

Následující segment videa se rychle přepošle na vysvětlení všeobecně dostupných algoritmů hodnocení používaných v Azure Kognitivní hledání. Pro další pozadí si můžete přehrát celé video.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

## <a name="relevance-scoring"></a>Vyhodnocení relevance

Bodování označuje výpočet skóre vyhledávání pro každou položku vrácenou ve výsledcích vyhledávání pro fulltextové vyhledávací dotazy. Skóre je indikátorem relevance položky v kontextu aktuálního dotazu. Čím vyšší je skóre, tím je daná položka relevantnější. Ve výsledcích hledání jsou položky seřazeny od vysoké po nejnižší na základě skóre hledání vypočítaného pro každou položku. Skóre se vrátí v odpovědi jako v @search.score každém dokumentu.

Ve výchozím nastavení se v odpovědi vrátí Top 50, ale můžete použít parametr **$Top** k vrácení menšího nebo většího počtu položek (až 1000 v jedné odpovědi) a **$Skip** k získání další sady výsledků.

Skóre hledání je vypočítáno na základě statistických vlastností dat a dotazu. Azure Kognitivní hledání vyhledá dokumenty, které se shodují na hledaných termínech (v závislosti na [searchMode](/rest/api/searchservice/search-documents#query-parameters)), a upřednostňuje dokumenty, které obsahují mnoho instancí hledaného termínu. Skóre hledání bude ještě vyšší, pokud je v indexu dat zřídka, ale v dokumentu běžné. Základem pro tento přístup k výpočetním významům je známý jako *TF-IDF nebo* četnost termínů – inverzní frekvence dokumentů.

Hodnoty skóre hledání je možné opakovat v rámci sady výsledků dotazu. Pokud má více přístupů stejné skóre hledání, řazení stejných položek skóre není definováno a není stabilní. Spusťte dotaz znovu a můžete se podívat na pozici posunu položek, zejména pokud používáte bezplatnou službu nebo fakturovatelnou službu s více replikami. Vzhledem k tomu, že se dvě položky shodují se stejným skóre, neexistuje žádná záruka, která se zobrazí jako první.

Pokud chcete přerušit vazbu mezi opakujícími se výsledky, můžete přidat klauzuli **$OrderBy** do prvního pořadí podle skóre a pak seřadit podle jiného pole, které lze seřadit (například `$orderby=search.score() desc,Rating desc` ). Další informace najdete v tématu [$OrderBy](search-query-odata-orderby.md).

> [!NOTE]
> `@search.score = 1.00`Symbol označuje sadu výsledků bez hodnocení nebo Neseřazený výsledek. Skóre je rovnoměrné napříč všemi výsledky. Pokud je formulář dotazu přibližné vyhledávání, zástupné dotazy nebo výrazy regulárního výrazu nebo výraz **$Filter** , dojde k neskóre výsledků.

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>Statistiky bodování a rychlé relace

Z důvodu škálovatelnosti Azure Kognitivní hledání distribuuje každý index vodorovně prostřednictvím procesu horizontálního dělení, což znamená, že [části indexu jsou fyzicky oddělené](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

Ve výchozím nastavení se skóre dokumentu počítá na základě statistických vlastností dat *v rámci horizontálních oddílů*. Tento přístup obecně není problémem pro velké corpusy dat a poskytuje lepší výkon než výpočet skóre na základě informací v rámci všech horizontálních oddílů. To, že použití této optimalizace výkonu může způsobit, že se dva velmi podobné dokumenty (nebo dokonce identické dokumenty) ukončí s různou závažností, pokud končí v různých horizontálních oddílů.

Pokud upřednostňujete výpočet skóre na základě statistických vlastností ve všech horizontálních oddílů, můžete to udělat přidáním *scoringStatistics = Global* jako [parametru dotazu](/rest/api/searchservice/search-documents) (nebo přidat *"scoringStatistics": "Global"* jako parametr těla [žádosti o dotaz](/rest/api/searchservice/search-documents)).

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Použití scoringStatistics zajistí, že všechny horizontálních oddílů ve stejné replice budou mít stejné výsledky. V takovém případě se jiné repliky mohou mírně lišit, protože jsou vždy aktualizovány pomocí nejnovějších změn v indexu. V některých scénářích můžete chtít, aby vaši uživatelé měli během dotazové relace k větší konzistenci výsledků. V takových scénářích můžete zadat `sessionId` jako součást dotazů. `sessionId`Je jedinečný řetězec, který vytvoříte pro odkazování na jedinečnou relaci uživatele.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```

Pokud `sessionId` použijete stejný postup, bude proveden doporučený pokus o dosažení stejné repliky. tím se zvýší konzistence výsledků, které uživatelé uvidí. 

> [!NOTE]
> Opakované opakované použití stejných `sessionId` hodnot může kolidovat s vyrovnáváním zatížení žádostí mezi replikami a nepříznivě ovlivnit výkon služby Search Service. Hodnota použitá jako sessionId nemůže začínat znakem podtržítka.

## <a name="scoring-profiles"></a>Profily skórování

Můžete přizpůsobit způsob řazení různých polí definováním *profilu vyhodnocování*. Profily vyhodnocování poskytují větší kontrolu nad hodnocením položek ve výsledcích vyhledávání. Například můžete chtít zvýšit množství položek na základě jejich potenciálních výnosů, zvýšit úroveň nových položek nebo možná zvýšit množství položek, které byly v inventáři příliš dlouho. 

Profil vyhodnocování je součástí definice indexu, která se skládá z vážených polí, funkcí a parametrů. Další informace o definování jednoho najdete v tématu [profily vyhodnocování](index-add-scoring-profiles.md).

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>parametr featuresMode (Preview)

Požadavky na [hledání dokumentů](/rest/api/searchservice/preview-api/search-documents) mají nový parametr [featuresMode](/rest/api/searchservice/preview-api/search-documents#featuresmode) , který může poskytnout další podrobnosti o závažnosti na úrovni pole. Vzhledem k tomu `@searchScore` , že se pro dokument počítá vše (jak to je důležité pro tento dokument v kontextu tohoto dotazu), můžete prostřednictvím featuresMode získat informace o jednotlivých polích, jak vyjadřují ve `@search.features` struktuře. Struktura obsahuje všechna pole, která se používají v dotazu (buď určitá pole prostřednictvím **searchFields** v dotazu, nebo všechna pole, která jsou v indexu **vyhledána jako prohledávatelný** ). Pro každé pole získáte následující hodnoty:

+ Počet jedinečných tokenů nalezených v poli
+ Skóre podobnosti nebo míra, jak podobný obsah pole je, relativní vzhledem k termínu dotazu
+ Frekvence termínu nebo počet, kolikrát byl termín dotazu nalezen v poli

Pro dotaz, který cílí na pole Description (popis) a "title", může odpověď, která obsahuje, `@search.features` vypadat takto:

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

Tyto datové body můžete využívat ve [vlastních řešeních bodování](https://github.com/Azure-Samples/search-ranking-tutorial) nebo je použít k ladění problémů s relevancí vyhledávání.

## <a name="see-also"></a>Viz také

+ [Profily vyhodnocování](index-add-scoring-profiles.md)
+ [Odkaz na REST API](/rest/api/searchservice/)
+ [Rozhraní API pro hledání dokumentů](/rest/api/searchservice/search-documents)
+ [Sada Azure Kognitivní hledání .NET SDK](/dotnet/api/overview/azure/search)