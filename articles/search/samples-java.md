---
title: Ukázky Java
titleSuffix: Azure Cognitive Search
description: Najděte si ukázky kódu Java pro Azure Kognitivní hledání demo, které používají sadu Azure .NET SDK pro jazyk Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: babd40e0e3a3b7a254a8410313c5d4e3bb2832f0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686770"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Ukázky kódu Java pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu Java, které ukazují funkce a funkce Azure Kognitivní hledání. Primární úložiště jsou následující:

| Repository | Description |
|------------|-------------|
| [Azure-SDK-for-Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Ukázky vytvářené týmem Azure SDK, které se dodávají s klientskou knihovnou Azure.Search.Documents v sadě SDK Můžete si také projít [testy jednotek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) klientské knihovny, abyste viděli, jak jsou volána různá rozhraní API. |
| [Azure-Samples/Azure-Search-Java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Ukázky kódu, které doprovázejí články s postupy. **Ukázky v tomto úložišti ještě nejsou aktualizované na používání sady Azure SDK pro jazyk Java**. V současné době tyto ukázky volají rozhraní REST API v kódu Java.|

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=csharp&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="java-sdk-samples"></a>Ukázky Java SDK

Sada Azure SDK pro jazyk Java obsahuje mnoho ukázek a [úvodní stránku](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) , která pokrývá instalaci balíčku. Stránka také obsahuje řadu příkladů. Několik častých operací je níže uvedených pro vaše pohodlí.

| ukázky | Description |
|---------|-------------|
| [Vytváření indexu vyhledávání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Ukazuje, jak vytvořit [indexy vyhledávání](search-what-is-an-index.md). |
| [Vytvoření synonyma](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Ukazuje, jak vytvořit [mapy synonym](search-synonyms.md).  |
| [Vytváření indexeru hledání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Ukazuje, jak vytvořit [indexery](search-indexer-overview.md). |
| [Vytvoření zdroje dat indexeru hledání](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Ukazuje, jak vytvořit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Vytváření dovednosti](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Ukazuje, jak vytvořit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [Nahrání dokumentů](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Ukazuje, jak nahrát nebo sloučit dokumenty do indexu v rámci operace [importu dat](search-what-is-data-import.md) . |
| [Syntaxe dotazu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Ukazuje, jak nastavit [základní dotaz](search-query-overview.md). |

## <a name="documentation-samples"></a>Ukázky v dokumentaci

Následující ukázky mají přidružený článek v [dokumentaci k Azure kognitivní hledání](https://docs.microsoft.com/azure/search/).

| ukázky | Description | 
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/Quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání](search-get-started-javascript.md). Tato ukázka volá rozhraní REST API. |
| [Search-Java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Ukazuje indexer Azure Cosmos DB v jazyce Java. Tato ukázka volá rozhraní REST API. |