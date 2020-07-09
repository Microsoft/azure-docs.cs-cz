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
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 3d9fb7f3fa01c06cc778e4062dd1d136e9c6e342
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103394"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="july-2020"></a>Červenec 2020

### <a name="text-analytics-for-health-container-public-gated-preview"></a>Analýza textu pro veřejný ověřovaný náhled kontejneru stavu

Analýza textu pro kontejner Health je nyní ve veřejné službě gated Preview, která umožňuje extrakci informací z nestrukturovaného textu v angličtině v klinických dokumentech, jako jsou například formuláře pro příjem pacientů, lékařské poznámky, výzkumné doklady a souhrny vypouštění. V současné době se vám nebudou účtovat Analýza textu pro využití kontejneru stavů. 

Kontejner nabízí následující funkce:

* Rozpoznávání pojmenovaných entit
* Extrakce vztahu
* Propojení entit
* Opačné číslo


> [!div class="nextstepaction"]
> [Další informace o Analýza textu pro kontejner stavu](how-tos/text-analytics-for-health.md)

## <a name="may-2020"></a>Květen 2020

### <a name="text-analytics-api-v3-general-availability"></a>Obecná dostupnost rozhraní API pro analýzu textu V3

Rozhraní API pro analýzu textu v3 je teď všeobecně dostupné s následujícími aktualizacemi:

* Verze modelu`2020-04-01`
* Nová [omezení dat](concepts/data-limits.md) pro jednotlivé funkce
* Aktualizovaná [jazyková podpora](language-support.md) pro [Analýza mínění (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Samostatný koncový bod pro propojení entit 
* Nová kategorie entit "adresa" ve funkci [rozpoznávání pojmenovaných entit (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nové podkategorie v NER V3:
   * Umístění – zeměpisné
   * Umístění – strukturální
   * Podniková výměna zásob
   * V organizaci – lékařské
   * Organizace – Sport
   * Událost – kulturní
   * Událost – přirozené
   * Událost – Sport

Byly přidány následující vlastnosti v odpovědi JSON:
   * `SentenceText`v Analýza mínění
   * `Warnings`pro každý dokument 

V případě potřeby se změnily názvy následujících vlastností v odpovědi JSON:

* Přejmenování `score` na `confidenceScore`
    * `confidenceScore`má dvě desetinná místa přesnosti. 
* Přejmenování `type` na `category`
* Přejmenování `subtype` na `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Rozhraní API pro analýzu textu v 3.1 Public Preview
   * Nové Analýza mínění dolování funkcí – [názory](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nový [ `PII` Filtr domény Personal ()](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) pro chráněné informace o stavu ( `PHI` ).

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v 3.1 verze Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Únor 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Podpora sady SDK pro rozhraní API pro analýzu textu V3 Public Preview

V rámci [sjednocené verze sady Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)je teď sada rozhraní API pro analýzu textu V3 SDK dostupná jako verze Public Preview pro následující programovací jazyky:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Další informace o sadě rozhraní API pro analýzu textu V3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Rozpoznávání pojmenovaných entit V3 Public Preview

Další typy entit jsou teď dostupné ve službě pro rozpoznávání pojmenovaných entit (NER) v3 ve verzi Public Preview, protože rozšíříme zjišťování obecných a osobních informací, které jsme v textu našli. Tato aktualizace zavádí [modelovou verzi](concepts/model-versioning.md) `2020-02-01` , která zahrnuje:

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
    * E-mail 
    * Telefonní číslo (jenom USA)
    * URL
    * IP adresa

> [!div class="nextstepaction"]
> [Další informace o rozpoznávání pojmenovaných entit V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Říjen 2019

#### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) pro rozpoznávání typů entit osobních informací (jenom v angličtině)

* Samostatné koncové body pro [rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) a [propojení entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Verze modelu](concepts/model-versioning.md) `2019-10-01` , která zahrnuje:
    * Rozšířené zjišťování a kategorizace entit nalezených v textu. 
    * Rozpoznávání následujících nových typů entit:
        * Telefonní číslo
        * IP adresa

Propojení entit podporuje angličtinu a španělštinu. Podpora jazyků NER se liší podle typu entity.

#### <a name="sentiment-analysis-v3-public-preview"></a>Verze Public Preview Analýza mínění V3

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) pro analýzu mínění.
* [Verze modelu](concepts/model-versioning.md) `2019-10-01` , která zahrnuje:

    * Významná vylepšení přesnosti a podrobností o kategorizaci a bodování textu rozhraní API.
    * Automatické označování pro různé zabarvení v textu
    * Mínění analýzy a výstupy na úrovni dokumentu a věty. 

Podporuje angličtinu ( `en` ), japonštinu () `ja` , zjednodušenou čínštinu ( `zh-Hans` ), tradiční čínštinu (), francouzštinu (), italštinu (), španělštinu (), holandštinu (), `zh-Hant` `fr` `it` `es` `nl` portugalštinu () `pt` a němčinu ( `de` ) a je k dispozici v následujících oblastech: `Australia East` , `Central Canada` , `Central US` , `East Asia` , `East US` , `East US 2` , `North Europe` , `Southeast Asia` , `South Central US` , `UK South` , `West Europe` a `West US 2` . 

> [!div class="nextstepaction"]
> [Další informace o Analýza mínění V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro analýzu textu?](overview.md)  
* [Ukázkové uživatelské scénáře](text-analytics-user-scenarios.md)
* [Analýza mínění](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rozpoznávání jazyka](how-tos/text-analytics-how-to-language-detection.md)
* [Rozpoznávání entit](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md)
