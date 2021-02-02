---
title: Hledání v případě objektů BLOB ve formátu prostého textu
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte vyhledávacího indexer pro extrakci prostého textu z objektů blob Azure pro fulltextové vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430424"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu prostého textu v Azure Kognitivní hledání

Při použití [indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md) k extrakci prohledávatelných textů pro fulltextové vyhledávání můžete vyvolat různé režimy analýzy, abyste získali lepší indexování výsledků. Ve výchozím nastavení indexer analyzuje obsah objektu BLOB jako jeden blok textu. Pokud ale všechny objekty blob obsahují prostý text ve stejném kódování, můžete významně zvýšit výkon při indexování pomocí `text` režimu analýzy.

Režim analýzy byste měli použít, `text` když:

+ Typ souboru je. txt.
+ Soubory jsou libovolného typu, ale samotný obsah je text (například zdrojový kód programu, HTML, XML a tak dále). Pro soubory v jazyce označení se jako statický text použijí všechny znaky syntaxe.

Odvolá indexery, které jsou serializovány do formátu JSON. Obsah celého textového souboru bude indexován v jednom velkém poli jako `"content": "<file-contents>"` . Nové řádky a návratové instrukce se vyjadřují jako `\r\n\` .

Pokud chcete podrobnější výsledek, vezměte v úvahu následující řešení:

+ [`delimitedText`](search-howto-index-csv-blobs.md) Režim analýzy, pokud je zdrojem CSV
+ [ `jsonArray` nebo `jsonLines` ](search-howto-index-json-blobs.md), pokud je zdrojem JSON

Třetí možnost pro poškození obsahu ve více částech vyžaduje rozšířené funkce ve formě [rozšíření AI](cognitive-search-concept-intro.md). Přidává analýzu, která identifikuje a přiřazuje bloky souboru do různých vyhledávacích polí. Můžete najít úplné nebo částečné řešení prostřednictvím [integrovaných dovedností](cognitive-search-predefined-skills.md), ale pravděpodobnější řešení by znamenalo model učení, který pochopení vašeho obsahu spolupracuje s vaším obsahem, který je ve vlastním výukovém modelu zabalený do [vlastní dovednosti](cognitive-search-custom-skill-interface.md).

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

## <a name="next-steps"></a>Další kroky

+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Přehled indexování objektů BLOB](search-blob-storage-integration.md)