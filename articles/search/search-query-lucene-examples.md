---
title: Použít úplnou syntaxi dotazu Lucene
titleSuffix: Azure Cognitive Search
description: Syntaxe dotazu Lucene pro přibližné vyhledávání, hledání bezkontaktní komunikace, podporu termínů, hledání regulárních výrazů a vyhledávání zástupných symbolů ve službě Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793431"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Použití syntaxe vyhledávání "full" Lucene (rozšířené dotazy v Azure Cognitive Search)

Při vytváření dotazů pro Azure Cognitive Search můžete nahradit výchozí [jednoduchý analyzátor dotazů](query-simple-syntax.md) rozsáhlejším [analyzátorem lucene dotazů v Azure Cognitive Search](query-lucene-syntax.md) a formulovat specializované a pokročilé definice dotazů. 

Analyzátor Lucene podporuje složité konstrukce dotazů, jako jsou dotazy s rozsahem polí, vyhledávání přibližovaných a předponami se zástupnými kódy, hledání bezkontaktních dat, zesílení termínů a vyhledávání regulárních výrazů. Další napájení je dodáváno s dalšími požadavky na zpracování, takže byste měli očekávat o něco delší dobu provádění. V tomto článku můžete krokovat příklady demonstrující operace dotazu k dispozici při použití úplné syntaxe.

> [!Note]
> Mnoho konstrukce specializované dotazu povolené prostřednictvím úplné syntaxe dotazu Lucene nejsou [textově analyzovány](search-lucene-query-architecture.md#stage-2-lexical-analysis), což může být překvapivé, pokud očekáváte vyplývající nebo lemmatizace. Lexikální analýza se provádí pouze za úplných termínů (termín ový dotaz nebo frázový dotaz). Typy dotazů s neúplnými termíny (předpona dotaz, zástupný dotaz, dotaz regulárního výrazu, přibližný dotaz) jsou přidány přímo do stromu dotazu, čímž se obejde fáze analýzy. Jedinou transformací provedenou na neúplných dotazových termínech je snížení počtu. 
>

## <a name="formulate-requests-in-postman"></a>Formulovat požadavky v Pošťákovi

Následující příklady využívají index hledání pracovních míst nyc skládající se z úloh, které jsou k dispozici na základě datové sady poskytované iniciativou [City of New York OpenData.](https://opendata.cityofnewyork.us/) Tyto údaje by neměly být považovány za aktuální nebo úplné. Index se nachází na izolovaném prostoru služby poskytované společností Microsoft, což znamená, že nepotřebujete předplatné Azure nebo Azure Cognitive Search vyzkoušet tyto dotazy.

Co potřebujete, je Postman nebo ekvivalentní nástroj pro vydávání požadavku HTTP na GET. Další informace naleznete v tématu [Explore with REST clients](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Nastavení hlavičky požadavku

1. V hlavičce požadavku nastavte `application/json`typ **obsahu** na .

2. Přidejte **klíč rozhraní api**a nastavte `252044BE3886FE4A8E3BAA4F595114BB`jej na tento řetězec: . Toto je klíč dotazu pro vyhledávací službu izolovaného prostoru, která je hostitelem indexu úloh NYC.

Po zadání hlavičky požadavku můžete znovu použít pro všechny dotazy v tomto článku a vyměnit pouze řetězec **search=.** 

  ![Hlavička žádosti Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Nastavení adresy URL požadavku

Požadavek je příkaz GET spárovaný s adresou URL obsahující koncový bod azure kognitivního vyhledávání a vyhledávací řetězec.

  ![Hlavička žádosti Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Složení adresy URL má následující prvky:

+ **`https://azs-playground.search.windows.net/`** je vyhledávací služba izolovaného prostoru spravovaná vývojovým týmem Azure Cognitive Search. 
+ **`indexes/nycjobs/`** je nyc jobs index v kolekci indexů této služby. Název služby a index jsou požadovány na žádost.
+ **`docs`** je kolekce dokumentů obsahující veškerý prohledávatelný obsah. Klíč rozhraní api dotazu uvedený v záhlaví požadavku funguje pouze na operacích čtení zaměřených na kolekci dokumentů.
+ **`api-version=2019-05-06`** nastaví verzi api, což je povinný parametr pro každý požadavek.
+ **`search=*`** je řetězec dotazu, který je v počátečním dotazu null a vrací prvních 50 výsledků (ve výchozím nastavení).

## <a name="send-your-first-query"></a>Odeslání prvního dotazu

Jako krok ověření vložte následující požadavek do příkazu GET a klepněte na tlačítko **Odeslat**. Výsledky jsou vráceny jako podrobné dokumenty JSON. Jsou vráceny celé dokumenty, což umožňuje zobrazit všechna pole a všechny hodnoty.

Vložte tuto adresu URL do klienta REST jako krok ověření a zobrazte strukturu dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Řetězec dotazu **`search=*`**, je nespecifikované hledání ekvivalentní hledání null nebo prázdné. Je to nejjednodušší hledání, které můžete udělat.

Volitelně můžete přidat **`$count=true`** do adresy URL a vrátit tak počet dokumentů odpovídajících kritériím vyhledávání. Na prázdný vyhledávací řetězec, toto je všechny dokumenty v indexu (asi 2800 v případě NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Jak vyvolat úplnou analýzu Lucene

Přidejte **queryType=full,** chcete-li vyvolat úplnou syntaxi dotazu, která přepíše výchozí jednoduchou syntaxi dotazu. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Všechny příklady v tomto článku zadejte **queryType=full** search parametr, označující, že úplná syntaxe je zpracována Analyzátorem dotazu Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Příklad 1: Dotaz s vymezeným oborem na seznam polí

Tento první příklad není specifické pro Lucene, ale vedeme s ním zavést první základní koncept dotazu: obor pole. Tento příklad obory celý dotaz a odpověď pouze několik konkrétních polí. Vědět, jak strukturovat čitelnou odpověď JSON, je důležité, když je váš nástroj Pošťák nebo Průzkumník vyhledávání. 

Pro stručnost dotaz cíle pouze *business_title* pole a určuje pouze obchodní tituly jsou vráceny. Parametr **searchFields** omezuje spuštění dotazu pouze na business_title pole a **vyberte,** která pole jsou zahrnuta do odpovědi.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
&search=*&searchFields=business_title&$select=business_title
```

Zde je stejný dotaz s více poli v seznamu odděleném čárkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Mezery za čárky jsou volitelné.

> [!Tip]
> Při použití rozhraní REST API z kódu aplikace nezapomeňte na url-kódovat parametry jako `$select` a `searchFields`.

### <a name="full-url"></a>Úplná adresa URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  ![Pošťák vzorodpověď](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre vyhledávání v odpovědi. K jednotnému skóre 1 dochází, pokud neexistuje žádná hodnost, protože hledání nebylo fulltextové vyhledávání, nebo proto, že nebyla použita žádná kritéria. Pro hledání null bez kritérií se řádky vrátí v libovolném pořadí. Když zahrnete skutečná kritéria vyhledávání, zobrazí se skóre vyhledávání, které se změní na smysluplné hodnoty.

## <a name="example-2-fielded-search"></a>Příklad 2: Vyhledávání v poli

Úplná syntaxe Lucene podporuje vymezení jednotlivých vyhledávacích výrazů do určitého pole. Tento příklad vyhledá obchodní tituly s termínem senior v nich, ale ne junior.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Zde je stejný dotaz s více poli.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Úplná adresa URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Pošťák vzorodpověď](media/search-query-lucene-examples/intrafieldfilter.png)

Můžete definovat operaci vyhledávání v poli s syntaxí **fieldName:searchExpression,** kde hledaný výraz může být jedno slovo nebo frázi nebo složitější výraz v závorcích, volitelně s logickými operátory. Některé příklady zahrnují následující:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Nezapomeňte vložit více řetězců do uvozovek, pokud chcete, aby byly oba řetězce vyhodnoceny jako `state` jedna entita, jako v tomto případě hledání dvou odlišných umístění v poli. Také ujistěte se, že operátor je velkými písmeny, jak vidíte s NOT a AND.

Pole zadané v **poli FieldName:searchExpression** musí být prohledávatelné pole. Viz [Vytvořit index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) podrobnosti o tom, jak se atributy indexu používají v definicích polí.

> [!NOTE]
> Ve výše uvedeném příkladu jsme `searchFields` nemuseli použít parametr, protože každá část dotazu má explicitně zadaný název pole. `searchFields` Parametr však můžete použít i v případě, že chcete spustit dotaz, kde jsou některé části vymezeny na určité pole, a zbytek může být aplikován na několik polí. Dotaz `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` by se například shodoval pouze s `senior NOT junior` polem, `business_title` zatímco `posting_type` by odpovídal "externímu" s polem. Název pole zadaný v **poliName:searchExpression** má `searchFields` vždy přednost před parametrem, což je důvod, proč v tomto příkladu nemusíme zahrnout `business_title` do parametru. `searchFields`

## <a name="example-3-fuzzy-search"></a>Příklad 3: Přibližné vyhledávání

Úplná syntaxe Lucene také podporuje přibližné vyhledávání, odpovídající termíny, které mají podobnou konstrukci. Chcete-li provést přibližné `~` vyhledávání, přidejte symbol vlnovky na konec jednoho slova s volitelným parametrem, hodnotou mezi 0 a 2, která určuje vzdálenost úprav. Například `blue~` nebo `blue~1` vrátí modrou, modrou a lepicí.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fráze nejsou podporovány přímo, ale můžete určit přibližnou shodu na součástech fráze.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Úplná adresa URL

Tento dotaz vyhledá úlohy s termínem "přidružit" (záměrně chybně napsané):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Přibližná odpověď vyhledávání](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Přibližné dotazy nejsou [analyzovány](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy dotazů s neúplnými termíny (předpona dotaz, zástupný dotaz, dotaz regulárního výrazu, přibližný dotaz) jsou přidány přímo do stromu dotazu, čímž se obejde fáze analýzy. Jedinou transformací provedenou na neúplných dotazových termínech je snížení počtu.
>

## <a name="example-4-proximity-search"></a>Příklad 4: Hledání bezkontaktní chod
Hledání bezkontaktní chodse se používá k vyhledání termínů, které jsou v dokumentu blízko sebe. Na konec fráze vložte symbol vlnovky "~", za nímž následuje počet slov, která vytvářejí hranici přiblížení. Například "hotelové letiště"~5 najde termíny hotel a letiště do 5 slov od sebe v dokumentu.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu pro úlohy s termínem "senior analyst", kde je oddělen a ne více než jedno slovo:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Dotaz na blízkost](media/search-query-lucene-examples/proximity-before.png)

Zkuste to znovu odstranění slov mezi termínem "senior analytik". Všimněte si, že 8 dokumenty jsou vráceny pro tento dotaz na rozdíl od 10 pro předchozí dotaz.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Příklad 5: Podpora termínů
Zesílení termínu odkazuje na hodnocení dokumentu vyšší, pokud obsahuje posílený termín, vzhledem k dokumentům, které neobsahují termín. Chcete-li výraz zvýšit, použijte symbol "^" s faktorem zvýšení (číslo) na konci hledaného termínu. 

### <a name="full-urls"></a>Úplné adresy URL

V tomto dotazu "před" vyhledejte pracovní místa s termínem *počítačový analytik* a všimněte si, že neexistují žádné výsledky s oběma slovy *počítače* a *analytika*, ale *počítačové* práce jsou na vrcholu výsledků.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Podpora termínu před](media/search-query-lucene-examples/termboostingbefore.png)

V dotazu "po" opakujte hledání, tentokrát zvýšení výsledků s termínem *analytik* přes termín *počítače,* pokud obě slova neexistují. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Více lidsky čitelná verze výše `search=business_title:computer analyst^2`uvedeného dotazu je . U funkčního dotazu `^2` je kódován jako `%5E2`, což je hůře vidět.

  ![Podpora termínu po](media/search-query-lucene-examples/termboostingafter.png)

Propagace termínů se liší od profilů hodnocení v tom, že profily hodnocení zvyšují určitá pole, nikoli konkrétní termíny. Následující příklad pomáhá ilustrovat rozdíly.

Zvažte profil bodování, který zvyšuje shody v určitém poli, jako je **například žánr** v příkladu musicstoreindex. Podpora termínu by mohla být použita k dalšímu posílení některých hledaných výrazů vyšší než ostatní. Například "rock^2 electronic" posílí dokumenty, které obsahují hledané termíny v poli **žánru** vyšší než ostatní prohledávatelná pole v indexu. Dokumenty, které obsahují hledaný výraz "rock", budou navíc hodnoceny výše než ostatní hledané výrazy "elektronické" v důsledku hodnoty zvýšení výrazu (2).

Při nastavování úrovně faktoru, čím vyšší je faktor zesílení, tím relevantnější bude termín relativní k ostatním hledaným výrazům. Ve výchozím nastavení je faktor zesílení 1. I když faktor zvýšení musí být pozitivní, může být menší než 1 (například 0,2).


## <a name="example-6-regex"></a>Příklad 6: Regex

Hledání regulárních výrazů vyhledá shodu na základě obsahu mezi lomítkem "/", jak je popsáno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu vyhledejte pracovní místa s `search=business_title:/(Sen|Jun)ior/`termínem Senior nebo Junior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Dotaz regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> Dotazy regexu nejsou [analyzovány](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedinou transformací provedenou na neúplných dotazových termínech je snížení počtu.
>

## <a name="example-7-wildcard-search"></a>Příklad 7: Hledání zástupných symbolů
Obecně rozpoznanou syntaxi\*můžete použít pro více násobné ( ) nebo jednoznakové zástupné vyhledávání. Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jediným termínem a nikoli frází.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu vyhledejte úlohy, které obsahují předponu "prog", která by zahrnovala obchodní tituly s termíny programování a programátor v něm. Nelze použít * nebo ? jako první znak vyhledávání.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Zástupný dotaz](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Zástupné dotazy nejsou [analyzovány](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedinou transformací provedenou na neúplných dotazových termínech je snížení počtu.
>

## <a name="next-steps"></a>Další kroky
Zkuste zadat Analyzátor dotazu Lucene v kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pro rozhraní .NET a rozhraní REST API. Odkazy používají výchozí jednoduchou syntaxi, takže budete muset použít to, co jste se naučili z tohoto článku, abyste určili **queryType**.

* [Dotaz na index pomocí sady .NET SDK](search-query-dotnet.md)
* [Dotaz na index pomocí rozhraní REST API](search-create-index-rest-api.md)

Další odkaz na syntaxi, architekturu dotazu a příklady naleznete v následujících odkazech:

+ [Jednoduché příklady syntaktických dotazů](search-query-simple-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)