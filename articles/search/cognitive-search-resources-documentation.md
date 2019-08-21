---
title: Prostředky dokumentace pro hledání vnímání – Azure Search
description: Seznam článků, kurzů, ukázek a příspěvků s poznámkami, které souvisejí s úlohami vyhledávání rozpoznávání v Azure Search.
services: search
manager: nitinme
author: HeidiSteen
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: 88e9db6526733960045e9a7f49e1f73fcaca8cf6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639109"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Materiály k dokumentaci pro úlohy rozpoznávání vyhledávání

Vyhledávání, které je teď všeobecně dostupné, je nová vrstva rozšíření v Azure Search indexování, která vyhledává latentní informace v netextových zdrojích a nerozlišený text, transformuje je na fulltextový prohledávatelný obsah v Azure Search.

Následující články jsou kompletní dokumentaci k hledání rozpoznávání.

## <a name="getting-started"></a>Začínáme
+ [Co je rozpoznávání rozpoznávání?](cognitive-search-concept-intro.md)
+ [Rychlé zprovoznění: Vyzkoušet rozpoznávání rozpoznávání na portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Přečtěte si rozhraní API pro hledání vnímání](cognitive-search-tutorial-blob.md)
+ [Příklad: Vytvoření vlastní dovednosti pro hledání vnímání](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Pokyny k návodům
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Postup při odkazování na poznámky v dovednosti](cognitive-search-concept-annotations-syntax.md)
+ [Mapování polí na index](cognitive-search-output-field-mapping.md)
+ [Postup zpracování a extrakce informací z imagí](cognitive-search-concept-image-scenarios.md)
+ [Postup opětovného sestavení indexu Azure Search](search-howto-reindex.md)
+ [Jak definovat vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Reference

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
  + [Microsoft. dovednosti. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. dovednosti. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. dovednosti. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Vlastní dovednosti
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Zastaralé dovednosti](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create Skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Create Indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Viz také:

+ [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery v Azure Search](search-indexer-overview.md)
+ [Co je Azure Search?](search-what-is-azure-search.md)
