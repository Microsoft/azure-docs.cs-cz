---
title: Předdefinované extrakce dat, přirozeného jazyka, bitové kopie zpracování dovednosti (Azure Search) | Microsoft Docs
description: Extrakce dat, přirozeného jazyka image zpracování kognitivní dovednosti přidat sémantiku a struktura k nezpracované obsahu v kanálu vyhledávací Azure.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790955"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Předdefinované dovednosti pro obohacení obsahu (Azure Search)

V tomto článku informace o kognitivní dovedností součástí kognitivní vyhledávání. A *kognitivní odborností* je operace, která transformuje obsah nějakým způsobem. Často je komponenty, která extrahuje data nebo odvodí struktura a proto rozšiřuje naše Principy vstupní data. Výstup téměř vždy je založený na textu. A *skillset* kolekce znalosti, které definují obohacení kanálu. 

## <a name="predefined-skills"></a>Předdefinované dovednosti

Několik dovednosti jsou flexibilní v co budou využívat, nebo vytvořit. Obecně platí většina dovednosti podle předem trénované modely, které znamená, že nelze trénování modelu s použitím vlastní Cvičná data. Pokyny týkající se vytvoření vlastního odborností najdete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md) a [příklad: vytvoření vlastní odborností](cognitive-search-create-custom-skill-example.md). Následující tabulka uvádí a popisuje znalosti společnosti Microsoft. 

| Dovednosti | Popis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Tato odborností používá pretrained model rozpoznat důležité frází na základě umístění termín, lingvistické pravidla, blízkosti jiné podmínky a jak neobvyklou termín je v rámci zdrojová data. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Toto využívá odborností, pretrained modelu k detekci jazyk, který se používá (jeden ID jazyka na dokument). V případě více jazyků používají v rámci stejné segmenty text, je výstup LCID převážně použité jazyka.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliduje text z kolekce pole do jednoho pole.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Tato odborností používá pretrained model k navázání entity pro pevnou sadu kategorií: uživatelé, umístění, organizace. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Tato odborností používá pretrained model skóre na základě záznamu podle postojích kladné a záporné. Skóre je mezi 0 a 1. Neutrální skóre dojít null případu, když nelze nalézt postojích a pro text, který považuje neutrální.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Rozdělí text na stránky, abyste mohli zlepšit komunikaci oddělení nebo posílení obsah postupně. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Tento odborností používá algoritmus detekce bitovou kopii k identifikaci obsahu bitové kopie a generovat textový popis. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optické rozpoznávání znaků. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Výstup mapy do komplexního typu (více částmi datového typu, který může být použit pro jméno a příjmení, adresu Víceřádkový nebo kombinaci příjmení a identifikátor osobní.) |

## <a name="see-also"></a>Další informace najdete v tématech

+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Vlastní definice rozhraní dovednosti](cognitive-search-custom-skill-interface.md)
+ [Kurz: Obohacená známým indexování s kognitivní search](cognitive-search-tutorial-blob.md)