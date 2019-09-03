---
title: Migrace rozhraní API v2 na V3
titleSuffix: Azure Cognitive Services
description: Rozhraní API koncového bodu verze 3 se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213511"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Verze Preview: Migrace na rozhraní API verze 3. x pro aplikace LUIS

Rozhraní API koncového bodu předpovědi dotazu se změnila. Tento průvodce vám pomůže pochopit, jak migrovat na rozhraní API koncového bodu verze 3. 

Toto rozhraní API v3 nabízí následující nové funkce, které zahrnují významné změny požadavků JSON a/nebo odpovědí: 

* [Externí entity](#external-entities-passed-in-at-prediction-time)
* [Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time)
* [Předem připravené změny JSON entity](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

[Požadavek](#request-changes) a [odpověď](#response-changes) koncového bodu předpovědi dotazu mají významné změny pro podporu nových funkcí uvedených výše, včetně následujících:

* [Změny objektu odpovědi](#top-level-json-changes)
* [Odkazy na název role entity místo názvu entity](#entity-role-name-instead-of-entity-name)
* [Vlastnosti k označení entit v projevy](#marking-placement-of-entities-in-utterances)

V rozhraní V3 API nejsou **podporovány** následující funkce Luis:

* Kontrola pravopisu Bingu v7

[Referenční dokumentace](https://aka.ms/luis-api-v3) je k dispozici pro v3.

## <a name="endpoint-url-changes-by-slot-name"></a>Změny adresy URL koncového bodu podle názvu slotu

Byl změněn formát volání rozhraní HTTP koncového bodu v3.

|METODA|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

Platné hodnoty pro Sloty:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>Změny adresy URL koncového bodu podle ID verze

Pokud se chcete dotazovat podle verze, musíte nejdřív [publikovat přes rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) pomocí `"directVersionPublish":true`. Dotaz na koncový bod odkazující na ID verze místo názvu slotu.


|METODA|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Předem připravené entity s novým kódem JSON

Změny objektu odpovědi V3 obsahují [předem připravené entity](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Vyžádání změn 

### <a name="query-string-parameters"></a>Parametry řetězce dotazu

Rozhraní API V3 má jiné parametry řetězce dotazu.

|Název param|type|Version|Výchozí|Účel|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Dotaz na uložení v souboru protokolu.| 
|`query`|řetězec|Jenom V3|Žádná výchozí hodnota – v žádosti GET se vyžaduje.|**V v2**je utterance, který má být předpovězen, v `q` parametru. <br><br>**V v3**je funkce předána do `query` parametru.|
|`show-all-intents`|boolean|Jenom V3|false|Vrátí všechny záměry s odpovídajícím skóre v objektu **předpověď. záměrs** . Záměry jsou vráceny jako objekty v nadřazeném `intents` objektu. To umožňuje programový přístup bez nutnosti najít záměr v poli: `prediction.intents.give`. V v2 byly vráceny v poli. |
|`verbose`|boolean|V2 & V3|false|**V v2**při nastavení na hodnotu true byly vráceny všechny předpovězené záměry. Pokud potřebujete všechny předpovídané záměry, použijte parametr `show-all-intents`v3.<br><br>**V v3**tento parametr poskytuje pouze podrobnosti o metadatech entit pro předpovědi entit.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Tělo JSON pro `POST` požadavek na předpověď dotazu

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Vlastnost|type|Version|Výchozí|Účel|
|--|--|--|--|--|
|`dynamicLists`|array|Jenom V3|Není nutné.|[Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time) umožňují rozšiřování existující entity vyškolené a publikované seznamu, která už je v aplikaci Luis.|
|`externalEntities`|array|Jenom V3|Není nutné.|[Externí entity](#external-entities-passed-in-at-prediction-time) poskytují vaší aplikaci Luis schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. |
|`options.datetimeReference`|řetězec|Jenom V3|Žádná výchozí|Používá se k určení [posunu datetimeV2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Formát pro datetimeReference je [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.overridePredictions`|boolean|Jenom V3|false|Určuje, jestli se použije [Externí entita uživatele (se stejným názvem jako existující entita)](#override-existing-model-predictions) , nebo jestli se existující entita v modelu používá pro předpověď. |
|`query`|řetězec|Jenom V3|Povinný parametr.|**V v2**je utterance, který má být předpovězen, v `q` parametru. <br><br>**V v3**je funkce předána do `query` parametru.|



## <a name="response-changes"></a>Změny odpovědí

Formát JSON odpovědi na dotaz byl změněn tak, aby umožňoval rychlejší programový přístup k datům použitým nejčastěji. 

### <a name="top-level-json-changes"></a>Změny JSON nejvyšší úrovně

Nejvyšší vlastnosti JSON pro v2 jsou, pokud `verbose` je nastaveno na hodnotu true, která vrátí všechny záměry a jejich skóre `intents` ve vlastnosti:

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
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

`intents` Objekt je neuspořádaný seznam. Nepředpokládáme, že první podřízená `intents` položka v odpovídá. `topIntent` Místo toho použijte `topIntent` hodnotu k nalezení skóre:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Změny schématu JSON odpovědi umožňují:

* Jasně rozlišovat mezi původními utterance `query`, a vrácenou `prediction`předpověď.
* Jednodušší programový přístup k předpokládaným datům. Místo vytváření výčtu pomocí pole ve verzi v2 můžete získat přístup k hodnotám podle **názvu** pro oba záměry a entity. U předpokládaných rolí entit se vrátí název role, protože je v celé aplikaci jedinečný.
* Jsou respektovány datové typy, pokud jsou určeny. Číselné znaky již nejsou vraceny jako řetězce.
* Rozdíl mezi informacemi o předpovědi první priority a dalšími metadaty, které `$instance` jsou vráceny v objektu. 

### <a name="access-instance-for-entity-metadata"></a>Přístup `$instance` k metadatům entit

Pokud potřebujete metadata entity, řetězec dotazu musí používat `verbose=true` příznak a odpověď obsahuje metadata `$instance` v objektu. Příklady jsou uvedeny v odpovědích JSON v následujících oddílech.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Každá předpovězená entita se reprezentuje jako pole.

`prediction.entities.<entity-name>` Objekt obsahuje pole, protože každou entitu lze v utterance předpovědět více než jednou. 

### <a name="list-entity-prediction-changes"></a>Vypsat změny předpovědi entit

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

Při mapování mezi `entities` objektem `$instance` na objekt je pořadí objektů zachované pro entitu seznamu předpovědi.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Místo názvu entity název role entity 

V v2 `entities` pole vrátilo všechny předpovězené entity s názvem entity, který je jedinečný identifikátor. Pokud ve verzi V3 používá entita role a předpověď je pro roli entity, primární identifikátor je název role. To je možné, protože názvy rolí entit musí být v celé aplikaci jedinečné, včetně jiných názvů modelů (záměr, entita).

V následujícím příkladu: Zvažte utterance, který obsahuje text, `Yellow Bird Lane`. Tento text je předpovězen jako `Location` `Destination`role vlastní entity.

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Externí entity předané v době předpovědi

Externí entity poskytují vaší aplikaci LUIS schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. To vám umožní používat vlastní extraktory samostatných a vlastních entit před odesláním dotazů do koncového bodu předpovědi. Vzhledem k tomu, že se jedná o koncový bod předpovědi dotazu, nemusíte model přeškolovat a publikovat.

Klientská aplikace poskytuje vlastní extrakci entit, která spravuje párování entit a určení umístění v rámci utterance odpovídající entity a odeslání těchto informací s požadavkem. 

Externí entity jsou mechanismus pro rozšíření libovolného typu entity, zatímco se pořád používají jako signály pro jiné modely, jako jsou role, kompozitní a jiné.

To je užitečné pro entitu, která má data dostupná pouze v modulu runtime předpovědi pro dotazování. Příklady tohoto typu dat neustále mění data nebo specifická pro jednotlivé uživatele. Entitu kontakt LUIS můžete v seznamu kontaktů uživatele roztáhnout externími informacemi. 

### <a name="entity-already-exists-in-app"></a>Entita už v aplikaci existuje.

Hodnota `entityName` pro externí entitu, která byla předána tělo požadavku koncového bodu, musí v době odeslání žádosti existovat v vyškolené a publikované aplikaci. Typ entity nezáleží na podporovaných typech.

### <a name="first-turn-in-conversation"></a>Nejprve zapnout konverzaci

Zvažte první utterance v konverzaci robota chatu, kde uživatel zadá následující neúplné informace:

`Send Hazem a new message`

Požadavek z robota chatu na Luis může předat informace v těle `Hazem` příspěvku, aby se přímo shodoval jako jeden z kontaktů uživatele.

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

V předchozím utterance se utterance používá `him` jako odkaz na. `Hazem` Robot konverzačního chatu v těle příspěvku může mapovat `him` na hodnotu entity extrahovanou z prvního utteranceu,. `Hazem`

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

Vlastnost `overridePredictions` Options určuje, že pokud uživatel odešle externí entitu, která se překrývá s předpovězenou entitou se stejným názvem, Luis zvolí entitu předanou nebo entitu existující v modelu. 

Zvažte například dotaz `today I'm free`. Luis se `today` detekuje jako datetimeV2 s následující odpovědí:

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

Pokud je nastaven na `false`, vrátí Luis odpověď, jako kdyby nebyla odeslána Externí entita. `overridePredictions` 

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

Pokud je nastaven na `true`, vrátí Luis odpověď, včetně: `overridePredictions`

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Řešení

_Volitelná_ `resolution` vlastnost se vrátí v odpovědi předpovědi, což vám umožní předat Metadata přidružená k externí entitě a pak ji znovu získat v odpovědi. 

Hlavním účelem je rozšiřování předem sestavených entit, ale není omezen na tento typ entity. 

`resolution` Vlastnost může být číslo, řetězec, objekt nebo pole:

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

Odeslat následující tělo JSON pro přidání nového podseznamu se synonymy do seznamu a předpověď entity seznamu pro text `LUIS`s požadavkem na `POST` předpověď dotazu:

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

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset přejmenováno na datetimeReference

**V v2** `timezoneOffset` je [parametr](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) odeslán v požadavku předpovědi jako parametr řetězce dotazu bez ohledu na to, zda je požadavek odeslán jako požadavek GET nebo post. 

**V v3**jsou stejné funkce k dispozici s parametrem `datetimeReference`tělo příspěvku. 

## <a name="marking-placement-of-entities-in-utterances"></a>Označení umístění entit v projevy

**V v2**byla entita označena v utterance s `startIndex` a. `endIndex` 

**V v3**je entita označena pomocí `startIndex` a. `entityLength`

## <a name="deprecation"></a>Vyřazení 

Rozhraní v2 API nebude zastaralé po dobu nejméně 9 měsíců od verze V3 Preview. 

## <a name="next-steps"></a>Další postup

Pomocí dokumentace k rozhraní V3 API aktualizujte existující volání REST na rozhraní API [koncového bodu](https://aka.ms/luis-api-v3) Luis. 
