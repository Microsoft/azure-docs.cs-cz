---
title: Indexování objektů BLOB CSV s Azure Search indexerem objektů blob – Azure Search
description: Procházení objektů BLOB ve formátu CSV v Azure Blob Storage pro fulltextové vyhledávání pomocí Azure Searchho indexu. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Blob Storage.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656756"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexování objektů BLOB CSV s Azure Search indexerem objektů BLOB

> [!Note]
> Režim analýzy delimitedText je ve verzi Preview a není určený pro použití v produkčním prostředí. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Ve výchozím nastavení [Azure Search indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md) analyzuje objekty BLOB s oddělovači textu jako jeden blok textu. Nicméně s objekty blob obsahujícími data CSV často chcete s každým řádkem v objektu BLOB zacházet jako se samostatným dokumentem. Například s ohledem na následující oddělený text můžete ho chtít analyzovat do dvou dokumentů, z nichž každý obsahuje pole ID, datePublished a Tags: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

V tomto článku se dozvíte, jak analyzovat objekty blobů sdíleného svazku clusteru pomocí Azure Search objektů BLOB `delimitedText` indexerby nastavení režimu analýzy. 

> [!NOTE]
> Při indexování několika dokumentů hledání z jednoho objektu blob Azure postupujte podle doporučení pro konfiguraci indexeru v indexu [1: n](search-howto-index-one-to-many-blobs.md) .

## <a name="setting-up-csv-indexing"></a>Nastavení indexování CSV
Chcete-li indexovat objekty blob ve formátu CSV, vytvořte nebo aktualizujte definici indexeru s `delimitedText` režimem analýzy u žádosti [vytvořit indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`označuje, že první (neprázdný) řádek každého objektu BLOB obsahuje hlavičky.
Pokud objekty blob neobsahují počáteční řádek záhlaví, hlavičky by se měly zadat v konfiguraci indexeru: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Pomocí `delimitedTextDelimiter` nastavení konfigurace můžete změnit znak oddělovače. Příklad:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> V současné době je podporována pouze kódování UTF-8. Pokud potřebujete podporu pro další kódování, Hlasujte pro ni na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Když použijete režim pro analýzu textu s oddělovači, Azure Search předpokládá, že všechny objekty blob ve zdroji dat budou CSV. Pokud potřebujete podporovat kombinaci sdílených svazků clusteru CSV a jiných objektů BLOB ve stejném zdroji dat, Hlasujte prosím na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Příklady požadavků
Zde jsou uvedeny kompletní příklady datové části. 

Datového 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomozte nám zajistit Azure Search lepší
Pokud máte na vylepšení žádosti o funkce nebo nápady, poskytněte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

