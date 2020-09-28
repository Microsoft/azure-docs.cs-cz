---
title: Hledání v případě objektů BLOB ve formátu CSV
titleSuffix: Azure Cognitive Search
description: Extrahujte a importujte CSV z úložiště objektů BLOB v Azure pomocí režimu analýzy delimitedText.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: f9c01e8e31e78c277a7a3ec1e5d8d0c32b58f8bc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403649"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu CSV pomocí režimu analýzy delimitedText a indexerů objektů BLOB v Azure Kognitivní hledání

Služba [Azure kognitivní hledání BLOB](search-howto-indexing-azure-blob-storage.md) ve výchozím nastavení analyzuje objekty BLOB s oddělovači textu jako jeden blok textu. Nicméně s objekty blob obsahujícími data CSV často chcete s každým řádkem v objektu BLOB zacházet jako se samostatným dokumentem. Například s ohledem na následující oddělený text můžete ho chtít analyzovat do dvou dokumentů, z nichž každý obsahuje pole ID, datePublished a Tags: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

V tomto článku se dozvíte, jak analyzovat objekty blob ve formátu CSV pomocí indexeru objektů BLOB v Azure Kognitivní hledání nastavením `delimitedText` režimu analýzy. 

> [!NOTE]
> Při indexování několika dokumentů hledání z jednoho objektu blob Azure postupujte podle doporučení pro konfiguraci indexeru v indexu [1: n](search-howto-index-one-to-many-blobs.md) .

## <a name="setting-up-csv-indexing"></a>Nastavení indexování CSV
Chcete-li indexovat objekty blob ve formátu CSV, vytvořte nebo aktualizujte definici indexeru s `delimitedText` režimem analýzy u žádosti [vytvořit indexer](/rest/api/searchservice/create-indexer) :

```http
    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }
```

`firstLineContainsHeaders` označuje, že první (neprázdný) řádek každého objektu BLOB obsahuje hlavičky.
Pokud objekty blob neobsahují počáteční řádek záhlaví, hlavičky by se měly zadat v konfiguraci indexeru: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Pomocí nastavení konfigurace můžete změnit znak oddělovače `delimitedTextDelimiter` . Příklad:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> V současné době je podporována pouze kódování UTF-8. Pokud potřebujete podporu pro další kódování, Hlasujte pro ni na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Když použijete režim pro analýzu textu s oddělovači, Azure Kognitivní hledání předpokládá, že všechny objekty blob ve zdroji dat budou CSV. Pokud potřebujete podporovat kombinaci sdílených svazků clusteru CSV a jiných objektů BLOB ve stejném zdroji dat, Hlasujte prosím na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Příklady požadavků
Zde jsou uvedeny kompletní příklady datové části. 

Datového 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   
```

Indexer

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání
Pokud máte na vylepšení žádosti o funkce nebo nápady, poskytněte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Pokud potřebujete pomoci s používáním stávající funkce, vystavte svůj dotaz na [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).