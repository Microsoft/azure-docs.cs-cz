---
title: Syntaxe dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Odkaz na úplnou syntaxi dotazů Lucene, jak se používá v Azure Kognitivní hledání pro zástupné znaky, přibližné vyhledávání, regulární výrazy a další konstrukce pokročilého dotazu.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801583"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Syntaxe dotazů Lucene v Azure Kognitivní hledání

Při vytváření dotazů můžete pro specializované formuláře dotazů použít syntaxi [analyzátoru dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) : zástupný znak, přibližné vyhledávání, vyhledávání blízkých výrazů, regulární výrazy. Většina syntaxe analyzátoru dotazů Lucene se [v Azure kognitivní hledání implementuje beze změny](search-lucene-query-architecture.md), s výjimkou *hledání rozsahů* , které jsou vytvořené pomocí **`$filter`** výrazů. 

Chcete-li použít úplnou syntaxi Lucene, nastavte parametr queryType na "Full" a předejte výraz dotazu vzor pro zástupné znaky, přibližné vyhledávání nebo jeden z dalších formulářů dotazů, které jsou podporovány úplnou syntaxí. V REST jsou výrazy dotazu k dispozici v **`search`** parametru žádosti [hledání dokumentů (REST API)](/rest/api/searchservice/search-documents) .

## <a name="example-full-syntax"></a>Příklad (Úplná syntaxe)

Následující příklad je požadavek na hledání vytvořený pomocí úplné syntaxe. Tento konkrétní příklad ukazuje vyhledávání v poli a termín zvyšování skóre. Hledá hotely, kde pole kategorie obsahuje výraz "rozpočet". Všechny dokumenty, které obsahují frázi "naposledy renovated", jsou seřazeny výše v důsledku hodnoty zvýšení termínu (3).  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

I když není specifické pro žádný typ dotazu, **`searchMode`** parametr je v tomto příkladu relevantní. Vždy, když jsou operátory na dotazu, je obecně nutné nastavit, aby bylo `searchMode=all` zajištěno, že *všechna* kritéria jsou shodná.  

Další příklady naleznete v tématu [Příklady syntaxe dotazů Lucene](search-query-lucene-examples.md). Podrobnosti o požadavku a parametrech dotazu, včetně searchMode, najdete v tématu [Search Documents (REST API)](/rest/api/searchservice/Search-Documents).

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> Základy syntaxe  

Následující Základy syntaxe se vztahují na všechny dotazy, které používají syntaxi Lucene.  

### <a name="operator-evaluation-in-context"></a>Vyhodnocení operátoru v kontextu

Umístění Určuje, zda je symbol interpretován jako operátor nebo pouze jiný znak v řetězci.

Například v úplné syntaxi aplikace Lucene se znak tilda (~) používá pro hledání přibližných výsledků hledání a blízkosti. Po umístění za citovanou frází ~ vyvolá hledání blízkosti. Po umístění na konci podmínky ~ vyvolá hledání přibližné.

V rámci období, jako např. "Business ~ Analytika", není znak vyhodnocen jako operátor. V takovém případě, předpokládá se, že dotaz je dotaz na termín nebo frázi, [fulltextové vyhledávání](search-lucene-query-architecture.md) s [lexikální analýzou](search-lucene-query-architecture.md#stage-2-lexical-analysis) . ~ a přerušuje pojem "business ~ analytik" ve dvou obchodních nebo analytikích.

Výše uvedený příklad je vlnovkou (~), ale stejný princip platí pro každý operátor.

### <a name="escaping-special-characters"></a>Speciální znaky pro uvozovací znaky

Chcete-li použít kterýkoli z operátorů hledání jako součást textu hledání, vydejte znak tak, že ho zavoláte s jedním zpětným lomítkem ( `\` ). Například pro hledání pomocí zástupných znaků `https://` , kde `://` je část řetězce dotazu, byste určili `search=https\:\/\/*` . Podobně může vypadat vzor telefonního čísla s řídicím číslem `\+1 \(800\) 642\-7676` .

Mezi speciální znaky, které vyžadují uvozovací znaky, patří následující:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> I když uvozovací znaky udržuje tokeny společně, [lexikální analýza](search-lucene-query-architecture.md#stage-2-lexical-analysis) během indexování může je oddělit. Například standardní analyzátor Lucene bude přerušit slova na spojovníkech, prázdných znacích a dalších znacích. Pokud v řetězci dotazu požadujete speciální znaky, možná budete potřebovat analyzátor, který je zachovává v indexu. Mezi možnosti patří [analyzátory](index-add-language-analyzers.md)přirozeného jazyka od Microsoftu, které zachovává rozdělená slova nebo vlastní analyzátor pro složitější vzory. Další informace najdete v tématu [částečné výrazy, vzory a speciální znaky](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kódování nebezpečných a vyhrazených znaků v adresách URL

Ujistěte se prosím, že všechny nebezpečné a rezervované znaky jsou v adrese URL zakódované. Například # je nebezpečný znak, protože se jedná o identifikátor fragmentu nebo kotvy v adrese URL. Znak musí být kódovaný na, `%23` Pokud je použit v adrese URL. ' & ' a ' = ' jsou příklady rezervovaných znaků, které jsou vymezují parametry a určují hodnoty v Azure Kognitivní hledání. Další podrobnosti najdete v tématu [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt) .

Nezabezpečené znaky jsou ``" ` < > # % { } | \ ^ ~ [ ]`` . Vyhrazené znaky jsou `; / ? : @ = + &` .

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> Logické operátory

Můžete vložit logické operátory do řetězce dotazu, aby se zlepšila přesnost shody. Úplná syntaxe podporuje kromě operátorů znaků i operátory textu. Vždy zadejte text Boolean Operators (AND, OR, NOT) All CAPS.

|Textový operátor | Znak | Příklad | Využití |
|--------------|----------- |--------|-------|
| A | `&`, `+` | `wifi + luxury` | Určuje výrazy, které musí shoda obsahovat. V tomto příkladu modul dotazů hledá dokumenty obsahující `wifi` a `luxury` . Znak plus ( `+` ) se používá pro požadované podmínky. Například `+wifi +luxury` stanoví, že oba výrazy se musí objevit někde v poli jediného dokumentu.|
| NEBO | `|` | `wifi | luxury` | Najde shodu, pokud je nalezen libovolný termín. V tomto příkladu dotazovací stroj vrátí shodu s dokumenty, které obsahují buď `wifi` nebo `luxury` nebo obojí. Vzhledem k tomu, že nebo je výchozím operátorem spojení, můžete jej také opustit, což `wifi luxury` je ekvivalent  `wifi | luxury` .|
| NOT | `!`, `-` | `wifi –luxury` | Vrátí shodu pro dokumenty, které vyloučily podmínky. Vyhledá například `wifi –luxury` dokumenty, které mají `wifi` termín, ale ne `luxury` . <br/><br/>`searchMode`Parametr v požadavku na dotaz řídí, zda je výraz s operátorem NOT ANDed nebo ORed s jinými podmínkami v dotazu (za předpokladu, že se nejedná `+` `|` o operátor nebo na jiné podmínky). Platné hodnoty jsou `any` nebo `all` .  <br/><br/>`searchMode=any` zvyšuje počet odvolání dotazů zahrnutím více výsledků a ve výchozím nastavení `-` bude interpretován jako "nebo ne". Například `wifi -luxury` se bude shodovat s dokumenty, které buď obsahují termín `wifi` , nebo ty, které neobsahují termín `luxury` .  <br/><br/>`searchMode=all` zvyšuje přesnost dotazů zahrnutím menšího počtu výsledků a ve výchozím nastavení je interpretována jako "a nikoli". Například `wifi -luxury` bude odpovídat dokumentům, které obsahují termín `wifi` a nesmí obsahovat pojem "luxus". To je pravděpodobně intuitivní chování `-` operátoru. Proto byste měli zvážit použití `searchMode=all` místo toho, `searchMode=any` Pokud chcete optimalizovat hledání přesnosti místo odvolání *a* vaši uživatelé často používají `-` operátor v hledání.<br/><br/>Při rozhodování o `searchMode` nastavení zvažte vzory interakce uživatele pro dotazy v různých aplikacích. Uživatelům, kteří hledají informace, je pravděpodobnější, že v dotazu zařadí operátor, a to na rozdíl od webů elektronického obchodování, které mají více integrovaných navigačních struktur. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> Hledání v poli

Můžete definovat operaci vyhledávání v poli s `fieldName:searchExpression` syntaxí, kde výraz vyhledávání může být jedno slovo nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:  

- Žánr: nehistorie Jazz  

- interprety:("míle Davis" "Jan Coltrane")

Nezapomeňte vložit více řetězců v uvozovkách, pokud chcete, aby oba řetězce byly vyhodnoceny jako jediná entita. v tomto případě vyhledávání dvou různých umělců v `artists` poli.  

Pole zadané v `fieldName:searchExpression` musí být `searchable` pole.  Podrobnosti o tom, jak se v definicích polí používají atributy indexu, najdete v tématu [Create index](/rest/api/searchservice/create-index) .  

> [!NOTE]
> Při použití vyhledávacích výrazů v poli není nutné používat `searchFields` parametr, protože každý výraz vyhledávání v poli má explicitně zadaný název pole. Tento parametr však lze použít i v `searchFields` případě, že chcete spustit dotaz, ve kterém jsou některé části vymezeny na konkrétní pole, a zbytek by se mohl vztahovat na několik polí. Dotaz by se například `search=genre:jazz NOT history&searchFields=description` shodoval pouze s `jazz` `genre` polem, zatímco by `NOT history` se shodoval s `description` polem. Název pole `fieldName:searchExpression` , který je k dispozici vždy, má přednost před `searchFields` parametrem, což znamená, že v tomto příkladu není nutné zahrnout `genre` do `searchFields` parametru.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> Hledání přibližných výsledků

Hledání přibližných výsledků vyhledává shody v termínech, které mají podobnou konstrukci, rozšíří pojem až do maximálního počtu 50 podmínek, které splňují kritéria vzdálenosti dvě nebo méně. Další informace najdete v tématu [hledání přibližných výsledků](search-query-fuzzy.md).

Chcete-li provést hledání přibližné hodnoty, použijte symbol tilda "~" na konci jednoho slova s volitelným parametrem, číslo mezi 0 a 2 (výchozí), které určuje vzdálenost úprav. Například "Blue ~" nebo "Blue ~ 1" vrátí "Blue", "blues" a "Glue".

Hledání přibližných hodnot lze použít pouze pro termíny, nikoli pro fráze, ale můžete přidat vlnovku k jednotlivým termínům jednotlivě v názvu nebo frázi s více částmi. Například "Unviersty ~ ~" Wshington ~ "by se shodovala s" University of Washington ".
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> Hledání blízkosti

Hledání v blízkosti se používá k vyhledání podmínek, které jsou poblíž sebe v dokumentu. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti. Například `"hotel airport"~5` nalezne termíny "Hotel" a "letiště" do 5 slov navzájem v dokumentu.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> Zvyšování termínů

Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. To se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní výrazy.  

Následující příklad pomáhá ilustrovat rozdíly. Předpokládejme, že existuje profil vyhodnocování, který zvyšuje shodu v určitém poli, řekněme jako *Žánr* v  [musicstoreindex příkladu](index-add-scoring-profiles.md#bkmk_ex). Za účelem dalšího zvýšení úrovně hledaného výrazu, který je vyšší než jiné, se dá použít zvyšování skóre termínu. Například `rock^2 electronic` bude zvyšovat dokumenty, které obsahují hledané výrazy v poli žánru, vyšší než jiná hledaná pole v indexu. V důsledku hodnoty zvýšení skóre (2) jsou dokumenty, které obsahují hledaný výraz *Rock* , řazeny vyšší než jiné hledané podmínky *elektronického* výrazu.  

 Chcete-li zvýšit pojem, použijte blikající kurzor "^", symbol s faktorem zvýšení (číslo) na konci hledaného výrazu. Můžete také posílit fráze. Čím vyšší je faktor zvýšení, tím důležitější je, že výraz bude relativní vzhledem k ostatním hledaným podmínkám. Ve výchozím nastavení je faktor zvýšení 1. I když faktor zvýšení musí být kladný, může být menší než 1 (například 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> Hledání regulárních výrazů  
 Hledání regulárního výrazu vyhledá shodu na základě vzorů, které jsou platné v rámci Apache Lucene, jak je uvedeno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html). V Azure Kognitivní hledání je regulární výraz uzavřený mezi lomítky `/` .

 Pokud například chcete najít dokumenty obsahující "Motel" nebo "Hotel", zadejte `/[mh]otel/` . Hledání regulárních výrazů se shoduje s jedním slovem.

Některé nástroje a jazyky ukládají další požadavky na řídicí znaky. V případě JSON jsou řetězce, které obsahují lomítko, uvozeny zpětným lomítkem: "microsoft.com/azure/" se změní `search=/.*microsoft.com\/azure\/.*/` `search=/.* <string-placeholder>.*/` regulární výraz a jedná se o `microsoft.com\/azure\/` řetězec s řídicím lomítkem.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> Hledání pomocí zástupných znaků

Můžete použít obecně rozpoznanou syntaxi pro hledání zástupných znaků vícenásobného () `*` nebo Single ( `?` ). Například výraz dotazu `search=alpha*` vrátí "alfanumerický" nebo "abecední". Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jedním výrazem a nikoli frází.

Úplná syntaxe Lucene podporuje prefix, vpony a příponu. Nicméně, pokud je vše nutné pro spárování předpon, můžete použít jednoduchou syntaxi (shoda předpony je podporována v obou).

Shoda přípony, kde `*` nebo `?` předchází řetězec (as in `search=/.*numeric./` ) nebo vpony, vyžaduje úplnou syntaxi Lucene a také oddělovače s přesměrováním regulárního výrazu `/` . Nemůžete použít * ani? symbol jako první znak podmínky nebo v rámci období bez `/` . 

> [!NOTE]  
> Porovnávání vzorů je jako pravidlo pomalé, takže byste mohli chtít prozkoumat alternativní metody, jako je například Edge n-gram, který vytváří tokeny pro sekvence znaků v termínu. Index bude větší, ale dotazy mohou být provedeny rychleji v závislosti na konstrukci vzoru a délce indexovaných řetězců.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>Dopad analyzátoru na zástupné dotazy

Během analýzy dotazů jsou dotazy, které jsou formulované jako předpona, přípona, zástupný znak nebo regulární výrazy, předány tak, jak jsou do stromu dotazu, obcházení [lexikální analýzy](search-lucene-query-architecture.md#stage-2-lexical-analysis). Shody budou nalezeny pouze v případě, že index obsahuje řetězce ve formátu, který určuje dotaz. Ve většině případů budete během indexování potřebovat analyzátor, který zachová integritu řetězce, aby bylo úspěšné porovnávání částečného a vzorového kódu. Další informace najdete v tématu [částečné hledání ve službě Azure kognitivní hledání dotazy](search-query-partial-matching.md).

Vezměte v úvahu situaci, kdy budete chtít, aby vyhledávací dotaz "terminat *" vracel výsledky, které obsahují podmínky jako "ukončení", "ukončení" a "ukončení".

Pokud byste chtěli použít analyzátor en. Lucene (English Lucene), uplatní se tím agresivní lemmatizátor každého období. Například "ukončení", "ukončení", "ukončení" budou v indexu vyraženy do tokenu ' terms '. Na druhé straně se neanalyzují všechny výrazy v dotazech využívajících zástupné znaky nebo přibližné vyhledávání. proto by nedocházelo k žádným výsledkům, které by odpovídaly dotazu "terminat *".

Na druhé straně analyzátory Microsoftu (v tomto případě je to v tomto případě en. Microsoft Analyzer) trochu pokročilejší a používají lemmatizátor nebo předzpracování místo odvozování. To znamená, že všechny vygenerované tokeny by měly být platná anglická slova. Například klíčové slovo "ukončit", "ukončeno" a "ukončení" většinou zachová celý index a může být vhodnější pro scénáře, které jsou závislé na zástupných znacích a přibližném vyhledávání.

## <a name="scoring-wildcard-and-regex-queries"></a>Vyhodnocování zástupných znaků a dotazů Regex

Azure Kognitivní hledání používá pro textové dotazy hodnocení založené na kmitočtech ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)). U dotazů se zástupným znakem a regulárních výrazů, u kterých může být obor pojmů potenciálně rozsáhlý, se ale četnost frekvencí ignoruje, aby nedocházelo k tomu, aby se zabránilo vyřazení shody směrem k Všechny shody jsou zpracovány stejně pro hledání pomocí zástupných znaků a Regex.

## <a name="special-characters"></a>Speciální znaky

V některých případech můžete chtít vyhledat speciální znak, například ❤ emoji nebo symbol €. V takových případech se ujistěte, že analyzátor, který používáte, nefiltruje tyto znaky. Standardní analyzátor obchází mnoho speciálních znaků a vyloučí je z indexu.

Analyzátory, které budou tokenizovat speciální znaky, zahrnují analyzátor "prázdných", který bere v úvahu všechny sekvence znaků oddělené prázdnými znaky jako tokeny (takže řetězec "❤" by byl považován za token). Také analyzátor jazyka, jako je Microsoft English Analyzer ("en. Microsoft"), by měl jako token přebírat řetězec "€". Můžete [otestovat analyzátor](/rest/api/searchservice/test-analyzer) a zjistit, jaké tokeny generuje pro daný dotaz.

Při použití znaků Unicode zajistěte, aby byly symboly v adrese URL dotazu správně uvozeny řídicími znaky (například ❤ by používaly řídicí sekvenci `%E2%9D%A4+` ). Post provede tento převod automaticky.  

## <a name="precedence-grouping"></a>Priorita (seskupení)

Pomocí závorek lze vytvořit poddotazy, včetně operátorů v rámci příkazu kulatého závorky. Nástroj například `motel+(wifi|luxury)` vyhledá dokumenty obsahující termín "Motel" a buď "WiFi", nebo "luxus" (nebo obojí).

Seskupení polí je podobné, ale obor seskupení do jednoho pole. Například `hotelAmenities:(gym+(wifi|pool))` vyhledá pole "hotelAmenities" pro "posilovně" a "WiFi" nebo "posilovně" a "Pool".  

## <a name="query-size-limits"></a>Omezení velikosti dotazů

Existuje omezení velikosti dotazů, které můžete odeslat do služby Azure Kognitivní hledání. Konkrétně můžete mít maximálně 1024 klauzulí (výrazy oddělené a, nebo a tak dále). Velikost jakéhokoli jednotlivého výrazu v dotazu je také omezena na přibližně 32 KB. Pokud vaše aplikace generuje vyhledávací dotazy programově, doporučujeme ji navrhovat takovým způsobem, že negeneruje dotazy na neohraničenou velikost.  

## <a name="see-also"></a>Viz také

+ [Příklady dotazů pro jednoduché hledání](search-query-simple-examples.md)
+ [Příklady dotazů pro úplné hledání Lucene](search-query-lucene-examples.md)
+ [Hledat dokumenty](/rest/api/searchservice/Search-Documents)
+ [Syntaxe výrazů OData pro filtry a řazení](query-odata-filter-orderby-syntax.md)   
+ [Jednoduchá syntaxe dotazů v Azure Kognitivní hledání](query-simple-syntax.md)
