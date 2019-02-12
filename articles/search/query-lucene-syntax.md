---
title: Syntaxe dotazů Lucene – Azure Search
description: Referenční informace pro úplnou syntaxi Lucene, jako používá se s Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
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
ms.openlocfilehash: 59362b28390556f12cce8813635894c9f06b9a20
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008472"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Syntaxe dotazů Lucene ve službě Azure Search
Můžete psát dotazy ve službě Azure Search založený na získáte bohaté [analyzátor dotazů Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) syntaxe pro formuláře specializovaném dotazovacím: zástupný znak, vyhledávání přibližných shod, vyhledávání blízkých výrazů regulární výrazy je pár příkladů. Velká část syntaxe analyzátor dotazů Lucene [Internet implementované ve službě Azure Search](search-lucene-query-architecture.md), s výjimkou produktů *rozsahu vyhledávání* které jsou vytvořeny ve službě Azure Search prostřednictvím `$filter` výrazy. 

## <a name="how-to-invoke-full-parsing"></a>Vyvolání úplná analýza kódu

Nastavte `queryType` parametr k určení, které analyzátor, který má použít vyhledávání. Platné hodnoty jsou `simple|full`, s `simple` jako výchozí, a `full` pro Lucene. 

<a name="bkmk_example"></a> 

## <a name="example-showing-full-syntax"></a>Příklad zobrazující úplnou syntaxi

Následující příklad vyhledá dokumenty v indexu pomocí syntaxi dotazů Lucene, zřejmé ve `queryType=full` parametru. Tento dotaz vrátí hotely, kde pole kategorie obsahuje výraz "budget" a všechna prohledatelná pole obsahující frázi "nedávno renovovanou". Dokumenty obsahující frázi "nedávno renovovanou" jsou řazeny výše v důsledku termín zvýšení hodnoty (3).  

`searchMode=all` Relevantní v tomto příkladu je parametr. Pokaždé, když operátory jsou u dotazu, měli byste obecně nastavit `searchMode=all` zajistit, aby *všechny* kritéria je nalezena shoda.

```  
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full  
```  

 Můžete taky použijte příspěvek:  

```  
POST /indexes/hotels/docs/search?api-version=2015-02-28  
{  
  "search": "category:budget AND \"recently renovated\"^3",  
  "queryType": "full",  
  "searchMode": "all"  
}  
```  

Další příklady najdete v tématu [příklady syntaxe dotazů Lucene pro tvorbu dotazů ve službě Azure Search](search-query-lucene-examples.md). Další informace o určení úplné závislé parametry dotazu, najdete v článku [vyhledávání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Služba Azure Search také podporuje [jednoduchá syntaxe dotazů](query-simple-syntax.md), jednoduchým a robustním dotazovací jazyk, který lze použít pro jednoduché klíčového slova.  


##  <a name="bkmk_fields"></a> Dotazy v rámci pole  
 Můžete zadat `fieldname:searchterm` konstrukce k definování operace fielded dotazu, kde je pole jednoho slova, a hledaný termín je také jedno slovo nebo slovní spojení, volitelně s logickými operátory. Mezi příklady patří následující:  

-   rozšířením podle tematických: jazz není historie  

-   vašim animátorům: ("mil Davis" "Jan Coltrane")

 Je potřeba umístit více řetězce v uvozovkách, pokud chcete obou řetězců má být vyhodnocen jako jedna entita v tomto případě hledání dvě odlišné umělci v `artists` pole.  

 V zadané pole `fieldname:searchterm` musí být `searchable` pole.  Zobrazit [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) podrobnosti o použití atributů indexu v definicích polí.  

##  <a name="bkmk_fuzzy"></a> vyhledávání přibližných shod  
 Přibližné vyhledávání vyhledá odpovídající položky v podmínkách, které mají podobné konstrukce. Za [Lucene dokumentaci](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), vyhledávání přibližných shod, které jsou založeny na [Damerau Levenshtein vzdálenost](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance).  

 Chcete-li provést vyhledávání přibližných shod, použijte tilda "~" symbolu na konci jednoho slova s volitelným parametrem, číslo mezi 0 a 2 (výchozí), která určuje vzdálenost úpravy. Například "blue ~" nebo "blue ~ 1" vrátí "blue", "blues" a "spojovací".

 Vyhledávání přibližných shod lze použít pouze s podmínkami, ne frází. Přibližné vyhledávání můžete rozšířit na dobu maximálně 50 podmínky, které splňují kritéria vzdálenost.

##  <a name="bkmk_proximity"></a> vyhledávání blízkých výrazů  
 Vyhledávání blízkých výrazů se používají k vyhledání podmínky, která jsou blízko sebe v dokumentu. Vložit tildou "~" symbolu na konci věty následovaný počtem slova, která vytvořit hranici blízkosti. Například `"hotel airport"~5` najdete v dokumentu podmínky "hotel" a "letiště" v rámci 5 slov mezi sebou.  


##  <a name="bkmk_termboost"></a> zvýšení skóre termínu  
 Zvyšování skóre termínu odkazuje na hodnocení vyšší, pokud obsahuje Posílený termín, vzhledem k dokumentům, které neobsahují termín dokumentu. Tím se liší od bodovací profily, profily vyhodnocování zvýšení určité pole, nikoli konkrétní podmínky.  

Následující příklad ilustruje znázorňují rozdíl. Předpokládejme, že se, zda je bodování profil, který zvyšuje odpovídá v určité pole, Dejme tomu, že *žánr* v [musicstoreindex příklad](index-add-scoring-profiles.md#bkmk_ex). Zvyšování skóre termínu může použít pro další zvýšení určité hledání podmínkami vyšším než jiné. Například `rock^2 electronic` bude zvýšení dokumenty, které obsahují hledané termíny v poli žánr vyšší než další prohledávatelná pole v indexu. Další dokumenty, které obsahují hledaný termín *rock* se určí vyšší než hledaný termín *elektronických* výsledkem hodnota boost období (2).  

 Pro zvýšení termín použít blikající kurzor, "^", symbol s faktorem zesílení (číslo) na konci se hledaný termín. Může také zvýšit frází. Čím vyšší faktor zesílení, více odpovídajících termín, budou relativní vůči jiné podmínky hledání. Ve výchozím nastavení je faktor zesílení 1. I když faktor zesílení musí být kladná, může být menší než 1 (například 0.20 a novější).  

##  <a name="bkmk_regex"></a> hledání regulárního výrazu  
 Hledání regulárního výrazu najde shodu na základě obsahu mezi lomítka "/", jako zdokumentované v [Třída RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Například pokud chcete najít dokumenty, které obsahují "motel" nebo "hotel", zadejte `/[mh]otel/`.  Jednotlivá slova budou hledána hledání regulárního výrazu.   

##  <a name="bkmk_wildcard"></a> hledání pomocí zástupných znaků  
 Obecně rozpoznaná syntaxe pro více (*) nebo jedné (?) můžete použít znak vyhledávání se zástupnými znaky. Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů se jeden výraz a ne frázi.  

 Například pokud chcete najít dokumenty obsahující slova s předponou "note", jako je například "Poznámkový blok" nebo "Poznámkový blok", zadejte "Poznámka: *".  

> [!NOTE]  
>  Nelze použít * nebo? symbol jako první znak vyhledávání.  
>  Je provedena žádná analýza textu na zástupný znak vyhledávací dotazy. V době zpracování dotazu jsou výrazy zástupný znak porovná analyzované podmínky v indexu vyhledávání a rozšířit.

##  <a name="bkmk_syntax"></a> Základy syntaxe  
 Následující syntaxe základy vztahovat na všechny dotazy, které používají syntaxi Lucene.  

### <a name="operator-evaluation-in-context"></a>Operátor hodnocení v kontextu

Umístění Určuje, zda je symbol interpretován jako operátor nebo jenom další znak v řetězci.

Úplná syntaxe Lucene, například tilda (~) se používá pro vyhledávání přibližných shod a vyhledávání blízkých výrazů. Při umístění po v uvozovkách frázi ~ vyvolá vyhledávání blízkých výrazů. Při umístění na konci období, ~ vyvolá vyhledávání přibližných shod.

V rámci doby jako například "obchodní ~ analytik", znak, který není vyhodnocen jako operátor. V tomto případě za předpokladu, že dotaz je dotaz termín nebo frázi, [fulltextové vyhledávání](search-lucene-query-architecture.md) s [provést lexikální analýzu](search-lucene-query-architecture.md#stage-2-lexical-analysis) odstraní ~ a přeruší termín "obchodní ~ analytik" ve dvou: obchodní analytik nebo.

V příkladu výše je tilda (~), ale stejný princip platí pro každý operátor.

### <a name="escaping-special-characters"></a>Speciální uvozovací znaky

 Speciální znaky musí být uvozena se použije jako součást hledaný text. Vám může uniknout jsou zpětné lomítko (\\). Speciální znaky, které musí být uvozeny řídicími znaky patří:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Například k návratu zástupný znak, použijte \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování znaků nebezpečné a vyhrazené v adresách URL

 Ujistěte se prosím, že všechny nebezpečné a vyhrazené znaky jsou kódovány v adrese URL. Například "#" je nebezpečný znak, protože identifikátor fragement/ukotvení v adrese URL. Znak, který musí být kódovány za účelem `%23` Pokud se používá v adrese URL. "&" a "=" jsou příkladem vyhrazené znaky oddělení parametrů a zadejte hodnoty ve službě Azure Search. Podrobnosti najdete na [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt) další podrobnosti.

 Jsou problematické znaky ``" ` < > # % { } | \ ^ ~ [ ] ``. Vyhrazené znaky jsou `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Priorita operátorů: seskupování a seskupení pole  
 Závorky slouží k vytvoření poddotazy, včetně operátorů v rámci kulatých závorek příkazu. Například `motel+(wifi||luxury)` Vyhledá dokumenty, které obsahují výraz "motel" a "Wi-Fi" nebo "luxusní" (nebo obojí).

Seskupení pole je podobné ale obory seskupení tváří umožňující jedno pole. Například `hotelAmenities:(gym+(wifi||pool))` vyhledá pole "hotelAmenities" v "posilovně" a "Wi-Fi" nebo "posilovně" a "fond".  

### <a name="searchmode-parameter-considerations"></a>Důležité informace o parametru SearchMode  
 Dopad `searchMode` na dotazy, jak je popsáno v [jednoduchá syntaxe dotazů ve službě Azure Search](query-simple-syntax.md), platí také pro syntaxi dotazů Lucene. Konkrétně `searchMode` ve spojení s NOT operátory může vést k výsledků dotazu, které může zdát neobvyklé, pokud si nejste vymazat v důsledcích nastavení parametru. Je-li zachovat výchozí nastavení, `searchMode=any`a použijte operátor NOT, operace je vypočítán jako akci nebo tak, aby "New York" Ne "Seattle" vrátí všechny města, ve kterých nejsou Seattle.  

##  <a name="bkmk_boolean"></a> Logické operátory  
 Všechna písmena velká vždy zadejte text logické operátory (AND, OR, NOT).  

#### <a name="or-operator-or-or-"></a>Operátor OR `OR` nebo `||`

Operátor OR je svislá čára neboli znakem přesměrování. Příklad: `wifi || luxury` Vyhledá dokumenty, které obsahují "Wi-Fi" nebo "luxusní" nebo obojí. Protože nebo je výchozí operátor spojení, může také ponechat ji tak, aby `wifi luxury` je ekvivalentem `wifi || luxuery`.

#### <a name="and-operator-and--or-"></a>AND – operátor `AND`, `&&` nebo `+`

Operátor AND je znak ampersand se ani plus. Příklad: `wifi && luxury` Vyhledá dokumenty, které obsahují "Wi-Fi" a "luxusní". Znak plus (+) se používá pro požadované podmínky. Například `+wifi +luxury` stanoví, že oba termíny musí nacházet někde v poli jednotlivý dokument.


#### <a name="not-operator-not--or--"></a>Operátor NOT `NOT`, `!` nebo `-`

Operátor NOT je vykřičníkem nebo mínus. Příklad: `wifi !luxury` bude hledání dokumentů, které mají "wifi" termín a/nebo nemají "luxusní". `searchMode` Možnost řídí, zda výraz s operátorem NOT spojeny nebo sloučeny pomocí operátoru OR s podmínkami v dotazu chybí + nebo || – operátor. Vzpomeňte si, že `searchMode` může být nastaven na hodnotu `any`(výchozí) nebo `all`.

Pomocí `searchMode=any` zahrnutím další výsledky a ve výchozím nastavení se zvyšuje odvolání dotazy – bude vyhodnocen jako "Nebo NOT". Například `wifi -luxury` bude odpovídaly i dokumenty, že buď obsahují pojem *Wi-Fi* nebo ty, které neobsahují termín *luxusní.*

Pomocí `searchMode=all` zvýší přesnost dotazy zahrnutím méně výsledků a ve výchozím nastavení – bude vyhodnocen jako "A ne". Například `wifi -luxury` bude odpovídat dokumenty, které obsahují pojem `wifi` a nesmí obsahovat výraz `luxury`. To je pravděpodobně mnohem intuitivnější chování pro-– operátor. Proto byste měli zvážit, zvolíte `searchMode=all` přes `searchMode=any` Pokud chcete optimalizovat vyhledá přesnost spojené s vracením *a* vaši uživatelé často používají `-` operátor v hledání.

##  <a name="bkmk_querysizelimits"></a> Omezení velikosti dotazu  
 Platí omezení na velikost dotazy odeslané do služby Azure Search. Konkrétně můžete mít maximálně 1024 klauzule (příkazy oddělené AND, OR a tak dále). Je také omezena limitem přibližně 32 KB na velikosti všechny jednotlivé termín v dotazu. Pokud vaše aplikace generuje vyhledávací dotazy prostřednictvím kódu programu, doporučujeme, abyste návrhu takovým způsobem, nevygeneruje dotazy neomezené velikosti.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Vyhodnocování dotazy se zástupnými znaky a regulární výraz
 Služba Azure Search používá na základě frekvence vyhodnocování ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) pro text dotazy. Zástupný znak a regulární výraz dotazů, kde obor podmínek může být potenciálně široké, je však faktor frekvence zabránit pořadí z usměrnění údajů vůči shody od vzácnějších podmínek ignorován. Ve shodách se zachází stejně pro zástupný znak a regulární výraz hledání.

## <a name="see-also"></a>Další informace najdete v tématech  

+ [Hledání dokumentů](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Syntaxe výrazů OData pro filtry a řazení](query-odata-filter-orderby-syntax.md)   
+ [Jednoduchá syntaxe dotazů ve službě Azure Search](query-simple-syntax.md)   
