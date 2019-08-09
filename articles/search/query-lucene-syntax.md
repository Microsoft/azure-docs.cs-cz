---
title: Syntaxe dotazů Lucene – Azure Search
description: Odkaz na úplnou syntaxi Lucene, jak se používá u Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: e8e9b737676b2695b7b88430f59b0b0e79bc477a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883867"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Syntaxe dotazů Lucene v Azure Search
Můžete zapisovat dotazy na Azure Search na základě rozšířené syntaxe [analyzátoru dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) pro specializované formuláře dotazů: zástupný znak, přibližné vyhledávání, vyhledávání blízkých výrazů, regulární výrazy jsou několik příkladů. Většina syntaxe analyzátoru dotazů Lucene se [v Azure Search implementuje beze změny](search-lucene-query-architecture.md), s výjimkou *hledání rozsahů* , které jsou vytvořené ve Azure Search `$filter` prostřednictvím výrazů. 

## <a name="how-to-invoke-full-parsing"></a>Postup vyvolání úplné analýzy

Nastavte parametr `queryType` Search a určete, který analyzátor se má použít. Platné hodnoty zahrnují `simple|full`, s `simple` jako výchozí a `full` pro Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Příklad znázorňující úplnou syntaxi

Následující příklad vyhledá dokumenty v indexu pomocí syntaxe dotazů Lucene, zjevné v `queryType=full` parametru. Tento dotaz vrátí hotely, kde pole kategorie obsahuje výraz "rozpočet" a všechna hledaná pole obsahující frázi "nedávno renovated". Dokumenty obsahující frázi "nedávno renovated" jsou seřazené výše v důsledku hodnoty zvýšení termínu (3).  

`searchMode=all` Parametr je v tomto příkladu relevantní. Vždy, když jsou operátory na dotazu, je obecně nutné `searchMode=all` nastavit, aby bylo zajištěno, že *všechna* kritéria jsou shodná.

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

Další příklady naleznete v tématu [Příklady syntaxe dotazů Lucene pro vytváření dotazů v Azure Search](search-query-lucene-examples.md). Podrobnosti o tom, jak zadat úplný odkaz na parametry dotazu, najdete v tématu [REST API &#40;&#41;vyhledávání dokumentů Azure Search služby](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Search také podporuje [jednoduchou syntaxi dotazů](query-simple-syntax.md), jednoduchý a robustní dotazovací jazyk, který lze použít pro klíčové vyhledávání přímočarých klíčových slov.  

##  <a name="bkmk_syntax"></a>Základy syntaxe  
 Následující Základy syntaxe se vztahují na všechny dotazy, které používají syntaxi Lucene.  

### <a name="operator-evaluation-in-context"></a>Vyhodnocení operátoru v kontextu

Umístění Určuje, zda je symbol interpretován jako operátor nebo pouze jiný znak v řetězci.

Například v úplné syntaxi aplikace Lucene se znak tilda (~) používá pro hledání přibližných výsledků hledání a blízkosti. Po umístění za citovanou frází ~ vyvolá hledání blízkosti. Po umístění na konci podmínky ~ vyvolá hledání přibližné.

V rámci období, jako např. "Business ~ Analytika", není znak vyhodnocen jako operátor. V takovém případě, předpokládá se, že dotaz je dotaz na termín nebo frázi, [fulltextové vyhledávání](search-lucene-query-architecture.md) s [lexikální analýzou](search-lucene-query-architecture.md#stage-2-lexical-analysis) . ~ a přerušuje pojem "business ~ analytik" ve dvou obchodních nebo analytikích.

Výše uvedený příklad je vlnovkou (~), ale stejný princip platí pro každý operátor.

### <a name="escaping-special-characters"></a>Speciální znaky pro uvozovací znaky

 Speciální znaky musí být uvozeny řídicím znakem, aby je bylo možné použít jako součást textu hledání. Můžete je nasměrovat tak, že je napíšete\\pomocí zpětného lomítka (). Mezi speciální znaky, které je třeba řídicím znakem, patří následující:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Například pro řídicí znak escape použijte \\. \*

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a vyhrazených znaků v adresách URL

 Ujistěte se prosím, že všechny nebezpečné a rezervované znaky jsou v adrese URL zakódované. Například ' # ' je nebezpečný znak, protože se jedná o identifikátor fragement/kotvy v adrese URL. Znak musí být kódovaný na, `%23` Pokud je použit v adrese URL. ' & ' a ' = ' jsou příklady vyhrazených znaků při jejich oddělování a určení hodnot v Azure Search. Podívejte [se prosím na RFC1738: Adresa URL (Uniform Resource Locator)](https://www.ietf.org/rfc/rfc1738.txt) , kde najdete další podrobnosti.

 Nezabezpečené znaky ``" ` < > # % { } | \ ^ ~ [ ]``jsou. Vyhrazené znaky jsou `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operátory priority: seskupení a seskupení polí  
 Pomocí závorek lze vytvořit poddotazy, včetně operátorů v rámci příkazu kulatého závorky. Nástroj například `motel+(wifi||luxury)` vyhledá dokumenty obsahující termín "Motel" a buď "WiFi", nebo "luxus" (nebo obojí).

Seskupení polí je podobné, ale obor seskupení do jednoho pole. Například `hotelAmenities:(gym+(wifi||pool))` vyhledá pole "hotelAmenities" pro "posilovně" a "WiFi" nebo "posilovně" a "Pool".  

### <a name="searchmode-parameter-considerations"></a>Doporučení pro parametry SearchMode  
 Dopad `searchMode` na dotazy, jak je popsáno v tématu [Jednoduchá syntaxe dotazů v Azure Search](query-simple-syntax.md), platí stejně jako syntaxe dotazů Lucene. `searchMode` Konkrétně v souvislosti s operátorem NOT mohou vzniknout výsledky dotazů, které se mohou zdát neobvyklé, pokud nechcete, aby se projevily důsledky nastavení parametru. Pokud zachováte výchozí nastavení `searchMode=any`, a použijete operátor NOT, bude operace vypočítána jako akce nebo, což znamená, že "New York" NOT "Praha" vrací všechna města, která nejsou v Seattlu.  

##  <a name="bkmk_boolean"></a>Logické operátory (a, nebo, NOT) 
 Vždy zadejte text Boolean Operators (AND, OR, NOT) All CAPS.  

### <a name="or-operator-or-or-"></a>Or – `OR` operátor OR`||`

Operátor OR je znak svislého pruhu nebo svislé čáry. Například: `wifi || luxury` vyhledá dokumenty obsahující buď "WiFi", nebo "luxus" nebo obojí. Vzhledem k tomu, že nebo je výchozím operátorem spojení, můžete jej také opustit, `wifi luxury` což je `wifi || luxuery`ekvivalent.

### <a name="and-operator-and--or-"></a>And – `AND`operátor `&&` or`+`

Operátor AND je znak ampersand nebo znaménko plus. Například: `wifi && luxury` vyhledá dokumenty obsahující obě "WiFi" i "luxus". Znak plus (+) se používá pro požadované podmínky. Například `+wifi +luxury` stanoví, že oba výrazy se musí objevit někde v poli jediného dokumentu.


### <a name="not-operator-not--or--"></a>NOT – `NOT`operátor `!` nebo`-`

Operátor NOT je vykřičník nebo symbol mínus. Například: `wifi !luxury` vyhledá dokumenty, které mají termín "WiFi" nebo nemají "luxus". `searchMode` Možnost určuje, zda je výraz s operátorem NOT ANDed nebo ORed s ostatními podmínkami v dotazu v případě absence operátoru + nebo | |. Odvolání může být nastaveno na hodnotu `any`(výchozí) nebo `all`. `searchMode`

Použití `searchMode=any` zvyšuje počet odvolání dotazů zahrnutím více výsledků a ve výchozím nastavení je interpretováno jako "nebo ne". Například `wifi -luxury` se bude shodovat s dokumenty, které buď obsahují výraz *WiFi* , nebo ty, které neobsahují pojem *luxus.*

Použití `searchMode=all` zvyšuje přesnost dotazů zahrnutím menších výsledků a ve výchozím nastavení je interpretováno jako "a nikoli". Například `wifi -luxury` bude odpovídat dokumentům, které obsahují termín `wifi` a nesmí obsahovat podmínky `luxury`. To je pravděpodobně intuitivní chování operátoru-. Proto `searchMode=all` byste měli zvážit možnost přenášet `searchMode=any` , pokud chcete optimalizovat hledání přesnosti místo odvolání `-` a uživatelé často používají operátor v hledání.

##  <a name="bkmk_querysizelimits"></a>Omezení velikosti dotazu  
 Existuje omezení velikosti dotazů, které lze odeslat do Azure Search. Konkrétně můžete mít maximálně 1024 klauzulí (výrazy oddělené a, nebo a tak dále). Velikost jakéhokoli jednotlivého výrazu v dotazu je také omezena na přibližně 32 KB. Pokud vaše aplikace generuje vyhledávací dotazy programově, doporučujeme ji navrhovat takovým způsobem, že negeneruje dotazy na neohraničenou velikost.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Vyhodnocování zástupných znaků a dotazů Regex
 Azure Search používá pro textové dotazy hodnocení založené na kmitočtech ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)). U dotazů se zástupným znakem a regulárních výrazů, u kterých může být obor pojmů potenciálně rozsáhlý, se ale četnost frekvencí ignoruje, aby nedocházelo k tomu, aby se zabránilo vyřazení shody směrem k Všechny shody jsou zpracovány stejně pro hledání pomocí zástupných znaků a Regex.

##  <a name="bkmk_fields"></a>Hledání v poli  
Můžete definovat operaci vyhledávání v poli s `fieldName:searchExpression` syntaxí, kde výraz vyhledávání může být jedno slovo nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:  

- Žánr: nehistorie Jazz  

- interprety:("míle Davis" "Jan Coltrane")

Nezapomeňte vložit více řetězců v uvozovkách, pokud chcete, aby oba řetězce byly vyhodnoceny jako jediná entita. v tomto případě vyhledávání dvou různých umělců v `artists` poli.  

Pole zadané v `fieldName:searchExpression` musí `searchable` být pole.  Podrobnosti o tom, jak se v definicích polí používají atributy indexu, najdete v tématu [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) .  

> [!NOTE]
> Při použití vyhledávacích výrazů v poli není nutné používat parametr, protože každý `searchFields` výraz vyhledávání v poli má explicitně zadaný název pole. Tento parametr však lze použít i v `searchFields` případě, že chcete spustit dotaz, ve kterém jsou některé části vymezeny na konkrétní pole, a zbytek by se mohl vztahovat na několik polí. Dotaz `search=genre:jazz NOT history&searchFields=description` by se například shodoval `jazz` pouze `genre` `NOT history` s`description` polem, zatímco by se shodoval s polem. Název pole, který je `fieldName:searchExpression` k dispozici vždy, `searchFields` má přednost před parametrem, což znamená, že `searchFields` v tomto příkladu není nutné `genre` zahrnout do parametru.

##  <a name="bkmk_fuzzy"></a>Hledání přibližných výsledků  
 Hledání přibližných výsledků vyhledává shody v rámci výrazů, které mají podobnou konstrukci. Pro každou [dokumentaci Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)je hledání přibližných výsledků založeno na [Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Přibližná hledání mohou rozšiřovat výraz až do maximálního počtu 50 podmínek, které splňují kritéria vzdálenosti. 

 Chcete-li provést hledání přibližné hodnoty, použijte symbol tilda "~" na konci jednoho slova s volitelným parametrem, číslo mezi 0 a 2 (výchozí), které určuje vzdálenost úprav. Například "Blue ~" nebo "Blue ~ 1" vrátí "Blue", "blues" a "Glue".

 Hledání přibližných hodnot lze použít pouze pro termíny, nikoli pro fráze, ale můžete přidat vlnovku k jednotlivým termínům jednotlivě v názvu nebo frázi s více částmi. Například "Unviersty ~ ~" Wshington ~ "by se shodovala s" University of Washington ".
 

##  <a name="bkmk_proximity"></a>Hledání blízkosti  
 Hledání v blízkosti se používá k vyhledání podmínek, které jsou poblíž sebe v dokumentu. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti. Například `"hotel airport"~5` nalezne termíny "Hotel" a "letiště" do 5 slov navzájem v dokumentu.  


##  <a name="bkmk_termboost"></a>Zvyšování termínů  
 Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. To se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní výrazy.  

Následující příklad pomáhá ilustrovat rozdíly. Předpokládejme, že existuje profil vyhodnocování, který zvyšuje shodu v určitém poli, řekněme jako *Žánr* v [musicstoreindex příkladu](index-add-scoring-profiles.md#bkmk_ex). Za účelem dalšího zvýšení úrovně hledaného výrazu, který je vyšší než jiné, se dá použít zvyšování skóre termínu. Například `rock^2 electronic` bude zvyšovat dokumenty, které obsahují hledané výrazy v poli žánru, vyšší než jiná hledaná pole v indexu. V důsledku hodnoty zvýšení skóre (2) jsou dokumenty, které obsahují hledaný výraz *Rock* , řazeny vyšší než jiné hledané podmínky *elektronického* výrazu.  

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

## <a name="see-also"></a>Viz také:  

+ [Hledat dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe výrazů OData pro filtry a řazení](query-odata-filter-orderby-syntax.md)   
+ [Jednoduchá syntaxe dotazů v Azure Search](query-simple-syntax.md)   
