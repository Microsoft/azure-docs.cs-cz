---
title: Co je nového ve službě
titleSuffix: Azure Cognitive Search
description: Oznámení nových a vylepšených funkcí, včetně přejmenování služby Azure Search do Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a2967ab40da8594b2d6372d5da816a6c76c9109
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721710"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nový název služby pro Azure Search

Azure Search se teď přejmenuje na **Azure kognitivní hledání** , aby odráželo rozšířené použití dovedností rozpoznávání a zpracování AI v základních operacích. I když vnímání dovedností přidávají nové funkce, je použití AI naprosto volitelné. Azure Kognitivní hledání bez AI můžete dál používat k vytváření bohatých fulltextových řešení pro vyhledávání v rámci privátního a heterogenního textového obsahu v indexu, který vytvoříte a spravujete v cloudu. 

Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Vaše stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="feature-announcements"></a>Oznámení funkcí

4\. listopadu 2019 – konference Ignite

+ [Přírůstkové indexování (Preview)](cognitive-search-incremental-indexing-conceptual.md) umožňuje zpracovat nebo znovu zpracovat pouze kroky, které jsou nezbytně nutné při provádění úprav kanálu rozšíření. To je užitečné hlavně v případě, že máte image s obsahem, který jste předtím analyzovali. Výstup drahých analýz je uložený a pak se používá jako základ pro další indexování nebo obohacení.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrakce dokumentu (ve verzi Preview)](cognitive-search-skill-document-extraction.md) je vnímání dovedností, která se používá při indexování, která umožňuje extrakci obsahu souboru v rámci dovednosti. Dříve došlo k odhalení dokumentu pouze před dovednosti prováděním. Při přidání této dovednosti můžete tuto operaci provést i v rámci provádění dovednosti.

+ [Překlad textu (ve verzi Preview)](cognitive-search-skill-text-translation.md) je vnímání dovedností použitou při indexování, která vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Šablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) můžou rychlé zprovoznění vaše vizualizace a analýzu obohaceného obsahu ve znalostní bázi Power BI Desktop. Tato šablona je určená pro projekce tabulky Azure vytvořené prostřednictvím [Průvodce importem dat](knowledge-store-create-portal.md).

+ V indexerech jsou nyní podporovány [Azure Data Lake Storage Gen2 (Preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)a [Cosmos DB rozhraní API Cassandra (Preview)](search-howto-index-cosmosdb.md) . Můžete se zaregistrovat pomocí [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po přijetí do programu Preview obdržíte e-mail s potvrzením.

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.