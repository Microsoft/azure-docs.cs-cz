---
title: Integrované zpracování textu a obrazu během indexování
titleSuffix: Azure Cognitive Search
description: Extrakce dat, přirozený jazyk, kognitivní dovednosti zpracování obrázků přidávají sémantiku a strukturu do nezpracovaného obsahu v kanálu Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e1f62dcdb122726fc1c08b7bea4e4c214ce7906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933368"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Integrované kognitivní dovednosti pro zpracování textu a obrazu během indexování (Azure Cognitive Search)

V tomto článku se dozvíte o kognitivní dovednosti poskytované s Azure Cognitive Search, které můžete zahrnout do skillset extrahovat obsah a strukturu. *Kognitivní dovednost* je modul nebo operace, která nějakým způsobem transformuje obsah. Často se jedná o součást, která extrahuje data nebo odvodí strukturu, a proto rozšiřuje naše chápání vstupních dat. Téměř vždy je výstup založen na textu. *Skillset* je kolekce dovedností, které definují kanál obohacení. 

> [!NOTE]
> Při rozšiřování oboru zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI budete muset [připojit fakturovatelný prostředek služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Poplatky narůstají při volání API ve službách Cognitive Services a pro extrakci image jako součást fáze prolomení dokumentů v Azure Cognitive Search. Za extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Provádění vestavěných dovedností se účtuje za stávající [cenu průběžných plateb služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsané na [stránce s cenami Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Integrované dovednosti

Několik dovedností je flexibilní v tom, co konzumují nebo produkují. Obecně platí, že většina dovedností je založena na předem trénovaných modelech, což znamená, že model nelze trénovat pomocí vlastních trénovacích dat. Následující tabulka obsahuje výčet a popisuje dovednosti poskytované společností Microsoft. 

| Dovednost | Popis |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Vyhledá text z vlastního, uživatelem definovaného seznamu slov a frází.|
| [Microsoft.Skills.Text.KeyphraseSkill](cognitive-search-skill-keyphrases.md) | Tato dovednost používá předem trénovaný model k detekci důležitých frází na základě umístění termínu, jazykových pravidel, blízkosti k jiným termínům a neobvyklému termínu ve zdrojových datech. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Tato dovednost používá předem trénovaný model ke zjištění, který jazyk se používá (jedno ID jazyka na dokument). Pokud je v rámci stejných textových segmentů použito více jazyků, výstupem je LCID převážně používaného jazyka.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Sloučí text ze sbírky polí do jednoho pole.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Tato dovednost používá předem trénovaný model k vytvoření entit pro pevnou sadu kategorií: osoby, umístění, organizace, e-maily, adresy URL, pole datetime. |
| [Microsoft.skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Tato dovednost používá předem trénovaný model k extrahování osobně identifikovatelných informací z daného textu. Dovednost také poskytuje různé možnosti maskování zjištěných entit PII v textu.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Tato dovednost používá předem trénovaný model k skóre kladné nebo záporné mínění na základě záznamu. Skóre je mezi 0 a 1. Neutrální skóre dojít pro oba nula případu, kdy nelze zjistit mínění a pro text, který je považován za neutrální.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Rozdělí text na stránky, abyste mohli postupně obohacovat nebo rozšiřovat obsah. |
| [Microsoft.skills.Text.PřekladSkill](cognitive-search-skill-text-translation.md) | Tato dovednost používá předem trénovaný model přeložit vstupní text do různých jazyků pro případy normalizace nebo lokalizace použití. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Tato dovednost používá algoritmus detekce obrazu k identifikaci obsahu obrazu a generování textového popisu. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optické rozpoznávání znaků. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Umožňuje filtrování, přiřazení výchozí hodnoty a slučování dat na základě podmínky.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrahuje obsah ze souboru v rámci kanálu obohacení. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapuje výstup na komplexní typ (vícedílný datový typ, který může být použit pro celé jméno, víceřádkovou adresu nebo kombinaci příjmení a osobního identifikátoru.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umožňuje rozšiřitelnost kanálu obohacení AI provedením volání HTTP do vlastního webového rozhraní API. |


Pokyny k vytvoření [vlastní dovednosti](cognitive-search-custom-skill-web-api.md)naleznete v [tématu Jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md) a [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou ai](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Viz také

+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Definice rozhraní vlastních dovedností](cognitive-search-custom-skill-interface.md)
+ [Kurz: Obohacené indexování s umělou ai](cognitive-search-tutorial-blob.md)
