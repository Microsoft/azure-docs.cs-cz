---
title: Syntaxe dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Odkaz na úplnou syntaxi dotazu Lucene, jak je použit v Azure Cognitive Search pro zástupné, přibližné hledání, RegEx a další pokročilé konstrukce dotazů.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: 1392f69bea09996e46ad4c112474f9067ff5a63d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656920"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Syntaxe dotazu Lucene v Azure Cognitive Search

Můžete psát dotazy proti Azure Cognitive Search na základě bohaté [syntaxe Lucene dotaz parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) pro formuláře specializované dotaz: zástupný znak, přibližné vyhledávání, hledání bez kontaktní místo, regulární výrazy jsou několik příkladů. Velká část syntaxe analyzátoru dotazů Lucene je [implementována neporušená v Azure Cognitive Search](search-lucene-query-architecture.md), s výjimkou hledání *rozsahu,* které jsou vytvořeny v Azure Cognitive Search prostřednictvím `$filter` výrazů. 

> [!NOTE]
> Úplná syntaxe Lucene se používá pro výrazy dotazu předané v parametru **vyhledávání** rozhraní API [hledání dokumentů,](https://docs.microsoft.com/rest/api/searchservice/search-documents) které nelze zaměňovat se [syntaxí OData](query-odata-filter-orderby-syntax.md) použitou pro [$filter](search-filters.md) parametr tohoto rozhraní API. Tyto různé syntaxe mají vlastní pravidla pro vytváření dotazů, úniku řetězce a tak dále.

## <a name="how-to-invoke-full-parsing"></a>Jak vyvolat úplnou analýzu

`queryType` Nastavte vyhledávací parametr a určete, který analyzátor má být používán. Platné hodnoty `simple|full`zahrnují `simple` , s `full` jako výchozí a pro Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Příklad zobrazení úplné syntaxe

Následující příklad vyhledá dokumenty v indexu pomocí syntaxe lucene dotazu, evidentní v parametru. `queryType=full` Tento dotaz vrátí hotely, kde pole kategorie obsahuje termín "rozpočet" a všechna prohledávatelná pole obsahující frázi "nedávno zrekonstruovaný". Dokumenty obsahující frázi "nedávno zrekonstruovaný" jsou hodnoceny výše v důsledku termínu hodnota zvýšení (3).  

Parametr `searchMode=all` je relevantní v tomto příkladu. Vždy, když jsou operátory na `searchMode=all` dotaz, měli byste obecně nastavit, aby bylo zajištěno, že *všechna* kritéria je spárována.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Případně použijte POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Další příklady najdete v [tématu Lucene dotaz syntaxe příklady pro vytváření dotazů v Azure Cognitive Search](search-query-lucene-examples.md). Podrobnosti o určení úplného kontingentu parametrů dotazu najdete [v tématu Hledání dokumentů &#40;azure cognitive search rozhraní REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Azure Cognitive Search také podporuje [jednoduchou syntaxi dotazu](query-simple-syntax.md), jednoduchý a robustní dotazovací jazyk, který lze použít pro jednoduché vyhledávání klíčových slov.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Základy syntaxe  
 Následující základy syntaxe platí pro všechny dotazy, které používají syntaxi Lucene.  

### <a name="operator-evaluation-in-context"></a>Hodnocení operátora v kontextu

Umístění určuje, zda je symbol interpretován jako operátor nebo jen jiný znak v řetězci.

Například v Lucene úplné syntaxi, tilda (~) se používá pro přibližné vyhledávání a bezkontaktní vyhledávání. Když je umístěn za citované fráze, ~ vyvolá blízkost vyhledávání. Když je umístěn na konci termínu, ~ vyvolá fuzzy vyhledávání.

V rámci termínu, například "business~analyst", znak není vyhodnocen jako operátor. V tomto případě za předpokladu, že dotaz je termín nebo frázový dotaz, [fulltextové vyhledávání](search-lucene-query-architecture.md) s [lexikální analýzou](search-lucene-query-architecture.md#stage-2-lexical-analysis) proužky z ~ a přestávky termín "business ~ analytik" ve dvou: obchodní nebo analytik.

Výše uvedený příklad je vlnovka (~), ale stejný princip platí pro každý operátor.

### <a name="escaping-special-characters"></a>Unikající speciální znaky

 Speciální znaky musí být uvozeny, aby mohly být použity jako součást hledaného textu. Můžete jim uniknout předponou pomocí\\zpětného lomítka ( ). Speciální znaky, které je třeba uvozevat patří následující:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Chcete-li například uniknout zástupný \\ \*znak, použijte .

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a rezervovaných znaků v adresách URL

 Zkontrolujte, zda jsou všechny nebezpečné a vyhrazené znaky zakódovány v adrese URL. Například '#' je nebezpečný znak, protože se jedná o identifikátor fragement/anchor v adrese URL. Znak musí být zakódován, `%23` pokud je použit v adrese URL. '&' a '=' jsou příklady vyhrazených znaků, protože vymezují parametry a určují hodnoty v Azure Cognitive Search. Další podrobnosti naleznete v tématu [RFC1738: Url adresy url jednotného zdroje.](https://www.ietf.org/rfc/rfc1738.txt)

 Nebezpečné znaky ``" ` < > # % { } | \ ^ ~ [ ]``jsou . Vyhrazené znaky `; / ? : @ = + &`jsou .

### <a name="precedence-operators-grouping-and-field-grouping"></a>Operátory s prioritami: seskupení a seskupení polí  
 Závorky můžete použít k vytvoření poddotazů, včetně operátorů v příkazu závorky. Například `motel+(wifi||luxury)` bude hledat dokumenty obsahující termín "motel" a buď "wifi" nebo "luxusní" (nebo obojí).

Seskupení polí je podobné, ale obory seskupení do jednoho pole. Například `hotelAmenities:(gym+(wifi||pool))` vyhledá v poli "hotelAmenities" pro "tělocvična" a "wifi", nebo "tělocvična" a "bazén".  

### <a name="searchmode-parameter-considerations"></a>Aspekty parametru SearchMode  
 Dopad `searchMode` na dotazy, jak je popsáno v [syntaxi jednoduchého dotazu v Azure Cognitive Search](query-simple-syntax.md), platí stejně pro syntaxi dotazu Lucene. Konkrétně ve `searchMode` spojení s OPERÁTORY NOT může mít za následek výsledky dotazu, které se mohou zdát neobvyklé, pokud nejste jasné, o důsledcích, jak nastavit parametr. Pokud zachováte `searchMode=any`výchozí operátor a použijete operátor NOT, bude operace vypočítána jako akce OR, takže "New York" NOT "Seattle" vrátí všechna města, která nejsou seattle.  

##  <a name="boolean-operators-and-or-not"></a><a name="bkmk_boolean"></a>Logické operátory (AND, OR, NOT) 
 Vždy zadejte textové logické operátory (AND, OR, NOT) ve všech písmenech a).  

### <a name="or-operator-or-or-"></a>Operátor `OR` or nebo`||`

Operátor OR je svislý znak pruhu nebo potrubí. Například: `wifi || luxury` bude hledat dokumenty obsahující buď "wifi" nebo "luxusní" nebo obojí. Vzhledem k tomu, že operátor OR je výchozí `wifi luxury` operátor konjunkce, můžete jej také vynechat, což odpovídá `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>operátor `AND`A `&&` nebo`+`

Operátor AND je ampersand nebo znaménko plus. Například: `wifi && luxury` bude hledat dokumenty obsahující jak "wifi" a "luxusní". Znak plus (+) se používá pro požadované termíny. Například `+wifi +luxury` stanoví, že oba termíny musí být uvedeny někde v poli jednoho dokumentu.


### <a name="not-operator-not--or--"></a>NE `NOT`obsluha , `!` nebo`-`

Operátor NOT je vykřičník nebo znaménko mínus. Například: `wifi !luxury` bude hledat dokumenty, které mají termín "wifi" a / nebo nemají "luxus". Tato `searchMode` možnost určuje, zda je termín s operátorem NOT anded nebo ORed s ostatními termíny v dotazu v případě nepřítomnosti + nebo || Operátor. Odvolání, `searchMode` které lze `any`nastavit buď `all`(výchozí) nebo .

Použití `searchMode=any` zvyšuje vyvolání dotazů zahrnutím dalších výsledků a ve výchozím nastavení - bude interpretováno jako "NEBO NE". Například `wifi -luxury` bude odpovídat dokumenty, které buď obsahují termín *wifi,* nebo ty, které neobsahují termín *luxus.*

Použití `searchMode=all` zvyšuje přesnost dotazů zahrnutím méně výsledků a ve výchozím nastavení - bude interpretovánjako "A ne". Bude například `wifi -luxury` odpovídat dokumentům, které obsahují termín `wifi` a neobsahují termín `luxury`. To je pravděpodobně intuitivnější chování pro - operátor. Proto byste měli `searchMode=all` zvážit `searchMode=any` výběr znovu, pokud chcete optimalizovat vyhledávání pro přesnost namísto odvolání *a* uživatelé často používají `-` operátor při vyhledávání.

##  <a name="query-size-limitations"></a><a name="bkmk_querysizelimits"></a>Omezení velikosti dotazu  
 Velikost dotazů, které můžete odeslat do Azure Cognitive Search, je omezena. Konkrétně můžete mít maximálně 1024 klauzule (výrazy oddělené AND, OR a tak dále). K dispozici je také limit přibližně 32 KB na velikost jednotlivých termín ů v dotazu. Pokud aplikace generuje vyhledávací dotazy programově, doporučujeme je navrhnout tak, aby negenerovaly dotazy neomezené velikosti.  

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Vyhodnocování dotazů se zástupnými dotazy a dotazy regulárních výrazů
 Azure Cognitive Search používá pro textové dotazy bodové hodnocení založené na frekvenci ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)). Však pro dotazy zástupné symboly a regulární výraz, kde rozsah termínů může být potenciálně široká, faktor frekvence je ignorována zabránit pořadí z kreslení směrem k zápasy z vzácnější termíny. Všechny shody jsou považovány za stejně pro vyhledávání zástupných a regulárních výrazů.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Vyhledávání v poli  
Můžete definovat operaci vyhledávání v `fieldName:searchExpression` poli se syntaxí, kde vyhledávací výraz může být jedno slovo nebo frázi nebo složitější výraz v závorcích, volitelně s logickými operátory. Některé příklady zahrnují následující:  

- žánr:jazz NOT historie  

- umělci:("Miles Davis" "John Coltrane")

Nezapomeňte vložit více řetězců do uvozovek, pokud chcete, aby byly oba řetězce vyhodnoceny jako `artists` jedna entita, v tomto případě hledání dvou odlišných umělců v poli.  

Pole zadané `fieldName:searchExpression` v poli `searchable` musí být pole.  Podrobnosti o tom, jak se v definicích polí používají atributy rejstříku, najdete v tématu [Vytvořit index.](https://docs.microsoft.com/rest/api/searchservice/create-index)  

> [!NOTE]
> Při použití vyhledávacích výrazů v poli není `searchFields` nutné použít parametr, protože každý vyhledávací výraz s polem má explicitně zadaný název pole. `searchFields` Parametr však můžete použít i v případě, že chcete spustit dotaz, kde jsou některé části vymezeny na určité pole, a zbytek může být aplikován na několik polí. `search=genre:jazz NOT history&searchFields=description` Dotaz `jazz` by se například shodoval `genre` pouze s polem, zatímco by `NOT history` odpovídal `description` poli. Název pole zadaný `fieldName:searchExpression` v vždy má `searchFields` přednost před parametrem, což je důvod, `genre` proč `searchFields` v tomto příkladu nemusíme zahrnout do parametru.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Přibližné hledání  
 Přibližné hledání najde shody v termínech, které mají podobnou konstrukci. Podle [lucene dokumentace](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy vyhledávání jsou založeny na [Damerau-Levenshtein Vzdálenost](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Přibližná hledání mohou rozšířit termín až na maximálně 50 termínů, které splňují kritéria vzdálenosti. 

 Chcete-li provést přibližné vyhledávání, použijte symbol vlnovky "~" na konci jednoho slova s volitelným parametrem, číslem mezi 0 a 2 (výchozí), který určuje vzdálenost úprav. Například "modrá~" nebo "modrá~1" vrátí "modrá", "modrá" a "lepidlo".

 Přibližné vyhledávání lze použít pouze u výrazů, nikoli frází, ale můžete připojit vlnovku ke každému výrazu jednotlivě ve vícedílném názvu nebo frázi. Například "Unviersty~ z ~ "Wshington~" by odpovídala na "University of Washington".
 

##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Bezkontaktní vyhledávání  
 Hledání bezkontaktní chodse se používá k vyhledání termínů, které jsou v dokumentu blízko sebe. Na konec fráze vložte symbol vlnovky "~", za nímž následuje počet slov, která vytvářejí hranici přiblížení. Například, `"hotel airport"~5` najde termíny "hotel" a "letiště" do 5 slov od sebe v dokumentu.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>Podpora termínů  
 Zesílení termínu odkazuje na hodnocení dokumentu vyšší, pokud obsahuje posílený termín, vzhledem k dokumentům, které neobsahují termín. To se liší od profilů hodnocení v tom, že profily hodnocení zvyšují určitá pole, nikoli konkrétní termíny.  

Následující příklad pomáhá ilustrovat rozdíly. Předpokládejme, že je bodování profil, který zvyšuje zápasy v určitém poli, řekněme *žánr* v [musicstoreindex příklad](index-add-scoring-profiles.md#bkmk_ex). Podpora termínu by mohla být použita k dalšímu posílení některých hledaných výrazů vyšší než ostatní. Například `rock^2 electronic` zvýší dokumenty, které obsahují hledané termíny v poli žánru vyšší než ostatní prohledávatelná pole v indexu. Dále dokumenty, které obsahují vyhledávací termín *rock,* budou hodnoceny výše než ostatní hledané *výrazy elektronické* v důsledku hodnoty zvýšení termínu (2).  

 Chcete-li zvýšit termín použijte stříšku, "^", symbol s faktorem zvýšení (číslo) na konci termínu, který hledáte. Můžete také zvýšit fráze. Čím vyšší je faktor zvýšení, tím relevantnější bude termín relativní k jiným vyhledávacím dotazům. Ve výchozím nastavení je faktor zesílení 1. I když faktor zvýšení musí být pozitivní, může být menší než 1 (například 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Hledání regulárních výrazů  
 Hledání regulárních výrazů vyhledá shodu na základě obsahu mezi lomítkem "/", jak je popsáno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Chcete-li například vyhledat dokumenty obsahující "motel" `/[mh]otel/`nebo "hotel", zadejte . Hledání regulárních výrazů je porovnáno s jednotlivými slovy.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Hledání zástupnými znaky  
 Obecně rozpoznanou syntaxi můžete použít pro více (*) nebo jednoznakové zástupné vyhledávání. Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jediným termínem a nikoli frází.

Hledání předpony také používá znak`*`hvězdička ( ). Například výraz dotazu `search=note*` vrátí "notebook" nebo "poznámkový blok". Úplná syntaxe Lucene není vyžadována pro vyhledávání předpon. Jednoduchá syntaxe podporuje tento scénář.

Vyhledávání přípon, `*` `?` kde nebo předchází řetězec, vyžaduje úplnou syntaxi Lucene a regulární výraz (nelze použít * nebo ? jako první znak vyhledávání). Vzhledem k termínu "alfanumerický"`search=/.*numeric.*/`výraz dotazu ( ) najde shodu.

> [!NOTE]  
> Během analýzy dotazů jsou dotazy, které jsou formulovány jako předpona, přípona, zástupný znak nebo regulární výrazy, předány stromu dotazů tak, aby se obešly [lexikální analýzou](search-lucene-query-architecture.md#stage-2-lexical-analysis). Shody budou nalezeny pouze v případě, že index obsahuje řetězce ve formátu, který určuje dotaz. Ve většině případů budete potřebovat alternativní analyzátor během indexování, který zachová integritu řetězce tak, aby částečné termín a porovnávání vzorů úspěšné. Další informace najdete [v tématu Částečné hledání termínů v dotazech Azure Cognitive Search](search-query-partial-matching.md).

## <a name="see-also"></a>Viz také  

+ [Hledat dokumenty](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe výrazu OData pro filtry a řazení](query-odata-filter-orderby-syntax.md)   
+ [Jednoduchá syntaxe dotazu v Azure Cognitive Search](query-simple-syntax.md)   
