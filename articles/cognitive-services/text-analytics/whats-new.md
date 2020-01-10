---
title: Co je nového v rozhraní API pro analýzu textu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Tento článek obsahuje informace o nových verzích a funkcích pro Azure Cognitive Services Analýza textu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 6fa7d6a93a56cc531df238a8580207ef7a89d5d0
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732616"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Recognitiond entity Recognition V3 verze Public Preview – říjen 2019

Další verze rozpoznávání pojmenovaných entit (NER) je teď dostupná ve verzi Public Preview a poskytuje rozšířenou detekci a kategorizaci entit nalezených v textu. Tato služba poskytuje:

* Rozpoznávání následujících nových typů entit:
    * Telefonní číslo
    * IP adresa

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pro rozpoznávání typů entit osobních informací (jenom v angličtině)
* Samostatné koncové body pro [rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) a [propojení entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

Propojení entit podporuje angličtinu a španělštinu. Podpora jazyků NER se liší podle typu entity. 

> [!div class="nextstepaction"]
> [Další informace o rozpoznávání pojmenovaných entit V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Analýza mínění V3 Public Preview – říjen 2019

[Další verze analýza mínění](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) je teď dostupná ve verzi Public Preview a přináší významná vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API. Navíc poskytuje:

* Automatické označování pro různé zabarvení v textu
* Mínění analýzy a výstupy na úrovni dokumentu a věty. 

Podporuje angličtinu (`en`), japonštinu (`ja`), zjednodušená čínština (`zh-Hans`), tradiční čínština (`zh-Hant`), francouzština (`fr`), italština (`it`), španělština (`es`), holandština (`nl`), portugalština (`pt`) a němčina (`de`) a je k dispozici v těchto oblastech: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`a `West US 2`. 

> [!div class="nextstepaction"]
> [Další informace o Analýza mínění V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)  
* [Příklady scénářů uživatelů](text-analytics-user-scenarios.md)
* [Analýza mínění](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání jazyka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznávání entit](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md)
