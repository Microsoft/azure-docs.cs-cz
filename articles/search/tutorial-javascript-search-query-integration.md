---
title: 'Kurz JavaScriptu: hledání v integraci – nejdůležitější'
titleSuffix: Azure Cognitive Search
description: Pochopení vyhledávacích dotazů sady JavaScript SDK používaných na webu s povoleným vyhledáváním
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726819"
---
# <a name="4---search-integration-highlights"></a>4. hledání v integraci – nejdůležitější

V předchozích lekcích jste přidali hledání do statické webové aplikace. V této lekci se zvýrazní základní kroky, které zavedou integraci. Pokud hledáte tahák list o tom, jak integrovat hledání do aplikace JavaScriptu, Tento článek vysvětluje, co potřebujete znát.

## <a name="azure-sdk-azuresearch-documents"></a>Sada Azure SDK @azure/search-documents 

Aplikace Function App používá sadu Azure SDK pro Kognitivní hledání:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Referenční dokumentace: [Klientská knihovna](/javascript/api/overview/azure/search-documents-readme)

Aplikace Function App se ověřuje prostřednictvím sady SDK pro cloudové Kognitivní hledání rozhraní API pomocí názvu prostředku, klíče prostředku a názvu indexu. Tajné kódy jsou uloženy v nastavení statické webové aplikace a jsou načteny do funkce jako proměnné prostředí. 

## <a name="configure-secrets-in-a-configuration-file"></a>Konfigurace tajných kódů v konfiguračním souboru

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Funkce Azure: hledání v katalogu

`Search` [Rozhraní API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) přebírá hledaný termín a hledá v dokumentech v indexu vyhledávání a vrátí seznam shod. 

Směrování pro rozhraní API pro hledání je obsažené v [function.jsu](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) vazeb.

Funkce Azure vrátí informace o konfiguraci hledání a splní dotaz.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Klient: hledání z katalogu

Zavolejte funkci Azure v klientovi s reakce pomocí následujícího kódu. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Funkce Azure Functions: návrhy z katalogu

`Suggest` [Rozhraní API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) přebírá hledaný termín, zatímco uživatel zadá a navrhuje hledané termíny, jako jsou tituly a autory v rámci dokumentů v indexu hledání, a vrátí malý seznam shod. 

Modul pro návrhy hledání `sg` je definován v [souboru schématu](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) používaném během hromadného nahrávání.

Směrování pro rozhraní API pro návrh je součástí [function.jsu](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) vazeb.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Klient: návrhy z katalogu

V aplikaci reakce se v rámci inicializace komponenty volá rozhraní API funkce naznačující návrh `\src\components\SearchBar\SearchBar.js` :

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Funkce Azure: načíst konkrétní dokument 

`Lookup` [Rozhraní API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) vezme ID a vrátí objekt dokumentu z indexu vyhledávání. 

Směrování pro vyhledávací rozhraní API je obsaženo v [function.jsu](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) vazeb.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Klient: načíst konkrétní dokument 

Rozhraní API funkce se volá v aplikaci reakce v `\src\pages\Details\Detail.js` rámci inicializace komponenty:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Další kroky

* [Indexování dat SQL Azure](search-indexer-tutorial.md)
