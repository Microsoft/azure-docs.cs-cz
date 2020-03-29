---
title: Co je nového v rozhraní API pro analýzu textu
titleSuffix: Text Analytics - Azure Cognitive Services
description: Tento článek obsahuje informace o nových verzích a funkcích pro Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188807"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu je průběžně aktualizováno. Chcete-li mít aktuální informace o nejnovějším vývoji, tento článek obsahuje informace o nových verzích a funkcích.

## <a name="february-2020"></a>Únor 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Podpora sady SDK pro rozhraní API pro analýzu textu v3 Public Preview

Jako součást [jednotné verze sady Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)je nyní sada Text Analytics API v3 SDK k dispozici jako veřejná předběžná verze pro následující programovací jazyky:
   * [C #](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Pojmenovaný rozpoznávání entit v3 public preview

Další typy entit jsou nyní k dispozici ve službě NEkv v3 public Preview, protože rozšiřujeme zjišťování obecných a osobních informací, které se nacházejí v textu. Tato aktualizace zavádí [verzi](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`modelu , která zahrnuje:

* Uznání následujících obecných typů entit (pouze v angličtině):
    * Typ osoby
    * Produkt
    * Událost
    * Geopolitická entita (GPE) jako podtyp v části Umístění
    * Dovednost

* Uznání následujících typů subjektů osobních údajů (pouze v angličtině):
    * Person (Osoba)
    * Organizace
    * Stáří jako podtyp pod položkou Množství
    * Datum jako podtyp v části DateTime
    * E-mail 
    * Telefonní číslo (pouze USA)
    * zprostředkovatele identity
    * IP adresa

> [!div class="nextstepaction"]
> [Další informace o rozpoznávání pojmenovaných entit v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Říjen 2019

#### <a name="named-entity-recognition-ner"></a>Uznání pojmenované entity (NER)

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pro rozpoznávání typů entit osobních informací (pouze v angličtině)

* Samostatné koncové body pro [rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) a [propojení entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Verze](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`modelu , která zahrnuje:
    * Rozšířená detekce a kategorizace entit nalezených v textu. 
    * Účtování následujících nových typů účetních údajů:
        * Telefonní číslo
        * IP adresa

Entity propojení podporuje angličtinu a španělštinu. Podpora jazyka NER se liší podle typu entity.

#### <a name="sentiment-analysis-v3-public-preview"></a>Analýza mínění v3 public preview

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) pro analýzu mínění.
* [Verze](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`modelu , která zahrnuje:

    * Významná vylepšení přesnosti a podrobností kategorizace a vyhodnocování textu rozhraní API.
    * Automatické označování různých mínění v textu.
    * Analýza mínění a výstup na úrovni dokumentu a věty. 

Podporuje angličtinu`en`(`ja`), japonštinu`zh-Hans`( ),`zh-Hant`zjednodušenou čínštinu ( ), tradiční čínštinu ( ), francouzštinu (`fr`), francouzštinu `Central Canada` `Central US`( `East Asia` `East US`), `East US 2` `North Europe`španělštinu `South Central US` `UK South`( `West Europe` `West US 2``it``es`), nizozemštinu`nl`( ), portugalštinu (`pt`a němčinu (`de`) a je k dispozici v následujících regionech: `Australia East`, , , , , `Southeast Asia`, , , , , a . 

> [!div class="nextstepaction"]
> [Další informace o analýze mínění v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)  
* [Ukázkové uživatelské scénáře](text-analytics-user-scenarios.md)
* [Analýza mínění](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Detekce jazyka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznávání entit](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md)
