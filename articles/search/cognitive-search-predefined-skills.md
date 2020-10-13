---
title: Integrované zpracování textu a obrázků při indexování
titleSuffix: Azure Cognitive Search
description: 'Extrakce dat, přirozený jazyk, rozpoznávání obrazu zpracování obrázků: Přidání sémantiky a struktury do nezpracovaného obsahu v kanálu Azure Kognitivní hledání.'
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 417cc13c4a201a0dd86a846c347cb395b8d81c43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535521"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Integrované schopnosti rozpoznávání textu a zpracování obrázků při indexování (Azure Kognitivní hledání)

V tomto článku se dozvíte o dovednostech rozpoznávání poskytované s Azure Kognitivní hledání, které můžete zahrnout do dovednosti k extrakci obsahu a struktury. *Vnímání dovedností* je modul nebo operace, které transformují obsah nějakým způsobem. Často se jedná o komponentu, která extrahuje data nebo odvodí strukturu, a proto rozšiřuje naše znalosti vstupních dat. Téměř vždy je výstupem na základě textu. *Dovednosti* je kolekce dovedností, které definují kanál obohacení. 

> [!NOTE]
> Když rozbalíte rozsah zvýšením četnosti zpracování, přidáním dalších dokumentů nebo přidáním dalších algoritmů AI, budete muset [připojit fakturovatelné Cognitive Services prostředku](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).
>
> Funkce [přírůstkového navýšení (verze Preview)](cognitive-search-incremental-indexing-conceptual.md) vám umožní poskytnout mezipaměť, která umožňuje efektivnější použití indexeru při spouštění pouze těch dovedností, které jsou nezbytné v případě, že v budoucnu upravíte dovednosti a ušetříte čas a peníze.


## <a name="built-in-skills"></a>Integrované dovednosti

Několik dovedností je flexibilní v tom, co spotřebovávají nebo vyrábí. Obecně platí, že většina dovedností je založena na předem vyškolených modelech, což znamená, že model nemůžete pomocí vlastních školicích dat proškolit. Následující tabulka obsahuje výčet dovedností poskytovaných Microsoftem a popisuje je. 

| Tuhle | Description |
|-------|-------------|
|[Microsoft. dovednosti. text. CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Vyhledá text z vlastního uživatelsky definovaného seznamu slov a frází.|
| [Microsoft. dovednosti. text. KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Tato dovednost používá předvýukový model k detekci důležitých frází na základě pojmu umístění, lingvistických pravidel, blízkost k ostatním podmínkám a způsobu, jakým je neobvyklá doba v rámci zdrojových dat. |
| [Microsoft. dovednosti. text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Tato dovednost používá předvlakový model ke zjištění, který jazyk se používá (jedno ID jazyka na dokument). Je-li v rámci stejných segmentů použito více jazyků, je výstupem identifikátor LCID převládajícího používaného jazyka.|
| [Microsoft. dovednosti. text. MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliduje text z kolekce polí do jednoho pole.  |
| [Microsoft. dovednosti. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Tato dovednost používá předem provedený model pro vytváření entit pro pevnou sadu kategorií: lidé, umístění, organizace, e-maily, adresy URL, pole DateTime. |
| [Microsoft. dovednosti. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Tato dovednost používá předvlakový model k extrakci osobních údajů z daného textu. Tato dovednost také nabízí různé možnosti maskování zjištěných osobních informací v textu.  |
| [Microsoft. dovednosti. text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Tato dovednost používá předvlakový model ke zjištění kladné nebo záporné míněníy záznamu na základě záznamů. Skóre je mezi 0 a 1. V případě, že mínění nelze detekovat, a pro text, který je považován za neutrální, se vyskytnou neutrální skóre.  |
| [Microsoft. dovednosti. text. SplitSkill](cognitive-search-skill-textsplit.md) | Rozdělí text na stránky, aby bylo možné přírůstkově rozšířit nebo rozšířit obsah. |
| [Microsoft. dovednosti. text. TranslationSkill](cognitive-search-skill-text-translation.md) | Tato dovednost používá předvlakový model k překladu vstupního textu do celé řady jazyků pro normalizaci nebo použití lokalizace. |
| [Microsoft. dovednosti. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Tato dovednost používá algoritmus pro detekci obrázků k identifikaci obsahu obrázku a vygenerování popisného textu. |
| [Microsoft. dovednosti. Vision. OcrSkill](cognitive-search-skill-ocr.md) | Optické rozpoznávání znaků. |
| [Microsoft. dovednosti. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Povoluje filtrování, přiřazuje výchozí hodnotu a slučuje data na základě podmínky.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrahuje obsah ze souboru v rámci kanálu pro obohacení. |
| [Microsoft. dovednosti. util. ShaperSkill](cognitive-search-skill-shaper.md) | Mapuje výstup na komplexní typ (datový typ s více částmi, který může být použit pro celé jméno, víceřádkovou adresu nebo kombinaci příjmení a osobní identifikátor). |
| [Microsoft. dovednosti. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Umožňuje rozšiřitelnost kanálu rozšíření AI vytvořením volání HTTP do vlastního webového rozhraní API. |
| [Microsoft. dovednosti. Custom. AmlSkill](cognitive-search-aml-skill.md) | Umožňuje rozšiřitelnost kanálu rozšíření AI s modelem Azure Machine Learning. |


Pokyny k vytvoření [vlastní dovednosti](cognitive-search-custom-skill-web-api.md)najdete v tématu [jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md) a [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Viz také

+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Definice rozhraní vlastní dovednosti](cognitive-search-custom-skill-interface.md)
+ [Kurz: obohacení indexování pomocí AI](cognitive-search-tutorial-blob.md)
