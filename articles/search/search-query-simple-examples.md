---
title: Použít jednoduchou syntaxi dotazů Lucene
titleSuffix: Azure Cognitive Search
description: Naučte se, když spustíte dotazy založené na jednoduché syntaxi pro fulltextové vyhledávání, vyfiltrujte hledání, geografické vyhledávání, omezující kontrolu proti indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: 51d36211c7ffa0507a186c9a1e1f2b52d478fe4e
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369088"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Použití jednoduché syntaxe hledání v Azure Kognitivní hledání

V Azure Kognitivní hledání vyvolá [Jednoduchá syntaxe dotazů](query-simple-syntax.md) výchozí analyzátor dotazu pro fulltextové vyhledávání. Analyzátor je rychlý a pracuje s běžnými scénáři, včetně fulltextového vyhledávání, filtrovaných a omezujících výsledků hledání a hledání předpon. Tento článek používá příklady k ilustraci jednoduchého použití syntaxe v žádosti [hledání dokumentů (REST API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Alternativná syntaxe dotazu je [Úplná Lucene](query-lucene-syntax.md), která podporuje složitější struktury dotazů, jako je hledání přibližných a zástupných znaků. Další informace a příklady najdete v tématu [použití úplné syntaxe Lucene](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Příklady úloh NYC

Následující příklady využívají [Index vyhledávání úloh NYC](https://azjobsdemo.azurewebsites.net/) sestávající z úloh, které jsou k dispozici na základě datové sady poskytované [městem OpenData iniciativy z New Yorku](https://nycopendata.socrata.com/). Tato data by se neměla považovat za aktuální nebo kompletní. Index je ve službě izolovaného prostoru, kterou poskytuje Microsoft, což znamená, že k tomu, abyste tyto dotazy zkusili, nepotřebujete předplatné Azure ani Azure Kognitivní hledání.

K tomu, co potřebujete, můžete využít nebo ekvivalentní nástroj pro vystavení požadavku HTTP při získání nebo zveřejnění. Pokud tyto nástroje neznáte, přečtěte si téma [rychlý Start: Prozkoumejte Azure Kognitivní hledání REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Nastavení žádosti

1. Hlavičky požadavku musí mít následující hodnoty:

   | Klíč | Hodnota |
   |-----|-------|
   | Typ obsahu | `application/json`|
   | klíč rozhraní API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (Toto je skutečný klíč rozhraní API pro vyhledávací službu izolovaného prostoru (sandbox), která hostuje index úloh NYC.) |

1. Nastavte operaci na **`GET`** .

1. Nastavte adresu URL na **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + Kolekce Documents v indexu obsahuje veškerý prohledávatelný obsah. Klíč rozhraní API pro dotaz zadaný v hlavičce požadavku funguje jenom pro operace čtení, které cílí na kolekci dokumentů.

   + **`$count=true`** Vrátí počet dokumentů, které odpovídají kritériím hledání. U prázdného vyhledávacího řetězce bude počet všechny dokumenty v indexu (přibližně 2558 v případě úloh NYC).

   + **`search=*`** je nespecifikovaný dotaz, který je ekvivalentní s hodnotou null nebo prázdným hledáním. Není to zvláště užitečné, ale je to nejjednodušší hledání, které můžete provést, a zobrazuje všechna pole, která lze načíst v indexu, včetně všech hodnot.

1. Jako krok ověření vložte následující žádost do pole získat a klikněte na **Odeslat**. Výsledky se vrátí jako podrobné dokumenty JSON.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Postup vyvolání jednoduchých analýz dotazů

U interaktivních dotazů není nutné zadávat nic: výchozí hodnota je jednoduchá. V kódu, pokud jste dřív vyvolali **`queryType=full`** , můžete výchozí nastavení obnovit pomocí **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Příklad 1: fulltextové vyhledávání u konkrétních polí

Tento první příklad není specifický pro specifickou analýzu, ale zavedeme ho, aby zavedl první základní koncept dotazu: omezení. Tento příklad omezuje provádění dotazů a odpověď na pouze několik konkrétních polí. Znalost způsobu strukturování čitelné odpovědi JSON je důležitá, pokud je váš nástroj pro odesílání nebo hledání v Průzkumníku služby Search. 

Tento dotaz cílí pouze na *business_title* v rámci **`searchFields`** parametru, **`select`** který určuje stejné pole v odpovědi.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Ukázková odpověď po ukázce" border="false":::

Možná jste si všimli skóre hledání v odpovědi. Rovnoměrné skóre **1** nastane, pokud neexistuje žádné pořadí, protože hledání nevrátilo fulltextové vyhledávání, nebo vzhledem k tomu, že nebyla zadána žádná kritéria. Pro prázdné hledání se řádky vrátí v libovolném pořadí. Pokud zahrnete skutečná kritéria, uvidíte, že výsledky hledání se budou vyvíjet na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledání podle ID

Když vrátíte výsledky hledání v dotazu, logické následné kroky je poskytnout stránku podrobností, která obsahuje více polí z dokumentu. V tomto příkladu se dozvíte, jak vrátit jeden dokument pomocí [operace vyhledávání](/rest/api/searchservice/lookup-document) , která bude předána ID dokumentu.

Všechny dokumenty mají jedinečný identifikátor. Chcete-li vyzkoušet syntaxi vyhledávacího dotazu, nejprve vraťte seznam ID dokumentů, abyste mohli najít jeden, který chcete použít. V případě úloh NYC jsou identifikátory uloženy v `id` poli.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Dále načtěte dokument z kolekce založené na `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", která se objevila jako první v předchozí odpovědi. Následující dotaz vrátí všechna pole k disvratení pro celý dokument.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Příklad 3: filtrování dotazů

[Syntaxe filtru](./search-query-odata-filter.md) je výraz OData, který můžete použít samostatně nebo s **`search`** . Samostatný filtr bez parametru vyhledávání je užitečný v případě, že výraz filtru dokáže plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu není k dispozici žádná lexikální nebo Lingvistická analýza, žádné bodování (všechny skóre jsou 1) a žádné hodnocení. Všimněte si, že hledaný řetězec je prázdný.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Při použití společně se filtr použije jako první pro celý index a pak se provede hledání na základě výsledků filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrovat odpověď dotazu" border="false":::

Dalším účinným způsobem, jak kombinovat filtrování a hledání **`search.ismatch*()`** , je ve výrazu filtru, kde můžete použít vyhledávací dotaz v rámci filtru. Tento výraz filtru používá zástupný *znak k výběru* business_title včetně termínu plánování, plánování, plánování a tak dále.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Další informace o této funkci naleznete v tématu [Search. Match in "Filter Examples"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Příklad 4: filtry rozsahu

Filtrování rozsahu je podporováno prostřednictvím **`$filter`** výrazů pro libovolný datový typ. Následující příklady vyhledají číselná a řetězcová pole. 

Datové typy jsou důležité v filtrech rozsahu a fungují nejlépe, pokud jsou číselná data v číselném poli a řetězcová data v polích řetězců. Číselná data v polích řetězců nejsou vhodná pro rozsahy, protože číselné řetězce nejsou srovnatelné v Azure Kognitivní hledání.

Následující dotaz je numerický rozsah:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtr rozsahu pro číselné rozsahy" border="false":::

V tomto dotazu je rozsah nad polem typu řetězec (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtr rozsahu pro textové rozsahy" border="false":::

> [!NOTE]
> Omezující vlastnosti přes rozsahy hodnot jsou běžným požadavkem na aplikaci vyhledávání. Další informace a příklady najdete v tématu [postup sestavení filtru omezující vlastnosti](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Příklad 5: geografické hledání

Vzorový index obsahuje pole geo_location s souřadnicemi zeměpisné šířky a délky. V tomto příkladu se používá [funkce Geo. Distance](search-query-odata-geo-spatial-functions.md#examples) , která filtruje dokumenty v rámci obvodu počátečního bodu, a to až do libovolné vzdálenosti (v kilometrech), kterou zadáte. Poslední hodnotu v dotazu (4) můžete upravit tak, aby se snížila nebo rozšířila oblast povrchu dotazu.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Pro lepší čitelnost výsledků se výsledky hledání oříznou tak, aby zahrnovaly název úlohy a pracovní umístění. Počáteční souřadnice byly získány z náhodného dokumentu v indexu (v tomto případě pro pracovní umístění na Staten ostrově.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Mapa Staten ostrova" border="false":::

## <a name="example-6-search-precision"></a>Příklad 6: přesnost vyhledávání

Termínové dotazy jsou jednoduché a pravděpodobně mnoho z nich, které jsou vyhodnocovány nezávisle. Frázové dotazy jsou uzavřeny v uvozovkách a vyhodnocovány jako doslovné řetězce. Přesnost shody je řízena operátory a searchMode.

Příklad 1: `search=fire`  odpovídá na 140 výsledků, kde všechny shody obsahují slovo, které se spustí někde v dokumentu.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Příklad 2: `search=fire department` vrátí 2002 výsledků. Shody se vrátí pro dokumenty, které obsahují buď oheň, nebo oddělení.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Příklad 3: `search="fire department"` vrátí 77 výsledků. Uzavření řetězce v uvozovkách vytvoří frázi, která se skládá z obou podmínek, a shody se v indexu skládají z kombinovaných podmínek. To vysvětluje, proč není podobné hledání `search=+fire +department` ekvivalentní. Oba podmínky jsou požadovány, ale jsou prohledávány nezávisle. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Vzhledem k tomu, že je zadán dotaz fráze pomocí uvozovek, tento příklad přidá řídicí znak ( `\` ) pro zachování syntaxe.

## <a name="example-7-booleans-with-searchmode"></a>Příklad 7: logické hodnoty s searchMode

Jednoduchá syntaxe podporuje logické operátory ve formě znaků ( `+, -, |` ). Parametr searchMode informuje o kompromisech mezi přesností a odvoláním a s **`searchMode=any`** upřednostněním odvolání (párování u všech kritérií má za následek, že dokument pro sadu výsledků) a **`searchMode=all`** upřednostňuje přesnost (všechna kritéria musí být shodná). 

Výchozí hodnota je **`searchMode=any`** , což může být matoucí v případě, že vytváříte dotaz s více operátory a místo užších výsledků získáváte širší výsledky. To platí zejména u možnosti Ne, kde výsledky zahrnují všechny dokumenty "neobsahující" konkrétní výraz.

Při použití výchozího searchMode (any) se vrátí 2800 dokumentů: těch, které obsahují frázi "Požární oddělení", a také všechny dokumenty, které nemají frázi "MetroTech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="libovolný režim hledání" border="false":::

Změna k **`searchMode=all`** vykonání kumulativního efektu pro kritéria a vrátí menší sadu výsledků dotazu-21 dokumentů, které obsahují dokumenty obsahující celou frázi "Požární oddělení", a tyto úlohy se v adrese centra MetroTech mínus.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="vše v režimu hledání" border="false":::

## <a name="example-8-structuring-results"></a>Příklad 8: strukturování výsledků

Několik parametrů řídí, která pole jsou ve výsledcích hledání, počet dokumentů vrácených v každé dávce a pořadí řazení. Tento příklad překryje několik předchozích příkladů a omezí výsledky na konkrétní pole pomocí **`$select`** příkazu a doslovného kritéria hledání a vrátí 82 shod.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Připojeno k předchozímu příkladu, můžete řadit podle názvu. Toto řazení *funguje, protože civil_service_title v indexu* .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Výsledky stránkování jsou implementovány pomocí **`$top`** parametru, v tomto případě vrací prvních 5 dokumentů:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Pokud chcete získat další 5, přeskočte první dávku:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

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