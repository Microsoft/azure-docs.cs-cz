---
title: Ukázky Pythonu
titleSuffix: Azure Cognitive Search
description: Najděte si ukázky kódu Pythonu pro Azure Kognitivní hledání, které používají sadu Azure .NET SDK pro Python nebo REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955118"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Ukázky kódu Pythonu pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu Pythonu, které ukazují funkčnost a pracovní postup řešení Azure Kognitivní hledání. Tyto ukázky používají [**klientskou knihovnu azure kognitivní hledání**](/python/api/overview/azure/search-documents-readme) pro [**sadu Azure SDK pro Python**](/azure/developer/python/), kterou můžete prozkoumat pomocí následujících odkazů.

| Cíl | Odkaz |
|--------|------|
| Stažení balíčku | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API – referenční informace | [Azure-Search – dokumenty](/python/api/azure-search-documents)  |
| Testovací případy rozhraní API | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Zdrojový kód | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>Ukázky sady SDK

Ukázky kódu z vývojového týmu Azure SDK ukazují použití rozhraní API. Tyto ukázky najdete v [**sadě Azure-SDK – for-Python/strom/Master/SDK/Search/Azure-Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) on GitHub.

| ukázky | Description |
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

## <a name="doc-samples"></a>Ukázky dokumentů

Ukázky kódu z týmu Kognitivní hledání ukazují funkce a pracovní postupy. Mnohé z těchto ukázek jsou odkazovány v kurzech, rychlých startech a článcích s postupy. Tyto ukázky najdete v ukázkách [**Azure-Samples/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) na GitHubu.

| ukázky | Článek |
|---------|---------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání v Pythonu](search-get-started-python.md) Tento článek popisuje základní pracovní postup pro vytvoření, načtení a dotazování indexu vyhledávání pomocí ukázkových dat. |
| [Kurz – rozšíření AI](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Zdrojový kód pro [kurz: pomocí Pythonu a AI můžete vygenerovat prohledávatelný obsah z objektů blob Azure](cognitive-search-tutorial-blob-python.md). V tomto článku se dozvíte, jak vytvořit indexer objektů BLOB s rozpoznáváním dovednosti, kde dovednosti vytváří a transformuje nezpracovaný obsah, aby bylo možné ho prohledávat nebo využít. |
| [AzureML – vlastní – dovednost](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Zdrojový kód [například: Vytvoření vlastní dovednosti pomocí Pythonu](cognitive-search-custom-skill-python.md). Tento článek ukazuje indexovací indexer a dovednosti integraci se modely obsáhlého učení v Azure Machine Learning. |

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=python&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.