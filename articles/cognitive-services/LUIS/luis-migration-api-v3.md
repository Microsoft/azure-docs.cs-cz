---
title: Změny koncového bodu předpovědi v rozhraní API V3
description: Koncový bod předpovědi dotazu V3 se změnil. V této příručce můžete pochopit, jak migrovat do prostředí API koncového bodu verze 3.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530381"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Změny koncového bodu předpovědi pro V3

Koncový bod předpovědi dotazu V3 se změnil. V této příručce můžete pochopit, jak migrovat do prostředí API koncového bodu verze 3.

**Obecně dostupný stav** – toto rozhraní API V3 zahrnuje významné změny požadavků JSON a odpovědí z rozhraní Api V2.

Rozhraní API V3 poskytuje následující nové funkce:

* [Externí subjekty](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dynamické seznamy](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Předem sestavené změny entity JSON](#prebuilt-entity-changes)

[Požadavek](#request-changes) a [odpověď](#response-changes) koncového bodu předpověď mají významné změny na podporu nové funkce uvedené výše, včetně následujících:

* [Změny objektu odpovědi](#top-level-json-changes)
* [Odkazy na názvy rolí entity namísto názvu entity](#entity-role-name-instead-of-entity-name)
* [Vlastnosti pro označení entit v projevech](#marking-placement-of-entities-in-utterances)

[Referenční dokumentace](https://aka.ms/luis-api-v3) je k dispozici pro V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 se změní z náhledu na GA

V3 provedl následující změny v rámci přechodu na GA:

* Následující předem vytvořené entity mají různé odpovědi JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeografieV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * Měřitelný název klíče jednotky `units` od do`unit`

* Tělo požadavku JSON změna:
    * od `preferExternalEntities` do`preferExternalEntities`
    * volitelný `score` parametr pro externí subjekty

* Reakční tělo JSON se mění:
    * `normalizedQuery`Odstraněn

## <a name="suggested-adoption-strategy"></a>Navrhovaná strategie přijetí

Pokud používáte Rozhraní botů, kontrola pravopisu Bingu V7 nebo chcete migrovat jenom vývoj aplikace LUIS, pokračujte v používání koncového bodu V2.

Pokud víte, že žádná z vašich klientských aplikací nebo integrace (Bot Framework a Kontrola pravopisu Bing V7) jsou ovlivněny a jsou pohodlné migraci vaší aplikace luis a koncový bod předpověď ve stejnou dobu, začít používat koncový bod předpověď V3. Koncový bod predikce V2 bude stále k dispozici a je dobrou záložní strategií.


## <a name="not-supported"></a>Nepodporuje se

### <a name="bing-spell-check"></a>Kontrola pravopisu Bingu

Toto rozhraní API není podporováno v koncovém bodě předpovědi V3 – nadále používat koncový bod předpovědi rozhraní API V2 pro opravy pravopisu. Pokud potřebujete opravu pravopisu při používání rozhraní API V3, mají klientská aplikace volat [rozhraní API kontroly pravopisu Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) a změnit text na správný pravopis před odesláním textu do rozhraní LUIS API.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Klientské aplikace Bot Framework a Azure Bot Service

Pokračujte v používání koncového bodu predikce rozhraní API V2, dokud nebude uvolněna v4.7 rozhraní Bot Framework.

## <a name="v2-api-deprecation"></a>Vyřazení rozhraní API V2

Rozhraní API predikce V2 nebude zastaralé po dobu nejméně 9 měsíců po náhledu V3, červen 8th, 2020.

## <a name="endpoint-url-changes"></a>Změny adresy URL koncového bodu

### <a name="changes-by-slot-name-and-version-name"></a>Změny podle názvu patice a názvu verze

Formát volání HTTP koncového bodu V3 se změnil.

Pokud chcete dotaz ovat podle verze, musíte nejprve [publikovat prostřednictvím rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) s . `"directVersionPublish":true` Dotaz na koncový bod odkazující na ID verze namísto názvu patice.

|PŘEDPOVĚĎ API VERZE|Metoda|zprostředkovatele identity|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/sloty/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/sloty/<b>{SLOT-NAME}</b>/predict|
|V2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Platné hodnoty pro`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Vyžádání změn

### <a name="query-string-changes"></a>Změny řetězce dotazu

Rozhraní API V3 má různé parametry řetězce dotazu.

|Název Param|Typ|Version|Výchozí|Účel|
|--|--|--|--|--|
|`log`|Boolean|V2 & V3|false (nepravda)|Uložit dotaz do souboru protokolu. Výchozí hodnota je false.|
|`query`|řetězec|Pouze V3|Žádné výchozí nastavení - je vyžadováno v požadavku GET|**V V2**utterance, které mají být `q` předpovězeny je v parametru. <br><br>**Ve V3**je funkce předána `query` v parametru.|
|`show-all-intents`|Boolean|Pouze V3|false (nepravda)|Vrátit všechny záměry s odpovídající skóre v **prediction.intents** objektu. Záměry jsou vráceny jako `intents` objekty v nadřazeném objektu. To umožňuje programový přístup bez nutnosti najít `prediction.intents.give`záměr v poli: . Ve V2 byly vráceny v poli. |
|`verbose`|Boolean|V2 & V3|false (nepravda)|**Ve V2**, pokud je nastavena na hodnotu true, byly vráceny všechny předpokládané záměry. Pokud potřebujete všechny předpokládané záměry, použijte V3 `show-all-intents`param .<br><br>**Ve V3**tento parametr poskytuje pouze podrobnosti metadat entity o predikci entity.  |
|`timezoneOffset`|řetězec|V2|-|Časové pásmo použité pro entity datetimeV2.|
|`datetimeReference`|řetězec|V3|-|[Časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) použité pro entity datetimeV2. Nahrazuje `timezoneOffset` se z V2.|


### <a name="v3-post-body"></a>Těleso V3 POST

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Vlastnost|Typ|Version|Výchozí|Účel|
|--|--|--|--|--|
|`dynamicLists`|pole|Pouze V3|Není vyžadováno.|[Dynamické seznamy](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) umožňují rozšířit existující trénované a publikované seznam entity, již v aplikaci LUIS.|
|`externalEntities`|pole|Pouze V3|Není vyžadováno.|[Externí entity](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) poskytují vaší aplikaci LUIS možnost identifikovat a označovat entity za běhu, které lze použít jako funkce pro existující entity. |
|`options.datetimeReference`|řetězec|Pouze V3|Žádné výchozí nastavení|Slouží k určení [posunu datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formát pro datetimeReference je [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|Boolean|Pouze V3|false (nepravda)|Určuje, zda je použita [externí entita uživatele (se stejným názvem jako existující entita)](schema-change-prediction-runtime.md#override-existing-model-predictions) nebo zda se pro předpověď používá existující entita v modelu. |
|`query`|řetězec|Pouze V3|Povinná hodnota.|**V V2**utterance, které mají být `q` předpovězeny je v parametru. <br><br>**Ve V3**je funkce předána `query` v parametru.|

## <a name="response-changes"></a>Změny odpovědí

Odpověď na dotaz JSON změněna tak, aby umožnila větší programový přístup k nejčastěji používaným datům.

### <a name="top-level-json-changes"></a>Změny JSON nejvyšší úrovně



Nejvyšší vlastnosti JSON pro V2 jsou, když `verbose` je nastavena na hodnotu true, která vrátí všechny záměry a jejich skóre ve vlastnosti: `intents`

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Nejvyšší vlastnosti JSON pro V3 jsou:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

Objekt `intents` je neuspořádaný seznam. Nepředpokládejte, že první `intents` dítě v `topIntent`odpovídá . Místo toho `topIntent` použijte hodnotu k nalezení skóre:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Odpověď Změny schématu JSON umožňují:

* Jasný rozdíl mezi původní `query`utterance , `prediction`a vrácené předpověď , .
* Snadnější programový přístup k předpovídanám datům. Namísto výčtu prostřednictvím pole ve V2, můžete přistupovat k hodnotám podle **názvu** pro záměry a entity. U předpovídaných rolí entity je název role vrácen, protože je jedinečný v celé aplikaci.
* Datové typy, pokud jsou určeny, jsou respektovány. Číselné číslo již nejsou vráceny jako řetězce.
* Rozdíl mezi informace o predikci první `$instance` priority a další metadata, vrácené v objektu.

### <a name="entity-response-changes"></a>Změny odezvy entity

#### <a name="marking-placement-of-entities-in-utterances"></a>Označení umístění entit v projevech

**Ve v2**byla entita označena `startIndex` utterance s a `endIndex`.

**Ve v3**je entita označena písmenem `startIndex` a `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Přístup `$instance` k metadatům entity

Pokud potřebujete metadata entity, řetězec dotazu `verbose=true` musí použít příznak a odpověď `$instance` obsahuje metadata v objektu. Příklady jsou uvedeny v odpovědích JSON v následujících částech.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Každá předpovídaná entita je reprezentována jako pole

Objekt `prediction.entities.<entity-name>` obsahuje pole, protože každá entita lze předpovědět více než jednou v utterance.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Předem vytvořené změny entity

Objekt odpovědi V3 zahrnuje změny předem sestavených entit. Další informace najdete v [konkrétních předem vytvořených entitách.](luis-reference-prebuilt-entities.md)

#### <a name="list-entity-prediction-changes"></a>Změny předpovědi entity seznamu

JSON pro předpověď entity seznamu se změnil na pole polí:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Každé vnitřní pole odpovídá textu uvnitř utterance. Vnitřní objekt je pole, protože stejný text se může objevit ve více než jednom podseznamu entity seznamu.

Při mapování `entities` mezi objektem `$instance` na objekt je zachováno pořadí objektů pro předpovědi entit seznamu.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Název role entity namísto názvu entity

Ve V2 `entities` pole vrátilvšechny předpovídané entity s názvem entity je jedinečný identifikátor. Ve V3, pokud entita používá role a předpověď je pro roli entity, primární identifikátor je název role. To je možné, protože názvy rolí entity musí být jedinečné v celé aplikaci, včetně jiných názvů modelu (záměr, entita).

V následujícím příkladu: zvažte utterance, `Yellow Bird Lane`který obsahuje text, . Tento text je předpovězen jako role vlastní `Location` entity . `Destination`

|Text utterance|Název entity|Název role|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Ve V2 je entita identifikována _názvem entity_ s rolí jako vlastností objektu:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Ve V3 je entita odkazována _rolí entity_, pokud je předpověď pro roli:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Ve V3 stejný výsledek `verbose` s příznakem vrátit metadata entity:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Rozšíření aplikace v době předpovědi

Naučte se [koncepty](schema-change-prediction-runtime.md) o tom, jak rozšířit aplikaci za běhu předpovědi.

## <a name="deprecation"></a>Vyřazení

Rozhraní API V2 nebude zastaralé po dobu nejméně 9 měsíců po náhledu V3.

## <a name="next-steps"></a>Další kroky

Pomocí dokumentace rozhraní API V3 aktualizovat existující volání REST na rozhraní API [koncového bodu](https://aka.ms/luis-api-v3) LUIS.
