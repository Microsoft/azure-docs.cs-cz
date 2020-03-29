---
title: Hledání přes objekty BLOB CSV
titleSuffix: Azure Cognitive Search
description: Extrahujte a importujte CSV z úložiště objektů Blob Azure pomocí režimu analýzy odděleného textu.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122317"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Jak indexovat objekty BLOB CSV pomocí režimu analýzy odděleného textu a indexerů objektů Blob v Azure Cognitive Search

Ve výchozím nastavení [indexer objektů blob Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) analyzuje objekty BLOB s omezeným přístupem jako jeden blok textu. U objektů BLOB obsahujících data CSV však často chcete považovat každý řádek v objektu blob za samostatný dokument. Například s ohledem na následující text oddělený, můžete jej analyzovat do dvou dokumentů, z nichž každý obsahuje "id", "datePublished" a "tagy" pole: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

V tomto článku se dozvíte, jak analyzovat objekty BLOB CSV pomocí indexeru objektů blob Azure Cognitive Search nastavením režimu `delimitedText` analýzy. 

> [!NOTE]
> Postupujte podle doporučení konfigurace indexeru v [indexování 1:N](search-howto-index-one-to-many-blobs.md) a vydejte se na výstupu více vyhledávacích dokumentů z jednoho objektu blob Azure.

## <a name="setting-up-csv-indexing"></a>Nastavení indexování CSV
Chcete-li indexovat objekty BLOB CSV, `delimitedText` vytvořte nebo aktualizujte definici indexeru s režimem analýzy v požadavku [vytvořit indexer:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`označuje, že první (neprázdný) řádek každého objektu blob obsahuje záhlaví.
Pokud objekty BLOB neobsahují počáteční řádek záhlaví, měly by být záhlaví zadána v konfiguraci indexeru: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Znak oddělovače můžete přizpůsobit `delimitedTextDelimiter` pomocí nastavení konfigurace. Například:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> V současné době je podporováno pouze kódování UTF-8. Pokud potřebujete podporu pro další kódování, hlasujte pro něj na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Při použití režimu oddělené analýzy textu Azure Cognitive Search předpokládá, že všechny objekty BLOB ve zdroji dat bude CSV. Pokud potřebujete podporovat kombinaci objektů BLOB CSV a nesouvisejících s CSV ve stejném zdroji dat, hlasujte pro něj na [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Příklady požadavků
Když to všechno dáme dohromady, zde jsou kompletní příklady užitečné hodu. 

Datasource: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Pomozte nám vylepšit Azure Cognitive Search
Pokud máte požadavky na funkce nebo nápady na vylepšení, zadejte svůj vstup na [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

