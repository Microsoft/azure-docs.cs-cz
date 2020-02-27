---
title: Odkazy na dokumentaci pro obohacení AI
titleSuffix: Azure Cognitive Search
description: Seznam článků, kurzů, ukázek a blogových příspěvků, které souvisejí s úlohami obohacení AI v Azure Kognitivní hledání, najdete v poznámce.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616197"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Materiály k dokumentaci pro rozšíření AI v Azure Kognitivní hledání

Rozšíření AI je schopnost indexování služby Azure Kognitivní hledání, která vyhledává latentní informace v netextových zdrojích a nerozlišeném textu, transformuje je na fulltextově prohledávatelný obsah ve službě Azure Kognitivní hledání.

Následující články jsou kompletní dokumentaci pro rozšíření AI.

## <a name="getting-started"></a>Začínáme
+ [Úvod do AI v Azure Kognitivní hledání](cognitive-search-concept-intro.md)
+ [Rychlý Start: vytvoření rozpoznávání dovednosti v Azure Portal](cognitive-search-quickstart-blob.md)
+ [Kurz: obohacení indexování pomocí AI](cognitive-search-tutorial-blob.md)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení AI](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Pokyny k návodům
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Odkazování na poznámky v sadě dovedností](cognitive-search-concept-annotations-syntax.md)
+ [Mapování polí na index](cognitive-search-output-field-mapping.md)
+ [Postup zpracování a extrakce informací z imagí](cognitive-search-concept-image-scenarios.md)
+ [Postup opětovného sestavení indexu služby Azure Kognitivní hledání](search-howto-reindex.md)
+ [Jak definovat vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referenční informace

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
  + [Microsoft. dovednosti. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. dovednosti. text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. dovednosti. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. dovednosti. text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. dovednosti. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. dovednosti. text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. dovednosti. text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. dovednosti. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. dovednosti. Vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. dovednosti. Vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. dovednosti. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. dovednosti. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. dovednosti. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Vlastní dovednosti
  + [Microsoft. dovednosti. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Zastaralé dovednosti](cognitive-search-skill-deprecated.md)
  + [Microsoft. dovednosti. text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create dovednosti (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Vytvoření indexeru (rozhraní API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Viz také

+ [REST API Kognitivní hledání Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Co je Azure Kognitivní hledání?](search-what-is-azure-search.md)
