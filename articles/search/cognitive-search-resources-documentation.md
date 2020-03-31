---
title: Odkazy na dokumentaci pro obohacení umělou ai.
titleSuffix: Azure Cognitive Search
description: Anotovaný seznam článků, kurzů, ukázek a příspěvků blogu souvisejících s úlohami obohacení umělá ai v Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616197"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Materiály dokumentace pro obohacení ai v Azure Cognitive Search

Obohacení umělou ai je možnost indexování Azure Cognitive Search, která vyhledá latentní informace v netextových zdrojích a nediferencovaném textu a transformuje je na fulltextový prohledávatelný obsah v Azure Cognitive Search.

Následující články jsou kompletní dokumentace pro obohacení AI.

## <a name="getting-started"></a>Začínáme
+ [Úvod k umělá ai v Azure Kognitivní vyhledávání](cognitive-search-concept-intro.md)
+ [Úvodní příručka: Vytvoření kognitivních dovedností na webu Azure Portal](cognitive-search-quickstart-blob.md)
+ [Kurz: Obohacené indexování s umělou ai](cognitive-search-tutorial-blob.md)
+ [Příklad: Vytvoření vlastní dovednosti pro obohacení umělou aicí](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Návody
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Odkazování na poznámky v sadě dovedností](cognitive-search-concept-annotations-syntax.md)
+ [Jak mapovat pole na index](cognitive-search-output-field-mapping.md)
+ [Jak zpracovat a extrahovat informace z obrázků](cognitive-search-concept-image-scenarios.md)
+ [Jak znovu sestavit index Azure Cognitive Search](search-howto-reindex.md)
+ [Jak definovat vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Odkaz

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.skills.Text.PřekladSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Vlastní dovednosti
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Zastaralé dovednosti](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [ROZHRANÍ API PRO ODPOČINEK](https://docs.microsoft.com/rest/api/searchservice/)
  + [Vytvořit skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Vytvořit indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Viz také

+ [Rozhraní REST API služby Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Co je kognitivní hledání Azure?](search-what-is-azure-search.md)
