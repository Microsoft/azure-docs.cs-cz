---
title: Použití nástroje Průzkumník služby Search pro dotazování na data v Azure Portal
titleSuffix: Azure Cognitive Search
description: Průzkumník služby Search je integrovaný do Azure Portal, který je užitečný pro zkoumání obsahu a ověřování dotazů v Azure Kognitivní hledání. Zadejte řetězce pro hledání termínu nebo fráze nebo plně kvalifikované výrazy hledání s pokročilou syntaxí.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0bb50bd50c58b85f1d5de100c34d00d114a9ed77
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792964"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Použití Průzkumníka služby Search v Azure Portal pro dotazování dokumentů v Azure Kognitivní hledání 

V tomto článku se dozvíte, jak pomocí **Průzkumníka služby Search** v Azure Portal dotazovat existující index služby Azure kognitivní hledání. Můžete spustit Průzkumníka služby Search na panelu příkazů a odeslat jednoduché nebo úplné výrazy dotazů Lucene do libovolného existujícího indexu ve vaší službě. 

   ![Příkaz Průzkumníka vyhledávání na portálu](./media/search-explorer/search-explorer-cmd2.png "Příkaz Průzkumníka vyhledávání na portálu")

## <a name="basic-search-strings"></a>Základní vyhledávací řetězce

V následujících příkladech se předpokládá, že je předdefinovaný ukázkový index z reálného majetku. Tento index můžete vytvořit pomocí Průvodce importem dat na portálu, přičemž jako zdroj dat vyberte **ukázky** .

### <a name="example-1---empty-search"></a>Příklad 1 – prázdné hledání

Pokud se chcete podívat na obsah, spusťte prázdné vyhledávání kliknutím na **Hledat** bez zadání podmínek. Prázdné hledání je užitečné jako první dotaz, protože vrací celé dokumenty, abyste mohli zkontrolovat složení dokumentu. Při prázdném hledání není k dispozici žádné pořadí hledání a dokumenty se vrátí v libovolném pořadí (`"@search.score": 1` pro všechny dokumenty). Ve výchozím nastavení jsou v žádosti o vyhledávání vráceny dokumenty 50.

Ekvivalentní syntaxe pro prázdné hledání je `*` nebo `search=*`.

   ```Input
   search=*
   ```

   **Results**
   
   ![Příklad prázdného dotazu](./media/search-explorer/search-explorer-example-empty.png "Příklad nekvalifikovaného nebo prázdného dotazu")

### <a name="example-2---free-text-search"></a>Příklad 2 – bezplatné hledání textu

Dotazy na bezplatné formuláře s operátory nebo bez nich jsou užitečné pro simulaci uživatelsky definovaných dotazů odeslaných z vlastní aplikace do Azure Kognitivní hledání. Všimněte si, že když zadáte podmínky dotazu nebo výrazy, bude se hledat v pořadí hledání. Následující příklad znázorňuje volné hledání textu.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Pomocí kombinace kláves CTRL-F můžete vyhledat konkrétní důležité výrazy v rámci výsledků.

   ![Příklad dotazů na volný text](./media/search-explorer/search-explorer-example-freetext.png "Příklad dotazů na volný text")

### <a name="example-3---count-of-matching-documents"></a>Příklad 3 – Počet vyhovujících dokumentů 

Přidejte **$Count** pro získání počtu shod nalezených v indexu. Při prázdném hledání je počet celkový počet dokumentů v indexu. U kvalifikovaného hledání se jedná o počet dokumentů, které odpovídají vstupu dotazu.

   ```Input1
   $count=true
   ```
   **Results**

   ![Počet dokumentů – příklad](./media/search-explorer/search-explorer-example-count.png "Počet vyhovujících dokumentů v indexu")

### <a name="example-4---restrict-fields-in-search-results"></a>Příklad 4 – omezení polí ve výsledcích hledání

Přidejte **$Select** pro omezení výsledků do výslovně pojmenovaných polí pro čitelnější výstup v **Průzkumníku služby Search**. Chcete-li zachovat hledaný řetězec a **$Count = true**, argumenty předpony s **&** . 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Příklad omezení polí](./media/search-explorer/search-explorer-example-selectfield.png "Omezit pole ve výsledcích hledání")

### <a name="example-5---return-next-batch-of-results"></a>Příklad 5 – vrácení další dávky výsledků

Azure Kognitivní hledání vrátí nejvyšší 50 shody na základě pořadí hledání. Chcete-li získat další sadu vyhovujících dokumentů, přidejte **$Top = 100, & $Skip = 50** pro zvýšení sady výsledků na 100 dokumenty (výchozí hodnota je 50, maximum je 1000), přeskočí první 50 dokumentů. Abyste získali seřazené výsledky, je nutné, abyste zadali kritéria hledání, jako je například termín dotazu nebo výraz. Všimněte si, že výsledky hledání snižují hlubší přístup k výsledkům vyhledávání.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Výsledky dávkového vyhledávání](./media/search-explorer/search-explorer-example-topskip.png "Vrátit další dávku výsledků hledání")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Výrazy filtru (větší než, menší než, rovno)

Použijte parametr **$Filter** , pokud chcete zadat přesnější kritéria místo hledání volného textu. Tento příklad vyhledá ložnicemi větší než 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Výraz filtru](./media/search-explorer/search-explorer-example-filter.png "Filtrovat podle kritérií")

## <a name="order-by-expressions"></a>Výrazy ORDER by

Přidejte **$OrderBy** pro řazení výsledků podle jiného pole než skóre vyhledávání. Příklad výrazu, který můžete použít k otestování tohoto:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![Výraz OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Změna pořadí řazení")

**$Filter** i **$OrderBy** výrazy jsou konstrukce OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Spuštění Průzkumníka vyhledávání

1. V [Azure Portal](https://portal.azure.com)otevřete stránku vyhledávací služby z řídicího panelu nebo [vyhledejte službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v seznamu služby.

2. Na stránce Přehled služby klikněte na **Průzkumník služby Search**.

   ![Příkaz Průzkumníka vyhledávání na portálu](./media/search-explorer/search-explorer-cmd2.png "Příkaz Průzkumníka vyhledávání na portálu")

3. Vyberte index, který se má dotazovat.

   ![Vyberte index, který se má dotazovat.](./media/search-explorer/search-explorer-changeindex-se2.png "Vyberte index.")

4. Volitelně můžete nastavit verzi rozhraní API. Ve výchozím nastavení je vybrána aktuální všeobecně dostupná verze rozhraní API, ale můžete zvolit verzi Preview nebo starší rozhraní API, pokud je syntaxe, kterou chcete použít, specifická pro danou verzi.

5. Po výběru index a verze rozhraní API zadejte na panelu hledání hledané výrazy nebo plně kvalifikované výrazy dotazu a klikněte na **Hledat** , které se spustí.

   ![Zadejte hledané výrazy a klikněte na Hledat.](./media/search-explorer/search-explorer-query-string-example.png "Zadejte hledané výrazy a klikněte na Hledat.")

Tipy pro hledání v **Průzkumníkovi služby Search**:

+ Výsledky jsou vráceny jako podrobné dokumenty JSON, takže můžete v celém rozsahu zobrazit konstrukci a obsah dokumentu. Pomocí výrazů dotazů zobrazených v příkladech můžete omezit, která pole jsou vrácena.

+ Dokumenty se skládají ze všech polí označených jako načístelné **v indexu** . Chcete-li zobrazit atributy indexu na portálu, klikněte na *realestate-US-Sample* v seznamu **indexy** na stránce s přehledem hledání.

+ Dotazy na volném formuláři, podobně jako to, co byste mohli zadat do komerčního webového prohlížeče, jsou užitečné při testování činnosti koncového uživatele. Předpokládejme například, že jste zavedli integrovaný vzorový index realestate, mohli byste zadat "Seattle Apartments Lake Washington" a potom můžete pomocí kombinace kláves CTRL-F vyhledat výrazy ve výsledcích hledání. 

+ Výrazy dotazu a filtru musí být nakloubované v syntaxi podporované službou Azure Kognitivní hledání. Výchozí hodnota je [Jednoduchá syntaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale pro výkonnější dotazy můžete použít také [úplné Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) . [Výrazy filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) jsou syntaxí OData.


## <a name="next-steps"></a>Další kroky

V následujících zdrojích najdete další informace o syntaxi dotazů a příklady.

 + [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Příklady dotazů Lucene](search-query-lucene-examples.md) 
 + [Syntaxe výrazů filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
