---
title: Jednoduchá syntaxe dotazů
titleSuffix: Azure Cognitive Search
description: Referenční informace pro jednoduchou syntaxi dotazů, která se používá pro dotazy fulltextového vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516582"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Jednoduchá syntaxe dotazů v Azure Kognitivní hledání

Azure Kognitivní hledání implementuje dva jazyky dotazů založené na Lucene: [jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Jednoduchý analyzátor je flexibilnější a pokusí se o interpretaci požadavku i v případě, že není dokonale složen. Z důvodu této flexibility je výchozí hodnotou pro dotazy v Azure Kognitivní hledání.

Jednoduchá syntaxe se používá pro výrazy dotazu předané v **`search`** parametru žádosti [hledání dokumentů (REST API)](/rest/api/searchservice/search-documents) , a nelze je zaměňovat se [syntaxí OData](query-odata-filter-orderby-syntax.md) použitými pro [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) výrazy a v rámci stejné žádosti. Parametry OData mají odlišnou syntaxi a pravidla pro sestavování dotazů, uvozovacích řetězců a tak dále.

I když je jednoduchý analyzátor založený na třídě [zjednodušeného analyzátoru dotazů Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , implementace v kognitivní hledání vyloučí přibližné hledání. Pokud potřebujete [hledání přibližných výsledků](search-query-fuzzy.md), zvažte místo toho alternativní [úplnou syntaxi dotazů Lucene](query-lucene-syntax.md) .

## <a name="example-simple-syntax"></a>Příklad (jednoduchá syntaxe)

I když **`queryType`** je nastavená níže, je výchozí nastavení a může být vynecháno, pokud se nevrátíte z alternativního typu. Následující příklad je hledání nezávisle za předpokladu, že všechny odpovídající dokumenty zahrnují "Pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

**`searchMode`** Parametr je v tomto příkladu relevantní. Vždy, když jsou logické operátory na dotazu, byste obecně měli nastavit `searchMode=all` , aby se zajistilo, že *všechna* kritéria jsou shodná. V opačném případě můžete použít výchozí `searchMode=any` , který upřednostňuje odvolání v případě přesnosti.

Další příklady najdete v tématu [jednoduché příklady syntaxe dotazů](search-query-simple-examples.md). Podrobnosti o požadavku a parametrech dotazu naleznete v tématu [Search Documents (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Hledání klíčových slov na základě podmínek a frází

Řetězce předané do **`search`** parametru mohou zahrnovat výrazy nebo fráze v libovolném podporovaném jazyce, logické operátory, operátory přednosti, zástupný znak nebo znaky předpony pro "začíná dotazy, řídicí znaky a znaky kódování adresy URL. **`search`** Parametr je nepovinný. Neurčeno, hledání ( `search=*` nebo `search=" "` ) vrátí prvních 50 dokumentů v libovolném (neseřazené) pořadí.

+ *Termínem hledání* je dotaz na jednu nebo více podmínek, kde některý z podmínek je považován za shodu.

+ *Hledání frází* je přesná fráze uzavřená v uvozovkách `" "` . Například při ```Roach Motel``` (bez uvozovek) by hledání dokumentů obsahujících ```Roach``` a/nebo ```Motel``` kdekoli v libovolném pořadí, ```"Roach Motel"``` (s uvozovkami), bude odpovídat pouze dokumentům, které obsahují tuto celou frázi společně a v tomto pořadí (lexikální analýza je stále použita). 

  V závislosti na vašem klientovi vyhledávání možná budete muset při hledání fráze řídicí uvozovky vymezit. Například v příspěvku v žádosti POST `"Roach Motel"` by se fráze v textu požadavku zadala jako `"\"Roach Motel\""` .

Ve výchozím nastavení se všechny výrazy nebo fráze předané v **`search`** parametru podrobují lexikální analýze. Ujistěte se, že rozumíte chování tokenizace analyzátoru, který používáte. V případě neočekávaných výsledků dotazu se často dá důvod trasovat na to, jak jsou výrazy v době dotazu vyvolány.

Libovolný text s jednou nebo více výrazy je považován za platný výchozí bod pro provedení dotazu. Azure Kognitivní hledání bude odpovídat dokumentům, které obsahují některé nebo všechny z těchto podmínek, včetně všech variant zjištěných během analýzy textu.

Stejně jako u těchto zvuků je ve službě Azure Kognitivní hledání jeden aspekt provádění dotazů, které *by mohly* vést k neočekávaným výsledkům, a ne zmenšení výsledků hledání, protože se do vstupního řetězce přidávají další výrazy a operátory. Bez ohledu na to, zda toto rozšíření skutečně probíhá, závisí na zahrnutí operátoru NOT v kombinaci s **`searchMode`** nastavením parametru, které určuje, jak není interpretováno v souvislosti s chováním a nebo nebo. Další informace naleznete v tématu operátor NOT v rámci [logických operátorů](#boolean-operators).

## <a name="boolean-operators"></a>Logické operátory

Můžete vložit logické operátory do řetězce dotazu, aby se zlepšila přesnost shody. V jednoduché syntaxi jsou logické operátory založené na znacích. Operátory textu, jako je například slovo a, nejsou podporovány.

| Znak | Příklad | Využití |
|----------- |--------|-------|
| `+` | `pool + ocean` | Operace a. Například `pool + ocean` stanoví, že dokument musí obsahovat oba výrazy.|
| `|` | `pool | ocean` | Operace nebo najde shodu, když je nalezen libovolný termín. V tomto příkladu dotazovací stroj vrátí shodu s dokumenty, které obsahují buď `pool` nebo `ocean` nebo obojí. Vzhledem k tomu, že nebo je výchozím operátorem spojení, můžete jej také opustit, což `pool ocean` je ekvivalent  `pool | ocean` .|
| `-` | `pool – ocean` | Operace NOT vrátí shodu u dokumentů, které vyloučily podmínky. <br/><br/>Chcete-li získat očekávané chování u výrazu NOT, zvažte nastavení **`searchMode=all`** v žádosti. V opačném případě se ve výchozím nastavení zobrazí **`searchMode=any`** Shoda `pool` se všemi dokumenty, které neobsahují `ocean` , což může být mnoho dokumentů. **`searchMode`** Parametr v požadavku na dotaz řídí, zda je výraz s operátorem NOT ANDed nebo ORed s jinými podmínkami v dotazu (za předpokladu, že se nejedná `+` `|` o operátor nebo na jiné podmínky). Použití **`searchMode=all`** zvyšuje přesnost dotazů zahrnutím menších výsledků a ve výchozím nastavení je interpretováno jako "a nikoli". <br/><br/>Při rozhodování o **`searchMode`** nastavení zvažte vzory interakce uživatele pro dotazy v různých aplikacích. Uživatelům, kteří hledají informace, je pravděpodobnější, že v dotazu zařadí operátor, a to na rozdíl od webů elektronického obchodování, které mají více integrovaných navigačních struktur. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Dotazy na předpony

Pro "začíná dotazy" přidejte operátor přípon ( `*` ) jako zástupný symbol pro zbytek období. Aby bylo možné přidat operátor přípon, musí dotaz na předponu začínat alespoň jedním alfanumerickým znakem.

| Znak | Příklad | Využití |
|----------- |--------|-------|
| `*` | `lingui*` bude odpovídat "jazyku" nebo "linguini" | Hvězdička ( `*` ) představuje jeden nebo více znaků libovolné délky, ignorování velkých a malých písmen.  |

Podobně jako filtry, dotaz předpony hledá přesnou shodu. V takovém případě neexistuje žádné bodové hodnocení (všechny výsledky obdrží skóre hledání 1,0). Upozorňujeme, že dotazy s předponou můžou být pomalé, zejména pokud je index velký a předpona obsahuje malý počet znaků. Alternativní metodologie, jako je Edge n-gram, může provádět rychleji.

Jednoduchá syntaxe podporuje pouze porovnání předpon. U přípon nebo vpony, které odpovídají konci nebo polovině podmínky, použijte [pro hledání pomocí zástupných znaků úplnou syntaxi Lucene](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Operátory hledání uvozovacích znaků  

V jednoduché syntaxi operátory hledání obsahují tyto znaky: `+ | " ( ) ' \`  

Pokud je některý z těchto znaků součástí tokenu v indexu, vydejte ho pomocí prefixu s jedním zpětným lomítkem ( `\` ) v dotazu. Předpokládejme například, že jste použili vlastní analyzátor pro celou funkci tokenizace a váš index obsahuje řetězec "luxus + hotelu". Chcete-li získat přesnou shodu s tímto tokenem, vložte řídicí znak: `search=luxury\+hotel` .

Chcete-li zjednodušit věci z více typických případů, existují dvě výjimky z tohoto pravidla, kde není nutné uvozovací znaky:  

+ `-`Pokud se jedná o první znak za prázdným znakem, musí být znak operátoru bez řídicí sekvence. Pokud `-` se zobrazí uprostřed (například v `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), můžete přeskočit uvozovací znaky.

+ Operátor přípona `*` musí být uvozena řídicím znakem pouze v případě, že se jedná o poslední znak před prázdným znakem. Pokud se `*` zobrazí uprostřed (například v `4*4=16` ), nevyžaduje se žádné uvozovací znaky.

> [!NOTE]  
> Ve výchozím nastavení Standard analyzátor odstraní a rozdělí slova na spojovníkech, prázdných znaků, ampersandech a dalších znacích během [lexikální analýzy](search-lucene-query-architecture.md#stage-2-lexical-analysis). Pokud vyžadujete, aby v řetězci dotazu zůstaly speciální znaky, budete možná potřebovat analyzátor, který je zachovává v indexu. Mezi možnosti patří [analyzátory](index-add-language-analyzers.md)přirozeného jazyka od Microsoftu, které zachovává rozdělená slova nebo vlastní analyzátor pro složitější vzory. Další informace najdete v tématu [částečné výrazy, vzory a speciální znaky](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a vyhrazených znaků v adresách URL

Zajistěte, aby všechny nebezpečné a vyhrazené znaky byly kódované v adrese URL. Například # je nebezpečný znak, protože se jedná o identifikátor fragmentu nebo kotvy v adrese URL. Znak musí být kódovaný na, `%23` Pokud je použit v adrese URL. ' & ' a ' = ' jsou příklady rezervovaných znaků, které jsou vymezují parametry a určují hodnoty v Azure Kognitivní hledání. Další informace najdete v tématu [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Nezabezpečené znaky jsou ``" ` < > # % { } | \ ^ ~ [ ]`` . Vyhrazené znaky jsou `; / ? : @ = + &` .

## <a name="special-characters"></a>Speciální znaky

V některých případech můžete chtít vyhledat speciální znak, například ❤ emoji nebo symbol €. V takových případech se ujistěte, že analyzátor, který používáte, nefiltruje tyto znaky. Standardní analyzátor obchází mnoho speciálních znaků a vyloučí je z indexu.

Analyzátory, které budou tokenizovat speciální znaky, zahrnují analyzátor "prázdných", který bere v úvahu všechny sekvence znaků oddělené prázdnými znaky jako tokeny (takže řetězec "❤" by byl považován za token). Také analyzátor jazyka, jako je Microsoft English Analyzer ("en. Microsoft"), by měl jako token přebírat řetězec "€". Můžete [otestovat analyzátor](/rest/api/searchservice/test-analyzer) a zjistit, jaké tokeny generuje pro daný dotaz.

Při použití znaků Unicode zajistěte, aby byly symboly v adrese URL dotazu správně uvozeny řídicími znaky (například ❤ by používaly řídicí sekvenci `%E2%9D%A4+` ). Post provede tento převod automaticky.  

## <a name="precedence-grouping"></a>Priorita (seskupení)

Pomocí závorek lze vytvořit poddotazy, včetně operátorů v rámci příkazu kulatého závorky. Nástroj například `motel+(wifi|luxury)` vyhledá dokumenty obsahující termín "Motel" a buď "WiFi", nebo "luxus" (nebo obojí).

## <a name="query-size-limits"></a>Omezení velikosti dotazů

Pokud vaše aplikace generuje vyhledávací dotazy programově, doporučujeme ji navrhovat takovým způsobem, že negeneruje dotazy na neohraničenou velikost. 

+ Pro GET nesmí délka adresy URL překročit 8 KB.

+ U POST (a všech ostatních požadavků), kde tělo žádosti zahrnuje `search` a další parametry, jako jsou `filter` a `orderby` , je maximální velikost 16 MB, kde maximální počet klauzulí v `search` (výrazy oddělené a, nebo a tak dále) je 1024. Velikost jakéhokoli jednotlivého výrazu v dotazu je také omezena na přibližně 32 KB. Další informace najdete v tématu [omezení požadavků rozhraní API](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Další kroky

Pokud budete vytvářet dotazy programově, Projděte si [text fulltextového vyhledávání v Azure kognitivní hledání](search-lucene-query-architecture.md) , abyste pochopili fáze zpracování dotazů a důsledky analýzy textu.

Další informace o vytváření dotazů najdete v následujících článcích:

+ [Příklady dotazů pro jednoduché hledání](search-query-simple-examples.md)
+ [Příklady dotazů pro úplné hledání Lucene](search-query-lucene-examples.md)
+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/Search-Documents)
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů Filter a Select (OData)](query-odata-filter-orderby-syntax.md)