---
title: Vyhledávací dotazovací nástroj průzkumníka na webu Azure Portal
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu na webu Azure Portal se pomocí Průzkumníka Vyhledávání dozvíte syntaxi dotazu, otestujete výrazy dotazu nebo zkontrolujete vyhledávací dokument. Hledání explorer dotazy indexy v Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369670"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Úvodní příručka: Spouštění dotazů na portálu pomocí Průzkumníka vyhledávání

**Průzkumník hledání** je integrovaný nástroj pro dotazy, který se používá pro spouštění dotazů proti indexu vyhledávání v Azure Cognitive Search. Tento nástroj usnadňuje učení syntaxe dotazu, testování dotazu nebo výrazu filtru nebo potvrzení výsledků aktualizace indexu ověřením, že existuje novější obsah.

Tento rychlý start používá **realestate-us-sample-index** k předvedení Průzkumníka vyhledávání. Požadavky jsou formulovány pomocí [rozhraní REST API hledání](https://docs.microsoft.com/rest/api/searchservice/)s odpověďmi vrácenými jako dokumenty JSON.

## <a name="prerequisites"></a>Požadavky

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu.

+ **realestate-us-sample-index** se používá pro tento rychlý start. Projděte si Průvodce [**importem dat**](search-import-data-portal.md) a vygenerujte index z předdefinovaného zdroje dat ukázek.

## <a name="start-search-explorer"></a>Spustit Průzkumníka hledání

1. Na [webu Azure Portal](https://portal.azure.com)otevřete stránku vyhledávací služby z řídicího panelu nebo [vyhledejte svou službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Otevřete Průzkumníka hledání z panelu příkazů:

   ![Příkaz Průzkumníka hledání na portálu](./media/search-explorer/search-explorer-cmd2.png "Příkaz Průzkumníka hledání na portálu")

    Nebo použijte vloženou kartu **Průzkumník hledání** na otevřeném rejstříku:

   ![Karta Průzkumník hledání](./media/search-explorer/search-explorer-tab.png "Karta Průzkumník hledání")

## <a name="unspecified-query"></a>Nespecifikovaný dotaz

První pohled na obsah provedete prázdné hledání kliknutím na **Hledat** bez termínů. Prázdné hledání je užitečné jako první dotaz, protože vrací celé dokumenty, takže můžete zkontrolovat složení dokumentu. Při prázdném hledání není k dispozici žádné pořadí hledání`"@search.score": 1` a dokumenty jsou vráceny v libovolném pořadí (pro všechny dokumenty). Ve výchozím nastavení je v požadavku na hledání vráceno 50 dokumentů.

Ekvivalentní syntaxe pro `*` prázdné `search=*`hledání je nebo .
   
   ```http
   search=*
   ```

   **Results**
   
   ![Prázdný příklad dotazu](./media/search-explorer/search-explorer-example-empty.png "Příklad neúplného nebo prázdného dotazu")

## <a name="free-text-search"></a>Volné vyhledávání textu

Volné dotazy, s operátory nebo bez nich, jsou užitečné pro simulaci uživatelem definovaných dotazů odeslaných z vlastní aplikace do Azure Cognitive Search. Pouze pole přiřazená jako **prohledávatelná** v definici indexu jsou kontrolována na shody. 

Všimněte si, že když zadáte kritéria vyhledávání, jako jsou dotazy nebo výrazy, pořadí hledání vstoupí do hry. Následující příklad ilustruje hledání volného textu.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Results**

   Pomocí kláves Ctrl-F můžete vyhledávat konkrétní podmínky zájmu ve výsledcích.

   ![Příklad volného textového dotazu](./media/search-explorer/search-explorer-example-freetext.png "Příklad volného textového dotazu")

## <a name="count-of-matching-documents"></a>Počet odpovídajících dokumentů 

Přidejte **$count=true,** chcete-li získat počet shod nalezených v indexu. Při prázdném hledání je celkový počet dokumentů v indexu. Při kvalifikovaném hledání je to počet dokumentů odpovídajících vstupu dotazu.

   ```http
   $count=true
   ```

   **Results**

   ![Příklad počtu dokumentů](./media/search-explorer/search-explorer-example-count.png "Počet odpovídajících dokumentů v rejstříku")

## <a name="limit-fields-in-search-results"></a>Omezit pole ve výsledcích hledání

Přidáním [**$select**](search-query-odata-select.md) omezíte výsledky na explicitně pojmenovaná pole pro čitelnější výstup v **průzkumníku vyhledávání**. Chcete-li zachovat hledaný řetězec a **$count=true**, předpona argumenty s **&**. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Results**

   ![Příklad omezení polí](./media/search-explorer/search-explorer-example-selectfield.png "Omezení polí ve výsledcích hledání")

## <a name="return-next-batch-of-results"></a>Vrátit další dávku výsledků

Azure Cognitive Search vrátí prvních 50 shod na základě pořadí hledání. Chcete-li získat další sadu odpovídajících dokumentů, přidejte **$top= 100 &$skip = 50** pro zvýšení sady výsledků na 100 dokumentů (výchozí je 50, maximum je 1000) přeskočení prvních 50 dokumentů. Připomeňme si, že je třeba zadat kritéria vyhledávání, například termín dotazu nebo výraz, abyste získali seřazené výsledky. Všimněte si, že skóre vyhledávání snižují čím hlouběji se dostanete do výsledků vyhledávání.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Results**

   ![Výsledky dávkového hledání](./media/search-explorer/search-explorer-example-topskip.png "Vrátit další dávku výsledků hledání")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Výrazy filtru (větší než, menší než, rovno)

Parametr [**$filter**](search-query-odata-filter.md) použijte, pokud chcete zadat přesná kritéria, nikoli volné hledání textu. Pole musí být v indexu přiřazeno jako **filtrovatelné.** Tento příklad vyhledá ložnice větší než 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Results**

   ![Filtrovat výraz](./media/search-explorer/search-explorer-example-filter.png "Filtrování podle kritérií")

## <a name="order-by-expressions"></a>Výrazy podle pořadí

Přidejte [**$orderby,**](search-query-odata-orderby.md) chcete-li výsledky seřadit podle jiného pole kromě skóre vyhledávání. Pole musí být v indexu přiřazeno jako **seřaditelné.** Příklad výrazu, který můžete použít k testování tohoto je:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Results**

   ![Orderby výraz](./media/search-explorer/search-explorer-example-ordery.png "Změna pořadí řazení")

**$filter** i **$orderby** výrazy jsou konstrukce OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Shrnutí

V tomto rychlém startu jste pomocí **průzkumníka vyhledávání** dotazovat index pomocí rozhraní REST API.

+ Výsledky jsou vráceny jako podrobné dokumenty JSON, takže můžete zobrazit konstrukci dokumentu a obsah v plném rozsahu. Pomocí výrazů dotazu zobrazených v příkladech můžete omezit, která pole budou vrácena.

+ Dokumenty se skládají ze všech polí označených jako **Retrievable** v indexu. Chcete-li zobrazit atributy indexu na portálu, klikněte na *položku Realestate-us-sample* v seznamu **Indexy** na stránce přehled u hledání.

+ Volné dotazy, podobné tomu, co byste mohli zadat v komerčním webovém prohlížeči, jsou užitečné pro testování prostředí pro koncové uživatele. Například za předpokladu, že vestavěný index vzorku nemovitostí, můžete zadat "Seattle apartmány lake washington", a pak můžete použít Ctrl-F najít termíny ve výsledcích vyhledávání. 

+ Výrazy dotazů a filtrů jsou formulovány v syntaxi podporované Azure Cognitive Search. Výchozí hodnota je [jednoduchá syntaxe](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ale volitelně můžete použít [plnou Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) pro výkonnější dotazy. [Výrazy filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) jsou syntaxí OData.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Chcete-li získat další informace o struktury dotazů a syntaxe, použijte Postman nebo ekvivalentní nástroj k vytvoření výrazy dotazu, které využívají více částí rozhraní API. [Rozhraní REST API pro vyhledávání](https://docs.microsoft.com/rest/api/searchservice/) je užitečné zejména pro učení a zkoumání.

> [!div class="nextstepaction"]
> [Vytvoření základního dotazu v Pošťákovi](search-query-simple-examples.md)