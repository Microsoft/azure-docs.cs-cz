---
title: Ukázky JavaScriptu
titleSuffix: Azure Cognitive Search
description: Najděte ukázky kódu JavaScriptu pro Azure Kognitivní hledání demo, které používají sadu Azure .NET SDK pro JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686725"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Ukázky kódu JavaScriptu pro Azure Kognitivní hledání

Přečtěte si o ukázkách kódu JavaScriptu, které ukazují funkce a funkce Azure Kognitivní hledání. Primární úložiště jsou následující:

| Repository | Description |
|------------|-------------|
| [Azure-SDK-for-js/strom/Master/SDK/vyhledat/Hledat – dokumenty](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Ukázky vytvářené týmem Azure SDK, které se dodávají s klientskou knihovnou Azure.Search.Documents v sadě SDK Můžete si také projít [testy jednotek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) klientské knihovny, abyste viděli, jak jsou volána různá rozhraní API. |
| [Azure-Samples/Azure-Search-JavaScript-Samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Ukázky kódu, které doprovázejí články s návody, včetně [rychlého startu: vytvoření indexu vyhledávání v JavaScriptu](search-get-started-javascript.md).|

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?languages=csharp&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="javascript-sdk-samples"></a>Ukázky sady JavaScript SDK

Sada Azure SDK pro jazyk Java obsahuje řadu ukázek a [úvodní stránku](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) , která pokrývá instalaci balíčku, instalaci klienta a řešení potíží. Stránka také popisuje následující vzorové kategorie uvedené pro usnadnění práce.

| ukázky | Description |
|---------|-------------|
| [indexy](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [vyhledávací indexy](search-what-is-an-index.md). Tato ukázková kategorie také obsahuje ukázkový test služby. |
| [dataSourceConnections (pro indexery)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Indexery](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Ukazuje, jak vytvořit, aktualizovat, získat, vypsat, resetovat a odstranit [indexery](search-indexer-overview.md).|
| [Dovednosti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [mapy synonym](search-synonyms.md).  |
| [Dotazy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Dotazy jsou jen pro čtení. Tento ukázkový dotaz se provede proti veřejnému indexu hostovanému společností Microsoft.  |

## <a name="typescript-samples"></a>Ukázky TypeScriptu

Sada SDK také nabízí příklady TypeScript, které jsou tady uvedené pro vaši pohodlí.

| ukázky | Description |
|---------|-------------|
| [indexy](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [vyhledávací indexy](search-what-is-an-index.md). Tato ukázková kategorie také obsahuje ukázkový test služby. |
| [dataSourceConnections (pro indexery)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit zdroje dat indexeru, které jsou vyžadovány pro indexování [podporovaných zdrojů dat Azure](search-indexer-overview.md#supported-data-sources)pomocí indexeru. |
| [Indexery](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Ukazuje, jak vytvořit, aktualizovat, získat, vypsat, resetovat a odstranit [indexery](search-indexer-overview.md).|
| [Dovednosti](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [dovednosti](cognitive-search-working-with-skillsets.md) , které jsou připojené indexery a které provádějí rozšíření na bázi AI během indexování. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Ukazuje, jak vytvořit, aktualizovat, získat, vypsat a odstranit [mapy synonym](search-synonyms.md).  |
| [Dotazy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | Ukazuje provádění dotazů proti veřejnému indexu, který je hostitelem společnosti Microsoft.  |

## <a name="documentation-samples"></a>Ukázky v dokumentaci

Následující ukázky mají přidružený článek v [dokumentaci k Azure kognitivní hledání](https://docs.microsoft.com/azure/search/).

| ukázky | Description | 
|---------|-------------|
| [rychlý Start](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání v JavaScriptu](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Samostatné ukázky

| ukázky | Description |
|---------|-------------|
| [Azure-Search-reagovat – šablona](https://github.com/dereklegenzoff/azure-search-react-template) | Reakce šablony pro Azure Kognitivní hledání (github.com) |