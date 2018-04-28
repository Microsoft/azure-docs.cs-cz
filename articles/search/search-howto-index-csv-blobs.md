---
title: Indexování CSV objekty BLOB se indexer objektu blob Azure Search | Microsoft Docs
description: Zjistěte, jak index CSV objektů BLOB s Azure Search
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: dfb1bd48a47e45363e8761a3d79901e5171b37d1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexování CSV objekty BLOB se indexer objektu blob Azure Search
Ve výchozím nastavení [indexer objektu blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyzuje oddělený text objekty BLOB jako jeden blok textu. Nicméně s objekty BLOB obsahující data ve formátu CSV, často chcete považovat každý řádek v objektu blob jako samostatný dokument. Například následující text oddělený uděleno: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

můžete k analýze do 2 dokumenty, každý obsahující pole "značky", "datePublished" a "id".

V tomto článku se dozvíte, jak analyzovat CSV objekty BLOB se indexer Azure Search objektů blob. 

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Je k dispozici pouze v rozhraní REST API pomocí verze **2015-02-28-Preview**. Prosím mějte na paměti, verzi preview rozhraní API jsou určené pro testování a vyhodnocení a neměl by se používat v produkčním prostředí. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Nastavení indexování sdíleného svazku clusteru
Index sdíleného svazku clusteru objekty BLOB, vytvořit nebo aktualizovat definici indexer s `delimitedText` analýza režimu:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Další informace o rozhraní API Indexer vytvořit, podívejte se na [vytvořit Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` Označuje, že první řádek (Neprázdné) každý objekt blob obsahuje záhlaví.
Pokud objekty BLOB neobsahují řádku s počáteční záhlaví, musí být zadán hlavičky v konfiguraci indexer: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Můžete přizpůsobit pomocí znak oddělovače `delimitedTextDelimiter` nastavení konfigurace. Příklad:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> V současné době je podporován pouze kódování UTF-8. Pokud potřebujete podporu pro jiné kódování, dejte nám vědět, na [našeho webu UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Pokud používáte oddělený text analýza režimu, Azure Search předpokládá, že všechny objekty BLOB ve zdroji dat bude sdílený svazek clusteru. Pokud potřebujete podporovat směs CSV a jiné než CSV objekty BLOB ve stejném datovém zdroji, dejte nám vědět, na [našeho webu UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Příklady požadavku
Vložení to všechny společně, zde jsou příklady úplnou datovou část. 

Zdroj dat: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Pomozte nám vylepšit Azure Search
Pokud máte žádosti o funkce nebo vylepšení nápady, kontaktujte nás na našem [UserVoice lokality](https://feedback.azure.com/forums/263029-azure-search/).

