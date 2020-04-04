---
title: Jednoduchá syntaxe dotazů
titleSuffix: Azure Cognitive Search
description: Odkaz na jednoduchou syntaxi dotazu použitou pro fulltextové vyhledávací dotazy v Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656171"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Jednoduchá syntaxe dotazu v Azure Cognitive Search

Azure Cognitive Search implementuje dva lucene-založené dotazovací [jazyky: jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [Lucene dotaz analyzátor](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). V Azure Cognitive Search, syntaxe jednoduchého dotazu vylučuje fuzzy/slop možnosti.  

> [!NOTE]
> Jednoduchá syntaxe dotazu se používá pro výrazy dotazu předané v parametru **vyhledávání** rozhraní API [pro hledání dokumentů,](https://docs.microsoft.com/rest/api/searchservice/search-documents) které nelze zaměňovat se [syntaxí OData](query-odata-filter-orderby-syntax.md) použitou pro [$filter](search-filters.md) parametr tohoto rozhraní API. Tyto různé syntaxe mají vlastní pravidla pro vytváření dotazů, úniku řetězce a tak dále.
>
> Azure Cognitive Search poskytuje alternativní [úplnou syntaxi lucene dotazu](query-lucene-syntax.md) pro složitější dotazy v parametru **vyhledávání.** Další informace o architektuře analýzy dotazů a výhodách jednotlivých syntaxí najdete v tématu [Jak funguje fulltextové vyhledávání v Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Jak vyvolat jednoduchou analýzu

Jednoduchá syntaxe je výchozí. Vyvolání je nutné pouze v případě, že resetujete syntaxi z úplné na jednoduchou. Chcete-li explicitně nastavit `queryType` syntaxi, použijte vyhledávací parametr. Platné hodnoty `simple|full`zahrnují `simple` , s `full` jako výchozí a pro Lucene. 

## <a name="query-behavior-anomalies"></a>Anomálie chování dotazu

Jakýkoli text s jedním nebo více podmínkami je považován za platný výchozí bod pro spuštění dotazu. Azure Cognitive Search bude odpovídat dokumentům obsahujícím některé nebo všechny podmínky, včetně všech variant zjištěných během analýzy textu. 

Jak jednoduché, jak to zní, je jeden aspekt provádění dotazů v Azure Cognitive Search, které *může* přinést neočekávané výsledky, zvýšení spíše než snížení výsledků hledání jako další termíny a operátory jsou přidány do vstupního řetězce. Zda k tomuto rozšíření skutečně dojde, závisí na zahrnutí operátoru NOT v kombinaci s nastavením parametru, `searchMode` které určuje, jak není interpretovánz hlediska chování AND nebo OR. Vzhledem k `searchMode=Any`výchozímu a NOT operátoru je operace vypočítána `"New York" NOT Seattle` jako akce OR, která vrátí všechna města, která nejsou seattle.  

Obvykle je pravděpodobnější, že se tato chování zobrazí ve vzorcích interakce s uživatelem pro aplikace, které vyhledávají obsah, kde uživatelé s větší pravděpodobností zahrnou operátora do dotazu, na rozdíl od webů elektronického obchodu, které mají více vestavěných navigačních struktur. Další informace naleznete v tématu [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logické operátory (AND, OR, NOT) 

Operátory můžete vložit do řetězce dotazu a vytvořit tak bohatou sadu kritérií, podle kterých jsou nalezeny odpovídající dokumenty. 

### <a name="and-operator-"></a>Operátor A`+`

Operátor AND je znaménko plus. Bude například `wifi+luxury` vyhledávat dokumenty obsahující `wifi` `luxury`obě a .

### <a name="or-operator-"></a>Operátor OR`|`

Operátor OR je svislý znak pruhu nebo potrubí. Bude například `wifi | luxury` vyhledávat dokumenty obsahující `wifi` `luxury` jednu nebo nebo obě.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT operátor`-`

Operátor NOT je znaménko mínus. Bude například vyhledávat `wifi –luxury` dokumenty, `wifi` které mají termín a/nebo nemají `luxury` (a/nebo jsou řízeny ). `searchMode`

> [!NOTE]  
>  Možnost `searchMode` určuje, zda termín s operátorem NOT je ANDed nebo ORed s `+` ostatními termíny v dotazu v nepřítomnosti operátoru nebo. `|` Odvolání, `searchMode` které lze `any` nastavit buď `all`(výchozí) nebo . Pokud použijete `any`, zvýší odvolání dotazů zahrnutím dalších `-` výsledků a ve výchozím nastavení bude interpretovánjako "NEBO NE". Bude například `wifi -luxury` odpovídat dokumentům, `wifi` které obsahují termín, `luxury`nebo dokumentům, které tento výraz neobsahují . Pokud použijete `all`, zvýší přesnost dotazů zahrnutím méně výsledků a ve výchozím nastavení - bude interpretován jako "A ne". Například `wifi -luxury` bude odpovídat dokumenty, `wifi` které obsahují termín a neobsahují termín "luxus". To je pravděpodobně intuitivnější chování pro operátora. `-` Proto byste měli `searchMode=all` zvážit `searchMode=any` použití namísto, pokud chcete optimalizovat vyhledávání pro přesnost místo `-` *odvolání, a* vaši uživatelé často používají operátor při vyhledávání.

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>Operátor přípony `*` pro vyhledávání předpon

Operátor přípony je hvězdička `*`. Bude například hledat dokumenty, `cap*` které mají `cap`termín začínající na , ignorování případu. 

Podobně jako u filtrů hledá dotaz předpony přesnou shodu. Jako takové neexistuje žádné skóre relevance (všechny výsledky obdrží skóre vyhledávání 1,0). Předpona dotazy mohou být pomalé, zejména v případě, že index je velký a předpona se skládá z malého počtu znaků. 

Pokud chcete spustit dotaz přípony, odpovídající na poslední část řetězce, použijte [zástupné hledání](query-lucene-syntax.md#bkmk_wildcard) a úplnou syntaxi Lucene.

## <a name="phrase-search-operator"></a>Operátor vyhledávání frází

Operátor fráze uzavře frázi do `" "`uvozovek . Například zatímco `Roach Motel` (bez uvozovek) by `Roach` hledat `Motel` dokumenty obsahující a `"Roach Motel"` / nebo kdekoli v libovolném pořadí, (s uvozovkami) bude odpovídat pouze dokumenty, které obsahují celou frázi dohromady a v tomto pořadí (analýza textu stále platí).

## <a name="precedence-operator"></a>Operátor Priorita

Operátor priority uzavře řetězec do závorek `( )`. Například `motel+(wifi | luxury)` bude hledat dokumenty obsahující motel termín `wifi` `luxury` a buď nebo (nebo obojí).  

## <a name="escaping-search-operators"></a>Unikající vyhledávací operátory  

 Aby bylo možné použít výše uvedené symboly jako skutečnou část hledaného textu, měly by být uvozeny předponou zpětným lomítkem. Například `luxury\+hotel` bude mít za `luxury+hotel`následek termín . Aby byly věci jednoduché pro typičtější případy, existují dvě výjimky z tohoto pravidla, kde není potřeba úniku:  

- Operátor NOT `-` musí být uvozen pouze v případě, že je prvním znakem po prázdné ploše, nikoli, pokud je uprostřed termínu. Například `wi-fi` je jeden termín; vzhledem k tomu, `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`že identifikátory GUID (například) jsou považovány za jeden token.
- Operátor `*` přípony musí být uvozeny pouze v případě, že je poslední znak před mezerami, ne pokud je uprostřed termínu. Například `wi*fi` je považován za jeden token.

> [!NOTE]  
>  Přestože uvození udržuje tokeny dohromady, analýza textu je může rozdělit v závislosti na režimu analýzy. Podrobnosti najdete [&#40;jazykovou podporu&#41;rozhraní REST API azure cognitive search.](index-add-language-analyzers.md)  

## <a name="see-also"></a>Viz také  

+ [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md) 
