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
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401453"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Použít úplnou syntaxi hledání Lucene (rozšířené dotazy v Azure Kognitivní hledání)

Při sestavování dotazů pro Azure Kognitivní hledání můžete nahradit výchozí [jednoduchý analyzátor dotazů](query-simple-syntax.md) výkonnějším [analyzátorem dotazů Lucene v Azure kognitivní hledání](query-lucene-syntax.md) a formulovat specializované a pokročilé definice dotazů. 

Analyzátor Lucene podporuje složité konstrukce dotazů, jako jsou dotazy v oboru pole, přibližné vyhledávání, vpony a zástupné vyhledávání zástupných znaků, hledání blízkosti, zvyšování skóre a hledání v regulárních výrazech. Další napájení přináší dodatečné požadavky na zpracování, takže byste měli očekávat trochu delší dobu provádění. V tomto článku si můžete projít příklady, které demonstrují operace s dotazy na základě úplné syntaxe.

> [!Note]
> Mnohé z specializovaných konstrukcí dotazů, které jsou povolené prostřednictvím úplné syntaxe dotazů Lucene, nejsou [analyzovány textem](search-lucene-query-architecture.md#stage-2-lexical-analysis), které mohou být překvapivé, pokud očekáváte odvozování nebo lemmatizátor nebo předzpracování. Lexikální analýza je prováděna pouze za úplných podmínek (dotaz dotaz nebo fráze). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace prováděná na částečných výrazech dotazu je lowercasing. 
>

## <a name="nyc-jobs-examples"></a>Příklady úloh NYC

Následující příklady využívají [Index vyhledávání úloh NYC](https://azjobsdemo.azurewebsites.net/)  sestávající z úloh, které jsou k dispozici na základě datové sady poskytované [městem OpenData iniciativy z New Yorku](https://nycopendata.socrata.com/). Tato data by se neměla považovat za aktuální nebo kompletní. Index je ve službě izolovaného prostoru, kterou poskytuje Microsoft, což znamená, že k tomu, abyste tyto dotazy zkusili, nepotřebujete předplatné Azure ani Azure Kognitivní hledání.

K tomu, co potřebujete, můžete využít nebo ekvivalentní nástroj pro vystavení požadavku HTTP při získání nebo zveřejnění. Pokud tyto nástroje neznáte, přečtěte si téma [rychlý Start: Prozkoumejte Azure Kognitivní hledání REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Nastavení žádosti

1. Hlavičky požadavku musí mít následující hodnoty:

   | Klíč | Hodnota |
   |-----|-------|
   | Typ obsahu | `application/json`|
   | klíč rozhraní API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (Toto je skutečný klíč rozhraní API pro vyhledávací službu izolovaného prostoru (sandbox), která hostuje index úloh NYC.) |

1. Nastavte operaci na **`GET`** .

1. Nastavte adresu URL na **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + Kolekce Documents v indexu obsahuje veškerý prohledávatelný obsah. Klíč rozhraní API pro dotaz zadaný v hlavičce požadavku funguje jenom pro operace čtení, které cílí na kolekci dokumentů.

   + **`$count=true`** Vrátí počet dokumentů, které odpovídají kritériím hledání. U prázdného vyhledávacího řetězce bude počet všechny dokumenty v indexu (přibližně 2558 v případě úloh NYC).

   + **`search=*`** je nespecifikovaný dotaz, který je ekvivalentní s hodnotou null nebo prázdným hledáním. Není to zvláště užitečné, ale je to nejjednodušší hledání, které můžete provést, a zobrazuje všechna pole, která lze načíst v indexu, včetně všech hodnot.

   + **`queryType=full`** vyvolá úplný analyzátor Lucene.

1. Jako krok ověření vložte následující žádost do pole získat a klikněte na **Odeslat**. Výsledky se vrátí jako podrobné dokumenty JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Postup volání úplné analýzy Lucene

Přidejte **`queryType=full`** k vyvolání úplné syntaxe dotazu a přepsání výchozí jednoduché syntaxe dotazu. Všechny příklady v tomto článku určují **`queryType=full`** parametr Search, který označuje, že je úplná syntaxe zpracována analyzátorem dotazů Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Příklad 1: určení oboru dotazu na seznam polí

Tento první příklad není specifický pro specifickou analýzu, ale zavedeme ho, aby zavedl první základní koncept dotazu: omezení. Tento příklad omezuje provádění dotazů a odpověď na pouze několik konkrétních polí. Znalost způsobu strukturování čitelné odpovědi JSON je důležitá, pokud je váš nástroj pro odesílání nebo hledání v Průzkumníku služby Search. 

Tento dotaz cílí pouze na *business_title* v rámci **`searchFields`** parametru, **`select`** který určuje stejné pole v odpovědi.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  ![Ukázková odpověď po ukázce s výsledky](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre hledání v odpovědi. Rovnoměrné skóre **1** nastane, pokud neexistuje žádné pořadí, protože hledání nevrátilo fulltextové vyhledávání, nebo vzhledem k tomu, že nebyla zadána žádná kritéria. Pro prázdné hledání se řádky vrátí v libovolném pořadí. Pokud zahrnete skutečná kritéria, uvidíte, že výsledky hledání se budou vyvíjet na smysluplné hodnoty.

## <a name="example-2-fielded-search"></a>Příklad 2: hledání v poli

Úplná syntaxe Lucene podporuje určení oboru jednotlivých vyhledávacích výrazů do konkrétního pole. Tento příklad vyhledá obchodní tituly s termínem "na začátku", ale ne méně. Můžete zadat více polí pomocí a.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky (posting_type není zobrazená).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Výraz pro hledání ukázkové odezvy po ukázce" border="false":::

Výraz hledání může být jedno slovo nebo fráze nebo složitější výraz v závorkách, volitelně s logickými operátory. Mezi příklady patří následující:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Nezapomeňte vložit více řetězců v uvozovkách, pokud chcete, aby byly oba řetězce vyhodnoceny jako jediná entita, jako v tomto případě vyhledávání dvou různých míst v `state` poli. V závislosti na nástroji může být nutné znak escape () označit uvozovkami ( `\` ). 

Pole zadané v poli **NázevPole: searchExpression** musí být vyhledávací pole. Podrobnosti o tom, jak se v definicích polí používají atributy indexu, najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-index) .

> [!NOTE]
> V předchozím příkladu **`searchFields`** je parametr vynechán, protože každá část dotazu má explicitně zadaný název pole. Přesto však můžete použít, **`searchFields`** Pokud má dotaz více částí (pomocí příkazů a). Dotaz by se například `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` shodoval pouze s `senior NOT junior` `business_title` polem, zatímco by odpovídal "externímu" s `posting_type` polem. Název pole `fieldName:searchExpression` , který je k dispozici vždy, má přednost před **`searchFields`** , což znamená, že v tomto příkladu můžete vynechat `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Příklad 3: vyhledávání s fuzzy logikou

Úplná syntaxe Lucene podporuje také přibližné vyhledávání, které se shoduje s výrazy, které mají podobnou konstrukci. Chcete-li provést hledání přibližné hodnoty, přidejte na `~` konci jednoho slova symbol tildy s volitelným parametrem, hodnotu mezi 0 a 2, která určuje vzdálenost úprav. Například `blue~` nebo `blue~1` by vrátil modrou, blues a glue.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Fráze nejsou podporované přímo, ale můžete zadat přibližnou shodu pro každý termín fráze s více částmi, jako je například `search=business_title:asosiate~ AND comm~` .  Na snímku obrazovky níže odpověď obsahuje shodu s *přidružením komunity*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Odezva vyhledávání s fuzzy logikou" border="false":::

> [!Note]
> Fuzzy dotazy nejsou [analyzovány](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy dotazů s neúplnými podmínkami (dotaz na předponu, dotaz se zástupnými znaky, regulární dotaz, nepřibližný dotaz) jsou přidány přímo do stromu dotazu a vycházejí z fáze analýzy. Jediná transformace prováděná na částečných výrazech dotazu je lowercasing.
>

## <a name="example-4-proximity-search"></a>Příklad 4: hledání blízkosti

Hledání v blízkosti se používá k vyhledání podmínek, které jsou poblíž sebe v dokumentu. Vložte symbol tildy "~" na konec fráze následovaný počtem slov, která vytvářejí hranice blízkosti. Například "hotelové letiště" ~ 5 vyhledá termíny Hotel a letiště v rámci 5 slov navzájem v dokumentu.

Tento dotaz vyhledá termíny "hlavní" a "analytika", kde je každý výraz oddělený maximálně jedním slovem, a uvozovky ( `\"` ) pro zachování fráze:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky. 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Dotaz na Proximity" border="false":::

Zkuste to znovu a Eliminujte vzdálenost ( `~0` ) mezi slovy "vedoucí analytik". Všimněte si, že pro tento dotaz jsou vraceny 8 dokumentů na rozdíl od 10 pro předchozí dotaz.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Příklad 5: posílení podmínek

Termín zvyšování skóre označuje, že dokument je vyšší, pokud obsahuje posílený termín vzhledem k dokumentům, které neobsahují termín. Chcete-li zvýšit výraz, použijte blikající kurzor, `^` symbol s faktorem zvýšení (číslo) na konci hledaného výrazu.

V tomto dotazu "před" vyhledejte úlohy pomocí programu *počítačový analytik* a Všimněte si, že se nevyskytly žádné výsledky s slovy *Computer* i *analytik*, ale v horní části výsledků se staly úlohy *počítačů* .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

V případě dotazu "po" Opakujte hledání, tentokrát při zvyšování výsledků s výrazem *analytika* v termínu " *počítač* ", pokud obě slova neexistují. Verze dotazu pro humánní čtení je `search=business_title:computer analyst^2` . Pro funkční dotaz v příkazu post `^2` je zakódován jako `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Zvyšování termínu po" border="false":::

Zvyšování termínů se liší od profilů vyhodnocování v tom, že profily vyhodnocování zvyšují určitá pole, nikoli konkrétní podmínky. Následující příklad pomáhá ilustrovat rozdíly.

Vezměte v úvahu profil vyhodnocování, který zvyšuje shodu v určitém poli, jako je třeba **Žánr** v musicstoreindex příkladu. Za účelem dalšího zvýšení úrovně hledaného výrazu, který je vyšší než jiné, se dá použít zvyšování skóre termínu. Například "Rock ^ 2 Electronic" bude zvyšovat dokumenty, které obsahují hledané výrazy v poli **žánru** vyšším než ostatní hledaná pole v indexu. Kromě toho jsou dokumenty, které obsahují hledaný termín "Rock", seřazené vyšší než druhý hledaný výraz "elektronický" v důsledku hodnoty zvýšení termínu (2).

Při nastavování úrovně faktoru čím vyšší je faktor zvýšení, tím důležitější je, že výraz bude relativní vzhledem k ostatním hledaným podmínkám. Ve výchozím nastavení je faktor zvýšení 1. I když faktor zvýšení musí být kladný, může být menší než 1 (například 0,2).

## <a name="example-6-regex"></a>Příklad 6: regulární výraz

Hledání regulárního výrazu vyhledá shodu na základě obsahu mezi lomítky "/", jak je uvedeno ve [třídě RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Dotaz Regex" border="false":::

> [!Note]
> Dotazy regulárního výrazu nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace prováděná na částečných výrazech dotazu je lowercasing.
>

## <a name="example-7-wildcard-search"></a>Příklad 7: hledání pomocí zástupných znaků

Můžete použít obecně rozpoznanou syntaxi pro \* hledání zástupných znaků více (?). Všimněte si, že analyzátor dotazů Lucene podporuje použití těchto symbolů s jedním výrazem a nikoli frází.

V tomto dotazu vyhledejte úlohy, které obsahují předponu "program", která by obsahovala obchodní tituly s výrazy programování a programátory. `*` `?` Jako první znak hledání nelze použít symbol nebo.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Zástupný dotaz" border="false":::

> [!Note]
> Dotazy se zástupnými znaky nejsou [analyzovány](./search-lucene-query-architecture.md#stage-2-lexical-analysis). Jediná transformace prováděná na částečných výrazech dotazu je lowercasing.
>

## <a name="next-steps"></a>Další kroky

Zkuste zadat dotazy v kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pomocí sad Azure SDK.

+ [Dotazování indexu pomocí sady .NET SDK](search-get-started-dotnet.md)
+ [Dotazování indexu pomocí sady Python SDK](search-get-started-python.md)
+ [Dotazování indexu pomocí sady JavaScript SDK](search-get-started-javascript.md)

Další informace o syntaxi, architektuře dotazů a příkladech najdete na následujících odkazech:

+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](query-simple-syntax.md)
+ [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe filtru](search-query-odata-filter.md)