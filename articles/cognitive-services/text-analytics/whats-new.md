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
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086785"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>Rozpoznání pojmenované entity V3 Public Preview – únor 2020

Další typy entit jsou teď dostupné ve službě pro rozpoznávání pojmenovaných entit (NER) v3 ve verzi Public Preview, protože rozšíříme zjišťování obecných a osobních informací, které jsme v textu našli. Tato aktualizace zavádí [model verze](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`, která zahrnuje:

* Rozpoznávání následujících obecných typů entit (jenom v angličtině):
    * PersonType
    * Produkt
    * Událost
    * Geopolitická entita (GPE) jako podtyp v rámci umístění
    * Tuhle

* Rozpoznávání následujících typů entit osobních informací (jenom v angličtině):
    * Person (Osoba)
    * Organizace
    * Stáří jako podtyp v rámci množství
    * Datum jako podtyp v hodnotě DateTime
    * Email 
    * Telefonní číslo (jenom USA)
    * Adresa URL
    * IP adresa

> [!div class="nextstepaction"]
> [Další informace o rozpoznávání pojmenovaných entit V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Říjen 2019

#### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pro rozpoznávání typů entit osobních informací (jenom v angličtině)

* Samostatné koncové body pro [rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) a [propojení entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Verze modelu](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`, která zahrnuje:
    * Rozšířené zjišťování a kategorizace entit nalezených v textu. 
    * Rozpoznávání následujících nových typů entit:
        * Telefonní číslo
        * IP adresa

Propojení entit podporuje angličtinu a španělštinu. Podpora jazyků NER se liší podle typu entity.

#### <a name="sentiment-analysis-v3-public-preview"></a>Verze Public Preview Analýza mínění V3

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) pro analýzu mínění.
* [Verze modelu](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`, která zahrnuje:

    * Významná vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API.
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
