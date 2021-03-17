---
title: Hledání v případě objektů BLOB ve formátu CSV
titleSuffix: Azure Cognitive Search
description: Extrahujte a importujte CSV z úložiště objektů BLOB v Azure pomocí režimu analýzy delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430475"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu CSV pomocí režimu analýzy delimitedText a indexerů objektů BLOB v Azure Kognitivní hledání

[Indexovací člen](search-howto-indexing-azure-blob-storage.md) služby Azure kognitivní hledání BLOB poskytuje `delimitedText` režim analýzy pro soubory CSV, které každý řádek ve sdíleném svazku clusteru považuje za samostatný dokument hledání. Například kvůli následujícímu textu oddělenému čárkami `delimitedText` by výsledkem bylo dva dokumenty v indexu hledání: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Bez `delimitedText` režimu analýzy bude celý obsah souboru CSV považován za jeden hledaný dokument.

Pokaždé, když vytváříte více vyhledávacích dokumentů z jednoho objektu blob, nezapomeňte si projít téma [indexování objektů BLOB a vytvoření více dokumentů hledání](search-howto-index-one-to-many-blobs.md) , abyste pochopili, jak přiřazení klíčů dokumentu fungují. Indexer objektů BLOB dokáže najít nebo vygenerovat hodnoty, které jedinečně definují každý nový dokument. Konkrétně může vytvořit přechodný `AzureSearch_DocumentKey` , který se vygeneroval při analýze objektu BLOB na menší části, kde se hodnota pak použije jako klíč vyhledávacího dokumentu v indexu.

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


