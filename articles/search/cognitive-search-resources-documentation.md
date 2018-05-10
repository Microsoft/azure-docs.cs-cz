---
title: Zdroje informací k dokumentaci kognitivní vyhledávání (Azure Search) | Microsoft Docs
description: S poznámkami seznam článků, kurzy, ukázky a blog odešle úlohy související s kognitivní vyhledávání ve službě Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: a96b66f61b19d218c5708a0ce967e0033ba26627
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Zdroje informací k dokumentaci pro úlohy kognitivní vyhledávání

Kognitivní hledání, nyní ve verzi public preview je novou vrstvu obohacení v indexu Azure Search, vyhledá latentní informace v jiné textové zdroje a poskytujících blíže neurčené text, převod do textu v plném znění s možností vyhledávání obsahu ve službě Azure Search.

V následujících článcích jsou kompletní dokumentaci k kognitivní vyhledávání.

## <a name="getting-started"></a>Začínáme
+ [Co je kognitivní hledání?](cognitive-search-concept-intro.md)
+ [Rychlý úvod: Zkuste kognitivní vyhledávání v portálu](cognitive-search-quickstart-blob.md)
+ [Kurz: Další kognitivní hledání rozhraní API](cognitive-search-tutorial-blob.md)
+ [Příklad: vytvoření vlastní dovedností](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Postupy: pokyny
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Jak odkazovat poznámky v skillset](cognitive-search-concept-annotations-syntax.md)
+ [Mapování polí do indexu](cognitive-search-output-field-mapping.md)
+ [Jak zpracovat a extrahovat informace z bitové kopie](cognitive-search-concept-image-scenarios.md)
+ [Postup opětovné sestavení indexu Azure Search](search-howto-reindex.md)
+ [Jak definovat vlastní dovednosti rozhraní](cognitive-search-custom-skill-interface.md)
+ [Rady pro řešení potíží](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referenční informace

+ [Předdefinované dovednosti](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Rozhraní API REST Preview](search-api-2017-11-11-preview.md)
  + [Vytvoření Skillset (api-version = 2017-11-11-Preview)](ref-create-skillset.md)
  + [Vytvoření Indexer (api-version = 2017-11-11-Preview)](ref-create-indexer.md)

## <a name="see-also"></a>Další informace najdete v tématech

+ [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Co je Azure Search?](search-what-is-azure-search.md)