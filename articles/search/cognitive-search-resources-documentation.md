---
title: Odkazy na dokumentaci pro obohacení AI
titleSuffix: Azure Cognitive Search
description: Seznam článků, kurzů, ukázek a blogových příspěvků, které souvisejí s úlohami obohacení AI v Azure Kognitivní hledání, najdete v poznámce.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935343"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Materiály k dokumentaci pro rozšíření AI v Azure Kognitivní hledání

Rozšíření AI je doplněk pro indexování založené na indexeru, který vyhledává latentní informace v netextových zdrojích a nerozlišený text, transformuje je na fulltextový prohledávatelný obsah v Azure Kognitivní hledání. 

Pro integrované zpracování jsou předem připravené modely AI v Cognitive Services označované interně, aby se prováděly analýzy. Můžete také integrovat vlastní modely pomocí Azure Machine Learning, Azure Functions nebo jiných přístupů.

Následuje konsolidovaný seznam dokumentace pro rozšíření AI.

## <a name="concepts"></a>Koncepty

+ [Rozšíření AI](cognitive-search-concept-intro.md)
+ [Sady dovedností](cognitive-search-working-with-skillsets.md)
+ [Ladicí relace](cognitive-search-debug-session.md)
+ [Úložiště znalostí](knowledge-store-concept-intro.md)
+ [Projekce](knowledge-store-projection-overview.md)
+ [Přírůstkové obohacení (opakované použití dokumentu obohaceného v mezipaměti)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Praktické návody

+ [Rychlý Start: vytvoření rozpoznávání dovednosti v Azure Portal](cognitive-search-quickstart-blob.md)
+ [Kurz: obohacení indexování pomocí AI](cognitive-search-tutorial-blob.md)
+ [Kurz: Diagnostika, oprava a potvrzení změn v dovednosti s využitím relací ladění](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Úložiště znalostí

+ [Rychlý Start: vytvoření znalostní databáze v Azure Portal](knowledge-store-create-portal.md)
+ [Vytvoření úložiště znalostí pomocí REST a předzálohovacího](knowledge-store-create-rest.md)
+ [Zobrazení znalostní báze s Průzkumník služby Storage](knowledge-store-view-storage-explorer.md)
+ [Připojit znalostní bázi Knowledge Store s Power BI](knowledge-store-connect-power-bi.md)
+ [Příklady projekce (jak natvarování a export obohacení)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Vlastní dovednosti (rozšířené)

+ [Jak definovat vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md)
+ [Příklad: Vytvoření vlastní dovednosti pomocí Azure Functions (a Vyhledávání entit Bingu rozhraní API)](cognitive-search-create-custom-skill-example.md)
+ [Příklad: Vytvoření vlastní dovednosti pomocí Pythonu](cognitive-search-custom-skill-python.md)
+ [Příklad: Vytvoření vlastní dovednosti pomocí nástroje pro rozpoznávání formulářů](cognitive-search-custom-skill-form.md) 
+ [Příklad: Vytvoření vlastní dovednosti pomocí Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Pokyny k návodům

+ [Připojení prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md)
+ [Definování dovedností](cognitive-search-defining-skillset.md)
+ [Poznámky k odkazům v dovednosti](cognitive-search-concept-annotations-syntax.md)
+ [Mapování polí na index](cognitive-search-output-field-mapping.md)
+ [Zpracování a extrahování informací z obrázků](cognitive-search-concept-image-scenarios.md)
+ [Konfigurace ukládání do mezipaměti pro přírůstkové obohacení](search-howto-incremental-index.md)
+ [Postup opětovného sestavení indexu služby Azure Kognitivní hledání](search-howto-reindex.md)
+ [Tipy k návrhům](cognitive-search-concept-troubleshooting.md)
+ [Běžné chyby a upozornění](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Referenční informace k dovednostem

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
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. dovednosti. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Vlastní dovednosti
  + [Microsoft. dovednosti. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft. dovednosti. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Zastaralé dovednosti](cognitive-search-skill-deprecated.md)
  + [Microsoft. dovednosti. text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>Rozhraní API

+ [REST API](/rest/api/searchservice/)
  + [Create dovednosti (rozhraní API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Vytvoření indexeru (rozhraní API-Version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Viz také

+ [Rozhraní REST API služby Azure Cognitive Search](/rest/api/searchservice/)
+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Co je Azure Cognitive Search?](search-what-is-azure-search.md)