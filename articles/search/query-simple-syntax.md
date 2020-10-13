---
title: Jednoduchá syntaxe dotazů
titleSuffix: Azure Cognitive Search
description: Referenční informace pro jednoduchou syntaxi dotazů, která se používá pro dotazy fulltextového vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923377"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Jednoduchá syntaxe dotazů v Azure Kognitivní hledání

Azure Kognitivní hledání implementuje dva jazyky dotazů založené na Lucene: [jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Jednoduchý analyzátor je flexibilnější a pokusí se o interpretaci požadavku i v případě, že není dokonale složen. Z důvodu této flexibility je výchozí hodnotou pro dotazy v Azure Kognitivní hledání. 

Jednoduchá syntaxe se používá pro výrazy dotazu předané v `search` parametru [žádosti hledání dokumentů](/rest/api/searchservice/search-documents), takže se nemusíte zaměňovat se [syntaxí OData](query-odata-filter-orderby-syntax.md) použitou pro parametr [$Filter Expressions](search-filters.md) stejného rozhraní API pro hledání dokumentů. `search`Parametry a `$filter` mají odlišnou syntaxi, se svými vlastními pravidly pro vytváření dotazů, uvozovacích řetězců a tak dále.

I když je jednoduchý analyzátor založený na třídě pro [zjednodušené analyzátory dotazů Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , implementace v Azure kognitivní hledání vyloučí přibližné hledání. Pokud potřebujete [hledání přibližné](search-query-fuzzy.md) nebo jiné rozšířené formuláře dotazů, zvažte místo toho alternativní [úplnou syntaxi dotazů Lucene](query-lucene-syntax.md) .

## <a name="invoke-simple-parsing"></a>Vyvolat jednoduchou analýzu

Výchozí hodnota je jednoduchá syntaxe. Volání je nezbytné pouze v případě, že obnovuje syntaxi z úplného na jednoduchou. K explicitnímu nastavení syntaxe použijte `queryType` parametr Search. Platné hodnoty zahrnují `queryType=simple` nebo `queryType=full` , kde `simple` je výchozí hodnota a `full` vyvolá [úplný analyzátor dotazů Lucene](query-lucene-syntax.md) pro pokročilejší dotazy. 

## <a name="syntax-fundamentals"></a>Základy syntaxe

Libovolný text s jednou nebo více výrazy je považován za platný výchozí bod pro provedení dotazu. Azure Kognitivní hledání bude odpovídat dokumentům, které obsahují některé nebo všechny z těchto podmínek, včetně všech variant zjištěných během analýzy textu.

Stejně jako u těchto zvuků je ve službě Azure Kognitivní hledání jeden aspekt provádění dotazů, které *by mohly* vést k neočekávaným výsledkům, a ne zmenšení výsledků hledání, protože se do vstupního řetězce přidávají další výrazy a operátory. Bez ohledu na to, zda toto rozšíření skutečně probíhá, závisí na zahrnutí operátoru NOT v kombinaci s parametrem **searchMode** , který určuje, jak není interpretováno v souvislosti s chováním a nebo nebo. Další informace naleznete v tématu [operátor NOT](#not-operator).

### <a name="precedence-operators-grouping"></a>Operátory priority (seskupení)

Pomocí závorek lze vytvořit poddotazy, včetně operátorů v rámci příkazu kulatého závorky. Nástroj například `motel+(wifi|luxury)` vyhledá dokumenty obsahující termín "Motel" a buď "WiFi", nebo "luxus" (nebo obojí).

Seskupení polí je podobné, ale obor seskupení do jednoho pole. Například `hotelAmenities:(gym+(wifi|pool))` vyhledá pole "hotelAmenities" pro "posilovně" a "WiFi" nebo "posilovně" a "Pool".  

### <a name="escaping-search-operators"></a>Operátory hledání uvozovacích znaků  

V jednoduché syntaxi operátory hledání obsahují tyto znaky: `+ | " ( ) ' \`  

Pokud je některý z těchto znaků součástí tokenu v indexu, vydejte ho pomocí prefixu s jedním zpětným lomítkem ( `\` ) v dotazu. Předpokládejme například, že jste použili vlastní analyzátor pro celou funkci tokenizace a váš index obsahuje řetězec "luxus + hotelu". Chcete-li získat přesnou shodu s tímto tokenem, vložte řídicí znak:  `search=luxury\+hotel` . 

Chcete-li zjednodušit věci z více typických případů, existují dvě výjimky z tohoto pravidla, kde není nutné uvozovací znaky:  

+ `-`Pokud se jedná o první znak za prázdným znakem, musí být znak operátoru bez řídicí sekvence. Pokud `-` se zobrazí uprostřed (například v `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), můžete přeskočit uvozovací znaky.

+ Operátor přípona `*` musí být uvozena řídicím znakem pouze v případě, že se jedná o poslední znak před prázdným znakem. Pokud se `*` zobrazí uprostřed (například v `4*4=16` ), nevyžaduje se žádné uvozovací znaky.

> [!NOTE]  
> Ve výchozím nastavení Standard analyzátor odstraní a rozdělí slova na spojovníkech, prázdných znaků, ampersandech a dalších znacích během [lexikální analýzy](search-lucene-query-architecture.md#stage-2-lexical-analysis). Pokud vyžadujete, aby v řetězci dotazu zůstaly speciální znaky, budete možná potřebovat analyzátor, který je zachovává v indexu. Mezi možnosti patří [analyzátory](index-add-language-analyzers.md)přirozeného jazyka od Microsoftu, které zachovává rozdělená slova nebo vlastní analyzátor pro složitější vzory. Další informace najdete v tématu [částečné výrazy, vzory a speciální znaky](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a vyhrazených znaků v adresách URL

Ujistěte se prosím, že všechny nebezpečné a rezervované znaky jsou v adrese URL zakódované. Například # je nebezpečný znak, protože se jedná o identifikátor fragmentu nebo kotvy v adrese URL. Znak musí být kódovaný na, `%23` Pokud je použit v adrese URL. ' & ' a ' = ' jsou příklady rezervovaných znaků, které jsou vymezují parametry a určují hodnoty v Azure Kognitivní hledání. Další podrobnosti najdete v tématu [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Nezabezpečené znaky jsou ``" ` < > # % { } | \ ^ ~ [ ]`` . Vyhrazené znaky jsou `; / ? : @ = + &` .

### <a name="querying-for-special-characters"></a>Dotazování na speciální znaky

V některých případech můžete chtít vyhledat speciální znak, například ❤ emoji nebo symbol €. V takovém případě se ujistěte, že analyzátor, který používáte, nefiltruje tyto znaky.  Standardní analyzátor ignoruje mnoho speciálních znaků, takže by se v indexu nestaly tokeny.

Proto je prvním krokem, abyste měli jistotu, že použijete analyzátor, který bude tyto tokeny považovat za tyto prvky. Analyzátor "Whitespace" například vezme v úvahu jakékoliv sekvence znaků oddělené mezerami jako tokeny, takže řetězec "❤" by byl považován za token. Také analyzátor, jako je Microsoft English Analyzer ("en. Microsoft"), by měl brát v úvahu řetězec "€" jako token. Můžete [otestovat analyzátor](/rest/api/searchservice/test-analyzer) a zjistit, jaké tokeny generuje pro daný dotaz.

Při použití znaků Unicode zajistěte, aby byly symboly v adrese URL dotazu správně uvozeny řídicími znaky (například ❤ by používaly řídicí sekvenci `%E2%9D%A4+` ). Post provede tento převod automaticky.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Omezení velikosti dotazů

 Existuje omezení velikosti dotazů, které můžete odeslat do služby Azure Kognitivní hledání. Konkrétně můžete mít maximálně 1024 klauzulí (výrazy oddělené a, nebo a tak dále). Velikost jakéhokoli jednotlivého výrazu v dotazu je také omezena na přibližně 32 KB. Pokud vaše aplikace generuje vyhledávací dotazy programově, doporučujeme ji navrhovat takovým způsobem, že negeneruje dotazy na neohraničenou velikost.  

## <a name="boolean-search"></a>Logické hledání

Můžete vložit logické operátory (a, nebo, ne) do řetězce dotazu pro sestavení bohatou sadu kritérií, proti které jsou nalezeny vyhovující dokumenty. 

### <a name="and-operator-"></a>AND – operátor `+`

Operátor AND je znaménko plus. Vyhledá například `wifi + luxury` dokumenty obsahující obojí `wifi` a `luxury` .

### <a name="or-operator-"></a>OR – operátor `|`

Operátor OR je znak svislého pruhu nebo svislé čáry. Vyhledá například `wifi | luxury` dokumenty obsahující buď `wifi` nebo `luxury` nebo obojí.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT – operátor `-`

Operátor NOT je symbol mínus. Vyhledá například `wifi –luxury` dokumenty, které mají `wifi` podmínky a/nebo nemají `luxury` .

Parametr **searchMode** v požadavku na dotaz řídí, zda je výraz s operátorem NOT ANDed nebo ORed s jinými podmínkami v dotazu (za předpokladu, že se nejedná `+` `|` o operátor jiné podmínky). Platné hodnoty jsou `any` nebo `all` .

`searchMode=any` zvyšuje počet odvolání dotazů zahrnutím více výsledků a ve výchozím nastavení `-` bude interpretován jako "nebo ne". Například `wifi -luxury` se bude shodovat s dokumenty, které buď obsahují termín `wifi` , nebo ty, které neobsahují termín `luxury` .

`searchMode=all` zvyšuje přesnost dotazů zahrnutím menšího počtu výsledků a ve výchozím nastavení je interpretována jako "a nikoli". Například `wifi -luxury` bude odpovídat dokumentům, které obsahují termín `wifi` a nesmí obsahovat pojem "luxus". To je pravděpodobně intuitivní chování `-` operátoru. Proto byste měli zvážit použití `searchMode=all` místo toho, `searchMode=any` Pokud chcete optimalizovat hledání přesnosti místo odvolání *a* vaši uživatelé často používají `-` operátor v hledání.

Při rozhodování o nastavení **searchMode** zvažte vzory interakce uživatele pro dotazy v různých aplikacích. Uživatelům, kteří hledají informace, je pravděpodobnější, že v dotazu zařadí operátor, a to na rozdíl od webů elektronického obchodování, které mají více integrovaných navigačních struktur.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Porovnání prefixu zástupného znaku (*,?)

U příkazu "začíná s" dotazy přidejte operátor přípony jako zástupný symbol pro zbytek období. Použijte hvězdičku `*` pro více znaků nebo `?` pro jednotlivé znaky. Například `lingui*` bude odpovídat "jazyku" nebo "linguini", ignorování velkých a malých písmen. 

Podobně jako filtry, dotaz předpony hledá přesnou shodu. V takovém případě neexistuje žádné bodové hodnocení (všechny výsledky obdrží skóre hledání 1,0). Upozorňujeme, že dotazy s předponou můžou být pomalé, zejména pokud je index velký a předpona obsahuje malý počet znaků. Alternativní metodologie, jako je Edge n-gram, může provádět rychleji.

Pro jiné varianty dotazů se zástupnými znaky, jako je třeba přípona nebo vpony, odpovídají konci nebo polovině podmínky, použijte [pro hledání pomocí zástupných znaků úplnou syntaxi Lucene](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Hledání frází `"`

Termínem hledání je dotaz na jednu nebo více podmínek, přičemž některý z podmínek je považován za shodu. Hledání frází je přesná fráze uzavřená v uvozovkách `" "` . Například při `Roach Motel` (bez uvozovek) by hledání dokumentů obsahujících `Roach` a/nebo `Motel` kdekoli v libovolném pořadí, `"Roach Motel"` (s uvozovkami), bude odpovídat pouze dokumentům, které obsahují tuto celou frázi společně a v tomto pořadí (lexikální analýza je stále použita).

## <a name="see-also"></a>Viz také  

+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Příklady dotazů pro jednoduché hledání](search-query-simple-examples.md)
+ [Příklady dotazů pro úplné hledání Lucene](search-query-lucene-examples.md)
+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/Search-Documents)
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md)