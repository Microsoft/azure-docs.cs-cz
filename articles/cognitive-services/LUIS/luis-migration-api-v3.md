---
title: Změny koncového bodu předpovědi v rozhraní V3 API
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro koncový bod pro předpověď dotazu se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 8756d8e60e7612c1610e07b0567465e3a0ea8884
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531492"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Předpověď změn koncového bodu pro v3

Rozhraní API pro koncový bod pro předpověď dotazu se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Všeobecně dostupný stav** – toto rozhraní V3 API zahrnuje významné změny požadavků a odpovědí JSON z rozhraní v2 API.

Rozhraní V3 API poskytuje následující nové funkce:

* [Externí entity](#external-entities-passed-in-at-prediction-time)
* [Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time)
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
    * Název klíče jednotky s měřitelnou z `units` `unit`

* Změna JSON textu požadavku:
    * z `preferExternalEntities` `preferExternalEntities`
    * volitelný parametr `score` pro externí entity

* Změny JSON těla odpovědi:
    * `normalizedQuery` odebrán

## <a name="suggested-adoption-strategy"></a>Navrhovaná strategie přijetí

Pokud používáte rozhraní bot Framework, Kontrola pravopisu Bingu v7 nebo chcete migrovat pouze vytváření aplikací LUIS, pokračujte v používání koncového bodu v2. 

Pokud víte, že žádná z vašich klientských aplikací nebo integrace (bot Framework a Kontrola pravopisu Bingu v7) je ovlivněná a vy budete mít na úmyslu migrovat vytváření aplikací LUIS a koncový bod předpovědi ve stejnou dobu, začněte používat koncový bod verze v3. Koncový bod verze v2 bude stále k dispozici a jedná se o dobrou strategii pro vrácení zpět. 

## <a name="not-supported"></a>Nepodporováno

* Rozhraní API Bingu pro kontrolu pravopisu se v koncovém bodě verze V3 nepodporuje. pro opravy pravopisu je nadále používán koncový bod rozhraní v2 API v2.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Rozhraní bot Framework a klientské aplikace Azure Bot Service

Pokračujte v použití koncového bodu předpovědi rozhraní v2 API, dokud se neuvolní v rozhraní bot Framework V 4.7. 

## <a name="v2-api-deprecation"></a>Zastaralé rozhraní API v2 

Předpokládané rozhraní API v2 nebude zastaralé po dobu nejméně 9 měsíců od verze V3 Preview, 8. června 2020. 

## <a name="endpoint-url-changes"></a>Změny adresy URL koncového bodu 

### <a name="changes-by-slot-name-and-version-name"></a>Změny podle názvu slotu a názvu verze

Byl změněn formát volání rozhraní HTTP koncového bodu v3.

Pokud se chcete dotazovat podle verze, musíte nejdřív [publikovat přes rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) pomocí `"directVersionPublish":true`. Dotaz na koncový bod odkazující na ID verze místo názvu slotu.

|PREDIKTIVNÍ VERZE ROZHRANÍ API|Metoda|Adresa URL|
|--|--|--|
|V3|GET|https://<b>{region}</b>. api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict? dotaz =<b>{Query}</b>|
|V3|POST|https://<b>{region}</b>. api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Slots/<b>{slot-Name}</b>/Predict|
|V2|GET|https://<b>{region}</b>. api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict? dotaz =<b>{Query}</b>|
|V2|POST|https://<b>{region}</b>. api.cognitive.microsoft.com/luis/<b>předpověď</b>/<b>v 3.0</b>/Apps/<b>{App-ID}</b>/Versions/<b>{Version-ID}</b>/Predict|

|Platné hodnoty pro `SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Vyžádání změn 

### <a name="query-string-changes"></a>Změny řetězce dotazu

Rozhraní API V3 má jiné parametry řetězce dotazu.

|Název param|Typ|Verze|Výchozí|Účel|
|--|--|--|--|--|
|`log`|Boolean|V2 & V3|false|Dotaz na uložení v souboru protokolu. Výchozí hodnota je false.| 
|`query`|string|Jenom V3|Žádná výchozí hodnota – v žádosti GET se vyžaduje.|**V v2**je utterance, který má být předpovězen, v parametru `q`. <br><br>**V v3**je funkce předána v parametru `query`.|
|`show-all-intents`|Boolean|Jenom V3|false|Vrátí všechny záměry s odpovídajícím skóre v objektu **předpověď. záměrs** . Záměry jsou vráceny jako objekty v nadřazeném objektu `intents`. To umožňuje programový přístup bez nutnosti najít záměr v poli: `prediction.intents.give`. V v2 byly vráceny v poli. |
|`verbose`|Boolean|V2 & V3|false|**V v2**při nastavení na hodnotu true byly vráceny všechny předpovězené záměry. Pokud potřebujete všechny předvídatelné záměry, použijte parametr V3 `show-all-intents`.<br><br>**V v3**tento parametr poskytuje pouze podrobnosti o metadatech entit pro předpovědi entit.  |
|`timezoneOffset`|string|V2|-|Časové pásmo bylo použito pro datetimeV2 entity.|
|`datetimeReference`|string|V3|-|[Časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) bylo použito pro datetimeV2 entity. Nahradí `timezoneOffset` z v2.|


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
|`dynamicLists`|pole|Jenom V3|Není nutné.|[Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time) umožňují rozšiřování existující entity vyškolené a publikované seznamu, která už je v aplikaci Luis.|
|`externalEntities`|pole|Jenom V3|Není nutné.|[Externí entity](#external-entities-passed-in-at-prediction-time) poskytují vaší aplikaci Luis schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. |
|`options.datetimeReference`|string|Jenom V3|Žádná výchozí|Používá se k určení [posunu datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formát pro datetimeReference je [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|Boolean|Jenom V3|false|Určuje, jestli se použije [Externí entita uživatele (se stejným názvem jako existující entita)](#override-existing-model-predictions) , nebo jestli se existující entita v modelu používá pro předpověď. |
|`query`|string|Jenom V3|Povinná hodnota.|**V v2**je utterance, který má být předpovězen, v parametru `q`. <br><br>**V v3**je funkce předána v parametru `query`.|



## <a name="response-changes"></a>Změny odpovědí

Formát JSON odpovědi na dotaz byl změněn tak, aby umožňoval rychlejší programový přístup k datům použitým nejčastěji. 

### <a name="top-level-json-changes"></a>Změny JSON nejvyšší úrovně



Nejvyšší vlastnosti JSON pro v2 jsou, pokud je `verbose` nastaveno na hodnotu true, která vrátí všechny záměry a jejich skóre ve vlastnosti `intents`:

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

Objekt `intents` je neuspořádaný seznam. Nepředpokládat, že první podřízená položka v `intents` odpovídá `topIntent`. Místo toho vyhledáte skóre pomocí `topIntent` hodnoty:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Změny schématu JSON odpovědi umožňují:

* Jasně rozlišovat mezi původními utterance, `query`a vrácenou předpověď `prediction`.
* Jednodušší programový přístup k předpokládaným datům. Místo vytváření výčtu pomocí pole ve verzi v2 můžete získat přístup k hodnotám podle **názvu** pro oba záměry a entity. U předpokládaných rolí entit se vrátí název role, protože je v celé aplikaci jedinečný.
* Jsou respektovány datové typy, pokud jsou určeny. Číselné znaky již nejsou vraceny jako řetězce.
* V objektu `$instance` se vrátí rozdíl mezi informacemi o předpovědi první priority a dalšími metadaty. 

### <a name="entity-response-changes"></a>Změny odezvy entity

#### <a name="marking-placement-of-entities-in-utterances"></a>Označení umístění entit v projevy

**V v2**byla entita označená v utterance s `startIndex` a `endIndex`. 

**V v3**je entita označená jako `startIndex` a `entityLength`.

#### <a name="access-instance-for-entity-metadata"></a>Přístup k `$instance` pro metadata entity

Pokud potřebujete metadata entity, řetězec dotazu musí používat příznak `verbose=true` a odpověď obsahuje metadata v objektu `$instance`. Příklady jsou uvedeny v odpovědích JSON v následujících oddílech.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Každá předpovězená entita se reprezentuje jako pole.

Objekt `prediction.entities.<entity-name>` obsahuje pole, protože každou entitu lze v utterance odhadnout více než jednou. 

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

Při mapování mezi objektem `entities` na objekt `$instance` je pořadí objektů zachováno pro entitu seznamu předpovědi.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Místo názvu entity název role entity 

V v2 pole `entities` vrátilo všechny předpovězené entity s názvem entity, který je jedinečným identifikátorem. Pokud ve verzi V3 používá entita role a předpověď je pro roli entity, primární identifikátor je název role. To je možné, protože názvy rolí entit musí být v celé aplikaci jedinečné, včetně jiných názvů modelů (záměr, entita).

V následujícím příkladu: Zvažte utterance, který obsahuje text, `Yellow Bird Lane`. Tento text je předpovězen jako role `Location` vlastní entity `Destination`.

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

V v3 stejný výsledek s příznakem `verbose` pro vrácení metadat entity:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Externí entity předané v době předpovědi

Externí entity poskytují vaší aplikaci LUIS schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. To vám umožní používat vlastní extraktory samostatných a vlastních entit před odesláním dotazů do koncového bodu předpovědi. Vzhledem k tomu, že se jedná o koncový bod předpovědi dotazu, nemusíte model přeškolovat a publikovat.

Klientská aplikace poskytuje vlastní extrakci entit, která spravuje párování entit a určení umístění v rámci utterance odpovídající entity a odeslání těchto informací s požadavkem. 

Externí entity jsou mechanismus pro rozšíření libovolného typu entity, zatímco se pořád používají jako signály pro jiné modely, jako jsou role, kompozitní a jiné.

To je užitečné pro entitu, která má data dostupná pouze v modulu runtime předpovědi pro dotazování. Příklady tohoto typu dat neustále mění data nebo specifická pro jednotlivé uživatele. Entitu kontakt LUIS můžete v seznamu kontaktů uživatele roztáhnout externími informacemi. 

### <a name="entity-already-exists-in-app"></a>Entita už v aplikaci existuje.

Hodnota `entityName` pro externí entitu, která byla předaná v těle požadavku koncového bodu, musí existovat v době, kdy se žádost nastavila v vyškolené a publikované aplikaci. Typ entity nezáleží na podporovaných typech.

### <a name="first-turn-in-conversation"></a>Nejprve zapnout konverzaci

Zvažte první utterance v konverzaci robota chatu, kde uživatel zadá následující neúplné informace:

`Send Hazem a new message`

Požadavek z robota chatu na LUIS může předat informace v těle příspěvku o `Hazem`, aby se přímo shodovala s jedním z kontaktů uživatele.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpověď předpovědi zahrnuje tuto externí entitu se všemi ostatními předpovězenými entitami, protože je definována v žádosti.  

### <a name="second-turn-in-conversation"></a>Druhá změna konverzace

Další uživatel, který se utterance do robota chatu, používá Vague termín:

`Send him a calendar reminder for the party.`

V předchozím utterance používá utterance `him` jako odkaz na `Hazem`. Robot konverzačního chatu v těle příspěvku může mapovat `him` na hodnotu entity extrahovanou z prvního utterance, `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Odpověď předpovědi zahrnuje tuto externí entitu se všemi ostatními předpovězenými entitami, protože je definována v žádosti.  

### <a name="override-existing-model-predictions"></a>Přepsat existující předpovědi modelů

Vlastnost `preferExternalEntities` Options určuje, že pokud uživatel odešle externí entitu, která se překrývá s předpovězenou entitou se stejným názvem, LUIS zvolí entitu předanou nebo entitu existující v modelu. 

Zvažte například `today I'm free`dotazu. LUIS detekuje `today` jako datetimeV2 s následující odpovědí:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Pokud uživatel odešle externí entitu:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Pokud je `preferExternalEntities` nastavená na `false`, vrátí LUIS odpověď, jako kdyby nebyla Externí entita odeslána. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Pokud je `preferExternalEntities` nastavená na `true`, LUIS vrátí odpověď, včetně:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Rozlišení

_Nepovinná_ vlastnost `resolution` se vrátí v odpovědi předpovědi, což vám umožní předat Metadata přidružená k externí entitě a pak ji znovu získat v odpovědi. 

Hlavním účelem je rozšiřování předem sestavených entit, ale není omezen na tento typ entity. 

Vlastnost `resolution` může být číslo, řetězec, objekt nebo pole:

* Praha
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>V době předpovědi byly předány dynamické seznamy.

Dynamické seznamy umožňují rozšiřování existující entity vyškolené a publikované seznamu, která už je v aplikaci LUIS. 

Tuto funkci použijte, pokud se hodnoty entit seznamu musí pravidelně měnit. Tato funkce umožňuje rozšířenou již vyškolenou a publikovanou seznam entit:

* V době požadavku koncového bodu předpovědi dotazu.
* Pro jeden požadavek.

Entita seznamu může být v aplikaci LUIS prázdná, ale musí existovat. Entita seznamu v aplikaci LUIS se nezměnila, ale schopnost předpovědi na koncovém bodu se rozšíří tak, aby obsahovala až 2 seznamy s přibližně 1 000 položkami.

### <a name="dynamic-list-json-request-body"></a>Text požadavku JSON dynamického seznamu

Odeslat následující tělo JSON pro přidání nového podseznamu se synonymy do seznamu a předpovědět entitu seznamu pro text `LUIS`s požadavkem `POST` předpovědi dotazu:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Tato odpověď obsahuje entitu seznam se všemi ostatními předpovězenými entitami, protože je definována v žádosti. 

## <a name="deprecation"></a>Vyřazení 

Rozhraní v2 API nebude zastaralé po dobu nejméně 9 měsíců od verze V3 Preview. 

## <a name="next-steps"></a>Další kroky

Pomocí dokumentace k rozhraní V3 API aktualizujte existující volání REST na rozhraní API [koncového bodu](https://aka.ms/luis-api-v3) Luis. 
