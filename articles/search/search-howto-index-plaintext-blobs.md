---
title: Hledání v případě objektů BLOB ve formátu prostého textu
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte vyhledávacího indexer pro extrakci prostého textu z objektů blob Azure pro fulltextové vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533921"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu prostého textu v Azure Kognitivní hledání

Při použití [indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md) k extrakci prohledávatelných textů pro fulltextové vyhledávání můžete vyvolat různé režimy analýzy, abyste získali lepší indexování výsledků. Ve výchozím nastavení analyzuje objekt bloby s oddělovači textu jako jeden blok textu. Pokud ale všechny objekty blob obsahují prostý text ve stejném kódování, můžete významně zvýšit výkon při indexování pomocí **režimu analýzy textu**.

## <a name="set-up-plain-text-indexing"></a>Nastavení indexu prostého textu

Chcete-li indexovat objekty blob ve formátu prostého textu, vytvořte nebo aktualizujte definici indexeru s `parsingMode` vlastností Configuration na `text` žádost o [Vytvoření indexeru](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Ve výchozím nastavení se `UTF-8` předpokládá kódování. Chcete-li zadat jiné kódování, použijte `encoding` vlastnost konfigurace: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Příklad požadavku

V definici indexeru jsou zadány režimy analýzy.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání

Pokud máte na vylepšení žádosti o funkce nebo nápady, poskytněte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Pokud potřebujete pomoci s používáním stávající funkce, vystavte svůj dotaz na [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Další kroky

* [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
* [Jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md)
* [Přehled indexování objektů BLOB](search-blob-storage-integration.md)