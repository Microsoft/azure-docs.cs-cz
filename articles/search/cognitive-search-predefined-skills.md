---
title: Předdefinované datové extrakce, přirozeného jazyka obrázků zpracování – Azure Search
description: Extrakce dat, přirozený jazyk a kognitivní dovednosti pro zpracování obrázků nezpracované obsahu v kanálu služby Azure Search přidat sémantiku a struktura.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 8f87cb68aeea8b953b9883e92fd5080569c6cdd0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203687"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Předdefinované dovednosti pro rozšiřování obsahu (Azure Search)

V tomto článku se dozvíte o kognitivní dovednosti s Azure Search. A *kognitivní dovednosti* je operace, která transformuje obsah nějakým způsobem. Často je součást, která extrahuje data nebo struktura odvodí a proto argumentech naše Principy vstupní data. Výstup je téměř vždy založený na textu. A *dovednosti* je kolekce dovednosti, které definují rozšíření kanálu. 

> [!NOTE]
> Od 21. prosince 2018 se budou moct přidružit dovednosti Azure Search prostředku služeb Cognitive Services. To vám umožní spouštění poplatků za využití jeho dovedností. K tomuto datu také začneme pro extrakci image jako součást fáze hádání dokumentu. Extrakce textu z dokumentů se bude dál nabízet bez dalších poplatků.
>
> Provádění předdefinované dovednosti budou účtovat stávající [přejít ceny služeb Cognitive Services, platit jako můžete](https://azure.microsoft.com/pricing/details/cognitive-services/) . Ceny za extrakce Image se bude účtovat ceny verze preview a je popsaný na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Přečtěte si [Další](cognitive-search-attach-cognitive-services.md).

## <a name="predefined-skills"></a>Předdefinované dovednosti

Několik dovednosti jsou flexibilní v co se používají, nebo vytvořit. Obecně platí většina dovednosti podle předem natrénovaných modelů, které znamená, že nelze trénování modelu s použitím cvičných dat. Pokyny k vytváření vlastních dovedností, naleznete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md) a [příklad: vytváření vlastních dovedností](cognitive-search-create-custom-skill-example.md). Následující tabulka uvádí a popisuje dovednosti od Microsoftu. 

| Dovednosti | Popis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Tento dovednosti používá pretrained model rozpoznat důležité frází na základě umístění termín, jazykovou pravidel, blízkosti jiné podmínky a jak neobvyklé termín je v rámci zdrojová data. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Používá tento dovednosti pretrained model rozpoznat jazyk, který je použit (jedno ID jazyka v jednom dokumentu). V případě více jazyků používají v rámci stejné segmenty text, výstup je LCID převážně používá jazyk.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliduje text z kolekce polí do jednoho pole.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Tato dovednosti pretrained modelu používá k vytvoření entity pro pevnou sadu kategorií: uživatelé, umístění, organizaci, e-mailem, adresy URL. pole data a času. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Tento dovednosti pomocí pretrained modelu na základě záznamu podle skóre mínění kladné nebo záporné. Je skóre mezi 0 a 1. Neutrální skóre dojde null případu nelze zjistit mínění, a pro text, který je považován za neutrální.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Rozdělí text na stránky, takže můžete rozšiřovat nebo upravte obsah postupně. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Tato dovednosti používá algoritmus detekce bitové kopie k identifikaci obsahu obrázku a vygenerovat textový popis. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optické rozpoznávání znaků. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Výstup mapy na komplexní typ (vícedílný datový typ, který může být použit k úplný název, adresu Víceřádkový nebo kombinaci příjmení a osobních identifikátorů.) |

## <a name="see-also"></a>Další informace najdete v tématech

+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Definice vlastního rozhraní dovednosti](cognitive-search-custom-skill-interface.md)
+ [Kurz: Obohacené indexování s využitím kognitivního vyhledávání](cognitive-search-tutorial-blob.md)