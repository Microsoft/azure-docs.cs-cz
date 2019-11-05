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
ms.openlocfilehash: e81eaf232e3234ac4de0cfb7412e23709f0c0b99
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549101"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nový název služby pro Azure Search

Azure Search se teď přejmenuje na **Azure kognitivní hledání** , aby odráželo rozšířené použití dovedností rozpoznávání a zpracování AI v základních operacích. I když vnímání dovedností přidávají nové funkce, je použití AI naprosto volitelné. Azure Kognitivní hledání bez AI můžete dál používat k vytváření bohatých fulltextových řešení pro vyhledávání v rámci privátního a heterogenního textového obsahu v indexu, který vytvoříte a spravujete v cloudu. 

Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Vaše stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="feature-announcements"></a>Oznámení funkcí

4\. listopadu 2019 – konference Ignite

+ [Přírůstkové indexování](cognitive-search-incremental-indexing-conceptual.md), nyní ve verzi Preview, umožňuje zpracovat nebo znovu zpracovat pouze kroky, které jsou nezbytně nutné při provádění úprav kanálu rozšíření. To je užitečné hlavně v případě, že máte image s obsahem, který jste předtím analyzovali. Výstup drahých analýz je uložený a pak se používá jako základ pro další indexování nebo obohacení.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrakce dokumentu](cognitive-search-skill-document-extraction.md) je vnímání dovedností, která se používá při indexování, která umožňuje extrakci obsahu souboru v rámci dovednosti. Dříve došlo k odhalení dokumentu pouze před dovednosti prováděním. Při přidání této dovednosti můžete tuto operaci provést i v rámci provádění dovednosti.

+ [Překlad textu](cognitive-search-skill-text-translation.md) je vnímání znalostí používané při indexování, které vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Šablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) můžou rychlé zprovoznění vaše vizualizace a analýzu obohaceného obsahu ve znalostní bázi Power BI Desktop. Tato šablona je určená pro projekce tabulky Azure vytvořené prostřednictvím [Průvodce importem dat](knowledge-store-create-portal.md).

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.