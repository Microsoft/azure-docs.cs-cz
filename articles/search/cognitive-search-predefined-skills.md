---
title: Integrovaná extrakce dat, přirozený jazyk, zpracování obrázků – Azure Search
description: 'Extrakce dat, přirozený jazyk, rozpoznávání obrazu zpracování obrázků: Přidání sémantiky a struktury do nezpracovaného obsahu v kanálu Azure Search.'
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 08f4d94f1cd5afc9e626d4b774c3924b89cbbfad
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639129"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Předdefinované dovednosti pro rozšíření obsahu (Azure Search)

V tomto článku se dozvíte o dovednostech rozpoznávání poskytované s Azure Search. *Vnímání dovedností* je operace, která nějakým způsobem transformuje obsah. Často se jedná o komponentu, která extrahuje data nebo odvodí strukturu, a proto rozšiřuje naše znalosti vstupních dat. Téměř vždy je výstupem na základě textu. *Dovednosti* je kolekce dovedností, které definují kanál obohacení. 

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů v Azure Search. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci obrázků jsou popsány na [stránce s cenami Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Předdefinované dovednosti

Několik dovedností je flexibilní v tom, co spotřebovávají nebo vyrábí. Obecně platí, že většina dovedností je založena na předem vyškolených modelech, což znamená, že model nemůžete pomocí vlastních školicích dat proškolit. Následující tabulka obsahuje výčet dovedností poskytovaných Microsoftem a popisuje je. 

| Tuhle | Popis |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Tato dovednost používá předvýukový model k detekci důležitých frází na základě pojmu umístění, lingvistických pravidel, blízkost k ostatním podmínkám a způsobu, jakým je neobvyklá doba v rámci zdrojových dat. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Tato dovednost používá předvlakový model ke zjištění, který jazyk se používá (jedno ID jazyka na dokument). Je-li v rámci stejných segmentů použito více jazyků, je výstupem identifikátor LCID převládajícího používaného jazyka.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje text z kolekce polí do jednoho pole.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Tato dovednost používá předem provedený model pro vytváření entit pro pevnou sadu kategorií: lidé, umístění, organizace, e-maily, adresy URL, pole DateTime. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Tato dovednost používá předvlakový model ke zjištění kladné nebo záporné míněníy záznamu na základě záznamů. Skóre je mezi 0 a 1. V případě, že mínění nelze detekovat, a pro text, který je považován za neutrální, se vyskytnou neutrální skóre.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Rozdělí text na stránky, aby bylo možné přírůstkově rozšířit nebo rozšířit obsah. |
| [Microsoft. dovednosti. text. TranslationSkill](cognitive-search-skill-text-translation.md) | Tato dovednost používá předvlakový model k překladu vstupního textu do celé řady jazyků pro normalizaci nebo použití lokalizace. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Tato dovednost používá algoritmus pro detekci obrázků k identifikaci obsahu obrázku a vygenerování popisného textu. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optické rozpoznávání znaků. |
| [Microsoft. dovednosti. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Povoluje filtrování, přiřazuje výchozí hodnotu a slučuje data na základě podmínky.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mapuje výstup na komplexní typ (datový typ s více částmi, který může být použit pro celé jméno, víceřádkovou adresu nebo kombinaci příjmení a osobní identifikátor). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umožňuje rozšiřitelnost kanálu hledání rozpoznávání prostřednictvím volání HTTP do vlastního webového rozhraní API. |


Pokyny k vytvoření [vlastní dovednosti](cognitive-search-custom-skill-web-api.md)najdete v tématu [Definování vlastního rozhraní](cognitive-search-custom-skill-interface.md) a [příkladu: Vytvoření vlastní dovednosti pro hledání](cognitive-search-create-custom-skill-example.md)vnímání.

## <a name="see-also"></a>Viz také:

+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Definice rozhraní vlastní dovednosti](cognitive-search-custom-skill-interface.md)
+ [Kurz: Obohacené indexování s vyhledáváním rozpoznávání](cognitive-search-tutorial-blob.md)
