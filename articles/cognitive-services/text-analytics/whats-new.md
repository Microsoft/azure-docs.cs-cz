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
ms.date: 09/29/2020
ms.author: aahi
ms.openlocfilehash: 12f3ce506d8b4588dc9c670e49cabd3bc7767566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460267"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="september-2020"></a>Září 2020

### <a name="general-api-updates"></a>Obecné aktualizace rozhraní API

* Vydání nové adresy URL pro verzi Public Preview Analýza textu v 3.1 pro podporu aktualizací následujících koncových bodů pro rozpoznávání pojmenovaných entit: 
    * `/pii` koncový bod nyní zahrnuje novou `redactedText` vlastnost v odpovědi JSON, kde zjištěné entity PII ve vstupním textu jsou nahrazeny řetězcem `*` pro každý znak těchto entit.
    * `/linking` koncový bod teď obsahuje `bingID` vlastnost v odpovědi JSON pro propojené entity.
* Následující koncové body rozhraní API Analýza textu Preview byly vyřazeny ze září 4. září 2020:
    * v 2.1 – Preview
    * v3.0-preview
    * v 3.0 – Preview. 1
    
> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v 3.1 – Preview. 2](quickstarts/text-analytics-sdk.md)

### <a name="text-analytics-for-health-container-updates"></a>Analýza textu pro aktualizace kontejneru stavu

Následující aktualizace jsou specifické pro září vydané verze Analýza textu jenom pro kontejner Health.
* Do úložiště containerpreview byla vydána nová image kontejneru s označením `1.1.013530001-amd64-preview` s novým modelem verze `2020-09-03` . 
* Tato verze modelu přináší vylepšení rozpoznávání entit, detekce zkratky a vylepšení latence.

> [!div class="nextstepaction"]
> [Další informace o Analýza textu pro stav](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Srpen 2020

### <a name="general-api-updates"></a>Obecné aktualizace rozhraní API

* Verze modelu `2020-07-01` pro v3 `/keyphrases` `/pii` a `/languages` koncové body, které přidávají:
    * Další [kategorie entit](named-entity-types.md?tabs=personal) a specifických pro zemi pro státní správu pro rozpoznávání pojmenovaných entit
    * Podpora norštiny a turečtiny v Analýza mínění v3.
* V případě požadavků V3 API, které překračují omezení publikovaných [dat](concepts/data-limits.md), se teď vrátí chyba HTTP 400. 
* Koncové body, které vrací posun, teď podporují volitelný `stringIndexType` parametr, který upraví vrácené `offset` `length` hodnoty a hodnot tak, aby odpovídaly podporovanému [schématu řetězcového indexu](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-updates"></a>Analýza textu pro aktualizace kontejneru stavu

Následující aktualizace jsou specifické pro verzi Analýza textu ze srpna pro kontejner Health.

* Nový model – verze pro Analýza textu pro stav: `2020-07-24`
* Nová adresa URL pro odeslání Analýza textu pro žádosti o stav: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Nezapomeňte, že pro použití ukázkové webové aplikace zahrnuté v této nové imagi kontejneru bude potřeba vymazat mezipaměť prohlížeče.)

Následující vlastnosti v odpovědi JSON se změnily:

* Přejmenování `type` na `category` 
* Přejmenování `score` na `confidenceScore`
* Entity v `category` poli výstupu JSON jsou teď v případě jazyka Pascal. Následující entity byly přejmenovány:
    * `EXAMINATION_RELATION` byla přejmenována na `RelationalOperator` .
    * `EXAMINATION_UNIT` byla přejmenována na `MeasurementUnit` .
    * `EXAMINATION_VALUE` byla přejmenována na `MeasurementValue` .
    * `ROUTE_OR_MODE` byl přejmenován `MedicationRoute` .
    * Relační entita `ROUTE_OR_MODE_OF_MEDICATION` byla přejmenována na `RouteOfMedication` .

Byly přidány následující entity:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Extrakce vztahu
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Další informace o Analýza textu pro kontejner stavu](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Červenec 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Analýza textu pro kontejner stavu – veřejná ověřovaná verze Preview

Analýza textu pro kontejner Health je nyní ve veřejné službě gated Preview, která umožňuje extrakci informací z nestrukturovaného textu v angličtině v klinických dokumentech, jako jsou například formuláře pro příjem pacientů, lékařské poznámky, výzkumné doklady a souhrny vypouštění. V současné době se vám nebudou účtovat Analýza textu pro využití kontejneru stavů.

Kontejner nabízí následující funkce:

* Rozpoznávání pojmenovaných entit
* Extrakce vztahu
* Propojení entit
* Opačné číslo

## <a name="may-2020"></a>Květen 2020

### <a name="text-analytics-api-v3-general-availability"></a>Obecná dostupnost rozhraní API pro analýzu textu V3

Rozhraní API pro analýzu textu v3 je teď všeobecně dostupné s následujícími aktualizacemi:

* Verze modelu `2020-04-01`
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
   * `SentenceText` v Analýza mínění
   * `Warnings` pro každý dokument 

V případě potřeby se změnily názvy následujících vlastností v odpovědi JSON:

* Přejmenování `score` na `confidenceScore`
    * `confidenceScore` má dvě desetinná místa přesnosti. 
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
    * Osoba
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
