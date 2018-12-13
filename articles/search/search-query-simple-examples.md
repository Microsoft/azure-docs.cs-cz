---
title: Příklady jednoduchých dotazů – Azure Search
description: Příklady jednoduchých dotazů pro fulltextové vyhledávání, vyhledávání filtr, geografické vyhledávání, Fasetové vyhledávání a jiných řetězců dotazu používá k dotazování indexu Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9697b88e23fea0cb06ab0c4a6197b5255e7076bf
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316263"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Příklady jednoduchá syntaxe dotazů pro tvorbu dotazů ve službě Azure Search

[Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vyvolá výchozí analyzátor dotazů pro provádění fulltextové vyhledávací dotazy na index Azure Search. Analyzátor jednoduchého dotazu je rychlá a zpracovává běžné scénáře ve službě Azure Search, včetně fulltextové vyhledávání, vyhledání filtrované a fasetová a geografické vyhledávání. V tomto článku krokovat příklady demonstrující operace dotazu jsou dostupné při použití jednoduchého syntaxe.

Syntaxe alternativní dotazu je [úplné Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), podpora složitější struktury, dotazu, jako například přibližných shod a hledání pomocí zástupných znaků, které můžete provést další čas ke zpracování. Další informace a příklady demonstrující úplnou syntaxi najdete v tématu [Příklady dotazů syntaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulování požadavků v nástroji Postman

Následující příklady využívají vyhledávací index NYC Jobs skládající se z úlohy nejsou k dispozici na základě datové poskytované [města New York OpenData](https://nycopendata.socrata.com/) iniciativy. Tato data by neměly být zahrnuté v aktuální nebo dokončení. Index je v sandboxu služby od Microsoftu, což znamená, že potřebujete předplatnému Azure nebo Azure Search vyzkoušet tyto dotazy.

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
+ **`api-version=2017-11-11`** Nastaví verzi api-version, což je povinný parametr u každého požadavku.
+ **`search=*`** řetězec dotazu, který počátečního dotazu má hodnotu null, vrátí prvních 50 výsledky (ve výchozím nastavení).

## <a name="send-your-first-query"></a>Odeslat svůj první dotaz

Jako ověřovací krok, vložte následující požadavek do GET a klikněte na tlačítko **odeslat**. Výsledky jsou vráceny jako podrobné dokumenty JSON. Je možné kopírování a vkládání tuto adresu URL v prvním příkladu níže.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

Řetězec dotazu **`search=*`**, odpovídá neurčené hledání hodnotu null nebo prázdné vyhledávání. Není zvlášť užitečné, ale je nejjednodušší vyhledávání, které vám pomůžou.

Volitelně můžete přidat **`$count=true`** pro adresu URL pro vrátí počet dokumentů odpovídajících kritériím vyhledávání. V prázdné hledaný řetězec je to všechny dokumenty v indexu (přibližně 2800 v případě NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Vyvolání parsování jednoduchého dotazu

Pro interaktivní dotazy, není nutné zadávat nemusíte: jednoduché je výchozí nastavení. V kódu, pokud dříve vyvolat **queryType = full** celý dotaz v syntaxi může resetovat výchozí **queryType = jednoduché**.

## <a name="example-1-field-scoped-query"></a>Příklad 1: S rozsahem pole dotazu

Tento první příklad není specifické pro analyzátor, ale jsme s ním vést k zavedení prvního pojem základních dotazů: členství ve skupině. V tomto příkladu obory, provádění dotazu a odpovědi na pár konkrétních polí. Znalost, jak strukturovat, čitelné odpověď JSON je důležité, pokud je nástroj Postman nebo vyhledávání explorer. 

Pro zkrácení, zaměřuje dotazu pouze *business_title* pole a určuje pouze názvy business jsou vráceny. Syntaxe je **searchFields** omezit spuštění dotazu pouze na pole business_title a **vyberte** k určení, která pole jsou zahrnuty v odpovědi.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpověď pro tento dotaz by měl vypadat podobně jako na následujícím snímku obrazovky.

  ![Ukázková odpověď postman](media/search-query-lucene-examples/postman-sample-results.png)

Možná jste si všimli skóre vyhledávání v odpovědi. Jednotné skóre 1 dojít, pokud neexistuje žádné pořadí buď protože vyhledávání nebylo fulltextové vyhledávání, nebo protože byla použita žádná kritéria. Pro hodnotu null vyhledávání se žádná kritéria vraťte řádků v pořadí. Pokud zahrnete skutečné kritéria, zobrazí se hledání, které skóre, které se změní na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledávání podle ID

V tomto příkladu je o něco neobvyklé, ale při vyhodnocování chování vyhledávání, můžete chtít zkontrolovat celý obsah určitého dokumentu pochopit, proč byla zahrnuty nebo vyloučeny ze výsledky. Chcete-li vrátit jednoho dokumentu v celém rozsahu, použijte [operace vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) a zajistěte tak předání ID dokumentu.

Všechny dokumenty měly jedinečný identifikátor. Vyzkoušet syntaxe vyhledávacího dotazu, nejprve vrátíte seznam ID dokumentu, abyste našli, z nich se má použít. NYC Jobs identifikátory jsou uloženy v `id` pole.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

Následující příklad je vyhledávací dotaz vrací na základě určitého dokumentu `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", která se nacházela v předchozí odpovědi. Následující dotaz vrátí celý dokument, pouze vybraná pole. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-filter-queries"></a>Příklad 3: Filtrování dotazů

[Syntaxe filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) je výraz OData, který vám pomůže s **hledání** nebo samostatně. Filtr samostatné bez parametrů vyhledávání, je užitečná, pokud výraz filtru je možné k plnému určení dokumenty, které vás zajímají. Bez řetězce dotazu, neexistuje žádná lexikální nebo jazyková analýza bez výsledků (všechny hodnoty jsou 1) a žádné řazení. Všimněte si, že se že hledaný řetězec je prázdný.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Použijí společně, filtr je na celý index použije první, a pak provede vyhledávání na výsledcích filtru. Používání filtrů tak může být užitečné pro zlepšení výkonu dotazů zmenšením sady dokumentů, které musí dotaz vyhledávání zpracovat.

  ![Odpověď na dotaz filtru](media/search-query-simple-examples/filtered-query.png)

Pokud chcete v nástroji Postman, pomocí GET to vyzkoušet, můžete vložit do tento řetězec:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
 ```

Další efektivní způsob, jak kombinovat filtru a vyhledávání je prostřednictvím **`search.ismatch*()`** ve výrazu filtru, kde můžete použít vyhledávací dotaz v rámci filtru. Tento výraz filtr používá zástupný znak na *plán* vyberte business_title včetně plánu termín, planner, plánování a tak dále.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
 ```

Další informace o této funkci najdete v části [search.ismatch v "Příklady filtrů"](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Příklad 4: Filtry rozsahu

Rozsah filtrování je podporované prostřednictvím **`$filter`** výrazy pro libovolného datového typu. Následující příklady vyhledávat číselným a řetězcovým pole. 

Datové typy jsou důležité pro filtry rozsahu a fungují lépe, když je číselná data v číselná pole a řetězcová data v polích řetězce. Číselná data ve pole řetězce není vhodný pro rozsahy adres, protože nejsou porovnatelné ve službě Azure Search, číselných řetězců. 

Následující příklady jsou ve formátu POST pro lepší čitelnost (číselného rozsahu, za nímž následuje rozsah textu):

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtr rozsahu pro číselných rozsahů](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtr rozsahu pro oblasti textu](media/search-query-simple-examples/rangefiltertext.png)

Můžete také vyzkoušet tyto v nástroji Postman, pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
 ```

> [!NOTE]
> "Faceting" nad oblastí hodnot je běžné požadavky aplikace hledání. Další informace a příklady vytváření filtry omezující vlastnost navigace struktur, naleznete v tématu ["Filtru na základě rozsahu" v *jak implementovat fasetovou navigaci*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Příklad 5: Geografické vyhledávání

Ukázkového indexu zahrnuje pole geo_location pomocí zeměpisné šířky a délky. V tomto příkladu [geo.distance funkce](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) , která filtruje na dokumenty v rámci obvod výchozí bod odhlašování libovolné vzdálenosti (v kilometrech), který zadáte. Můžete upravit poslední hodnotu v dotazu (4), zmenšit nebo zvětšit plochu dotazu.

V následujícím příkladu je ve formátu POST pro lepší čitelnost:

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Výsledky hledání jsou výsledků dosáhnete lépe čitelný, oříznut id úlohy, pracovní pozice a pracovní umístění. Souřadnice počátečního byly získány z náhodných dokument v indexu (v takovém případě pro pracovní umístění na ostrově Staten.

Můžete také vyzkoušet to v nástroji Postman, pomocí GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Příklad 6: Hledání přesnosti

Termín dotazy jsou jedněch podmínkách, možná spousta z nich, které vyhodnocují nezávisle na sobě. Fráze dotazy jsou uzavřena v uvozovkách a vyhodnotí jako doslovný řetězec. Přesnost shody se řídí operátory a searchMode.

Příklad 1: **`&search=fire`** vrátí výsledky, 150, pokud obsahují všechny shody fire slovo někde v dokumentu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Příklad 2: **`&search=fire department`** vrátí 2002 výsledky. Shody jsou vráceny pro dokumenty, které obsahují fire nebo oddělení.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Příklad 3: **`&search="fire department"`** vrátí 82 výsledky. Vnější řetězec v uvozovkách je verbatim hledání na oba výrazy a shoda nenajde na tokenizovaná podmínky v indexu skládající se z kombinované podmínky. To vysvětluje, proč vyhledávání jako **`search=+fire +department`** není ekvivalentní. Oba výrazy jsou povinné, ale vyhledávají, nezávisle na sobě. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Příklad 7: Logických hodnot s searchMode

Jednoduchá syntaxe podporuje logické operátory ve formě znaků (`+, -, |`). Parametr searchMode informuje kompromisy mezi přesnosti a Vzpomínáte, s `searchMode=any` elegantní spojené s vracením (odpovídající se žádná kritéria kvalifikuje dokument pro sada výsledků), a `searchMode=all` elegantní přesnosti (všechna kritéria musí odpovídat). Výchozí hodnota je `searchMode=any`, což být matoucí, pokud jsou překrývání dotaz s více operátorů a získání širší místo zúžit výsledky. To platí zejména s NOT, pokud výsledky obsahují všechny dokumenty "neobsahující" konkrétní období.

Pomocí searchMode výchozí (všechny), se vrátí 2800 dokumenty: těch, které obsahují více částí termín "fire oddělení" a navíc všechny dokumenty, které nemají termín "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Hledat všechny režimu](media/search-query-simple-examples/searchmodeany.png)

Změna searchMode k `all` vynucuje kumulativní efekt na kritériích a vrátí menší sadu výsledků – 21 dokumentů - skládající se z dokumenty, které obsahují celou frázi "fire oddělení" minus tyto úlohy na adrese Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![režim hledání všech](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Příklad 8: Strukturování výsledky

Několik parametrů řídit, která pole jsou v hledání výsledků, počet dokumentů v každé dávky a pořadí řazení. V tomto příkladu resurfaces některé z předchozích příkladů omezení výsledků na konkrétní pole pomocí **$select** příkazu a kritéria hledání verbatim vrácení 82 shody 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Připojí na předchozí příklad, můžete seřadit podle názvu. Tento typ funguje, protože je civil_service_title *seřaditelné* v indexu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Výsledky stránkování je implementováno pomocí **$top** parametrů v tomto případě vrátí prvních 5 dokumenty:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Pokud chcete získat další 5, přeskočte první dávky:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Další postup
Zkuste zadat dotazy ve vašem kódu. Následující odkazy popisují, jak nastavit vyhledávacích dotazů pro .NET a rozhraní REST API pomocí jednoduché syntaxe výchozí.

* [Dotazování indexu Azure Search pomocí .NET SDK](search-query-dotnet.md)
* [Dotazování indexu Azure Search pomocí rozhraní REST API](search-query-rest-api.md)

Odkaz na další syntaxi dotazu architektury a příklady najdete v následujících odkazů:

+ [Příklady dotazů syntaxe Lucene pro vytváření upřesňující dotazy](search-query-lucene-examples.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md)
+ [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Úplné dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filtrovat a řadit podle syntaxe](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
