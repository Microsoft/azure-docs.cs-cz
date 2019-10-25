---
title: Indexování objektů BLOB CSV pomocí indexeru Azure Kognitivní hledání BLOB
titleSuffix: Azure Cognitive Search
description: Procházení objektů BLOB ve formátu CSV v Azure Blob Storage pro fulltextové vyhledávání pomocí indexu služby Azure Kognitivní hledání. Indexery automatizují přijímání dat pro vybrané zdroje dat, jako je Azure Blob Storage.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18d0eb704deba80bf83b5cae0a598f47181700f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793785"
---
# <a name="how-to-index-csv-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Indexování objektů BLOB CSV pomocí indexeru objektů BLOB v Azure Kognitivní hledání 

> [!Note]
> Režim analýzy delimitedText je ve verzi Preview a není určený pro použití v produkčním prostředí. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>

Služba [Azure kognitivní hledání BLOB](search-howto-indexing-azure-blob-storage.md) ve výchozím nastavení analyzuje objekty BLOB s oddělovači textu jako jeden blok textu. Nicméně s objekty blob obsahujícími data CSV často chcete s každým řádkem v objektu BLOB zacházet jako se samostatným dokumentem. Například s ohledem na následující oddělený text můžete ho chtít analyzovat do dvou dokumentů, z nichž každý obsahuje pole ID, datePublished a Tags: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

V tomto článku se dozvíte, jak analyzovat objekty blob ve formátu CSV pomocí Azure Kognitivní hledání BLOB indexerby nastavení režimu analýzy `delimitedText`. 

> [!NOTE]
> Při indexování několika dokumentů hledání z jednoho objektu blob Azure postupujte podle doporučení pro konfiguraci indexeru v indexu [1: n](search-howto-index-one-to-many-blobs.md) .

## <a name="setting-up-csv-indexing"></a>Nastavení indexování CSV
Chcete-li indexovat objekty blob ve formátu CSV, vytvořte nebo aktualizujte definici indexeru pomocí režimu analýzy `delimitedText` v žádosti o [Vytvoření indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` označuje, že první (neprázdný) řádek každého objektu BLOB obsahuje hlavičky.
Pokud objekty blob neobsahují počáteční řádek záhlaví, hlavičky by se měly zadat v konfiguraci indexeru: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Pomocí nastavení konfigurace `delimitedTextDelimiter` můžete přizpůsobit znak oddělovače. Například:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> V současné době je podporována pouze kódování UTF-8. Pokud potřebujete podporu pro další kódování, Hlasujte pro ni na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Když použijete režim pro analýzu textu s oddělovači, Azure Kognitivní hledání předpokládá, že všechny objekty blob ve zdroji dat budou CSV. Pokud potřebujete podporovat kombinaci sdílených svazků clusteru CSV a jiných objektů BLOB ve stejném zdroji dat, Hlasujte prosím na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám zdokonalit Azure Kognitivní hledání
Pokud máte na vylepšení žádosti o funkce nebo nápady, poskytněte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

