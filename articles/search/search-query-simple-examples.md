---
title: Vytvoření jednoduchého dotazu
titleSuffix: Azure Cognitive Search
description: Naučte se, když spustíte dotazy založené na jednoduché syntaxi pro fulltextové vyhledávání, vyfiltrujte hledání, geografické vyhledávání, omezující kontrolu proti indexu služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 027852216b3f2055a5a381d00aff94526953b898
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007870"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Vytvoření jednoduchého dotazu ve službě Azure Kognitivní hledání

V Azure Kognitivní hledání vyvolá [Jednoduchá syntaxe dotazů](query-simple-syntax.md) výchozí analyzátor dotazů pro provádění fulltextových vyhledávacích dotazů pro index. Tento analyzátor je rychlý a pracuje s běžnými scénáři, včetně fulltextového vyhledávání, filtrovaných a omezujících výsledků hledání a geografického vyhledávání. 

V tomto článku používáme příklady k ilustraci jednoduché syntaxe a naplnění `search=` parametru operace [hledání dokumentů](/rest/api/searchservice/search-documents) .

Alternativná syntaxe dotazu je [Úplná Lucene](query-lucene-syntax.md), která podporuje složitější struktury dotazů, jako je hledání přibližných a zástupných znaků. to může trvat déle. Další informace a příklady, které demonstrují úplnou syntaxi, najdete v tématu [použití úplné syntaxe Lucene](search-query-lucene-examples.md).

Vyberte si z následujících nástrojů a rozhraní API pro odesílání dotazů.

| Metodologie | Popis |
|-------------|-------------|
| [Průzkumník vyhledávání (portál)](search-explorer.md) | Poskytuje panel hledání a možnosti pro indexování a výběry verzí rozhraní API. Výsledky se vrátí jako dokumenty JSON. Doporučuje se pro zkoumání, testování a ověřování. <br/>[Další informace](search-get-started-portal.md#query-index) | 
| [Post nebo Visual Studio Code](search-get-started-rest.md) | Nástroje pro testování webu jsou vynikající volbou pro formulování volání REST v [dokumentech hledání](/rest/api/searchservice/search-documents) . REST API podporuje každou programovou operaci ve službě Azure Kognitivní hledání, takže můžete požadavky vystavovat interaktivně, aby se průzkum zaměřil na konkrétní úkol.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Klient, který se dá použít k dotazování indexu služby Azure Kognitivní hledání.  <br/>[Další informace](search-howto-dotnet-sdk.md)  |

## <a name="formulate-requests-in-postman"></a>Formulování požadavků v post

Následující příklady využívají index vyhledávání úloh NYC sestávající z úloh, které jsou k dispozici na základě datové sady poskytované [městem OpenData iniciativy z New Yorku](https://nycopendata.socrata.com/) . Tato data by se neměla považovat za aktuální nebo kompletní. Index je ve službě izolovaného prostoru, kterou poskytuje Microsoft, což znamená, že k tomu, abyste tyto dotazy zkusili, nepotřebujete předplatné Azure ani Azure Kognitivní hledání.

K tomu, co potřebujete, je odeslání nebo ekvivalent nástroje pro vystavení požadavku HTTP na GET. Další informace najdete v tématu [rychlý Start: Prozkoumejte Azure Kognitivní hledání REST API](search-get-started-rest.md).

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

Řetězec dotazu, **`search=*`** , je nespecifikované vyhledávání odpovídající hodnotě null nebo prázdné vyhledávání. Není to zvláště užitečné, ale jedná se o nejjednodušší hledání, které můžete provést.

Volitelně můžete přidat **`$count=true`** adresu URL a vrátit tak počet dokumentů, které odpovídají kritériím vyhledávání. U prázdného vyhledávacího řetězce se jedná o všechny dokumenty v indexu (přibližně 2800 v případě úloh NYC).

## <a name="how-to-invoke-simple-query-parsing"></a>Postup vyvolání jednoduchých analýz dotazů

U interaktivních dotazů není nutné zadávat nic: výchozí hodnota je jednoduchá. Pokud jste v kódu dříve vyvolali příkaz **querytype = Full** pro úplnou syntaxi dotazu, mohli byste výchozí nastavení obnovit pomocí příkazu **querytype = Simple**.

## <a name="example-1-field-scoped-query"></a>Příklad 1: dotaz v oboru pole

Tento první příklad není specifický pro specifickou analýzu, ale zavedeme ho, aby zavedl první základní koncept dotazu: omezení. V tomto příkladu se jedná o provádění dotazů a odpověď na pouze několik konkrétních polí. Znalost způsobu strukturování čitelné odpovědi JSON je důležitá, pokud je váš nástroj pro odesílání nebo hledání v Průzkumníku služby Search. 

Pro zkrácení se dotaz zaměřuje jenom na pole *business_title* a určí jenom obchodní tituly. Syntaxe je **searchFields** k omezení provádění dotazu pouze na pole business_title a **Vyberte** , která pole jsou obsažena v odpovědi.

### <a name="partial-query-string"></a>Částečný řetězec dotazu

```http
searchFields=business_title&$select=business_title&search=*
```

Tady je stejný dotaz s více poli v seznamu odděleném čárkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Úplná adresa URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Ukázková odpověď po ukázce" border="false":::

Možná jste si všimli skóre hledání v odpovědi. Rovnoměrné skóre 1 nastane, pokud není k dispozici žádný rozměr, protože hledání nevrátilo fulltextové vyhledávání, nebo vzhledem k tomu, že se nepoužila žádná kritéria. Pro prázdné vyhledávání bez kritérií se řádky vrátí v libovolném pořadí. Pokud zahrnete skutečná kritéria, uvidíte, že výsledky hledání se budou vyvíjet na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledání podle ID

Tento příklad je netypický, ale při vyhodnocování chování vyhledávání můžete chtít zkontrolovat celý obsah konkrétního dokumentu a pochopit, proč byl zahrnutý nebo vyloučený z výsledků. Chcete-li vrátit jediný dokument v celém rozsahu, použijte [operaci vyhledávání](/rest/api/searchservice/lookup-document) a předejte mu ID dokumentu.

Všechny dokumenty mají jedinečný identifikátor. Chcete-li vyzkoušet syntaxi vyhledávacího dotazu, nejprve vraťte seznam ID dokumentů, abyste mohli najít jeden, který chcete použít. V případě úloh NYC jsou identifikátory uloženy v `id` poli.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

Dalším příkladem je vyhledávací dotaz vracející konkrétní dokument založený na `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", který se objevil jako první v předchozí odpovědi. Následující dotaz vrátí celý dokument, nikoli pouze vybraná pole. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Příklad 3: filtrování dotazů

[Syntaxe filtru](./search-query-odata-filter.md) je výraz OData, který můžete použít se službou **Search** nebo sám o sobě. Samostatný filtr bez parametru vyhledávání je užitečný v případě, že výraz filtru dokáže plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu není k dispozici žádná lexikální nebo Lingvistická analýza, žádné bodování (všechny skóre jsou 1) a žádné hodnocení. Všimněte si, že hledaný řetězec je prázdný.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Při použití společně se filtr použije jako první pro celý index a pak se provede hledání na základě výsledků filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrovat odpověď dotazu" border="false":::

Pokud si to chcete vyzkoušet v příspěvku pomocí GET, můžete vložit do tohoto řetězce:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Dalším účinným způsobem, jak kombinovat filtrování a hledání **`search.ismatch*()`** , je ve výrazu filtru, kde můžete použít vyhledávací dotaz v rámci filtru. Tento výraz filtru používá zástupný *znak k výběru* business_title včetně termínu plánování, plánování, plánování a tak dále.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Další informace o této funkci naleznete v tématu [Search. Match in "Filter Examples"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Příklad 4: filtry rozsahu

Filtrování rozsahu je podporováno prostřednictvím **`$filter`** výrazů pro libovolný datový typ. Následující příklady vyhledají číselná a řetězcová pole. 

Datové typy jsou důležité v filtrech rozsahu a fungují nejlépe, pokud jsou číselná data v číselném poli a řetězcová data v polích řetězců. Číselná data v polích řetězců nejsou vhodná pro rozsahy, protože číselné řetězce nejsou srovnatelné v Azure Kognitivní hledání. 

Následující příklady jsou ve formátu POST pro čitelnost (číselný rozsah následovaný textem Range):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtr rozsahu pro číselné rozsahy" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtr rozsahu pro textové rozsahy" border="false":::

Můžete je také vyzkoušet v příspěvku pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Omezující vlastnosti přes rozsahy hodnot jsou běžným požadavkem na aplikaci vyhledávání. Další informace a příklady vytváření filtrů pro navigační struktury omezující vlastnosti najdete v části ["filtrování na základě rozsahu" v tématu *implementace omezujících navigačních*](search-faceted-navigation.md#filter-based-on-a-range)objektů.

## <a name="example-5-geo-search"></a>Příklad 5: geografické hledání

Vzorový index obsahuje pole geo_location s souřadnicemi zeměpisné šířky a délky. V tomto příkladu se používá [funkce Geo. Distance](./search-query-odata-geo-spatial-functions.md#examples) , která filtruje dokumenty v rámci obvodu počátečního bodu, a to až do libovolné vzdálenosti (v kilometrech), kterou zadáte. Poslední hodnotu v dotazu (4) můžete upravit tak, aby se snížila nebo rozšířila oblast povrchu dotazu.

Následující příklad je ve formátu POST pro čitelnost:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Pro lepší čitelnost výsledků se výsledky hledání oříznou tak, aby zahrnovaly ID úlohy, název úlohy a pracovní umístění. Počáteční souřadnice byly získány z náhodného dokumentu v indexu (v tomto případě pro pracovní umístění na Staten ostrově.

Můžete to také vyzkoušet v příspěvku pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Příklad 6: přesnost vyhledávání

Termínové dotazy jsou jednoduché a pravděpodobně mnoho z nich, které jsou vyhodnocovány nezávisle. Frázové dotazy jsou uzavřeny v uvozovkách a vyhodnocovány jako doslovné řetězce. Přesnost shody je řízena operátory a searchMode.

Příklad 1: **`&search=fire`**  vrátí 150 výsledků, kde všechny shody obsahují slovo, které se spustí někde v dokumentu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Příklad 2: **`&search=fire department`** vrátí 2002 výsledků. Shody se vrátí pro dokumenty, které obsahují buď oheň, nebo oddělení.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Příklad 3: **`&search="fire department"`** vrátí 82 výsledků. Uzavření řetězce v uvozovkách je doslovné hledání obou podmínek a shody se v indexu skládají z kombinovaných podmínek. To vysvětluje, proč není podobné hledání **`search=+fire +department`** ekvivalentní. Oba podmínky jsou požadovány, ale jsou prohledávány nezávisle. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Příklad 7: logické hodnoty s searchMode

Jednoduchá syntaxe podporuje logické operátory ve formě znaků ( `+, -, |` ). Parametr searchMode informuje o kompromisech mezi přesností a odvoláním a s `searchMode=any` upřednostněním odvolání (párování u všech kritérií má za následek, že dokument pro sadu výsledků) a `searchMode=all` upřednostňuje přesnost (všechna kritéria musí být shodná). Výchozí hodnota je `searchMode=any` , což může být matoucí v případě, že vytváříte dotaz s více operátory a místo užších výsledků získáváte širší výsledky. To platí zejména u možnosti Ne, kde výsledky zahrnují všechny dokumenty "neobsahující" konkrétní výraz.

Při použití výchozího searchMode (any) se vrátí 2800 dokumentů: těch, které obsahují "Požární oddělení", a navíc všechny dokumenty, které nemají výraz "MetroTech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="libovolný režim hledání" border="false":::

Změna searchMode k `all` vykonání kumulativního efektu pro kritéria a vrátí menší sadu výsledků dotazu-21 dokumentů, které obsahují dokumenty obsahující celou frázi "Požární oddělení", a tyto úlohy se na adrese MetroTech centra minus.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="vše v režimu hledání" border="false":::

## <a name="example-8-structuring-results"></a>Příklad 8: strukturování výsledků

Několik parametrů řídí, která pole jsou ve výsledcích hledání, počet dokumentů vrácených v každé dávce a pořadí řazení. Tento příklad překryje několik předchozích příkladů a omezí výsledky na konkrétní pole pomocí příkazu **$Select** a doslovného kritéria, vrátí 82 shod. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Připojeno k předchozímu příkladu, můžete řadit podle názvu. Toto řazení *funguje, protože civil_service_title v indexu* .

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Výsledky stránkování se implementují pomocí parametru **$Top** , v tomto případě vrátí prvních 5 dokumentů:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Pokud chcete získat další 5, přeskočte první dávku:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Další kroky
Zkuste zadat dotazy ve svém kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pro rozhraní .NET i REST API s použitím výchozí jednoduché syntaxe.

* [Dotazování indexu pomocí sady .NET SDK](./search-get-started-dotnet.md)
* [Dotazování indexu pomocí REST API](./search-get-started-powershell.md)

Další informace o syntaxi, architektuře dotazů a příkladech najdete na následujících odkazech:

+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplný dotaz Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Syntaxe Filter a OrderBy](/rest/api/searchservice/odata-expression-syntax-for-azure-search)