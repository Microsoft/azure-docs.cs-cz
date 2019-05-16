---
title: Příklady dotazů Lucene – Azure Search
description: Syntaxe dotazů Lucene pro vyhledávání přibližných shod, vyhledávání blízkých výrazů, zvyšování skóre termínu, hledání regulárního výrazu a vyhledávání se zástupnými znaky v rámci služby Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 467c323a0b669e70e12f801fd8fdd6df119e793d
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595911"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Příklady dotazů pomocí syntaxe vyhledávání "úplné" Lucene (pokročilé dotazy ve službě Azure Search)

Při vytváření dotazů pro službu Azure Search, můžete nahradit výchozí [jednoduchý analyzátor dotazů](query-simple-syntax.md) s více obsáhlém [analyzátor dotazů Lucene ve službě Azure Search](query-lucene-syntax.md) formulovat specializované a pokročilé dotazu definice. 

Analyzátor Lucene podporuje složitého dotazu konstrukce, jako jsou dotazy v rámci pole, přibližných shod a hledání pomocí zástupných znaků předpony, vyhledávání blízkých výrazů, zvyšování skóre termínu a hledání regulárního výrazu. Zvýšit výkon se dodává s požadavky na další zpracování, měli byste očekávat, o něco delší dobu provádění. V tomto článku můžete krokovat příklady demonstrující operace dotazu jsou dostupné při používání úplnou syntaxi.

> [!Note]
> Mnohé z konstrukce specializovaném dotazovacím zajišťuje úplnou syntaxi dotazů Lucene nejsou [analyzovat text](search-lucene-query-architecture.md#stage-2-lexical-analysis), což může být překvapivé, pokud očekáváte, že vyplývající nebo lemmatizátor. Lexikální analýzu se provádí pouze v dokončení podmínky (termín dotazu nebo dotaz fráze). Typy dotazů s neúplné podmínky (předponu dotazu, dotaz zástupný znak, regulární výraz dotazu, fuzzy dotazu) jsou přidány přímo do stromu dotazu obcházení fázi analýzy. Pouze transformace provedené na neúplný dotaz podmínky je předpoklady. 
>

## <a name="formulate-requests-in-postman"></a>Formulování požadavků v nástroji Postman

Následující příklady využívají vyhledávací index NYC Jobs skládající se z úlohy nejsou k dispozici na základě datové poskytované [města New York OpenData](https://opendata.cityofnewyork.us/) iniciativy. Tato data by neměly být zahrnuté v aktuální nebo dokončení. Index je v sandboxu služby od Microsoftu, což znamená, že potřebujete předplatnému Azure nebo Azure Search vyzkoušet tyto dotazy.

Co je třeba je Postman nebo ekvivalentní nástroje pro vydání požadavku HTTP na GET. Další informace najdete v tématu [prozkoumat s využitím klientů REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Nastavte hlavičku žádosti

1. V hlavičce požadavku, nastavte **Content-Type** k `application/json`.

2. Přidat **klíč api-key**a nastavte ho na tento řetězec: `252044BE3886FE4A8E3BAA4F595114BB`. Toto je klíč dotazu hostování NYC Jobs indexu služby search izolovaného prostoru.

Po zadání hlavičce žádosti, jej můžete znovu použít pro všechny dotazy v tomto článku záměnu pouze **hledání =** řetězec. 

  ![Hlavička žádosti Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Nastavení adresy URL požadavku

Požadavek je spárovaná s adresy URL obsahující řetězce koncový bod a vyhledávání Azure Search příkaz GET.

  ![Hlavička žádosti Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Adresa URL sestavení obsahuje následující prvky:

+ **`https://azs-playground.search.windows.net/`** Vyhledávací služba izolovaného prostoru udržuje vývojový tým Azure Search. 
+ **`indexes/nycjobs/`** je index NYC Jobs v kolekci indexů této služby. Název služby a index se vyžadují v požadavku.
+ **`docs`** je kolekce dokumenty obsahující všechny prohledávatelný obsah. Dotazu api-key zadaný v hlavičce žádosti funguje pouze na operace čtení, které cílí na kolekce dokumentů.
+ **`api-version=2019-05-06`** Nastaví verzi api-version, což je povinný parametr u každého požadavku.
+ **`search=*`** řetězec dotazu, který počátečního dotazu má hodnotu null, vrátí prvních 50 výsledky (ve výchozím nastavení).

## <a name="send-your-first-query"></a>Odeslat svůj první dotaz

Jako ověřovací krok, vložte následující požadavek do GET a klikněte na tlačítko **odeslat**. Výsledky jsou vráceny jako podrobné dokumenty JSON. Vrátí se celý dokumenty, které vám umožní vidět všechna pole a všechny hodnoty.

Vložte tuto adresu URL do klienta REST jako krok ověření a chcete-li zobrazit strukturu dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Řetězec dotazu **`search=*`**, odpovídá neurčené hledání hodnotu null nebo prázdné vyhledávání. Je nejjednodušší vyhledávání, které vám pomůžou.

Volitelně můžete přidat **`$count=true`** pro adresu URL pro vrátí počet dokumentů odpovídajících kritériím vyhledávání. V prázdné hledaný řetězec je to všechny dokumenty v indexu (přibližně 2800 v případě NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Vyvolání úplné analýze Lucene

Přidat **queryType = full** k vyvolání celý dotaz v syntaxi, přepisuje výchozí jednoduchá syntaxe dotazů. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Zadejte všechny příklady v tomto článku **queryType = full** parametr určující, zda analyzátor dotazů Lucene zpracovává úplnou syntaxi vyhledávání. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Příklad 1: Dotaz s rozsahem seznam polí

Tento první příklad není Lucene konkrétní, ale jsme s ním vést k zavedení prvního pojem základních dotazů: pole oboru. V tomto příkladu obory celý dotaz a odpovědi na pár konkrétních polí. Znalost, jak strukturovat, čitelné odpověď JSON je důležité, pokud je nástroj Postman nebo vyhledávání explorer. 

Pro zkrácení, zaměřuje dotazu pouze *business_title* pole a určuje pouze názvy business jsou vráceny. **SearchFields** omezuje parametr spuštění dotazu pouze na pole business_title a **vyberte** určuje pole, která jsou zahrnuty v odpovědi.

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
&search=*&searchFields=business_title&$select=business_title
```

Zde je stejný dotaz obsahuje několik polí v seznamu odděleného čárkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Tyto mezery za čárkou jsou volitelné.

> [!Tip]
> Při použití rozhraní REST API z kódu aplikace, nezapomeňte kódování URL parametry jako `$select` a `searchFields`.

### <a name="full-url"></a>Úplná adresa URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpověď pro tento dotaz by měl vypadat podobně jako na následujícím snímku obrazovky.

  ![Ukázková odpověď postman](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre vyhledávání v odpovědi. Jednotné skóre 1 dojít, pokud neexistuje žádné pořadí buď protože vyhledávání nebylo fulltextové vyhledávání, nebo protože byla použita žádná kritéria. Pro hodnotu null vyhledávání se žádná kritéria vraťte řádků v pořadí. Pokud zahrnete skutečné vyhledávací kritéria, zobrazí se hledání, které skóre, které se změní na smysluplné hodnoty.

## <a name="example-2-fielded-search"></a>Příklad 2: Fielded vyhledávání

Úplná syntaxe Lucene podporuje oboru jednotlivých vyhledávacích výrazech na konkrétní pole. Tento příklad vyhledá názvy firmy s vedoucí termín v nich, ale ne méně.

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Zde je stejný dotaz s více polí.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Úplná adresa URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Ukázková odpověď postman](media/search-query-lucene-examples/intrafieldfilter.png)

Můžete definovat fielded vyhledávací operace s **fieldName:searchExpression** syntaxi, kde hledaný výraz může být jedno slovo nebo frázi nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Nezapomeňte vložit více řetězce v uvozovkách, pokud chcete, který se má vyhodnotit jako jedna entita, jako v tomto případě hledání dvou různých umístěních v obou řetězců `state` pole. Také se ujistěte, operátor, který je velkými písmeny, jak vidíte s NOT a AND.

V zadané pole **fieldName:searchExpression** musí být prohledávatelné pole. Zobrazit [vytvoření indexu (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) podrobnosti o použití atributů indexu v definicích polí.

> [!NOTE]
> V předchozím příkladu jsme není nutné používat `searchFields` parametr protože každá část dotazu má název pole explicitně zadán. Ale můžete pořád použít `searchFields` parametr, pokud chcete spustit dotaz, kde některé části oborem pro určité pole a ostatní můžou použít pro několik polí. Například dotaz `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` odpovídají `senior NOT junior` pouze `business_title` pole, když bude odpovídat "externí" s `posting_type` pole. Název pole, které jsou součástí **fieldName:searchExpression** vždy má přednost před `searchFields` parametr, což je důvod, proč se v tomto příkladu jsme není nutné zahrnout `business_title` v `searchFields` parametru.

## <a name="example-3-fuzzy-search"></a>Příklad 3: vyhledávání přibližných shod

Úplná syntaxe Lucene také podporuje přibližné vyhledávání shody s termíny, které mají podobné konstrukce. Chcete-li provést vyhledávání přibližných shod, přidejte tilda `~` symbolu na konci jednoho slova s volitelný parametr, hodnotu mezi 0 a 2, která určuje vzdálenost úpravy. Například `blue~` nebo `blue~1` vracel spojovací, modrá a blues.

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fráze nejsou přímo podporované. můžete ale zadat přibližné shody na součásti frázi.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Úplná adresa URL

Tento dotaz vyhledává úlohy se termín "partner" (záměrně chybně):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Vyhledávání přibližných shod odpovědi](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Přibližné dotazy nejsou [analyzovat](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy dotazů s neúplné podmínky (předponu dotazu, dotaz zástupný znak, regulární výraz dotazu, fuzzy dotazu) jsou přidány přímo do stromu dotazu obcházení fázi analýzy. Pouze transformace provedené na neúplný dotaz podmínky je předpoklady.
>

## <a name="example-4-proximity-search"></a>Příklad 4: vyhledávání blízkých výrazů
Vyhledávání blízkých výrazů se používají k vyhledání podmínky, která jsou blízko sebe v dokumentu. Vložit tildou "~" symbolu na konci věty následovaný počtem slova, která vytvořit hranici blízkosti. Například "hotelu letiště" přibližně 5 najdou podmínky hotelu a letiště v rámci 5 slov v dokumentu.

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu pro úlohy s označením "vedoucí analytik", kde jsou oddělené oddělovačem více než jedno slovo:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Blízkých výrazů dotazu](media/search-query-lucene-examples/proximity-before.png)

Zkuste to znovu odebrání slova mezi termín "vedoucí analytik". Všimněte si, že pro tento dotaz na rozdíl od 10 pro předchozí dotaz se vrátí 8 dokumenty.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Příklad 5: zvýšení skóre termínu
Zvyšování skóre termínu odkazuje na hodnocení vyšší, pokud obsahuje Posílený termín, vzhledem k dokumentům, které neobsahují termín dokumentu. Zvyšte termín, použijte blikající kurzor, "^", symbol s faktorem zesílení (číslo) na konci se hledaný termín. 

### <a name="full-urls"></a>Úplné adresy URL

V tomto "dotazu before" hledání pro úlohy s označením *počítače analytik* a Všimněte si, že neexistují žádné výsledky obsahující oba slova *počítače* a *analytik*, ještě  *počítač* úlohy jsou v horní části výsledků.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Před zvýšení skóre termínu](media/search-query-lucene-examples/termboostingbefore.png)

V dotazu "po" a opakujte vyhledávání, tentokrát zvýšení skóre výsledky s označením *analytik* přes termín *počítače* Pokud oba slova neexistují. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Víc jako lidé čitelná verze výše uvedeném dotazu je `search=business_title:computer analyst^2`. Pro funkční dotaz `^2` zakódován `%5E2`, což je těžší zobrazíte.

  ![Po zvýšení skóre termínu](media/search-query-lucene-examples/termboostingafter.png)

Zvyšování skóre termínu se liší od bodovací profily, profily vyhodnocování zvýšení určité pole, nikoli konkrétní podmínky. Následující příklad ilustruje znázorňují rozdíl.

Vezměte v úvahu bodovací profil, který zvyšuje odpovídá určité pole, jako například **žánr** v příkladu musicstoreindex. Zvyšování skóre termínu může použít pro další zvýšení určité hledání podmínkami vyšším než jiné. Například "rock ^ elektronické 2" se zvýšit dokumenty, které obsahují hledaný text v **žánr** vyšší než další prohledávatelná pole v indexu pole. Kromě toho dokumenty, které obsahují hledaný termín "rock" bude provedeno řazení vyšší než "elektronického" výsledkem hodnota boost období (2) hledaný termín.

Po nastavení úrovně faktor, tím větší faktor zesílení, více odpovídajících termín, budou relativní vůči jiné podmínky hledání. Ve výchozím nastavení je faktor zesílení 1. I když faktor zesílení musí být kladná, může být menší než 1 (například 0,2).


## <a name="example-6-regex"></a>Příklad 6: Regex

Hledání regulárního výrazu najde shodu na základě obsahu mezi lomítka "/", jako zdokumentované v [Třída RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu hledání úloh pomocí termín vyšší nebo nižší: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Regulární výraz dotazu](media/search-query-lucene-examples/regex.png)

> [!Note]
> Regulární výraz dotazy nejsou [analyzovat](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Pouze transformace provedené na neúplný dotaz podmínky je předpoklady.
>

## <a name="example-7-wildcard-search"></a>Příklad 7: hledání pomocí zástupných znaků
Obecně rozpoznaná syntaxe můžete použít pro více (\*) nebo jednotné hledání zástupný znak (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů se jeden výraz a ne frázi.

### <a name="partial-query-string"></a>Řetězec dotazu částečné

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu hledání pro úlohy, které obsahují předponu "ProgID' bude to zahrnovat obchodní názvy s podmínkami, programování a programátor v ní. Nelze použít * nebo? symbol jako první znak vyhledávání.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Zástupný znak dotazu](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Zástupný znak dotazy nejsou [analyzovat](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Pouze transformace provedené na neúplný dotaz podmínky je předpoklady.
>

## <a name="next-steps"></a>Další postup
Zkuste zadat analyzátor dotazů Lucene ve vašem kódu. Následující odkazy popisují, jak nastavit vyhledávacích dotazů pro .NET a rozhraní REST API. Odkazy používat jednoduchá syntaxe výchozí, budete muset použít, co jste se naučili v tomto článku k určení **queryType**.

* [Dotazování indexu Azure Search pomocí .NET SDK](search-query-dotnet.md)
* [Dotazování indexu Azure Search pomocí rozhraní REST API](search-create-index-rest-api.md)

Odkaz na další syntaxi dotazu architektury a příklady najdete v následujících odkazů:

+ [Příklady jednoduchá syntaxe dotazů](search-query-simple-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplná syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)