---
title: Vytvoření jednoduchého dotazu
titleSuffix: Azure Cognitive Search
description: Naučte se podle příkladu spuštěním dotazů založených na jednoduché syntaxi pro fulltextové vyhledávání, vyhledávání filtrů, geografické vyhledávání, famitové vyhledávání podle indexu Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a801af7b97954510139a009a6d1344b281cf056
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261801"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Vytvoření jednoduchého dotazu v Azure Cognitive Search

V Azure Cognitive Search [syntaxe jednoduchého dotazu](query-simple-syntax.md) vyvolá výchozí analyzátor dotazů pro spuštění fulltextové vyhledávací dotazy proti indexu. Tento analyzátor je rychlý a zpracovává běžné scénáře, včetně fulltextového vyhledávání, filtrovaného a fazetovaného vyhledávání a geografického vyhledávání. 

V tomto článku používáme příklady pro ilustraci `search=` jednoduché syntaxe, vyplnění parametr u operace [hledání dokumentů.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Alternativní syntaxe dotazu je [Úplná Lucene](query-lucene-syntax.md), podporující složitější struktury dotazů, jako je například vyhledávání přibližných a zástupných symbolů, což může trvat déle. Další informace a příklady demonstrující úplnou syntaxi naleznete [v tématu Použití úplné syntaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulovat požadavky v Pošťákovi

Následující příklady využívají index hledání pracovních míst nyc skládající se z úloh, které jsou k dispozici na základě datové sady poskytované iniciativou [City of New York OpenData.](https://nycopendata.socrata.com/) Tyto údaje by neměly být považovány za aktuální nebo úplné. Index se nachází na izolovaném prostoru služby poskytované společností Microsoft, což znamená, že nepotřebujete předplatné Azure nebo Azure Cognitive Search vyzkoušet tyto dotazy.

Co potřebujete, je Postman nebo ekvivalentní nástroj pro vydávání požadavku HTTP na GET. Další informace najdete [v tématu Úvodní příručka: Prozkoumejte rozhraní REST API Azure cognitive search pomocí postman](search-get-started-postman.md).

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

Řetězec dotazu **`search=*`**, je nespecifikované hledání ekvivalentní hledání null nebo prázdné. Není to zvlášť užitečné, ale je to nejjednodušší vyhledávání, které můžete udělat.

Volitelně můžete přidat **`$count=true`** do adresy URL a vrátit tak počet dokumentů odpovídajících kritériím vyhledávání. Na prázdný vyhledávací řetězec, toto je všechny dokumenty v indexu (asi 2800 v případě NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Jak vyvolat jednoduchou analýzu dotazu

U interaktivních dotazů není nutné nic zadávat: výchozí je jednoduché. V kódu, pokud jste dříve vyvolali **queryType=full** pro úplnou syntaxi dotazu, můžete obnovit výchozí hodnotu **pomocí queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Příklad 1: Dotaz s rozsahem pole

Tento první příklad není specifický pro analyzátor, ale vedeme s ním k zavedení prvnízákladní koncept dotazu: uzavření. Tento příklad obory provádění dotazu a odpověď pouze na několik konkrétních polí. Vědět, jak strukturovat čitelnou odpověď JSON, je důležité, když je váš nástroj Pošťák nebo Průzkumník vyhledávání. 

Pro stručnost dotaz cíle pouze *business_title* pole a určuje pouze obchodní tituly jsou vráceny. Syntaxe je **searchFields** omezit spuštění dotazu pouze business_title pole a **vyberte** určit, která pole jsou zahrnuty v odpovědi.

### <a name="partial-query-string"></a>Řetězec částečného dotazu

```http
searchFields=business_title&$select=business_title&search=*
```

Zde je stejný dotaz s více poli v seznamu odděleném čárkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Úplná adresa URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpověď pro tento dotaz by měla vypadat podobně jako na následujícím snímku obrazovky.

  ![Pošťák vzorodpověď](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre vyhledávání v odpovědi. K jednotnému skóre 1 dochází, pokud neexistuje žádná hodnost, protože hledání nebylo fulltextové vyhledávání, nebo proto, že nebyla použita žádná kritéria. Pro hledání null bez kritérií se řádky vrátí v libovolném pořadí. Když zahrnete skutečná kritéria, zobrazí se skóre vyhledávání, které se změní na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledávání podle ID

Tento příklad je trochu atypický, ale při vyhodnocování chování hledání můžete chtít zkontrolovat celý obsah konkrétního dokumentu, abyste pochopili, proč byl zahrnut nebo vyloučen z výsledků. Chcete-li vrátit celý dokument, předajte ID dokumentu pomocí [operace vyhledávání.](https://docs.microsoft.com/rest/api/searchservice/lookup-document)

Všechny dokumenty mají jedinečný identifikátor. Chcete-li vyzkoušet syntaxi vyhledávacího dotazu, nejprve vraťte seznam ID dokumentů, abyste mohli najít jeden, který chcete použít. Pro nyc úlohy jsou identifikátory `id` uloženy v poli.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Dalším příkladem je vyhledávací dotaz vracející určitý dokument `id` založený na "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", který se objevil jako první v předchozí odpovědi. Následující dotaz vrátí celý dokument, nikoli pouze vybraná pole. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Příklad 3: Filtrování dotazů

[Syntaxe filtru](https://docs.microsoft.com/azure/search/search-query-odata-filter) je výraz OData, který můžete použít při **hledání** nebo samostatně. Samostatný filtr bez parametru vyhledávání je užitečný, pokud je výraz filtru schopen plně kvalifikovat dokumenty, které vás zajímají. Bez řetězce dotazu neexistuje žádná lexikální nebo jazyková analýza, žádné bodování (všechny skóre jsou 1) a žádné pořadí. Všimněte si, že vyhledávací řetězec je prázdný.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Při použití společně se filtr použije nejprve na celý index a poté se provede hledání výsledků filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

  ![Filtrovat odpověď na dotaz](media/search-query-simple-examples/filtered-query.png)

Pokud chcete vyzkoušet v Postman pomocí GET, můžete vložit do tohoto řetězce:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Dalším účinným způsobem kombinování **`search.ismatch*()`** filtru a vyhledávání je ve výrazu filtru, kde můžete použít vyhledávací dotaz v rámci filtru. Tento výraz filtru používá zástupný znak v *plánu* k výběru business_title včetně plánu termínu, plánovače, plánování atd.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Další informace o funkci naleznete v tématu [search.ismatch v "Filter examples"](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Příklad 4: Filtry rozsahu

Filtrování rozsahu je **`$filter`** podporováno prostřednictvím výrazů pro libovolný datový typ. Následující příklady prohledávají číselná a řetězcová pole. 

Datové typy jsou důležité ve filtrech rozsahu a fungují nejlépe, když jsou číselná data v číselných polích a řetězcová data v řetězcových polích. Numerická data v řetězcových polích není vhodná pro oblasti, protože číselné řetězce nejsou srovnatelné v Azure Cognitive Search. 

Následující příklady jsou ve formátu POST pro čitelnost (číselný rozsah, následovaný rozsahem textu):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtr rozsahu pro číselné oblasti](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtr rozsahu pro oblasti textu](media/search-query-simple-examples/rangefiltertext.png)

Můžete také vyzkoušet tyto v Postman pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Fazetování přes rozsahy hodnot je běžný požadavek na vyhledávací aplikace. Další informace a příklady týkající se vytváření filtrů pro fazetové navigační struktury naleznete [v tématu "Filtr na základě rozsahu" v *tématu Jak implementovat fazetovou navigaci*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Příklad 5: Geografické vyhledávání

Index vzorku obsahuje geo_location pole s souřadnicemi zeměpisné šířky a délky. Tento příklad používá [funkci geo.distance,](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) která filtruje dokumenty v obvodu počátečního bodu, na libovolnou vzdálenost (v kilometrech), kterou zadáte. Můžete upravit poslední hodnotu v dotazu (4) pro zmenšení nebo zvětšení plochy dotazu.

Následující příklad je ve formátu POST pro čitelnost:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Chcete-li získat čitelnější výsledky, výsledky hledání jsou oříznuty tak, aby zahrnovaly ID úlohy, název pracovní pozice a pracovní místo. Počáteční souřadnice byly získány z náhodného dokumentu v indexu (v tomto případě pro pracovní místo na ostrově Staten.

Můžete také vyzkoušet v Postman pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Příklad 6: Přesnost vyhledávání

Termín dotazy jsou jednotlivé termíny, možná mnoho z nich, které jsou vyhodnocovány nezávisle. Frázové dotazy jsou uzavřeny v uvozovkách a vyhodnoceny jako doslovný řetězec. Přesnost shody je řízena operátory a searchMode.

Příklad 1: **`&search=fire`** vrátí 150 výsledků, kde všechny shody obsahují slovo oheň někde v dokumentu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Příklad 2: **`&search=fire department`** Vrátí výsledky za rok 2002. Shody jsou vráceny pro dokumenty obsahující buď požární nebo oddělení.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Příklad 3: **`&search="fire department"`** vrátí 82 výsledků. Uzavření řetězce v uvozovkách je doslovné hledání na obou termínech a shody se nacházejí na tokenizovaných termínech v indexu skládajícím se z kombinovaných termínů. To vysvětluje, proč **`search=+fire +department`** hledání jako není ekvivalentní. Oba podmínky jsou povinné, ale jsou kontrolovány nezávisle. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Příklad 7: Logické hodnoty s režimem vyhledávání

Jednoduchá syntaxe podporuje logické operátory`+, -, |`ve formě znaků ( ). Parametr searchMode informuje kompromisy mezi přesností `searchMode=any` a odvolání, s upřednostnění odvolání (odpovídající na všechna `searchMode=all` kritéria kvalifikuje dokument pro sadu výsledků) a upřednostňuje přesnost (všechna kritéria musí být spárována). Výchozí hodnota `searchMode=any`je , což může být matoucí, pokud sčítáte dotaz s více operátory a získáváte širší místo užších výsledků. To platí zejména s NOT, kde výsledky zahrnují všechny dokumenty "neobsahující" konkrétní termín.

Pomocí výchozísearchMode (any), 2800 dokumenty jsou vráceny: ty, které obsahují vícedílný termín "hasiči", plus všechny dokumenty, které nemají termín "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![režimu vyhledávání](media/search-query-simple-examples/searchmodeany.png)

Změna searchMode `all` vynucuje kumulativní účinek na kritéria a vrátí menší sadu výsledků - 21 dokumentů - skládající se z dokumentů obsahujících celou frázi "hasiči", minus tyto úlohy na adrese Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![režim vyhledávání vše](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Příklad 8: Strukturování výsledků

Několik parametrů řídí, která pole jsou ve výsledcích hledání, počet dokumentů vrácených v každé dávce a pořadí řazení. Tento příklad znovu vynoří několik předchozích příkladů, omezení výsledků na konkrétní pole pomocí **$select** příkaz a doslovné kritéria vyhledávání, vrácení 82 shod 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Připojeno k předchozímu příkladu, můžete řadit podle názvu. Toto řazení funguje, protože civil_service_title je v indexu *seřaditelné.*

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Stránkování výsledky je implementována pomocí **$top** parametr, v tomto případě vrací top 5 dokumentů:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Chcete-li získat další 5, přeskočte první dávku:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Další kroky
Zkuste zadat dotazy v kódu. Následující odkazy vysvětlují, jak nastavit vyhledávací dotazy pro rozhraní .NET a rozhraní REST API pomocí výchozí jednoduché syntaxe.

* [Dotaz na index pomocí sady .NET SDK](search-query-dotnet.md)
* [Dotaz na index pomocí rozhraní REST API](search-create-index-rest-api.md)

Další odkaz na syntaxi, architekturu dotazu a příklady naleznete v následujících odkazech:

+ [Příklady dotazů syntaxe Lucene pro vytváření pokročilých dotazů](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplný dotaz Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Syntaxe filtru a pořadí](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
