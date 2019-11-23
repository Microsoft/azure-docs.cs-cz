---
title: Create a search index in the Azure portal
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to use the Import Data wizard to create, load, and query your first search index in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: be2425d97573e7990ea7f0dfd4c2d999e85fe922
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407000"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Create an Azure Cognitive Search index in the Azure portal
> [!div class="op_single_selector"]
> * [Azure Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)

Use the portal's **Import data** wizard and **Search explorer** tools to quickly ramp up on concepts, and write interesting queries against an index within minutes.

If the tools are too limiting, you can consider a [code-based introduction to programming Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md) or use [Postman for making REST API calls](search-get-started-postman.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Předpoklady

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

### <a name="check-for-space"></a>Kontrola místa

Mnoho zákazníků začíná s bezplatnou službou. Tato verze je omezená na tři indexy, tři zdroje dat a tři indexery. Než začnete, ujistěte se, že máte místo pro další položky. V tomto kurzu se vytváří od každého objektu jeden.

Sections on the service dashboard show how many indexes, indexers, and data sources you already have. 

![Lists of indexes, indexers, and datasources](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a>Vytvoření indexu a načtení dat

Vyhledávací dotazy provádějí iterace [*indexu*](search-what-is-an-index.md), který obsahuje data s možností vyhledávání, metadata a další konstrukce, které optimalizují určité chování vyhledávání.

For this tutorial, we use a built-in sample dataset that can be crawled using an [*indexer*](search-indexer-overview.md) via the [**Import data** wizard](search-import-data-portal.md). Indexer je prohledávací modul pro určitý zdroj, který dokáže číst metadata a obsah z podporovaných zdrojů dat Azure. Normally, indexers are used programmatically, but in the portal, you can access them through the **Import data** wizard. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Step 1 - Start the Import data wizard and create a data source

1. On the Azure Cognitive Search service dashboard, click **Import data** on the command bar to create and populate a search index.

   ![Příkaz pro import dat](media/search-get-started-portal/import-data-cmd.png)

2. In the wizard, click **Connect to your data** > **Samples** > **hotels-sample**. This data source is built-in. If you were creating your own data source, you would need to specify a name, type, and connection information. Po vytvoření se z něj stane „existující zdroj dat“, který je možné využít v dalších operacích importu.

   ![Výběr ukázkové datové sady](media/search-get-started-portal/import-datasource-sample.png)

3. Continue to the next page.

### <a name="step-2---skip-the-enrich-content-page"></a>Step 2 - Skip the "Enrich content" page

The wizard supports the creation of an [AI enrichment pipeline](cognitive-search-concept-intro.md) for incorporating the Cognitive Services AI algorithms into indexing. 

We'll skip this step for now, and move directly on to **Customize target index**.

   ![Vynechání kroku kognitivních dovedností](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> You can step through an AI-indexing example in a [quickstart](cognitive-search-quickstart-blob.md) or [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Step 3 - Configure index

Typically, index creation is a code-based exercise, completed prior to loading data. However, as this tutorial indicates, the wizard can generate a basic index for any data source it can crawl. Index vyžaduje minimálně název a kolekci polí s jedním polem označeným jako klíč dokumentu, aby bylo možné dokument jednoznačně identifikovat. Additionally, you can specify language analyzers or suggesters if you want autocomplete or suggested queries.

Pole mají datové typy a atributy. Zaškrtávací políčka v horní části jsou *atributy indexu*, které určují způsob použití pole.

* **Retrievable** (Zobrazitelné) znamená, že se zobrazí v seznamu výsledků vyhledávání. You can mark individual fields as off limits for search results by clearing this checkbox, for example for fields used only in filter expressions.
* **Key** is the unique document identifier. It's always a string, and it is required.
* **Filterable**, **Sortable**, and **Facetable** determine whether fields are used in a filter, sort, or faceted navigation structure.
* **Searchable** (Prohledávatelné) znamená, že je pole součástí fulltextové vyhledávání. Řetězce je možné prohledávat. Číselná pole a logická pole jsou často označena jako neprohledávatelné.

Storage requirements do not vary as a result of your selection. For example, if you set the **Retrievable** attribute on multiple fields, storage requirements do not go up.

Ve výchozím nastavení průvodce vyhledá ve zdroji dat jedinečné identifikátory jako základ pro klíčové pole. *Strings* are attributed as **Retrievable** and **Searchable**. *Integers* are attributed as **Retrievable**, **Filterable**, **Sortable**, and **Facetable**.

1. Přijměte výchozí hodnoty. 

   If you rerun the wizard a second time using an existing hotels data source, the index won't be configured with default attributes. You'll have to manually select attributes on future imports. 

   ![Generated hotels index](media/search-get-started-portal/hotelsindex.png)

2. Continue to the next page.


### <a name="step-4---configure-indexer"></a>Step 4 - Configure indexer

Stále v **Průvodci importem dat** klikněte na **Indexer** > **Název** a zadejte název indexeru.

Tento objekt definuje spustitelný proces. You could put it on recurring schedule, but for now use the default option to run the indexer once, immediately.

Click **Submit** to create and simultaneously run the indexer.

  ![hotels indexer](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitor progress

The wizard should take you to the Indexers list where you can monitor progress. For self-navigation, go to the Overview page and click **Indexers**.

It can take a few minutes for the portal to update the page, but you should see the newly created indexer in the list, with status indicating "in progress" or success, along with the number of documents indexed.

   ![Zpráva indexeru o průběhu](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Zobrazení indexu

The main service page provides links to the resources created in your Azure Cognitive Search service.  To view the index you just created, click **Indexes** from the list of links. 

Wait for the portal page to refresh. After a few minutes, you should see the index with a document count and storage size.

   ![Indexes list on the service dashboard](media/search-get-started-portal/indexes-list.png)

From this list, you can click on the *hotels-sample* index that you just created, view the index schema. and optionally add new fields. 

The **Fields** tab shows the index schema. Scroll to the bottom of the list to enter a new field. In most cases, you cannot change existing fields. Existing fields have a physical representation in Azure Cognitive Search and are thus non-modifiable, not even in code. To fundamentally change an existing field, create a new index, dropping the original.

   ![Ukázková definice indexu](media/search-get-started-portal/sample-index-def.png)

Kdykoli lze přidat další konstrukce, jako jsou bodovací profily a možnosti CORS.

Vyhraďte si chvilku na prostudování možností definice indexu, abyste dobře chápali, co všechno můžete při návrhu indexu upravovat, a co naopak ne. Možnosti zobrazené šedě naznačují, že se určitá hodnota nedá upravit nebo odstranit. 

## <a name="query-index"></a> Query using Search explorer

Teď už byste měli mít vyhledávací index, který je připravený na dotazování pomocí integrované stránky dotazů [**Průzkumník služby Hledání**](search-explorer.md). Ta obsahuje vyhledávací pole, ve kterém si můžete otestovat libovolné řetězce dotazů.

**Search explorer** is only equipped to handle [REST API requests](https://docs.microsoft.com/rest/api/searchservice/search-documents), but it accepts syntax for both [simple query syntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) and [full Lucene query parser](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), plus all the search parameters available in [Search Document REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) operations.

> [!TIP]
> The following steps are demonstrated at 6m08s into the [Azure Cognitive Search Overview video](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Klikněte na **Průzkumník služby Search** na panelu příkazů.

   ![Příkaz průzkumníka služby Search](media/search-get-started-portal/search-explorer-cmd.png)

2. From the **Index** dropdown, choose  *hotels-sample-index*. Click the **API Version** dropdown, to see which REST APIs are available. For the queries below, use the generally available version (2019-05-06).

   ![Příkazy rozhraní API a index](media/search-get-started-portal/search-explorer-changeindex.png)

3. In the search bar, paste in the query strings below and click **Search**.

   ![Query string and search button](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Example queries

You can enter terms and phrases, similar to what you might do in a Bing or Google search, or fully-specified query expressions. Results are returned as verbose JSON documents.

### <a name="simple-query-with-top-n-results"></a>Jednoduchý dotaz s horními N výsledky

#### <a name="example-string-query-searchspa"></a>Example (string query): `search=spa`

* The **search** parameter is used to input a keyword search for full text search, in this case, returning hotel data for those containing *spa* in any searchable field in the document.

* **Průzkumník služby Search** vrátí výsledky ve formátu JSON, který je podrobný a těžko čitelný, pokud mají dokumenty kompaktní strukturu. This is intentional; visibility into the entire document is important for development purposes, especially during testing. Pokud chcete uživatelské prostředí zlepšit, je potřeba napsat kód, který [zpracuje výsledky hledání](search-pagination-page-layout.md) a vybere z nich důležité elementy.

* Dokumenty se skládají ze všech polí, která mají v indexu označení Zobrazitelné. To view index attributes in the portal, click *hotels-sample* in the **Indexes** list.

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Example (parameterized query): `search=spa&$count=true&$top=10`

* Symbol **&** slouží k připojení parametrů vyhledávání, které lze zadat v libovolném pořadí.

* The **$count=true** parameter returns the total count of all documents returned. Tato hodnota se zobrazí v horní části výsledků hledání. Monitorováním změn hlášených parametrem **$count=true** můžete ověřovat filtrovací dotazy. Menší počet výsledků naznačuje, že filtr funguje.

* The **$top=10** returns the highest ranked 10 documents out of the total. By default, Azure Cognitive Search returns the first 50 best matches. Pomocí parametru **$top** můžete tento počet navýšit nebo snížit.

### <a name="filter-query"></a>Filtrování dotazu

Filtry se do požadavků hledání zahrnou po připojení parametru **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Příklad (filtrovaný): `search=beach&$filter=Rating gt 4`

* Parametr **$filter** vrací výsledky odpovídající kritériím, která jste zadali. In this case, ratings greater than 4.

* Syntaxe parametru Filter je založená na konstruktech jazyka OData. Další informace najdete v tématu věnovaném [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a>Omezení vlastností dotazu

Součástí požadavků hledání jsou filtry omezující vlastnost. Pomocí parametru facet můžete vrátit celkový počet dokumentů odpovídajících hodnotě omezující vlastnosti, kterou zadáte.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Příklad (s omezujícími vlastnostmi a zmenšením rozsahu): `search=*&facet=Category&$top=2`

* Parametr **search=** * znamená prázdné vyhledávání. Prázdné vyhledávání prohledává všechno. Jedním z důvodů odeslání prázdného dotazu je použití filtru nebo omezující vlastnosti na kompletní sadu dokumentů. For example, you want a faceting navigation structure to consist of all hotels in the index.
* Parametr **facet** vrací navigační strukturu, kterou můžete předat ovládacímu prvku uživatelského rozhraní. Vrací kategorie a počet. In this case, categories are based on a field conveniently called *Category*. There is no aggregation in Azure Cognitive Search, but you can approximate aggregation via `facet`, which gives a count of documents in each category.

* Parametr **$top=2** vrací dva dokumenty a ilustruje, že parametr `top` můžete použít ke snížení i navýšení počtu výsledků.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Example (facet on numeric values): `search=spa&facet=Rating`

* This query is facet for rating, on a text search for *spa*. The term *Rating* can be specified as a facet because the field is marked as retrievable, filterable, and facetable in the index, and the values it contains (numeric, 1 through 5), are suitable for categorizing listings into groups.

* Kategorizovat je možné pouze filtrovatelná pole. Ve výsledcích je možné vrátit pouze zobrazitelná pole.

* The *Rating* field is double-precision floating point and the grouping will be by precise value. For more information on grouping by interval (for instance, "3 star ratings," "4 star ratings," etc.), see [How to implement faceted navigation in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Zvýraznění výsledků hledání

Zvýrazňování shod označuje formátování textu odpovídajícího klíčovému slovu (za předpokladu, že se v konkrétním poli našly shody). Pokud je hledaný termín schovaný v popisu, můžete pomocí zvýrazňování shod usnadnit jeho nalezení.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Příklad (zvýraznění): `search=beach&highlight=Description`

* In this example, the formatted word *beach* is easier to spot in the description field.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Příklad (lingvistická analýza): `search=beaches&highlight=Description`

* Full text search recognizes basic variations in word forms. In this case, search results contain highlighted text for "beach", for hotels that have that word in their searchable fields, in response to a keyword search on "beaches". Ve výsledcích se díky lingvistické analýze mohou zobrazit různé tvary téhož slova. 

* Azure Cognitive Search supports 56 analyzers from both Lucene and Microsoft. The default used by Azure Cognitive Search is the standard Lucene analyzer.

### <a name="fuzzy-search"></a> Zkouška vyhledávání přibližných shod

By default, misspelled query terms, like *seatle* for "Seattle", fail to return matches in typical search. Následující příklad nevrátí žádné výsledky.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Příklad (chybně zadaný termín bez zpracování): `search=seatle`

Ke zpracování chybně napsaných slov můžete využít vyhledávání přibližných shod. Vyhledávání přibližných shod se povolí v případě, že použijete úplnou syntaxi dotazů Lucene. K tomu je potřeba provést dvě věci: nastavit v dotazu **queryType=full** a připojit k řetězci dotazu znak **~** .

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Příklad (chybně zadaný termín se zpracováním): `search=seatle~&queryType=full`

This example now returns documents that include matches on "Seattle".

Pokud parametr **queryType** není zadaný, použije se výchozí jednoduchý analyzátor dotazů. Jednoduchý analyzátor dotazů je rychlejší, ale pokud vyžadujete vyhledávání přibližných shod, regulární výrazy, vyhledávání blízkých výrazů nebo jiné pokročilé typy dotazů, budete potřebovat celou syntaxi.

Vyhledávání přibližných shod a vyhledávání pomocí zástupných znaků mají vliv na výstup hledání. U těchto formátů dotazů se neprovádí lingvistická analýza. Before using fuzzy and wildcard search, review [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) and look for the section about exceptions to lexical analysis.

For more information about query scenarios enabled by the full query parser, see [Lucene query syntax in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a>Vyzkoušení geoprostorového hledání

Geoprostorové hledání je podporované prostřednictvím [datového typu edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) v polích, které obsahují souřadnice. Geoprostorové hledání je typ filtru určený v [syntaxi jazyka OData pro filtry](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Příklad (filtry geografických souřadnic): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

Tento vzorový dotaz vyfiltruje všechny výsledky pro poziční data, kde jsou výsledky od daného bodu (zadaného pomocí zeměpisné šířky a délky) vzdálené méně než 5 kilometrů. Přidáním parametru **$count** můžete zobrazit, kolik výsledků se vrátí, když změníte vzdálenost nebo souřadnice.

Geoprostorové hledání je užitečné, pokud vaše vyhledávací aplikace obsahuje funkci typu „najít poblíž“ nebo používá navigaci podle map. Není to ale fulltextové vyhledávání. If you have user requirements for searching on a city or country/region by name, add fields containing city or country/region names, in addition to coordinates.

## <a name="takeaways"></a>Shrnutí

This tutorial provided a quick introduction to Azure Cognitive Search using the Azure portal.

Zjistili jste, jak vytvořit index vyhledávání pomocí průvodce pro **Import dat**. Dozvěděli jste se o [indexerech](search-indexer-overview.md) a také o základních pracovních postupech návrhu indexu, včetně [podporovaných úprav publikovaného indexu](https://docs.microsoft.com/rest/api/searchservice/update-index).

V **průzkumníku služby Search** na webu Azure Portal jste se prostřednictvím praktických příkladů klíčových funkcí, jako jsou filtry, zvýrazňování shod, vyhledávání přibližných shod a geografické vyhledávání, seznámili se syntaxí dotazů.

You also learned how to find indexes, indexers, and data sources in the portal. Pomocí portálu můžete v budoucnu u nových zdrojů dat s minimálním úsilím rychle zkontrolovat jejich definice nebo kolekce polí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Další kroky

While the portal is useful for initial exploration and small tasks, reviewing the APIs early on will help you understand the concepts and workflow on a deeper level:

> [!div class="nextstepaction"]
> [Create an index using .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)