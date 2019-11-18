---
title: Hledání v případě objektů BLOB ve formátu CSV
titleSuffix: Azure Cognitive Search
description: Extrahujte a importujte CSV z úložiště objektů BLOB v Azure pomocí režimu analýzy delimitedText, který je aktuálně ve verzi Public Preview.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113033"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu CSV pomocí režimu analýzy delimitedText a indexerů objektů BLOB v Azure Kognitivní hledání 

> [!IMPORTANT] 
> Režim analýzy delimitedText je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Služba [Azure kognitivní hledání BLOB](search-howto-indexing-azure-blob-storage.md) ve výchozím nastavení analyzuje objekty BLOB s oddělovači textu jako jeden blok textu. Nicméně s objekty blob obsahujícími data CSV často chcete s každým řádkem v objektu BLOB zacházet jako se samostatným dokumentem. Například s ohledem na následující oddělený text můžete ho chtít analyzovat do dvou dokumentů, z nichž každý obsahuje pole ID, datePublished a Tags: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

V tomto článku se dozvíte, jak analyzovat objekty blob ve formátu CSV pomocí indexeru objektů BLOB v Azure Kognitivní hledání nastavením režimu analýzy `delimitedText`. 

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

Pomocí nastavení konfigurace `delimitedTextDelimiter` můžete přizpůsobit znak oddělovače. Příklad:

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

