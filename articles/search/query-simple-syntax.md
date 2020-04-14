---
title: Jednoduchá syntaxe dotazů
titleSuffix: Azure Cognitive Search
description: Odkaz na jednoduchou syntaxi dotazu použitou pro fulltextové vyhledávací dotazy v Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258860"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Jednoduchá syntaxe dotazu v Azure Cognitive Search

Azure Cognitive Search implementuje dva lucene-založené dotazovací [jazyky: jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [Lucene dotaz analyzátor](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). 

V Azure Cognitive Search, jednoduché syntaxe dotazu vylučuje fuzzy vyhledávací operace. Místo toho použijte úplnou syntaxi Lucene pro [přibližné vyhledávání](search-query-fuzzy.md).

> [!NOTE]
> Jednoduchá syntaxe dotazu se používá pro výrazy dotazu předané v parametru **vyhledávání** rozhraní API [pro hledání dokumentů,](https://docs.microsoft.com/rest/api/searchservice/search-documents) které nelze zaměňovat se [syntaxí OData](query-odata-filter-orderby-syntax.md) použitou pro [$filter](search-filters.md) parametr tohoto rozhraní API. Tyto různé syntaxe mají vlastní pravidla pro vytváření dotazů, úniku řetězce a tak dále.
>
> Azure Cognitive Search poskytuje alternativní [úplnou syntaxi lucene dotazu](query-lucene-syntax.md) pro složitější dotazy v parametru **vyhledávání.** Další informace o architektuře analýzy dotazů a výhodách jednotlivých syntaxí najdete v tématu [Jak funguje fulltextové vyhledávání v Azure Cognitive Search](search-lucene-query-architecture.md).

## <a name="invoke-simple-parsing"></a>Vyvolat jednoduchou analýzu

Jednoduchá syntaxe je výchozí. Vyvolání je nutné pouze v případě, že resetujete syntaxi z úplné na jednoduchou. Chcete-li explicitně nastavit `queryType` syntaxi, použijte vyhledávací parametr. Platné hodnoty `queryType=simple` `queryType=full`zahrnují `simple` nebo , kde `full` je výchozí a vyvolá [úplný analyzátor dotazů Lucene](query-lucene-syntax.md) pro pokročilejší dotazy. 

## <a name="syntax-fundamentals"></a>Základy syntaxe

Jakýkoli text s jedním nebo více podmínkami je považován za platný výchozí bod pro spuštění dotazu. Azure Cognitive Search bude odpovídat dokumentům obsahujícím některé nebo všechny podmínky, včetně všech variant zjištěných během analýzy textu.

Jak jednoduché, jak to zní, je jeden aspekt provádění dotazů v Azure Cognitive Search, které *může* přinést neočekávané výsledky, zvýšení spíše než snížení výsledků hledání jako další termíny a operátory jsou přidány do vstupního řetězce. Zda k tomuto rozšíření skutečně dochází, závisí na zahrnutí operátoru NOT v kombinaci s nastavením **parametru searchMode,** které určuje, jak není interpretovánz hlediska chování AND nebo OR. Další informace naleznete v tématu [NOT operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Operátory s prioritami (seskupení)

Závorky můžete použít k vytvoření poddotazů, včetně operátorů v příkazu závorky. Například `motel+(wifi||luxury)` bude hledat dokumenty obsahující termín "motel" a buď "wifi" nebo "luxusní" (nebo obojí).

Seskupení polí je podobné, ale obory seskupení do jednoho pole. Například `hotelAmenities:(gym+(wifi||pool))` vyhledá v poli "hotelAmenities" pro "tělocvična" a "wifi", nebo "tělocvična" a "bazén".  

### <a name="escaping-search-operators"></a>Unikající vyhledávací operátory  

Chcete-li použít některý z vyhledávacích operátorů jako součást hledaného textu, uniknete znaku předponou jediným zpětným lomítkem (`\`). Například pro hledání se `https://`zástupnými `://` symboly na , kde `search=https\:\/\/*`je součástí řetězce dotazu, byste zadali . Podobně může vypadat vzor uvozené `\+1 \(800\) 642\-7676`telefonní číslo .

Speciální znaky, které vyžadují únikpatří následující:`- * ? \ /`  

Aby byly věci jednoduché pro typičtější případy, existují dvě výjimky z tohoto pravidla, kde není potřeba úniku:  

+ Operátor NOT `-` musí být uvozen pouze v případě, že je prvním znakem po prázdné ploše, nikoli, pokud je uprostřed termínu. Například následující identifikátor GUID je platný `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`bez řídicího znaku: .

+ Operátor `*` přípony musí být uvozeny pouze v případě, že je poslední znak před mezerami, ne pokud je uprostřed termínu. Například `4*4=16` nevyžaduje zpětné lomítko.

> [!NOTE]  
> Přestože uvození udržuje tokeny dohromady, [lexikální analýza](search-lucene-query-architecture.md#stage-2-lexical-analysis) během indexování může je odstranit. Například standardní analyzátor Lucene odstraní a přeruší slova na spojovníky, prázdné znaky a další znaky. Pokud požadujete speciální znaky v řetězci dotazu, budete pravděpodobně potřebovat analyzátor, který je zachová v indexu. Některé možnosti zahrnují [analyzátory přirozeného jazyka](index-add-language-analyzers.md)společnosti Microsoft , které zachovávají slova s pomlčkou, nebo vlastní analyzátor pro složitější vzory. Další informace naleznete [v tématu Částečné termíny, vzory a speciální znaky](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a rezervovaných znaků v adresách URL

Zkontrolujte, zda jsou všechny nebezpečné a vyhrazené znaky zakódovány v adrese URL. Například '#' je nebezpečný znak, protože se jedná o identifikátor fragmentu nebo kotvy v adrese URL. Znak musí být zakódován, `%23` pokud je použit v adrese URL. '&' a '=' jsou příklady vyhrazených znaků, protože vymezují parametry a určují hodnoty v Azure Cognitive Search. Další podrobnosti naleznete v tématu [RFC1738: Url adresy url jednotného zdroje.](https://www.ietf.org/rfc/rfc1738.txt)

Nebezpečné znaky ``" ` < > # % { } | \ ^ ~ [ ]``jsou . Vyhrazené znaky `; / ? : @ = + &`jsou .

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Omezení velikosti dotazu

 Velikost dotazů, které můžete odeslat do Azure Cognitive Search, je omezena. Konkrétně můžete mít maximálně 1024 klauzule (výrazy oddělené AND, OR a tak dále). K dispozici je také limit přibližně 32 KB na velikost jednotlivých termín ů v dotazu. Pokud aplikace generuje vyhledávací dotazy programově, doporučujeme je navrhnout tak, aby negenerovaly dotazy neomezené velikosti.  

## <a name="boolean-search"></a>Logické hledání

Do řetězce dotazu můžete vložit logické operátory (AND, OR, NOT) a vytvořit tak bohatou sadu kritérií, podle kterých jsou nalezeny odpovídající dokumenty. 

### <a name="and-operator-"></a>Operátor A`+`

Operátor AND je znaménko plus. Bude například `wifi+luxury` vyhledávat dokumenty obsahující `wifi` `luxury`obě a .

### <a name="or-operator-"></a>Operátor OR`|`

Operátor OR je svislý znak pruhu nebo potrubí. Bude například `wifi | luxury` vyhledávat dokumenty obsahující `wifi` `luxury` jednu nebo nebo obě.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT operátor`-`

Operátor NOT je znaménko mínus. Bude například vyhledávat dokumenty, `wifi –luxury` `wifi` které mají termín a/nebo nemají `luxury`.

Parametr **searchMode** v požadavku na dotaz určuje, zda je termín s operátorem NOT ANDed nebo `+` `|` ORed s jinými termíny v dotazu (za předpokladu, že neexistuje žádný nebo operátor na jiné termíny). Mezi platné `any` `all`hodnoty patří nebo .

`searchMode=any`zvyšuje odvolání dotazů zahrnutím více výsledků `-` a ve výchozím nastavení bude interpretován jako "NEBO NE". Bude například `wifi -luxury` odpovídat dokumentům, `wifi` které obsahují termín, `luxury`nebo dokumentům, které tento výraz neobsahují .

`searchMode=all`zvyšuje přesnost dotazů zahrnutím méně výsledků a ve výchozím nastavení - bude interpretován jako "A ne". Například `wifi -luxury` bude odpovídat dokumenty, `wifi` které obsahují termín a neobsahují termín "luxus". To je pravděpodobně intuitivnější chování pro operátora. `-` Proto byste měli `searchMode=all` zvážit `searchMode=any` použití namísto, pokud chcete optimalizovat vyhledávání pro přesnost místo `-` odvolání *a* vaši uživatelé často používají operátor při vyhledávání.

Při rozhodování o **searchMode** nastavení, zvažte vzory interakce uživatele pro dotazy v různých aplikacích. Uživatelé, kteří hledají informace, s větší pravděpodobností zahrnou do dotazu operátora, na rozdíl od webů elektronického obchodování, které mají více vestavěných navigačních struktur.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Hledání předpony

Operátor přípony je hvězdička `*`. Například `lingui*` najde "jazykové" nebo "linguini", ignoruje případ. 

Podobně jako u filtrů hledá dotaz předpony přesnou shodu. Jako takové neexistuje žádné skóre relevance (všechny výsledky obdrží skóre vyhledávání 1,0). Předpona dotazy mohou být pomalé, zejména v případě, že index je velký a předpona se skládá z malého počtu znaků. 

Pokud chcete spustit dotaz přípony, odpovídající na poslední část řetězce, použijte [zástupné hledání](query-lucene-syntax.md#bkmk_wildcard) a úplnou syntaxi Lucene.

## <a name="phrase-search-"></a>Hledání frází`"`

Hledání termínů je dotaz na jeden nebo více termínů, kde jsou některé z výrazů považovány za shodu. Hledání frází je přesná fráze `" "`uzavřená v uvozovkách . Například zatímco `Roach Motel` (bez uvozovek) by `Roach` hledat `Motel` dokumenty obsahující a `"Roach Motel"` / nebo kdekoli v libovolném pořadí, (s uvozovkami) bude odpovídat pouze dokumenty, které obsahují celou frázi dohromady a v tomto pořadí (analýza textu stále platí).

## <a name="see-also"></a>Viz také  

+ [Příklady dotazů pro jednoduché hledání](search-query-simple-examples.md)
+ [Příklady dotazu pro úplné vyhledávání Lucene](search-query-lucene-examples.md)
+ [Hledání dokumentů &#40;azure kognitivní vyhledávání rozhraní API rozhraní&#41;API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md) 
