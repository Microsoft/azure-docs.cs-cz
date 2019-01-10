---
title: Průzkumník služby Search na webu Azure portal k dotazování indexů – Azure Search
description: Použijte Azure portal nástrojů, jako je Průzkumník služby Search na dotaz indexy ve službě Azure Search. Zadejte hledané termíny nebo plně kvalifikovaný vyhledávacích řetězců s rozšířené syntaxe.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2aa372d1f917608de753007cc75ab0d608cafbba
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188721"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Použití Průzkumníka služby Search na dotaz indexy ve službě Azure Search 

V tomto článku se dozvíte, jak zadávat dotazy na existující index Azure Search pomocí **Průzkumníka služby Search** na webu Azure Portal. Průzkumník služby Search můžete odesílat jednoduché nebo úplné řetězce dotazů Lucene do jakéhokoli existujícího indexu ve službě.

## <a name="start-search-explorer"></a>Spusťte Průzkumníka služby Search

1. V [webu Azure portal](https://portal.azure.com), otevřete stránku vyhledávací služby z řídicího panelu nebo [svou službu vyhledejte](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služeb.

2. Na stránce Přehled služby, klikněte na tlačítko **Průzkumníka služby Search**.

   ![Příkaz Průzkumníka služby hledání portálu](./media/search-explorer/search-explorer-cmd2.png "příkaz Průzkumníka služby hledání na portálu")

3. Vyberte index dotazu.

   ![Vyberte index dotazu](./media/search-explorer/search-explorer-changeindex-se2.png "vyberte index")

4. Volitelně můžete nastavte verzi rozhraní API. Ve výchozím nastavení je vybrána aktuální obecně dostupná verze rozhraní API, ale můžete použít ve verzi preview nebo starší rozhraní API, pokud syntaxi, kterou chcete použít, je specifické pro verzi.

5. Jednou index a vybrané verze rozhraní API, zadejte hledané výrazy nebo výrazy plně kvalifikovaný dotazů na panelu hledání a klikněte na tlačítko **hledání** ke spuštění.

   ![Zadejte hledaný text a klikněte na tlačítko Hledat](./media/search-explorer/search-explorer-query-string-example.png "vyhledávání zadejte podmínky a klikněte na tlačítko Hledat")

Tipy pro hledání v **Průzkumníka služby Search**:

+ Výsledky jsou vráceny jako podrobné dokumenty JSON tak, aby dokument konstrukce a obsah, můžete zobrazit v celém rozsahu. Můžete použít výrazy dotazu, je znázorněno v příkladu, k omezení, která pole jsou vráceny.

+ Dokumenty se skládají ze všech polí označených jako **Retrievable** v indexu. Chcete-li zobrazit atributy indexu na portálu, klikněte na tlačítko *realestate-us-sample* v **indexy** seznamu na stránce Přehled hledání.

+ Dotazy volného tvaru, podobně jako například zadat v komerční webový prohlížeč, jsou vhodné pro testovací prostředí koncového uživatele. Například za předpokladu, že integrované realestate ukázkového indexu, můžete například zadat "Seattle objekty apartment lake washington", a pak vám pomůže Ctrl + F lze najít termíny ve výsledcích hledání. 

+ Výrazy dotazu a filtrovat musí kloubové v syntaxi podporovány službou Azure Search. Výchozí hodnota je [jednoduchá syntaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale můžete volitelně použít [úplné Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) pro výkonnějších dotazů. [Filtr výrazů](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) jsou syntaxe OData.

## <a name="basic-search-strings"></a>Základní hledání řetězce

Následující příklady předpokládají integrované realestate ukázkového indexu. Další informace o vytvoření tohoto indexu najdete v tématu [rychlý start: Import, index a dotaz na webu Azure portal](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Příklad 1 - prázdné vyhledávání

Pro první pohled na váš obsah, spusťte kliknutím na prázdné vyhledávání **hledání** s žádné podmínky k dispozici. Prázdné vyhledávání je užitečné jako první dotaz, protože tak, aby najdete v dokumentu složení vrátí všechny dokumenty. V prázdné vyhledávání, neexistuje žádné pořadí hledání a dokumenty jsou vráceny v libovolné pořadí (`"@search.score": 1` pro všechny dokumenty). Ve výchozím nastavení se vrátí 50 dokumenty v požadavku hledání.

Je ekvivalentní syntaxi prázdné vyhledávání `*` nebo `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Příklad dotazu prázdný](./media/search-explorer/search-explorer-example-empty.png "Unqualified nebo příkladu prázdný dotaz")

### <a name="example-2---free-text-search"></a>Příklad 2 – textové vyhledávání

Dotazy volného tvaru, s nebo bez něj operátory, jsou užitečné pro simulaci uživatelské dotazy odeslané z vlastních aplikací do služby Azure Search. Všimněte si, že když zadáte výrazy nebo výrazy, vyhledávání pořadí vstupu do play. Následující příklad ukazuje textové vyhledávání.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Ctrl + F můžete v rámci výsledků hledejte konkrétní termíny, které vás zajímají.

   ![Příklad dotazu volné](./media/search-explorer/search-explorer-example-freetext.png "příklad volný text dotazu")

### <a name="example-3---count-of-matching-documents"></a>Příklad 3 - počtu odpovídajících dokumentů 

Přidat **$count** zobrazíte počet shod v indexu. V prázdné vyhledávání počet je celkový počet dokumentů v indexu. Na kvalifikovaný vyhledávání je to počet dokumentů, které vyhovují dotazu vstup.

   ```Input1
   $count=true
   ```
   **Results**

   ![Počet dokumentů příklad](./media/search-explorer/search-explorer-example-count.png "počtu odpovídajících dokumentů do indexu")

### <a name="example-4---restrict-fields-in-search-results"></a>Příklad 4 - omezit pole ve výsledcích hledání

Přidat **$select** omezit rozsah výsledků na explicitně pojmenované pole pro lépe čitelný výstup v **Průzkumníka služby Search**. Aby se hledaný řetězec a **$count = true**, předpona argumenty s **&**. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Limit pole příklad](./media/search-explorer/search-explorer-example-selectfield.png "omezit pole ve výsledcích hledání")

### <a name="example-5---return-next-batch-of-results"></a>Příklad 5 - návratový další dávku výsledků

Azure Search vrací začátek 50 shodnými podle pořadí hledání. Chcete-li získat další sadu odpovídajících dokumentů, přidejte **$top = 100 & $skip = 50** zvýšit sadu výsledků do 100 dokumentů (výchozí hodnota je 50, maximální hodnota je 1000), přeskočí prvních 50 dokumentů. Připomínáme, že je potřeba zadat kritéria hledání, jako je například termín dotazu nebo výraz, chcete-li získat seřazené výsledky. Všimněte si, že skóre vyhledávání snížit hlubší nedostanete do výsledků hledání.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Results**

   ![Dávkovat výsledky hledání](./media/search-explorer/search-explorer-example-topskip.png "návratový další dávku výsledků hledání")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtr výrazů (větší než, menší než, rovna)

Použití **$filter** parametr, pokud chcete určit přesný kritéria spíše než textové vyhledávání. Tento příklad vyhledá víc než 3 ložnice: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Výraz filtru](./media/search-explorer/search-explorer-example-filter.png "filtrovat podle kritérií")

## <a name="order-by-expressions"></a>Výrazy Order by

Přidat **$orderby** seřadit výsledky podle jiného pole kromě skóre vyhledávání. Příklad výrazu, který můžete použít k testování to všechno je `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Výraz OrderBy](./media/search-explorer/search-explorer-example-ordery.png "změňte pořadí řazení")

Obě **$filter** a **$orderby** jsou výrazy konstrukcí OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

## <a name="next-steps"></a>Další postup

V následujících zdrojích najdete další informace o syntaxi dotazů a příklady.

 + [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Příklady dotazů Lucene](search-query-lucene-examples.md) 
 + [Syntaxe výrazů filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 