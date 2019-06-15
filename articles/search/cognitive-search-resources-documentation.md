---
title: Kognitivní vyhledávání dokumentační zdroje – Azure Search
description: S poznámkami seznam článků, kurzy, ukázky a blogový příspěvky související s kognitivní vyhledávání ve službě Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 225ac86efba8a32b085a5fb7fc41bed48c499eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65022169"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentační zdroje pro úlohy kognitivního vyhledávání

Kognitivní vyhledávání, teď všeobecně dostupná, je novou vrstvu rozšíření v indexování Azure Search, která vyhledá latentní informace v jiné textové zdrojích a nediferencovanými text transformace na prohledávatelný obsah textu v plném znění ve službě Azure Search.

Následující články jsou kompletní dokumentaci k kognitivního vyhledávání.

## <a name="getting-started"></a>Začínáme
+ [Co je kognitivního vyhledávání?](cognitive-search-concept-intro.md)
+ [Rychlé zprovoznění: Vyzkoušení kognitivního vyhledávání v portálu](cognitive-search-quickstart-blob.md)
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

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Create Skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Create Indexer (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Další informace najdete v tématech

+ [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexery ve službě Azure Search](search-indexer-overview.md)
+ [Co je Azure Search?](search-what-is-azure-search.md)
