---
title: Syntaxe dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Odkaz na úplnou syntaxi dotazů Lucene, jak se používá v Azure Kognitivní hledání pro zástupné znaky, přibližné vyhledávání, regulární výrazy a další konstrukce pokročilého dotazu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 0bb8474b30c05e21a62ded1fa2cb8a6df8e4e321
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112182"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Syntaxe dotazů Lucene v Azure Kognitivní hledání

Dotazy na Azure Kognitivní hledání můžete zapisovat na základě syntaxe s bohatou syntaxí [analyzátoru dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) pro specializované formuláře dotazů: zástupné vyhledávání, přibližné vyhledávání, vyhledávání blízkých výrazů, regulární výrazy jsou několik příkladů. Většina syntaxe analyzátoru dotazů Lucene se [v azure kognitivní hledání implementuje beze změny](search-lucene-query-architecture.md), s výjimkou *hledání rozsahů* , které jsou vytvořené v Azure kognitivní hledání prostřednictvím `$filter` výrazů. 

## <a name="how-to-invoke-full-parsing"></a>Postup vyvolání úplné analýzy

Nastavením parametru `queryType` Search určete, který analyzátor chcete použít. Platné hodnoty zahrnují `simple|full`, s `simple` jako výchozí a `full` pro Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Příklad znázorňující úplnou syntaxi

Následující příklad vyhledá dokumenty v indexu pomocí syntaxe dotazu Lucene, která je zřejmá v parametru `queryType=full`. Tento dotaz vrátí hotely, kde pole kategorie obsahuje výraz "rozpočet" a všechna hledaná pole obsahující frázi "nedávno renovated". Dokumenty obsahující frázi "nedávno renovated" jsou seřazené výše v důsledku hodnoty zvýšení termínu (3).  

V tomto příkladu je relevantní parametr `searchMode=all`. Vždy, když jsou v dotazu operátory, byste měli obecně nastavit `searchMode=all`, aby se zajistilo, že *všechna* kritéria jsou shodná.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternativně můžete použít POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Další příklady najdete v tématu [Příklady syntaxe dotazů Lucene pro vytváření dotazů v Azure kognitivní hledání](search-query-lucene-examples.md). Podrobnosti o tom, jak zadat úplný odkaz na parametry dotazu, najdete v článku [hledání &#40;dokumentů&#41;Azure kognitivní hledání REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Kognitivní hledání podporuje také [jednoduchou syntaxi dotazů](query-simple-syntax.md), jednoduchý a robustní dotazovací jazyk, který je možné použít pro jednoznačné hledání klíčových slov.  

##  <a name="bkmk_syntax"></a>Základy syntaxe  
 Následující Základy syntaxe se vztahují na všechny dotazy, které používají syntaxi Lucene.  

### <a name="operator-evaluation-in-context"></a>Vyhodnocení operátoru v kontextu

Umístění Určuje, zda je symbol interpretován jako operátor nebo pouze jiný znak v řetězci.

Například v úplné syntaxi aplikace Lucene se znak tilda (~) používá pro hledání přibližných výsledků hledání a blízkosti. Po umístění za citovanou frází ~ vyvolá hledání blízkosti. Po umístění na konci podmínky ~ vyvolá hledání přibližné.

V rámci období, jako např. "Business ~ Analytika", není znak vyhodnocen jako operátor. V takovém případě, předpokládá se, že dotaz je dotaz na termín nebo frázi, [fulltextové vyhledávání](search-lucene-query-architecture.md) s [lexikální analýzou](search-lucene-query-architecture.md#stage-2-lexical-analysis) . ~ a přerušuje pojem "business ~ analytik" ve dvou obchodních nebo analytikích.

Výše uvedený příklad je vlnovkou (~), ale stejný princip platí pro každý operátor.

### <a name="escaping-special-characters"></a>Speciální znaky pro uvozovací znaky

 Speciální znaky musí být uvozeny řídicím znakem, aby je bylo možné použít jako součást textu hledání. Můžete je řídicím znakem přesměrovat pomocí zpětného lomítka (\\). Mezi speciální znaky, které je třeba řídicím znakem, patří následující:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Chcete-li například řídicí znak escape zadat jako zástupný znak, použijte \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a vyhrazených znaků v adresách URL

 Ujistěte se prosím, že všechny nebezpečné a rezervované znaky jsou v adrese URL zakódované. Například ' # ' je nebezpečný znak, protože se jedná o identifikátor fragement/kotvy v adrese URL. Znak musí být kódovaný, aby `%23`, pokud se používá v adrese URL. ' & ' a ' = ' jsou příklady rezervovaných znaků, které jsou vymezují parametry a určují hodnoty v Azure Kognitivní hledání. Další podrobnosti najdete v tématu [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

 Nezabezpečené znaky jsou ``" ` < > # % { } | \ ^ ~ [ ]``. Vyhrazené znaky jsou `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operátory priority: seskupení a seskupení polí  
 Pomocí závorek lze vytvořit poddotazy, včetně operátorů v rámci příkazu kulatého závorky. `motel+(wifi||luxury)` například vyhledá dokumenty obsahující termín "Motel" a buď "WiFi", nebo "luxus" (nebo obojí).

Seskupení polí je podobné, ale obor seskupení do jednoho pole. `hotelAmenities:(gym+(wifi||pool))` například vyhledá pole "hotelAmenities" pro "posilovně" a "WiFi" nebo "posilovně" a "Pool".  

### <a name="searchmode-parameter-considerations"></a>Doporučení pro parametry SearchMode  
 Dopad `searchMode` na dotazy, jak je popsáno v tématu [Jednoduchá syntaxe dotazů ve službě Azure kognitivní hledání](query-simple-syntax.md), platí stejně jako syntaxe dotazů Lucene. Konkrétně `searchMode` ve spojení s operátorem NOT (neobsluha) může vést k tomu, že výsledky dotazu, které se mohou zdát neobvyklé, pokud nebudete mít jasné vliv na nastavení parametru. Pokud zachováte výchozí `searchMode=any`a použijete operátor NOT, bude operace vypočítána jako akce nebo, což znamená, že "New York" NOT "Praha" vrátí všechna města, která nejsou v Seattlu.  

##  <a name="bkmk_boolean"></a>Logické operátory (a, nebo, NOT) 
 Vždy zadejte text Boolean Operators (AND, OR, NOT) All CAPS.  

### <a name="or-operator-or-or-"></a>Operátor OR `OR` nebo `||`

Operátor OR je znak svislého pruhu nebo svislé čáry. Například: `wifi || luxury` vyhledá dokumenty obsahující buď "WiFi", nebo "luxus" nebo obojí. Vzhledem k tomu, že nebo je výchozím operátorem spojení, můžete ho také opustit, například `wifi luxury` je ekvivalentem `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>AND – operátor `AND`, `&&` nebo `+`

Operátor AND je znak ampersand nebo znaménko plus. Například: `wifi && luxury` vyhledá dokumenty obsahující obě "WiFi" i "luxus". Znak plus (+) se používá pro požadované podmínky. Například `+wifi +luxury` stanoví, že oba výrazy se musí objevit někde v poli jednoho dokumentu.


### <a name="not-operator-not--or--"></a>Operátor NOT `NOT`, `!` nebo `-`

Operátor NOT je vykřičník nebo symbol mínus. Například: `wifi !luxury` vyhledá dokumenty, které mají termín "WiFi" nebo nemají "luxus". Možnost `searchMode` určuje, zda je výraz s operátorem NOT ANDed nebo ORed s ostatními podmínkami v dotazu při absenci a + nebo | | podnikatel. Odvolání tohoto `searchMode` lze nastavit buď na `any`(výchozí), nebo na `all`.

Použití `searchMode=any` zvyšuje navracení dotazů zahrnutím více výsledků a ve výchozím nastavení je interpretováno jako "nebo ne". Například `wifi -luxury` budou odpovídat dokumentům, které buď obsahují výraz *WiFi* nebo ty, které neobsahují pojem *luxus.*

Použití `searchMode=all` zvyšuje přesnost dotazů zahrnutím menšího počtu výsledků a ve výchozím nastavení je interpretováno jako "a ne". Například `wifi -luxury` budou odpovídat dokumentům, které obsahují termín `wifi` a neobsahují termín `luxury`. To je pravděpodobně intuitivní chování operátoru-. Proto byste měli zvážit možnost `searchMode=all` přes `searchMode=any`, pokud chcete optimalizovat hledání přesnosti místo odvolání *a* uživatelé často používají `-` operátora v hledání.

##  <a name="bkmk_querysizelimits"></a>Omezení velikosti dotazu  
 Existuje omezení velikosti dotazů, které můžete odeslat do služby Azure Kognitivní hledání. Konkrétně můžete mít maximálně 1024 klauzulí (výrazy oddělené a, nebo a tak dále). Velikost jakéhokoli jednotlivého výrazu v dotazu je také omezena na přibližně 32 KB. Pokud vaše aplikace generuje vyhledávací dotazy programově, doporučujeme ji navrhovat takovým způsobem, že negeneruje dotazy na neohraničenou velikost.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Vyhodnocování zástupných znaků a dotazů Regex
 Azure Kognitivní hledání používá pro textové dotazy hodnocení založené na kmitočtech ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)). U dotazů se zástupným znakem a regulárních výrazů, u kterých může být obor pojmů potenciálně rozsáhlý, se ale četnost frekvencí ignoruje, aby nedocházelo k tomu, aby se zabránilo vyřazení shody směrem k Všechny shody jsou zpracovány stejně pro hledání pomocí zástupných znaků a Regex.

##  <a name="bkmk_fields"></a>Hledání v poli  
Můžete definovat operaci vyhledávání v terénu pomocí syntaxe `fieldName:searchExpression`, kde výraz hledání může být jedno slovo nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:  

- Žánr: nehistorie Jazz  

- interprety:("míle Davis" "Jan Coltrane")

Nezapomeňte vložit více řetězců v uvozovkách, pokud chcete, aby oba řetězce byly vyhodnoceny jako jediná entita. v tomto případě vyhledávání dvou různých umělců v poli `artists`.  

Pole zadané v `fieldName:searchExpression` musí být pole `searchable`.  Podrobnosti o tom, jak se v definicích polí používají atributy indexu, najdete v tématu [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) .  

> [!NOTE]
> Při použití vyhledávacích výrazů v poli není nutné používat parametr `searchFields`, protože každý výraz pro hledání v poli má explicitně zadaný název pole. Můžete však i nadále používat parametr `searchFields`, pokud chcete spustit dotaz, ve kterém jsou některé části vymezeny na konkrétní pole, a zbytek by se mohl vztahovat na několik polí. Dotaz `search=genre:jazz NOT history&searchFields=description` by se například shodoval s `jazz` pouze v poli `genre`, zatímco by odpovídal `NOT history` pole `description`. Název pole zadaný v `fieldName:searchExpression` vždy má přednost před parametrem `searchFields`, což znamená, že v tomto příkladu není nutné do parametru `searchFields` zahrnout `genre`.

##  <a name="bkmk_fuzzy"></a>Hledání přibližných výsledků  
 Hledání přibližných výsledků vyhledává shody v rámci výrazů, které mají podobnou konstrukci. Pro každou [dokumentaci Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)je hledání přibližných výsledků založeno na [Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Přibližná hledání mohou rozšiřovat výraz až do maximálního počtu 50 podmínek, které splňují kritéria vzdálenosti. 

 Chcete-li provést hledání přibližné hodnoty, použijte symbol tilda "~" na konci jednoho slova s volitelným parametrem, číslo mezi 0 a 2 (výchozí), které určuje vzdálenost úprav. Například "Blue ~" nebo "Blue ~ 1" vrátí "Blue", "blues" a "Glue".

 Hledání přibližných hodnot lze použít pouze pro termíny, nikoli pro fráze, ale můžete přidat vlnovku k jednotlivým termínům jednotlivě v názvu nebo frázi s více částmi. Například "Unviersty ~ ~" Wshington ~ "by se shodovala s" University of Washington ".
 

##  <a name="bkmk_proximity"></a>Hledání blízkosti  
 Hledání v blízkosti se používá k vyhledání podmínek, které jsou poblíž sebe v dokumentu. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti. `"hotel airport"~5` například vyhledá termíny "Hotel" a "letiště" do 5 slov navzájem v dokumentu.  


##  <a name="bkmk_termboost"></a>Zvyšování termínů  
 Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. To se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní výrazy.  

Následující příklad pomáhá ilustrovat rozdíly. Předpokládejme, že existuje profil vyhodnocování, který zvyšuje shodu v určitém poli, řekněme jako *Žánr* v [musicstoreindex příkladu](index-add-scoring-profiles.md#bkmk_ex). Za účelem dalšího zvýšení úrovně hledaného výrazu, který je vyšší než jiné, se dá použít zvyšování skóre termínu. `rock^2 electronic` například vylepší dokumenty, které obsahují hledané výrazy v poli žánru vyšší než ostatní hledaná pole v indexu. V důsledku hodnoty zvýšení skóre (2) jsou dokumenty, které obsahují hledaný výraz *Rock* , řazeny vyšší než jiné hledané podmínky *elektronického* výrazu.  

 Chcete-li zvýšit pojem, použijte blikající kurzor "^", symbol s faktorem zvýšení (číslo) na konci hledaného výrazu. Můžete také posílit fráze. Čím vyšší je faktor zvýšení, tím důležitější je, že výraz bude relativní vzhledem k ostatním hledaným podmínkám. Ve výchozím nastavení je faktor zvýšení 1. I když faktor zvýšení musí být kladný, může být menší než 1 (například 0,20).  

##  <a name="bkmk_regex"></a>Hledání regulárních výrazů  
 Hledání regulárního výrazu vyhledá shodu na základě obsahu mezi lomítky "/", jak je uvedeno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Pokud například chcete najít dokumenty obsahující "Motel" nebo "Hotel", zadejte `/[mh]otel/`.  Hledání regulárních výrazů se shoduje s jedním slovem.   

##  <a name="bkmk_wildcard"></a>Hledání pomocí zástupných znaků  
 Můžete použít obecně rozpoznanou syntaxi pro hledání zástupných znaků (*) nebo Single (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jedním výrazem a nikoli frází.  

 Pokud například chcete najít dokumenty obsahující slova s předponou "Note", jako je například "Poznámkový blok" nebo "Notepad", zadejte "Note *".  

> [!NOTE]  
>  Nemůžete použít * ani? symbol jako první znak hledání.  
>  U vyhledávacích dotazů se zástupnými znaky se neprovede žádná analýza textu. V době dotazu jsou výrazy se zástupnými znaky porovnány s analyzovanými podmínkami v indexu vyhledávání a rozbaleny.

## <a name="see-also"></a>Viz také  

+ [Hledat dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe výrazů OData pro filtry a řazení](query-odata-filter-orderby-syntax.md)   
+ [Jednoduchá syntaxe dotazů v Azure Kognitivní hledání](query-simple-syntax.md)   
