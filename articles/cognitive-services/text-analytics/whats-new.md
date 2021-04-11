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
ms.date: 03/25/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 0ed3a11381285a9422380eb14ff301a2b9ea816a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093550"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Co je nového v rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu se aktualizuje průběžně. V tomto článku najdete informace o nových vydaných verzích a funkcích, abyste měli přehled o aktuálním vývoji.

## <a name="march-2021"></a>Březen 2021

### <a name="general-api-updates"></a>Obecné aktualizace rozhraní API
* Vydání nového rozhraní API v 3.1-Preview. 4, které zahrnuje 
   * Změny v těle odpovědi JSON pro dolování stanovisek: 
      * `aspects` je teď `targets` a `opinions` teď `assessments` . 
   * Změny v těle odpovědi JSON hostovaného webového rozhraní API Analýza textu pro stav: 
      * `isNegated`Logický název zjištěného objektu entity pro negaci je zastaralý a nahrazen detekcí kontrolního výrazu.
      * Nová vlastnost s názvem `role` je nyní součástí extrahované relace mezi atributem a entitou a také vztahem mezi entitami.  Tím se přidá specifická Vazba na zjištěný typ vztahu.
   * Propojení entit je teď k dispozici jako asynchronní úloha v `/analyze` koncovém bodu.
   * `pii-categories`V koncovém bodu je nyní k dispozici nový parametr `/pii` .
      * Tento parametr umožňuje zadat entity PII, které se ve výchozím nastavení nepodporují, a to pro vstupní jazyk.
* Aktualizované klientské knihovny, které zahrnují asynchronní analýzu a Analýza textu pro operace stavu. Příklady najdete na GitHubu:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v 3.1 – Preview. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Analýza textu pro aktualizace stavu

* Nová verze modelu `2021-03-01` pro `/health` koncový bod a místní kontejner, který poskytuje
    * Přejmenování `Gene` typu entity na `GeneOrProtein` .
    * Nový `Date` typ entity.
    * Detekce kontrolního výrazu, která nahrazuje detekci negace (k dispozici pouze v rozhraní API verze 3.1-Preview. 4).
    * Nová upřednostňovaná `name` vlastnost pro propojené entity, která je normalizována z různých systémů ontologie a kódování (k dispozici pouze v rozhraní API verze 3.1-Preview. 4). 
* Do úložiště náhledu kontejneru se uvolnila nová image kontejneru s označením `3.0.015490002-onprem-amd64` a novou verzí modelu `2021-03-01` . 
    * Tato bitová kopie kontejneru již nebude k dispozici ke stažení `containerpreview.azurecr.io` po 26 2021. dubna.
* K dispozici je teď nový Analýza textu pro Image kontejneru Health se stejným modelem – verze `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare` . Od dubna 26 budete moct stáhnout jenom kontejner z tohoto úložiště.

> [!div class="nextstepaction"]
> [Další informace o Analýza textu pro stav](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>Analýza textu aktualizace portálu prostředků
* **Zpracované textové záznamy** jsou nyní k dispozici jako metrika v části **monitorování** pro prostředek analýza textu v Azure Portal.  

## <a name="february-2021"></a>Únor 2021

* `2021-01-15`Verze modelu pro koncový bod PII v [pojmenované entitě Recognition](how-tos/text-analytics-how-to-entity-linking.md) v 3.1-Preview. x, která poskytuje 
  * Rozšířená podpora 9 nových jazyků
  * Vylepšená kvalita AI u pojmenovaných kategorií entit pro podporované jazyky.
* Cenové úrovně S0 až S4 se vyřadí do 8. března 2021. Pokud máte existující Analýza textu prostředek pomocí cenové úrovně S4 S0, měli byste ho aktualizovat tak, aby používaly [cenovou úroveň](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)Standard (S).
* [Kontejner pro detekci jazyka](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) je teď všeobecně dostupný.
* v 2.1 rozhraní API se vyřazuje. 

## <a name="january-2021"></a>Leden 2021

* `2021-01-15`Verze modelu pro [rozpoznávání pojmenovaných entit](how-tos/text-analytics-how-to-entity-linking.md) v3. x, která poskytuje 
  * Rozšířená podpora jazyků pro [několik obecných kategorií entit](named-entity-types.md) 
  * Vylepšená kvalita AI pro obecné kategorie entit pro všechny podporované jazyky v3. 

* `2021-01-05`Verze modelu pro [detekci jazyka](how-tos/text-analytics-how-to-language-detection.md), která poskytuje další [jazykovou podporu](language-support.md?tabs=language-detection).

Tyto verze modelů nejsou aktuálně k dispozici v Východní USA oblasti. 

> [!div class="nextstepaction"]
> [Další informace o novém modelu NER](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>Prosinec 2020

* [Aktualizované](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) podrobnosti o cenách pro rozhraní API pro analýzu textu.

## <a name="november-2020"></a>Listopad 2020

* [Nový koncový bod](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) s rozhraní API pro analýzu textu v 3.1-Preview. 3 pro nové asynchronní [rozhraní analyzovat rozhraní API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze), které podporuje dávkové zpracování pro operace extrakce ner, PII a klíčové fráze.
* [Nový koncový bod](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) s rozhraní API pro analýzu textu v 3.1-Preview. 3 pro nové asynchronní analýza textu rozhraní API pro hostování [stavu](how-tos/text-analytics-for-health.md) s podporou pro dávkové zpracování.
* Výše uvedené nové funkce jsou dostupné jenom v těchto oblastech: `West US 2` ,, a `East US 2` `Central US` `North Europe` `West Europe` .
* Portugalština (Brazílie) `pt-BR` je teď podporovaná v [Analýza mínění](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x počínaje verzí modelu `2020-04-01` . Přičítá se k existující `pt-PT` podpoře pro portugalštinu.
* Aktualizované klientské knihovny, které zahrnují asynchronní analýzu a Analýza textu pro operace stavu. Příklady najdete na GitHubu:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v 3.1 – Preview. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Říjen 2020

* Podpora Hindština pro Analýza mínění v3. x počínaje verzí modelu `2020-04-01` . 
* Verze modelu `2020-09-01` pro koncový bod V3/languages, která přináší vylepšené funkce pro detekci a přesnost jazyka.
* dostupnost V3 v Střed Indie a Spojené arabské emiráty sever.

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
> [Další informace o rozhraní API pro analýzu textu v 3.1 – Preview. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Analýza textu pro aktualizace kontejneru stavu

Následující aktualizace jsou specifické pro září vydané verze Analýza textu jenom pro kontejner Health.
* Do úložiště náhledu kontejneru se uvolnila nová image kontejneru s označením `1.1.013530001-amd64-preview` s novým modelem verze `2020-09-03` . 
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

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Rozhraní API pro analýzu textu v 3.1 Public Preview
   * Nové Analýza mínění dolování funkcí – [názory](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nový `PII` Filtr domény Personal () pro chráněné informace o stavu ( `PHI` ).

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro analýzu textu v 3.1 verze Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Únor 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Podpora sady SDK pro rozhraní API pro analýzu textu V3 Public Preview

V rámci [sjednocené verze sady Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)je teď sada rozhraní API pro analýzu textu V3 SDK dostupná jako verze Public Preview pro následující programovací jazyky:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Další informace o sadě rozhraní API pro analýzu textu V3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

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

### <a name="october-2019"></a>Říjen 2019

#### <a name="named-entity-recognition-ner"></a>Rozpoznávání pojmenovaných entit (NER)

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) pro rozpoznávání typů entit osobních informací (jenom v angličtině)

* Samostatné koncové body pro [rozpoznávání entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) a [propojení entit](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Verze modelu](concepts/model-versioning.md) `2019-10-01` , která zahrnuje:
    * Rozšířené zjišťování a kategorizace entit nalezených v textu. 
    * Rozpoznávání následujících nových typů entit:
        * Telefonní číslo
        * IP adresa

Propojení entit podporuje angličtinu a španělštinu. Podpora jazyků NER se liší podle typu entity.

#### <a name="sentiment-analysis-v3-public-preview"></a>Verze Public Preview Analýza mínění V3

* [Nový koncový bod](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) pro analýzu mínění.
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
