---
title: Změny koncového bodu předpovědi v rozhraní V3 API
description: Rozhraní API pro koncový bod pro předpověď dotazu se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 59cf250a9db5a1f6759495c1b5a3c48cb07cde15
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018782"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Předpověď změn koncového bodu pro v3

Rozhraní API pro koncový bod pro předpověď dotazu se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3.

**Všeobecně dostupný stav** – toto rozhraní V3 API zahrnuje významné změny požadavků a odpovědí JSON z rozhraní v2 API.

Rozhraní V3 API poskytuje následující nové funkce:

* [Externí entity](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Dynamické seznamy](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Předem připravené změny JSON entity](#prebuilt-entity-changes)

[Požadavek](#request-changes) a [odpověď](#response-changes) koncového bodu předpovědi mají významné změny pro podporu nových funkcí uvedených výše, včetně následujících:

* [Změny objektu odpovědi](#top-level-json-changes)
* [Odkazy na název role entity místo názvu entity](#entity-role-name-instead-of-entity-name)
* [Vlastnosti k označení entit v projevy](#marking-placement-of-entities-in-utterances)

[Referenční dokumentace](https://aka.ms/luis-api-v3) je k dispozici pro v3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 změny z verze Preview na GA

Hodnota V3 provedla v rámci přechodu na GA následující změny:

* Následující předem připravené entity mají různé odpovědi JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * S měřitelnou příponou klíče jednotky z `units` na `unit`

* Změna JSON textu požadavku:
    * od `preferExternalEntities` do `preferExternalEntities`
    * volitelný `score` parametr pro externí entity

* Změny JSON těla odpovědi:
    * `normalizedQuery` odstraněn

## <a name="suggested-adoption-strategy"></a>Navrhovaná strategie přijetí

Pokud používáte rozhraní bot Framework, Kontrola pravopisu Bingu v7 nebo chcete migrovat pouze vytváření aplikací LUIS, pokračujte v používání koncového bodu v2.

Pokud víte, že žádná z vašich klientských aplikací nebo integrace (bot Framework a Kontrola pravopisu Bingu v7) je ovlivněná a vy budete mít na úmyslu migrovat vytváření aplikací LUIS a koncový bod předpovědi ve stejnou dobu, začněte používat koncový bod verze v3. Koncový bod verze v2 bude stále k dispozici a jedná se o dobrou strategii pro vrácení zpět.


## <a name="not-supported"></a>Nepodporováno

### <a name="bing-spell-check"></a>Kontrola pravopisu Bingu

Toto rozhraní API se v koncovém bodě verze V3 nepodporuje – pro opravy pravopisu je dál používán koncový bod pro předpověď rozhraní v2 API. Pokud při použití rozhraní V3 API potřebujete korekci pravopisu, požádejte o klientskou aplikaci rozhraní API [Kontrola pravopisu Bingu](../bing-spell-check/overview.md) a před odesláním textu do rozhraní API Luis změňte text na správný pravopis.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Rozhraní bot Framework a klientské aplikace Azure Bot Service

Pokračujte v použití koncového bodu předpovědi rozhraní v2 API, dokud se neuvolní v rozhraní bot Framework V 4.7.

## <a name="v2-api-deprecation"></a>Zastaralé rozhraní API v2

Předpokládané rozhraní API v2 nebude zastaralé po dobu nejméně 9 měsíců od verze V3 Preview, 8. června 2020.

## <a name="endpoint-url-changes"></a>Změny adresy URL koncového bodu

### <a name="changes-by-slot-name-and-version-name"></a>Změny podle názvu slotu a názvu verze

Byl změněn [Formát volání rozhraní HTTP koncového bodu V3](developer-reference-resource.md#rest-endpoints) .

Pokud se chcete dotazovat podle verze, musíte nejdřív [publikovat přes rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) pomocí `"directVersionPublish":true` . Dotaz na koncový bod odkazující na ID verze místo názvu slotu.

|Platné hodnoty pro `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Vyžádání změn

### <a name="query-string-changes"></a>Změny řetězce dotazu

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

### <a name="v3-post-body"></a>Tělo pro POST verze V3

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

|Vlastnost|Typ|Verze|Výchozí|Účel|
|--|--|--|--|--|
|`dynamicLists`|array|Jenom V3|Nepožadováno.|[Dynamické seznamy](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) umožňují rozšiřování existující entity vyškolené a publikované seznamu, která už je v aplikaci Luis.|
|`externalEntities`|array|Jenom V3|Nepožadováno.|[Externí entity](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) poskytují vaší aplikaci Luis schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. |
|`options.datetimeReference`|řetězec|Jenom V3|Žádná výchozí|Používá se k určení [posunu datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formát pro datetimeReference je [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|boolean|Jenom V3|false (nepravda)|Určuje, jestli se použije [Externí entita uživatele (se stejným názvem jako existující entita)](schema-change-prediction-runtime.md#override-existing-model-predictions) , nebo jestli se existující entita v modelu používá pro předpověď. |
|`query`|řetězec|Jenom V3|Povinná hodnota.|**V v2** je utterance, který má být předpovězen, v `q` parametru. <br><br>**V v3** je funkce předána do `query` parametru.|

## <a name="response-changes"></a>Změny odpovědí

Formát JSON odpovědi na dotaz byl změněn tak, aby umožňoval rychlejší programový přístup k datům použitým nejčastěji.

### <a name="top-level-json-changes"></a>Změny JSON nejvyšší úrovně



Nejvyšší vlastnosti JSON pro v2 jsou, pokud `verbose` je nastaveno na hodnotu true, která vrátí všechny záměry a jejich skóre ve `intents` vlastnosti:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Horních vlastností JSON pro v3 jsou:

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

`intents`Objekt je neuspořádaný seznam. Nepředpokládáme, že první podřízená položka v `intents` odpovídá `topIntent` . Místo toho použijte `topIntent` hodnotu k nalezení skóre:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Změny schématu JSON odpovědi umožňují:

* Jasně rozlišovat mezi původními utterance, `query` a vrácenou předpověď `prediction` .
* Jednodušší programový přístup k předpokládaným datům. Místo vytváření výčtu pomocí pole ve verzi v2 můžete získat přístup k hodnotám podle **názvu** pro oba záměry a entity. U předpokládaných rolí entit se vrátí název role, protože je v celé aplikaci jedinečný.
* Jsou respektovány datové typy, pokud jsou určeny. Číselné znaky již nejsou vraceny jako řetězce.
* Rozdíl mezi informacemi o předpovědi první priority a dalšími metadaty, které jsou vráceny v `$instance` objektu.

### <a name="entity-response-changes"></a>Změny odezvy entity

#### <a name="marking-placement-of-entities-in-utterances"></a>Označení umístění entit v projevy

**V v2** byla entita označena v utterance s `startIndex` a `endIndex` .

**V v3** je entita označena pomocí `startIndex` a `entityLength` .

#### <a name="access-instance-for-entity-metadata"></a>Přístup `$instance` k metadatům entit

Pokud potřebujete metadata entity, řetězec dotazu musí používat `verbose=true` příznak a odpověď obsahuje metadata v `$instance` objektu. Příklady jsou uvedeny v odpovědích JSON v následujících oddílech.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Každá předpovězená entita se reprezentuje jako pole.

`prediction.entities.<entity-name>`Objekt obsahuje pole, protože každou entitu lze v utterance předpovědět více než jednou.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Předem připravené změny entit

Objekt odpovědi V3 obsahuje změny předem sestavených entit. Další informace najdete v [konkrétních předem připravených entitách](luis-reference-prebuilt-entities.md) .

#### <a name="list-entity-prediction-changes"></a>Vypsat změny předpovědi entit

KÓD JSON pro předpověď seznamu entit se změnil na pole polí:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Každé vnitřní pole odpovídá textu v utterance. Vnitřní objekt je pole, protože stejný text se může objevit ve více než jednom podseznamu entity seznamu.

Při mapování mezi `entities` objektem na `$instance` objekt je pořadí objektů zachované pro entitu seznamu předpovědi.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Místo názvu entity název role entity

V v2 `entities` pole vrátilo všechny předpovězené entity s názvem entity, který je jedinečný identifikátor. Pokud ve verzi V3 používá entita role a předpověď je pro roli entity, primární identifikátor je název role. To je možné, protože názvy rolí entit musí být v celé aplikaci jedinečné, včetně jiných názvů modelů (záměr, entita).

V následujícím příkladu: Zvažte utterance, který obsahuje text, `Yellow Bird Lane` . Tento text je předpovězen jako `Location` role vlastní entity `Destination` .

|Utterance text|Název entity|Název role|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

V v2 je entita identifikována _názvem entity_ role jako vlastnost objektu:

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

V v3 je entita odkazována _rolí entity_, pokud je předpověď pro roli:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V v3 stejný výsledek s `verbose` příznakem pro vrácení metadat entity:

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

## <a name="extend-the-app-at-prediction-time"></a>Rozšiřování aplikace v době předpovědi

Přečtěte [si informace o](schema-change-prediction-runtime.md) tom, jak aplikaci rozvést v předpokládaném prostředí.

## <a name="deprecation"></a>Vyřazení

Rozhraní v2 API nebude zastaralé po dobu nejméně 9 měsíců od verze V3 Preview.

## <a name="next-steps"></a>Další kroky

Pomocí dokumentace k rozhraní V3 API aktualizujte existující volání REST na rozhraní API [koncového bodu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) Luis.