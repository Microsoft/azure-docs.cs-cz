---
title: Ukázky JavaScriptu
titleSuffix: Azure Cognitive Search
description: Najděte ukázky kódu JavaScriptu pro Azure Kognitivní hledání demo, které používají sadu Azure .NET SDK pro JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955937"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Ukázky kódu JavaScriptu pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu JavaScriptu, které ukazují funkčnost a pracovní postup řešení Azure Kognitivní hledání. Tyto ukázky používají [**klientskou knihovnu azure kognitivní hledání**](/javascript/api/overview/azure/search-documents-readme) pro [**sadu Azure SDK pro JavaScript**](/azure/developer/javascript/), kterou můžete prozkoumat pomocí následujících odkazů.

| Cíl | Odkaz |
|--------|------|
| Stažení balíčku | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API – referenční informace | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| Testovací případy rozhraní API | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Zdrojový kód | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>Ukázky sady SDK

Ukázky kódu z vývojového týmu Azure SDK ukazují použití rozhraní API. Tyto ukázky najdete v [**Azure-SDK-for-js/Tree/Master/SDK/Search/Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) on GitHub.

### <a name="javascript-sdk-samples"></a>Ukázky sady JavaScript SDK

| ukázky | Description |
|---------|-------------|
| [indexy](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [vyhledávací indexy](search-what-is-an-index.md). Tato ukázková kategorie také obsahuje ukázkový test služby. |
| [dataSourceConnections (pro indexery)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Indexery](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Ukazuje, jak vytvořit, aktualizovat, získat, vypsat, resetovat a odstranit [indexery](search-indexer-overview.md).|
| [Dovednosti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [mapy synonym](search-synonyms.md).  |

### <a name="typescript-samples"></a>Ukázky TypeScriptu

| ukázky | Description |
|---------|-------------|
| [indexy](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [vyhledávací indexy](search-what-is-an-index.md). Tato ukázková kategorie také obsahuje ukázkový test služby. |
| [dataSourceConnections (pro indexery)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Indexery](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Ukazuje, jak vytvořit, aktualizovat, získat, vypsat, resetovat a odstranit [indexery](search-indexer-overview.md).|
| [Dovednosti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [mapy synonym](search-synonyms.md).  |

## <a name="doc-samples"></a>Ukázky dokumentů

Ukázky kódu z týmu Kognitivní hledání ukazují funkce a pracovní postupy. Mnohé z těchto ukázek jsou odkazovány v kurzech, rychlých startech a článcích s postupy. Tyto ukázky najdete v [**Azure-Samples/Azure-Search-JavaScript-Samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) na GitHubu.

| ukázky | Článek |
|---------|---------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání v JavaScriptu](search-get-started-javascript.md). Tento článek popisuje základní pracovní postup pro vytvoření, načtení a dotazování indexu vyhledávání pomocí ukázkových dat. |

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=javascript&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="other-samples"></a>Další ukázky

Následující ukázky jsou publikovány také týmem Kognitivní hledání, ale nejsou odkazovány v dokumentaci. Přidružené soubory Readme poskytují pokyny k používání.

| ukázky | Description |
|---------|-------------|
| [Azure-Search-reagovat – šablona](https://github.com/dereklegenzoff/azure-search-react-template) | Reakce šablony pro Azure Kognitivní hledání (github.com) |