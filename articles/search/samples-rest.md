---
title: Ukázky REST
titleSuffix: Azure Cognitive Search
description: Najděte ukázky kódu REST pro Azure Kognitivní hledání, které používají rozhraní REST API pro vyhledávání nebo správu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956487"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Ukázky kódu REST pro Azure Kognitivní hledání

Přečtěte si o ukázkách REST API, které předvádějí funkce a pracovní postup řešení Azure Kognitivní hledání. Tyto ukázky používají [**rozhraní REST API pro hledání**](/rest/api/searchservice).

REST je konečné programovací rozhraní pro Azure Kognitivní hledání a všechny operace, které lze vyvolat programově, jsou k dispozici jako první v REST a potom v sadách SDK. Z tohoto důvodu se většina příkladů v dokumentaci využívá rozhraní REST API k předvedení nebo vysvětlení důležitých konceptů.

Ukázky REST jsou obvykle vyvíjeny a testovány na post, ale můžete použít libovolného klienta, který podporuje volání HTTP:

+ Začínáme s [rychlým startem: vytvoření indexu služby Azure kognitivní hledání pomocí rozhraní REST API](search-get-started-rest.md) , které vám pomůžou při formulování volání http.
+ Pokud pracujete v Visual Studio Code, vyzkoušejte [Visual Studio Code rozšíření pro Azure kognitivní hledání](search-get-started-vs-code.md), který je aktuálně ve verzi Preview.

## <a name="doc-samples"></a>Ukázky dokumentů

Ukázky kódu z týmu Kognitivní hledání ukazují funkce a pracovní postupy. Mnohé z těchto ukázek jsou odkazovány v kurzech, rychlých startech a článcích s postupy. Tyto ukázky najdete v [**Azure-Samples/Azure-Search-post-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) na GitHubu.

| ukázky | Článek |
|---------|---------|
| [Rychlý start](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Zdrojový kód pro [rychlý Start: vytvoření indexu vyhledávání pomocí rozhraní REST API](search-get-started-rest.md). Tento článek popisuje základní pracovní postup pro vytvoření, načtení a dotazování indexu vyhledávání pomocí ukázkových dat. |
| [Kurz](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Zdrojový kód pro [kurz: pomocí Rest a AI můžete vygenerovat prohledávatelný obsah z objektů blob Azure](cognitive-search-tutorial-blob.md). V tomto článku se dozvíte, jak vytvořit dovednosti, který prochází přes objekty blob Azure za účelem extrakce informací a odvození struktury.|
| [Ladění – relace](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Zdrojový kód pro [kurz: Diagnostika, oprava a potvrzení změn ve vašem dovednosti](cognitive-search-tutorial-debug-sessions.md). V tomto článku se dozvíte, jak používat dovednosti relaci ladění v Azure Portal. REST se používá k vytvoření objektů používaných při ladění.|
| [vlastní analyzátory](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Zdrojový kód pro [kurz: Vytvoření vlastního analyzátoru pro telefonní čísla](tutorial-create-custom-analyzer.md). Tento článek vysvětluje, jak používat analyzátory k zachování vzorů a speciálních znaků v prohledávatelných obsahu.|
| [znalostní báze Knowledge Store](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Zdrojový kód pro [vytvoření znalostní báze s využitím Rest a následného](knowledge-store-create-rest.md). Tento článek popisuje nezbytné kroky pro naplnění znalostní báze používaného pro pracovní postupy dolování znalostí. |
| [projekce](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Zdrojový kód pro [způsob, jakým se naformují obohacení a export](knowledge-store-projections-examples.md). Tento článek vysvětluje, jak zadat fyzické datové struktury ve znalostní bázi Knowledge Store.|
| [indexově šifrované – objekty blob](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Zdrojový kód pro [indexování šifrovaných objektů BLOB pomocí indexerů objektů BLOB a dovednosti](search-howto-index-encrypted-blobs.md). Tento článek popisuje, jak indexovat dokumenty v úložišti objektů BLOB v Azure, které byly dříve zašifrované pomocí Azure Key Vault. |

> [!Tip]
> Vyzkoušejte si [prohlížeč ukázek](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) , kde najdete ukázky kódu Microsoftu v GitHubu, filtrované podle produktu, služby a jazyka.

## <a name="other-samples"></a>Další ukázky

Následující ukázky jsou publikovány také týmem Kognitivní hledání, ale nejsou odkazovány v dokumentaci. Přidružené soubory Readme poskytují pokyny k používání.

| ukázky | Description |
|---------|-------------|
| [Dotaz – příklady](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Vyúčtování kolekcí prokázaly různé techniky dotazů, včetně hledání přibližných výsledků, regulárního výrazu a hledání zástupných znaků, automatického dokončování atd. |