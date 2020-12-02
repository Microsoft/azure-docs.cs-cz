---
title: Ukázky Pythonu
titleSuffix: Azure Cognitive Search
description: Najděte si ukázky kódu Pythonu pro Azure Kognitivní hledání, které používají sadu Azure .NET SDK pro Python nebo REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 244408cc512a51683ecb3c062113170b1c7f4781
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499556"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Ukázky kódu Pythonu pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu Pythonu, které ukazují funkce a funkce Azure Kognitivní hledání. Primární úložiště jsou následující:

| Repository | Popis |
|------------|-------------|
| [Azure-SDK-for-Python/strom/Master/SDK/Search/Azure-Search-Documents/Samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Ukázky vytvářené týmem Azure SDK, které se dodávají s klientskou knihovnou Azure.Search.Documents v sadě SDK Můžete si také projít [testy jednotek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) klientské knihovny, abyste viděli, jak jsou volána různá rozhraní API. |
| [Azure-Samples/Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples) | Ukázky kódu, které doprovázejí články s návody, včetně [rychlého startu: vytvoření indexu vyhledávání v Pythonu](search-get-started-python.md).|

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=csharp&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="python-sdk-samples"></a>Ukázky Python SDK

Sada Azure SDK for Python zahrnuje řadu ukázek a [úvodní stránku](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) , která zahrnuje požadavky a instalaci balíčku. Stránka obsahuje také odkazy na následující ukázky, které jsou zde uvedeny pro usnadnění.

| ukázky | Popis |
|---------|-------------|
| [Ověření](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Ukazuje, jak nakonfigurovat klienta a ověřit službu. | 
| [Vytvoření indexu – operace čtení i aktualizace – odstranění](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [vyhledávací indexy](search-what-is-an-index.md). |
| [Vytvoření indexeru-čtení-aktualizace-odstranění – operace](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat, resetovat a odstranit [indexery](search-indexer-overview.md). |
| [Zdroje dat indexeru hledání](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Synonyma](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [mapy synonym](search-synonyms.md).  |
| [Nahrání dokumentů](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Ukazuje, jak nahrát nebo sloučit dokumenty do indexu v rámci operace [importu dat](search-what-is-data-import.md) . |
| [Jednoduchý dotaz](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Ukazuje, jak nastavit [základní dotaz](search-query-overview.md). |
| [Filtrovat dotaz](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Ukazuje nastavení [výrazu filtru](search-filters.md). |
| [Dotaz na omezující vlastnost](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Ukazuje práci s [omezujícími vlastnostmi](search-filters-facets.md). |

## <a name="documentation-samples"></a>Ukázky v dokumentaci

Následující ukázky mají přidružený článek v [dokumentaci k Azure kognitivní hledání](./index.yml).

| ukázky | Popis | 
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání v Pythonu](search-get-started-python.md)  |
| [Kurz – rozšíření AI](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Zdrojový kód pro [kurz: pomocí Pythonu a AI můžete vygenerovat prohledávatelný obsah z objektů blob Azure](cognitive-search-tutorial-blob-python.md).  |
| [AzureML – vlastní – dovednost](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Zdrojový kód [například: Vytvoření vlastní dovednosti pomocí Pythonu](cognitive-search-custom-skill-python.md).  |