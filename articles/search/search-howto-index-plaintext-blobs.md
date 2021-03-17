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
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509448"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Indexování objektů BLOB ve formátu prostého textu v Azure Kognitivní hledání

Při použití [indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md) k extrakci vyhledávaného textu objektu BLOB pro fulltextové vyhledávání můžete přiřadit režim analýzy, abyste získali lepší indexování výsledků. Ve výchozím nastavení indexer analyzuje obsah objektu BLOB jako jeden blok textu. Pokud ale všechny objekty blob obsahují prostý text ve stejném kódování, můžete významně zvýšit výkon při indexování pomocí `text` režimu analýzy.

Mezi doporučení k `text` analýze použití patří:

+ Typ souboru je. txt.
+ Soubory jsou libovolného typu, ale samotný obsah je text (například zdrojový kód programu, HTML, XML a tak dále). Pro soubory v jazyce označení se jako statický text použijí všechny znaky syntaxe.

Odvolat všechny indexery, které jsou serializovány do formátu JSON. Ve výchozím nastavení bude obsah celého textového souboru indexován v jednom velkém poli jako `"content": "<file-contents>"` . Jakékoli nové řádky a návratové instrukce jsou vložené do pole obsah a vyjádřené jako `\r\n\` .

Pokud chcete podrobnější výsledek a pokud je typ souboru kompatibilní, vezměte v úvahu následující řešení:

+ [`delimitedText`](search-howto-index-csv-blobs.md) Režim analýzy, pokud je zdrojem CSV
+ [ `jsonArray` nebo `jsonLines` ](search-howto-index-json-blobs.md), pokud je zdrojem JSON

Třetí možnost pro rozdělení obsahu do více částí vyžaduje rozšířené funkce ve formě [rozšíření AI](cognitive-search-concept-intro.md). Přidává analýzu, která identifikuje a přiřazuje bloky souboru do různých vyhledávacích polí. Můžete najít úplné nebo částečné řešení prostřednictvím [integrovaných dovedností](cognitive-search-predefined-skills.md), ale pravděpodobnější řešení by znamenalo model učení, který pochopení vašeho obsahu spolupracuje s vaším obsahem, který je ve vlastním výukovém modelu zabalený do [vlastní dovednosti](cognitive-search-custom-skill-interface.md).

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