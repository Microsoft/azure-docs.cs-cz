---
title: Použít úplnou syntaxi dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Syntaxe dotazů Lucene pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínů, hledání v regulárních výrazech a hledání pomocí zástupných znaků ve službě Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: ae4dd8b82e40b46da52a1b1f396569fda1dfea2b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694622"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Použít úplnou syntaxi hledání Lucene (rozšířené dotazy v Azure Kognitivní hledání)

Při sestavování dotazů pro Azure Kognitivní hledání můžete nahradit výchozí [jednoduchý analyzátor dotazů](query-simple-syntax.md) dalšími [analyzátory dotazů obsáhlém Lucene v Azure kognitivní hledání](query-lucene-syntax.md) a formulovat specializované a pokročilé definice dotazů. 

Analyzátor Lucene podporuje složité konstrukce dotazů, jako jsou dotazy v oboru pole, přibližné vyhledávání, vpony a zástupné vyhledávání zástupných znaků, hledání blízkosti, zvyšování skóre a hledání v regulárních výrazech. Další napájení přináší dodatečné požadavky na zpracování, takže byste měli očekávat trochu delší dobu provádění. V tomto článku si můžete projít příklady, které demonstrují operace dotazů dostupné při použití úplné syntaxe.

> [!Note]
> Mnohé z specializovaných konstrukcí dotazů, které jsou povolené prostřednictvím úplné syntaxe dotazů Lucene, nejsou [analyzovány textem](search-lucene-query-architecture.md#stage-2-lexical-analysis), které mohou být překvapivé, pokud očekáváte odvozování nebo lemmatizátor nebo předzpracování. Lexikální analýza je prováděna pouze za úplných podmínek (dotaz dotaz nebo fráze). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace prováděná na částečných výrazech dotazu je lowercasing. 
>

## <a name="formulate-requests-in-postman"></a>Formulování požadavků v post

Následující příklady využívají index vyhledávání úloh NYC sestávající z úloh, které jsou k dispozici na základě datové sady poskytované [městem OpenData iniciativy z New Yorku](https://opendata.cityofnewyork.us/) . Tato data by se neměla považovat za aktuální nebo kompletní. Index je ve službě izolovaného prostoru, kterou poskytuje Microsoft, což znamená, že k tomu, abyste tyto dotazy zkusili, nepotřebujete předplatné Azure ani Azure Kognitivní hledání.

K tomu, co potřebujete, je odeslání nebo ekvivalent nástroje pro vystavení požadavku HTTP na GET. Další informace najdete v tématu [prozkoumání pomocí klientů REST](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Nastavit hlavičku požadavku

1. V hlavičce požadavku nastavte **typ obsahu** na `application/json` .

2. Přidejte **klíč API-Key** a nastavte jej na tento řetězec: `252044BE3886FE4A8E3BAA4F595114BB` . Toto je klíč dotazu pro vyhledávací službu izolovaného prostoru (sandbox), která hostuje index úloh NYC.

Po zadání hlavičky žádosti ji můžete znovu použít pro všechny dotazy v tomto článku, přičemž se odkládá jenom řetězec **Search =** String. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Parametry nastavení hlavičky žádosti post" border="false":::

### <a name="set-the-request-url"></a>Nastavení adresy URL žádosti

Request je příkaz GET spárováný s adresou URL, která obsahuje koncový bod Azure Kognitivní hledání a hledaný řetězec.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Hlavička žádosti post-GET" border="false":::

Složení adresy URL má následující prvky:

+ **`https://azs-playground.search.windows.net/`** je vyhledávací služba izolovaného prostoru, kterou udržuje vývojový tým Azure Kognitivní hledání. 
+ **`indexes/nycjobs/`** je index úloh NYC v kolekci indexů dané služby. V žádosti se vyžaduje název služby i index.
+ **`docs`** je kolekce dokumentů obsahující veškerý prohledávatelný obsah. Klíč rozhraní API pro dotaz zadaný v hlavičce požadavku funguje jenom na operacích čtení, které cílí na kolekci dokumentů.
+ **`api-version=2020-06-30`** nastaví verzi rozhraní API, což je povinný parametr u všech požadavků.
+ **`search=*`** je řetězec dotazu, který je v počátečním dotazu null, vrací první 50 výsledků (ve výchozím nastavení).

## <a name="send-your-first-query"></a>Odeslání prvního dotazu

Jako krok ověření vložte následující žádost do pole získat a klikněte na **Odeslat**. Výsledky se vrátí jako podrobné dokumenty JSON. Vrátí se celé dokumenty, což vám umožní zobrazit všechna pole a všechny hodnoty.

Vložte tuto adresu URL do klienta REST jako krok ověření a zobrazte strukturu dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Řetězec dotazu, **`search=*`** , je nespecifikované vyhledávání odpovídající hodnotě null nebo prázdné vyhledávání. Je to nejjednodušší hledání, které můžete udělat.

Volitelně můžete přidat **`$count=true`** adresu URL a vrátit tak počet dokumentů, které odpovídají kritériím vyhledávání. U prázdného vyhledávacího řetězce se jedná o všechny dokumenty v indexu (přibližně 2800 v případě úloh NYC).

## <a name="how-to-invoke-full-lucene-parsing"></a>Postup volání úplné analýzy Lucene

Přidáním metody **queryType = Full** můžete vyvolat úplnou syntaxi dotazu a přepsat výchozí jednoduchou syntaxi dotazu. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

Všechny příklady v tomto článku určují parametr **queryType = Full** Search, který označuje, že je úplná syntaxe zpracována analyzátorem dotazů Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Příklad 1: určení oboru dotazu na seznam polí

Tento první příklad není specifický pro Lucene, ale můžeme s ním zavádět první základní koncept dotazu: obor pole. V tomto příkladu je celý dotaz a odpověď na pouze několik konkrétních polí. Znalost způsobu strukturování čitelné odpovědi JSON je důležitá, pokud je váš nástroj pro odesílání nebo hledání v Průzkumníku služby Search. 

Pro zkrácení se dotaz zaměřuje jenom na pole *business_title* a určí jenom obchodní tituly. Parametr **searchFields** omezuje provádění dotazu pouze na pole business_title a **možnost** určuje, která pole jsou obsažena v odpovědi.

### <a name="search-expression"></a>Výraz hledání

```http
&search=*&searchFields=business_title&$select=business_title
```

Tady je stejný dotaz s více poli v seznamu odděleném čárkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Mezery za čárkami jsou nepovinné.

> [!Tip]
> Při použití REST API z kódu aplikace Nezapomeňte zadat parametry pro kódování adresy URL jako `$select` a `searchFields` .

### <a name="full-url"></a>Úplná adresa URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  ![Ukázková odpověď po ukázce s výsledky](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre hledání v odpovědi. Rovnoměrné skóre 1 nastane, pokud není k dispozici žádný rozměr, protože hledání nevrátilo fulltextové vyhledávání, nebo vzhledem k tomu, že se nepoužila žádná kritéria. Pro prázdné vyhledávání bez kritérií se řádky vrátí v libovolném pořadí. Když zahrnete skutečná kritéria hledání, uvidíte výsledky hledání, které se budou vyvíjet na smysluplné hodnoty.

## <a name="example-2-fielded-search"></a>Příklad 2: hledání v poli

Úplná syntaxe Lucene podporuje určení oboru jednotlivých vyhledávacích výrazů do konkrétního pole. Tento příklad vyhledá obchodní tituly s termínem "na začátku", ale ne méně.

### <a name="search-expression"></a>Výraz hledání

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Toto je stejný dotaz s více poli.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Úplná adresa URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Výraz pro hledání ukázkové odezvy po ukázce" border="false":::

Můžete definovat operaci vyhledávání pomocí pole s syntaxí **NázevPole: searchExpression** , kde výraz vyhledávání může být jedno slovo nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Nezapomeňte vložit více řetězců v uvozovkách, pokud chcete, aby byly oba řetězce vyhodnoceny jako jediná entita, jako v tomto případě vyhledávání dvou různých míst v `state` poli. Také se ujistěte, že je operátor velkými písmeny, jak vidíte s ne a a.

Pole zadané v poli **NázevPole: searchExpression** musí být vyhledávací pole. Podrobnosti o tom, jak se v definicích polí používají atributy indexu, najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-index) .

> [!NOTE]
> V předchozím příkladu jsme nemuseli použít `searchFields` parametr, protože každá část dotazu má explicitně zadaný název pole. Tento parametr však lze použít i v `searchFields` případě, že chcete spustit dotaz, ve kterém jsou některé části vymezeny na konkrétní pole, a zbytek by se mohl vztahovat na několik polí. Dotaz by se například `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` shodoval pouze s `senior NOT junior` `business_title` polem, zatímco by odpovídal "externímu" s `posting_type` polem. Název pole zadaný v poli **NázevPole: searchExpression** vždycky má přednost před `searchFields` parametr, což je proč v tomto příkladu nemusíme do `business_title` parametru zahrnout `searchFields` .

## <a name="example-3-fuzzy-search"></a>Příklad 3: vyhledávání s fuzzy logikou

Úplná syntaxe Lucene podporuje také přibližné vyhledávání, které se shoduje s výrazy, které mají podobnou konstrukci. Chcete-li provést hledání přibližné hodnoty, přidejte na `~` konci jednoho slova symbol tildy s volitelným parametrem, hodnotu mezi 0 a 2, která určuje vzdálenost úprav. Například `blue~` nebo `blue~1` by vrátil modrou, blues a glue.

### <a name="search-expression"></a>Výraz hledání

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Fráze nejsou podporovány přímo, ale můžete zadat přibližnou shodu pro části komponenty fráze.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Úplná adresa URL

Tento dotaz vyhledá úlohy s termínem "přidružit" (záměrně nesprávně napsaný):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Odezva vyhledávání s fuzzy logikou](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Fuzzy dotazy nejsou [analyzovány](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace provedená v neúplných výrazech dotazu je lowercasing.
>

## <a name="example-4-proximity-search"></a>Příklad 4: hledání blízkosti
Hledání v blízkosti se používá k vyhledání podmínek, které jsou poblíž sebe v dokumentu. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti. Například "hotelové letiště" ~ 5 vyhledá termíny Hotel a letiště v rámci 5 slov navzájem v dokumentu.

### <a name="search-expression"></a>Výraz hledání

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu pro úlohy s termínem "vedoucí analytik", kde je oddělený o více než jedno slovo:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Dotaz na Proximity" border="false":::

Zkuste to znovu a odstraňte slova mezi termínem "vedoucí analytik". Všimněte si, že pro tento dotaz jsou vraceny 8 dokumentů na rozdíl od 10 pro předchozí dotaz.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Příklad 5: posílení podmínek
Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. Chcete-li zvýšit výraz, použijte blikající kurzor "^", symbol s faktorem zvýšení (číslo) na konci hledaného výrazu. 

### <a name="full-urls"></a>Úplné adresy URL

V tomto dotazu "před" vyhledejte úlohy pomocí programu *počítačový analytik* a Všimněte si, že se nevyskytly žádné výsledky s slovy *Computer* i *analytik*, ale v horní části výsledků se staly úlohy *počítačů* .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  :::image type="content" source="media/search-query-lucene-examples/termboostingbefore.png" alt-text="Zvyšování skóre termínu před" border="false":::

V případě dotazu "po" Opakujte hledání, tentokrát při zvyšování výsledků s výrazem *analytika* v termínu " *počítač* ", pokud obě slova neexistují. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Lépe čitelná verze výše uvedeného dotazu je `search=business_title:computer analyst^2` . Pro funkční dotaz `^2` je zakódován jako `%5E2` , což je těžké vidět.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Zvyšování termínu po" border="false":::

Zvyšování termínů se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní podmínky. Následující příklad pomáhá ilustrovat rozdíly.

Vezměte v úvahu profil vyhodnocování, který zvyšuje shodu v určitém poli, jako je třeba **Žánr** v musicstoreindex příkladu. Za účelem dalšího zvýšení úrovně hledaného výrazu, který je vyšší než jiné, se dá použít zvyšování skóre termínu. Například "Rock ^ 2 Electronic" bude zvyšovat dokumenty, které obsahují hledané výrazy v poli **žánru** vyšším než ostatní hledaná pole v indexu. Kromě toho jsou dokumenty, které obsahují hledaný termín "Rock", seřazené vyšší než druhý hledaný výraz "elektronický" v důsledku hodnoty zvýšení termínu (2).

Při nastavování úrovně faktoru čím vyšší je faktor zvýšení, tím důležitější je, že výraz bude relativní vzhledem k ostatním hledaným podmínkám. Ve výchozím nastavení je faktor zvýšení 1. I když faktor zvýšení musí být kladný, může být menší než 1 (například 0,2).


## <a name="example-6-regex"></a>Příklad 6: regulární výraz

Hledání regulárního výrazu vyhledá shodu na základě obsahu mezi lomítky "/", jak je uvedeno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Výraz hledání

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu vyhledejte úlohy, které mají termín vyšší nebo nižší: `search=business_title:/(Sen|Jun)ior/` .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Dotaz Regex" border="false":::

> [!Note]
> Dotazy regulárního výrazu nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace provedená v neúplných výrazech dotazu je lowercasing.
>

## <a name="example-7-wildcard-search"></a>Příklad 7: hledání pomocí zástupných znaků
Můžete použít obecně rozpoznanou syntaxi pro \* hledání zástupných znaků více (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jedním výrazem a nikoli frází.

### <a name="search-expression"></a>Výraz hledání

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Úplná adresa URL

V tomto dotazu vyhledejte úlohy, které obsahují předponu "program", která by obsahovala obchodní tituly s výrazy programování a programátory. Nemůžete použít * ani? symbol jako první znak hledání.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Zástupný dotaz" border="false":::

> [!Note]
> Dotazy se zástupnými znaky nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace provedená v neúplných výrazech dotazu je lowercasing.
>

## <a name="next-steps"></a>Další kroky
Zkuste zadat analyzátor dotazů Lucene ve vašem kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pro rozhraní .NET i REST API. Odkazy používají výchozí jednoduchou syntaxi, takže budete muset použít, co jste se naučili v tomto článku, a zadat tak **queryType**.

* [Dotazování indexu pomocí sady .NET SDK](./search-get-started-dotnet.md)
* [Dotazování indexu pomocí REST API](./search-get-started-powershell.md)

Další informace o syntaxi, architektuře dotazů a příkladech najdete na následujících odkazech:

+ [Příklady dotazů jednoduchých syntaxí](search-query-simple-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplná syntaxe dotazů Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)