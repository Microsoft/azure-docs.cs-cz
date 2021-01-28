---
title: Ukázky Java
titleSuffix: Azure Cognitive Search
description: Najděte si ukázky kódu Java pro Azure Kognitivní hledání demo, které používají sadu Azure .NET SDK pro jazyk Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955033"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Ukázky kódu Java pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu Java, které ukazují funkčnost a pracovní postup řešení Azure Kognitivní hledání. Tyto ukázky používají [**klientskou knihovnu azure kognitivní hledání**](/java/api/overview/azure/search-documents-readme) pro [**sadu Azure SDK pro jazyk Java**](/azure/developer/java/sdk), kterou můžete prozkoumat pomocí následujících odkazů.

| Cíl | Odkaz |
|--------|------|
| Stažení balíčku | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API – referenční informace | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| Testovací případy rozhraní API | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Zdrojový kód | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>Ukázky sady SDK

Ukázky kódu z vývojového týmu Azure SDK ukazují použití rozhraní API. Tyto ukázky najdete v [**Azure/Azure-SDK-for-Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) na GitHubu.

| ukázky | Description |
|---------|-------------|
| [Vytváření indexu vyhledávání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Ukazuje, jak vytvořit [indexy vyhledávání](search-what-is-an-index.md). |
| [Vytvoření synonyma](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Ukazuje, jak vytvořit [mapy synonym](search-synonyms.md).  |
| [Vytváření indexeru hledání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Ukazuje, jak vytvořit [indexery](search-indexer-overview.md). |
| [Vytvoření zdroje dat indexeru hledání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Ukazuje, jak vytvořit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Vytváření dovednosti](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Ukazuje, jak vytvořit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [Nahrání dokumentů](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Ukazuje, jak nahrát nebo sloučit dokumenty do indexu v rámci operace [importu dat](search-what-is-data-import.md) . |
| [Syntaxe dotazu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Ukazuje, jak nastavit [základní dotaz](search-query-overview.md). |

## <a name="doc-samples"></a>Ukázky dokumentů

Ukázky kódu z týmu Kognitivní hledání ukazují funkce a pracovní postupy. Mnohé z těchto ukázek jsou odkazovány v kurzech, rychlých startech a článcích s postupy. Tyto ukázky najdete v [**Azure-Samples/Azure-Search-Java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) na GitHubu.

| ukázky | Článek | 
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání v jazyce Java a REST](search-get-started-java.md). Tato ukázka se pro sadu Java SDK neaktualizovala. Volá rozhraní REST API. |

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=java&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="other-samples"></a>Další ukázky

Následující ukázky jsou publikovány také týmem Kognitivní hledání, ale nejsou odkazovány v dokumentaci. Přidružené soubory Readme poskytují pokyny k používání.

| ukázky | Description |
|---------|-------------|
| [hledání – Java – Začínáme](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Používá klientskou knihovnu Java SDK k vytvoření, načtení a dotazování indexu vyhledávání. Tato ukázka je aktuálně samostatná. |
| [Search-Java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Ukazuje indexer Azure Cosmos DB v jazyce Java. Tato ukázka se pro sadu Java SDK neaktualizovala. Volá rozhraní REST API.|