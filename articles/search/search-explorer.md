---
title: Dotazovací nástroj Průzkumníka hledání v Azure Portal
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu Azure Portal použijte Průzkumníka služby Search k získání syntaxe dotazů, testování výrazů dotazů nebo kontrole dokumentu hledání. V Průzkumníku služby Search se dotazují indexy v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/07/2020
ms.openlocfilehash: 19d46c034d56c1c54f8a00f08a7e3e72e758984f
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488201"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Rychlý Start: použití Průzkumníka pro hledání ke spouštění dotazů na portálu

**Průzkumník služby Search** je vestavěný dotazovací nástroj, který se používá ke spouštění dotazů na vyhledávacím indexu v Azure kognitivní hledání. Tento nástroj usnadňuje seznámení s syntaxí dotazů, testování dotazu nebo výrazu filtru nebo potvrzení aktualizace dat tím, že zkontroluje, jestli v indexu existuje nový obsah.

V tomto rychlém startu se k předvedení Průzkumníka služby Search použije existující index. Žádosti se formulují pomocí [REST API vyhledávání](https://docs.microsoft.com/rest/api/searchservice/)s odpověďmi vrácenými jako dokumenty JSON.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ *Realestate-US-Sample-index* se používá pro tento rychlý Start. K vytvoření indexu použijte průvodce [**importem dat**](search-import-data-portal.md) . V prvním kroku, po zobrazení výzvy k zadání zdroje dat, zvolte **ukázky** a pak vyberte zdroj dat **realestate-US-Sample** . Přijměte všechny výchozí hodnoty průvodce pro vytvoření indexu.

## <a name="start-search-explorer"></a>Spustit Průzkumníka vyhledávání

1. V [Azure Portal](https://portal.azure.com)otevřete na řídicím panelu stránku vyhledávací služby nebo [Najděte svoji službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Otevřete Průzkumníka služby Search na panelu příkazů:

   ![Příkaz Průzkumníka vyhledávání na portálu](./media/search-explorer/search-explorer-cmd2.png "Příkaz Průzkumníka vyhledávání na portálu")

    Nebo na otevřeném indexu použijte kartu vloženého **Průzkumníka služby Search** :

   ![Karta Hledat v Průzkumníkovi](./media/search-explorer/search-explorer-tab.png "Karta Hledat v Průzkumníkovi")

## <a name="unspecified-query"></a>Neurčený dotaz

Pokud se chcete podívat na obsah, spusťte prázdné vyhledávání kliknutím na **Hledat** bez zadání podmínek. Prázdné hledání je užitečné jako první dotaz, protože vrací celé dokumenty, abyste mohli zkontrolovat složení dokumentu. Při prázdném hledání není k dispozici žádné pořadí hledání a dokumenty se vrátí v libovolném pořadí ( `"@search.score": 1` pro všechny dokumenty). Ve výchozím nastavení jsou v žádosti o vyhledávání vráceny dokumenty 50.

Ekvivalentní syntaxe pro prázdné hledání je `*` nebo `search=*` .
   
   ```http
   search=*
   ```

   **Výsledky**
   
   ![Příklad prázdného dotazu](./media/search-explorer/search-explorer-example-empty.png "Příklad nekvalifikovaného nebo prázdného dotazu")

## <a name="free-text-search"></a>Bezplatné vyhledávání textu

Dotazy na bezplatné formuláře s operátory nebo bez nich jsou užitečné pro simulaci uživatelsky definovaných dotazů odeslaných z vlastní aplikace do Azure Kognitivní hledání. Pouze pole, která jsou v definici indexu **vyhledána jako prohledávatelné** , jsou kontrolovány shody. 

Všimněte si, že když zadáte kritéria hledání, jako jsou například podmínky dotazu nebo výrazy, bude se hledat v pořadí hledání. Následující příklad znázorňuje volné hledání textu.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Výsledky**

   Pomocí kombinace kláves CTRL-F můžete vyhledat konkrétní důležité výrazy v rámci výsledků.

   ![Příklad dotazů na volný text](./media/search-explorer/search-explorer-example-freetext.png "Příklad dotazů na volný text")

## <a name="count-of-matching-documents"></a>Počet vyhovujících dokumentů 

Přidejte **$Count = true** pro získání počtu shod nalezených v indexu. Při prázdném hledání je počet celkový počet dokumentů v indexu. U kvalifikovaného hledání se jedná o počet dokumentů, které odpovídají vstupu dotazu.

   ```http
   $count=true
   ```

   **Výsledky**

   ![Počet dokumentů – příklad](./media/search-explorer/search-explorer-example-count.png "Počet vyhovujících dokumentů v indexu")

## <a name="limit-fields-in-search-results"></a>Omezit pole ve výsledcích hledání

Přidejte [**$Select**](search-query-odata-select.md) pro omezení výsledků do výslovně pojmenovaných polí pro čitelnější výstup v **Průzkumníku služby Search**. Chcete-li zachovat hledaný řetězec a **$Count = true**, nahraďte argumenty předponou **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Výsledky**

   ![Příklad omezení polí](./media/search-explorer/search-explorer-example-selectfield.png "Omezit pole ve výsledcích hledání")

## <a name="return-next-batch-of-results"></a>Vrátit další dávku výsledků

Azure Kognitivní hledání vrátí nejvyšší 50 shody na základě pořadí hledání. Chcete-li získat další sadu vyhovujících dokumentů, přidejte **$Top = 100, &$Skip = 50** pro zvýšení sady výsledků dotazu na 100. (výchozí hodnota je 50, maximum je 1000), přeskakuje se první 50 dokumentů. Abyste získali seřazené výsledky, je nutné, abyste zadali kritéria hledání, jako je například termín dotazu nebo výraz. Všimněte si, že výsledky hledání snižují hlubší přístup k výsledkům vyhledávání.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Výsledky**

   ![Výsledky dávkového vyhledávání](./media/search-explorer/search-explorer-example-topskip.png "Vrátit další dávku výsledků hledání")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Výrazy filtru (větší než, menší než, rovno)

Použijte parametr [**$Filter**](search-query-odata-filter.md) , pokud chcete zadat přesnější kritéria místo hledání volného textu. Pole musí být v indexu s atributem **Filtered** . Tento příklad vyhledá ložnicemi větší než 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Výsledky**

   ![Výraz filtru](./media/search-explorer/search-explorer-example-filter.png "Filtrovat podle kritérií")

## <a name="order-by-expressions"></a>Výrazy ORDER by

Přidejte [**$OrderBy**](search-query-odata-orderby.md) pro řazení výsledků podle jiného pole než skóre vyhledávání. Pole musí **být v indexu v podobě atributu** . Příklad výrazu, který můžete použít k otestování tohoto:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Výsledky**

   ![Výraz OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Změna pořadí řazení")

**$Filter** i **$OrderBy** výrazy jsou konstrukce OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Shrnutí

V tomto rychlém startu jste použili **Průzkumníka služby Search** k dotazování indexu pomocí REST API.

+ Výsledky jsou vráceny jako podrobné dokumenty JSON, takže můžete v celém rozsahu zobrazit konstrukci a obsah dokumentu. Pomocí výrazů dotazů zobrazených v příkladech můžete omezit, která pole jsou vrácena.

+ Dokumenty se skládají ze všech polí označených jako načístelné **v indexu** . Chcete-li zobrazit atributy indexu na portálu, klikněte na *realestate-US-Sample* v seznamu **indexy** na stránce s přehledem hledání.

+ Dotazy na volném formuláři, podobně jako to, co byste mohli zadat do komerčního webového prohlížeče, jsou užitečné při testování činnosti koncového uživatele. Předpokládejme například, že jste zavedli integrovaný vzorový index realestate, mohli byste zadat "Seattle Apartments Lake Washington" a potom můžete pomocí kombinace kláves CTRL-F vyhledat výrazy ve výsledcích hledání. 

+ Výrazy dotazů a filtrů jsou kloubem v syntaxi podporované službou Azure Kognitivní hledání. Výchozí hodnota je [Jednoduchá syntaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale pro výkonnější dotazy můžete použít také [úplné Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) . [Výrazy filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) jsou syntaxí OData.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Chcete-li získat další informace o strukturách a syntaxech dotazů, použijte metodu post nebo ekvivalentní nástroj k vytvoření výrazů dotazů, které využívají více částí rozhraní API. [REST API hledání](https://docs.microsoft.com/rest/api/searchservice/) je užitečné zejména pro učení a průzkum.

> [!div class="nextstepaction"]
> [Vytvoření základního dotazu v post](search-query-simple-examples.md)