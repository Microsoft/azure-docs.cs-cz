---
title: Kognitivní vyhledávání dokumentační zdroje – Azure Search
description: S poznámkami seznam článků, kurzy, ukázky a blogový příspěvky související s kognitivní vyhledávání ve službě Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 609b5d990cffce10733f6fc82e6b1032ad0f06bb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314597"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentační zdroje pro úlohy kognitivního vyhledávání

Kognitivní vyhledávání, nyní ve verzi public preview je novou vrstvu rozšíření v indexování Azure Search, která vyhledá latentní informace v jiné textové zdrojích a nediferencovanými text transformace na prohledávatelný obsah textu v plném znění ve službě Azure Search.

Následující články jsou kompletní dokumentaci k kognitivního vyhledávání.

## <a name="getting-started"></a>Začínáme
+ [Co je kognitivního vyhledávání?](cognitive-search-concept-intro.md)
+ [Rychlý start: Vyzkoušení kognitivního vyhledávání v portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Další rozhraní API pro kognitivní vyhledávání](cognitive-search-tutorial-blob.md)
+ [Příklad: vytvoření vlastní dovednosti](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Příručky s postupy
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Způsob vytvoření odkazu poznámky v dovedností](cognitive-search-concept-annotations-syntax.md)
+ [Způsob mapování polí do indexu](cognitive-search-output-field-mapping.md)
+ [Postupy zpracování a extrahování informací z obrázků](cognitive-search-concept-image-scenarios.md)
+ [Postup opětovné sestavení indexu Azure Search](search-howto-reindex.md)
+ [Definování rozhraní vlastních dovedností](cognitive-search-custom-skill-interface.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referenční informace

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Rozhraní REST API ve verzi Preview](search-api-2017-11-11-preview.md)
  + [Vytvoření dovedností (api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Vytvoření indexeru (rozhraní api-version = 2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Další informace najdete v tématech

+ [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Co je Azure Search?](search-what-is-azure-search.md)
