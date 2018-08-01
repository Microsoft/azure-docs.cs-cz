---
title: Příklady jednoduchých dotazů pro službu Azure Search | Dokumentace Microsoftu
description: Příklady jednoduchých dotazů pro fulltextové vyhledávání, vyhledávání filtr, geografické vyhledávání, Fasetové vyhledávání a jiných řetězců dotazu používá k dotazování indexu Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369118"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Příklady jednoduchá syntaxe dotazů pro tvorbu dotazů ve službě Azure Search

[Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vyvolá výchozí analyzátor dotazů pro provádění fulltextové vyhledávací dotazy na index Azure Search. Analyzátor jednoduchého dotazu je rychlá a zpracovává běžné scénáře ve službě Azure Search, včetně fulltextové vyhledávání, vyhledání filtrované a fasetová a geografické vyhledávání. V tomto článku krokovat příklady demonstrující operace dotazu jsou dostupné při použití jednoduchého syntaxe.

Syntaxe alternativní dotazu je [úplné Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), podpora složitější struktury, dotazu, jako například přibližných shod a hledání pomocí zástupných znaků, které můžete provést další čas ke zpracování. Další informace a příklady demonstrující úplnou syntaxi najdete v tématu [Příklady dotazů syntaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formulování požadavků v nástroji Postman

Následující příklady využívají vyhledávací index NYC Jobs skládající se z úlohy nejsou k dispozici na základě datové poskytované [města New York OpenData](https://nycopendata.socrata.com/) iniciativy. Tato data by neměly být zahrnuté v aktuální nebo dokončení. Index je v sandboxu služby od Microsoftu, což znamená, že potřebujete předplatnému Azure nebo Azure Search vyzkoušet tyto dotazy.

Co je třeba je Postman nebo ekvivalentní nástroje pro vydání požadavku HTTP na GET. Další informace najdete v tématu [Test s využitím klientů REST](search-fiddler.md).

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
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

Řetězec dotazu **`search=*`**, odpovídá neurčené hledání hodnotu null nebo prázdné vyhledávání. Není zvlášť užitečné, ale je nejjednodušší vyhledávání, které vám pomůžou.

Volitelně můžete přidat **`$count=true`** pro adresu URL pro vrátí počet dokumentů odpovídajících kritériím vyhledávání. V prázdné hledaný řetězec je to všechny dokumenty v indexu (v případě NYC Jobs. 2802).

## <a name="how-to-invoke-simple-query-parsing"></a>Vyvolání parsování jednoduchého dotazu

Pro interaktivní dotazy, není nutné zadávat nemusíte: jednoduché je výchozí nastavení. V kódu, pokud dříve vyvolat **queryType = full** pro celý dotaz v syntaxi, může resetovat zpátky na výchozí s **queryType = jednoduché**.

## <a name="example-1-field-scoped-query"></a>Příklad 1: S rozsahem pole dotazu

První dotaz není syntaxe specifické (dotaz funguje pro jednoduché a úplnou syntaxi) ale vést v tomto příkladu zavedení konceptu směrný plán dotazu, který vytváří rozumně čitelné odpověď ve formátu JSON. Pro zkrácení, zaměřuje dotazu pouze *business_title* pole a určuje pouze názvy business jsou vráceny. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

**SearchFields** parametr omezuje vyhledávání na jenom pole název firmy. **Vyberte** parametr určuje, která pole jsou zahrnuty v sadě výsledků.

Odpověď pro tento dotaz by měl vypadat podobně jako na následujícím snímku obrazovky.

  ![Ukázková odpověď postman](media/search-query-lucene-examples/postman-sample-results.png)

Jste si možná všimli, že skóre vyhledávání je také vrácen pro každý dokument i v případě, že není zadán skóre vyhledávání. Je to proto, že skóre vyhledávání jsou metadata s hodnotou označující pořadí řazení výsledků. Jednotné skóre 1 dojít, pokud neexistuje žádné pořadí buď protože vyhledávání nebylo fulltextové vyhledávání, nebo protože neexistuje žádná kritéria a nastavte. Vyhledat hodnotu null není žádná kritéria a vrátit se zpátky řádky jsou v pořadí. Jako kritéria hledání, bere na další definice, zobrazí se hledání, které skóre, které se změní na smysluplné hodnoty.

## <a name="example-2-look-up-by-id"></a>Příklad 2: Vyhledávání podle ID

V tomto příkladu je o něco neobvyklé, ale při vyhodnocování chování vyhledávání, můžete chtít zkontrolovat celý obsah dokumentu pochopit, proč byla zahrnuty nebo vyloučeny ze výsledky. Chcete-li vrátit celý dokument, použijte [operace vyhledávání](https://docs.microsoft.com/rest/api/searchservice/lookup-document) a zajistěte tak předání ID dokumentu.

Všechny dokumenty měly jedinečný identifikátor. Vyzkoušet syntaxe vyhledávacího dotazu, nejprve vrátíte seznam ID dokumentu, abyste našli, z nich se má použít. NYC Jobs identifikátory jsou uloženy v `id` pole.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

Následující příklad je vyhledávací dotaz vrací na základě určitého dokumentu `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", která se nacházela v předchozí odpovědi. Následující dotaz vrátí celý dokument, pouze vybraná pole. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>Příklad 3: Vyhledávání přesnosti

Termín dotazy jsou jedněch podmínkách, možná spousta z nich, které vyhodnocují nezávisle na sobě. Fráze dotazy jsou uzavřena v uvozovkách a vyhodnotí jako doslovný řetězec. Přesnost shody se řídí operátory a searchMode.

Příklad 1: **`&search=fire`** vrátí výsledky, 150, pokud obsahují všechny shody fire slovo někde v dokumentu.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Příklad 2: **`&search=fire department`** vrátí 2002 výsledky. Shody jsou vráceny pro dokumenty, které obsahují fire nebo oddělení.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Příklad 3: **`&search="fire department"`** vrátí 82 výsledky. Vnější řetězec v uvozovkách je verbatim hledání na oba výrazy a shoda nenajde na tokenizovaná podmínky v indexu skládající se z kombinované podmínky. To vysvětluje, proč vyhledávání jako **`search=+fire +department`** není ekvivalentní. Oba výrazy jsou povinné, ale vyhledávají, nezávisle na sobě. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>Příklad 4: Logických hodnot s searchMode

Jednoduchá syntaxe podporuje logické operátory ve formě znaků (`+, -, |`). Parametr searchMode informuje kompromisy mezi přesnosti a Vzpomínáte, s `searchMode=any` elegantní spojené s vracením (odpovídající se žádná kritéria kvalifikuje dokument pro sada výsledků), a `searchMode=all` elegantní přesnosti (všechna kritéria musí odpovídat). Výchozí hodnota je `searchMode=any`, což být matoucí, pokud jsou překrývání dotaz s více operátorů a získání širší místo zúžit výsledky. To platí zejména s NOT, pokud výsledky obsahují všechny dokumenty "neobsahující" konkrétní období.

Pomocí searchMode výchozí (všechny), se vrátí 2800 dokumenty: těch, které obsahují více částí termín "fire oddělení" a navíc všechny dokumenty, které nemají termín "Metrotech Center".

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
Změna searchMode k `all` vynucuje kumulativní efekt na kritériích a vrátí menší sadu výsledků – 21 dokumentů - skládající se z dokumenty, které obsahují celou frázi "fire oddělení" minus tyto úlohy na adrese Metrotech Center.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>Příklad 5: Strukturování výsledky

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
