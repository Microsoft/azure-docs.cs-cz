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
ms.openlocfilehash: 7e4eb8f7f9077ce04ee8138580a836637b89cf8c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835599"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Recognitiond entity Recognition V3 verze Public Preview – říjen 2019

Další verze rozpoznávání pojmenovaných entit (NER) je teď dostupná ve verzi Public Preview a poskytuje rozšířenou detekci a kategorizaci entit nalezených v textu. Tato služba poskytuje:

* Rozpoznávání následujících nových typů entit:
    * Telefonní číslo
    * IP adresa

* Nový koncový bod pro rozpoznávání typů entit osobních informací (jenom v angličtině)
* Samostatné koncové body pro rozpoznávání entit a propojení entit.

Propojení entit podporuje angličtinu a španělštinu. Podpora jazyků NER se liší podle typu entity. Další informace najdete v následujícím odkazu. 

> [!div class="nextstepaction"]
> [Další informace o rozpoznávání pojmenovaných entit V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Analýza mínění V3 Public Preview – říjen 2019

Další verze Analýza mínění je teď dostupná ve verzi Public Preview a přináší významná vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API. Navíc poskytuje:

* Automatické označování pro různé zabarvení v textu
* Mínění analýzy a výstupy na úrovni dokumentu a věty. 

Podporuje angličtinu (`en`), japonštinu (`ja`), zjednodušená čínština (`zh-Hans`), tradiční čínština (`zh-Hant`), francouzština (`fr`), italština (`it`), španělština (`es`), holandština (`nl`), portugalština (`pt`) a němčina (`de`) a je k dispozici v těchto oblastech: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`a `West US 2`.

> [!div class="nextstepaction"]
> [Další informace o Analýza mínění V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)  
* [Příklady scénářů uživatelů](text-analytics-user-scenarios.md)
* [Analýza mínění](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání jazyka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznávání entit](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md)
