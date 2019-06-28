---
title: V2 na V3 rozhraní API migrace
titleSuffix: Azure Cognitive Services
description: Verze 3 koncový bod rozhraní API změnily. Tento průvodce vám pochopit, jak migrovat na verzi 3 koncový bod rozhraní API.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442523"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Verze Preview: Migrovat na verzi rozhraní API pro aplikace LUIS 3.x

Koncový bod dotazu predikcí rozhraní API změnily. Tento průvodce vám pochopit, jak migrovat na verzi 3 koncový bod rozhraní API. 

Toto rozhraní API V3 poskytuje následující nové funkce, mezi které patří významné změny požadavek nebo odpověď JSON: 

* [Externí entity](#external-entities-passed-in-at-prediction-time)
* [Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time)
* [Změny JSON předem připravených entit](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Koncový bod dotaz předpovědi [požadavek](#request-changes) a [odpovědi](#response-changes) mít významné změny pro podporu nové funkce uvedené výše, včetně následujících:

* [Změny objektu odpovědi](#top-level-json-changes)
* [Odkazy na entity role název místo názvu entity](#entity-role-name-instead-of-entity-name)
* [Vlastnosti označování entit v projevy](#marking-placement-of-entities-in-utterances)

Spadají následující funkce služby LUIS **nepodporuje** v rozhraní API V3:

* V7 kontrolu pravopisu Bingu

[Referenční dokumentace](https://aka.ms/luis-api-v3) je k dispozici pro V3.

## <a name="endpoint-url-changes-by-slot-name"></a>Změny adresy URL koncového bodu podle názvu slot

Formát volání koncového bodu HTTP V3 změnila.

|– METODA|zprostředkovatele identity|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>Změny adresy URL koncového bodu podle ID verze

Pokud chcete zadat dotaz podle verze, musíte nejprve [publikování přes rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) s `"directVersionPublish":true`. Dotazování na koncový bod odkazující na ID verze místo názvu slot.


|– METODA|zprostředkovatele identity|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Předem připravených entit pomocí nového kódu JSON

Změny objektu odpovědi V3 zahrnují [předem připravených entit](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Vyžádání změn 

### <a name="query-string-parameters"></a>Parametry řetězce dotazu

Rozhraní API V3 má parametry řetězce dotazu.

|Název parametru|Type|Version|Výchozí|Účel|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Query Store v souboru protokolu.| 
|`query`|string|Pouze v3|Žádná výchozí hodnota - je požadováno v požadavek GET|**Ve verzi V2**, probíhá utterance k předpovídat `q` parametr. <br><br>**Ve verzi 3**, je předáno funkci `query` parametru.|
|`show-all-intents`|boolean|Pouze v3|false|Vrátí všechny příkazy se odpovídající skóre v **prediction.intents** objektu. Příkazy se vrátí jako objekty v nadřazeném prvku `intents` objektu. To umožňuje programový přístup, aniž byste museli najít záměr v poli: `prediction.intents.give`. Ve verzi V2 tyto se vrátí ve formě pole. |
|`verbose`|boolean|V2 & V3|false|**Ve verzi V2**, když byly vráceny nastavenou na hodnotu true, všechny předpokládané záměry. Pokud budete potřebovat všechny předpokládané záměrů, použijte param V3 z `show-all-intents`.<br><br>**Ve verzi 3**, tento parametr pouze poskytuje entity metadata podrobné údaje o predikci entity.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Text dotazu předpovědi JSON pro `POST` žádosti

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

|Vlastnost|Type|Version|Výchozí|Účel|
|--|--|--|--|--|
|`dynamicLists`|array|Pouze v3|Není nutné.|[Dynamické seznamy](#dynamic-lists-passed-in-at-prediction-time) vám umožní rozšířit existující entity trénovaného a publikované seznam již v aplikaci LUIS.|
|`externalEntities`|array|Pouze v3|Není nutné.|[Externí entity](#external-entities-passed-in-at-prediction-time) aplikace LUIS umožnit k identifikaci a entity za běhu, který může sloužit jako funkce do existující entity. |
|`options.datetimeReference`|string|Pouze v3|žádná výchozí hodnota|Používá k určení [datetimeV2 posun](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Pouze v3|false|Určuje, pokud uživatele [externí entitu (se stejným názvem jako stávající entity)](#override-existing-model-predictions) se používá nebo stávající entity v modelu se používá k předpovědi. |
|`query`|string|Pouze v3|Povinná hodnota.|**Ve verzi V2**, probíhá utterance k předpovídat `q` parametr. <br><br>**Ve verzi 3**, je předáno funkci `query` parametru.|



## <a name="response-changes"></a>Změny odpovědi

Odpověď na dotaz JSON změní na povolit větší programový přístup k datům používá nejčastěji. 

### <a name="top-level-json-changes"></a>Změny na nejvyšší úrovni JSON

Hlavní vlastnosti JSON pro V2 nejsou, když `verbose` nastavena na hodnotu true, která vrátí všechny příkazy a jejich výsledky v `intents` vlastnost:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

Hlavní vlastnosti JSON pro V3 jsou:

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

`intents` Objekt je neuspořádaný seznam. Nepředpokládejte v prvním podřízeným objektem `intents` odpovídá `topIntent`. Místo toho použijte `topIntent` hodnota k vyhledání skóre:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Povolení změny schématu odpověď JSON:

* Vymazat rozdíl mezi původní utterance `query`a vrátí predikcí, `prediction`.
* Snazší programový přístup k datům předpokládané. Místo vytváření výčtů pole ve V2, můžete přístup k hodnoty podle **s názvem** záměry a entity. Pro prediktivní entity role se vrátí název role, protože byla jedinečná napříč celou aplikaci.
* Datové typy, je-li určena, jsou dodržovány. Numerické hodnoty se už vrátila jako řetězce.
* Vrátí rozdíl mezi první priority předpovědi informace a další metadata v `$instance` objektu. 

### <a name="access-instance-for-entity-metadata"></a>Přístup `$instance` pro metadata entit

Pokud potřebujete metadata entit, řetězec dotazu musí používat `verbose=true` příznak a odpovědi obsahuje metadata v `$instance` objektu. V odpovědi JSON v následujících částech jsou uvedeny příklady.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Každý prediktivní entity je vyjádřena jako pole

`prediction.entities.<entity-name>` Objekt obsahuje pole, protože každá entita může předpovídat více než jednou v utterance. 

### <a name="list-entity-prediction-changes"></a>Seznam entit předpověď změn

JSON pro entity predikcí seznamu se změní, aby byla pole polí:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Každé vnitřní pole odpovídá textu uvnitř utterance. Vnitřní objekt je pole, protože stejný text mohou objevit ve více než jeden dílčí seznam seznam entit. 

Při mapování mezi `entities` objektu `$instance` objektu, zachování pořadí objektů pro předpovědi seznam entit.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Název entity role místo názvu entity 

Ve verzi V2 `entities` pole vrácené prediktivní entity s názvem entity je jedinečný identifikátor. Ve verzi 3 Pokud entita používá role a do predikce. je pro roli entity je identifikátor primární název role. To je možné, protože role názvy entit musí být jedinečné v celé aplikaci, včetně další názvy modelu (záměru, entity).

V následujícím příkladu: Zvažte utterance, který obsahuje text, `Yellow Bird Lane`. Tento text očekává se, že jako vlastní `Location` entity role `Destination`.

|Utterance text|Název entity|Název role|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Ve verzi 2 je identifikovaná entita _název entity_ s rolí jako vlastnost v objektu:

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

Ve verzi 3, entita odkazuje _entity role_, pokud je do predikce. pro roli:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Ve verzi 3, výsledek s `verbose` příznak, který vrátí metadata entit:

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

## <a name="external-entities-passed-in-at-prediction-time"></a>Externí entity předaný v době predikcí

Externí entity umožnit aplikace LUIS k identifikaci a entity za běhu, který může sloužit jako funkce do existující entity. To umožňuje používat vlastní samostatné a vlastní entity – extraktory před odesláním dotazy do vašeho koncového bodu předpovědi. Protože to se provádí v koncovém bodě dotaz předpovědi, není nutné přetrénujete a publikování vašeho modelu.

Klientská aplikace poskytuje své vlastní Extraktor entity pomocí správy odpovídající entity a určuje se umístění v rámci utterance odpovídající entity a potom odešlete těchto informací s požadavkem. 

Externí entity jsou mechanismus pro rozšíření libovolný typ entity stále používán jako signály s jinými modely, jako jsou role, složený a dalších.

To je užitečné pro entitu, která obsahuje data k dispozici pouze za běhu dotazu předpovědi. Příkladem tento typ dat se neustále mění data nebo specifické pro jednotlivé uživatele. Můžete rozšířit na LUIS kontaktní entitu s externí informace ze seznamu kontaktů uživatele. 

### <a name="entity-already-exists-in-app"></a>Entita již existuje v aplikaci

Hodnota `entityName` pro externí entitu předané v textu POST požadavku koncového bodu už musí existovat ve trénovaného a publikované aplikace v době se požadavek. Typ entity, nezáleží, jsou podporovány všechny typy.

### <a name="first-turn-in-conversation"></a>Nejdříve zapněte konverzace

Vezměte v úvahu první utterance bot konverzace chatu ve kterém uživatel zadá následující neúplné informace:

`Send Hazem a new message`

Žádost od chatovací robot k LUIS můžete předat informace v textu POST o `Hazem` tak spárován přímo mezi kontakty uživatele.

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

Předpověď odpovědi obsahuje této externí entity, všechny ostatní prediktivní entity, protože je definována v požadavku.  

### <a name="second-turn-in-conversation"></a>Za druhé zapnout v konverzace

Další utterance uživatele do chatovací robot používá termín víc vágní:

`Send him a calendar reminder for the party.`

V předchozím utterance utterance používá `him` jako odkaz na `Hazem`. Můžete namapovat konverzační chatovací robot, v textu POST `him` hodnotě entity extrahují z první utterance `Hazem`.

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

Předpověď odpovědi obsahuje této externí entity, všechny ostatní prediktivní entity, protože je definována v požadavku.  

### <a name="override-existing-model-predictions"></a>Přepsat existující předpovědí modelu

`overridePredictions` Možnosti vlastnost určuje, že pokud uživatel odešle externí entita, která se překrývá s prediktivní entity se stejným názvem, LUIS zvolí entity předaný nebo entity v modelu. 

Představte si třeba dotaz `today I'm free`. Služba LUIS zjistí `today` jako datetimeV2 s následující odpověď:

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

Pokud `overridePredictions` je nastavena na `false`, LUIS vrátí odpověď, jakoby nebyly odeslány externí entity. 

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

Pokud `overridePredictions` je nastavena na `true`, LUIS, vrátí odpověď včetně:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Řešení

_Volitelné_ `resolution` vrátí vlastnost v předpovědi odpověď, a to díky tomu umožňuje předat metadata přidružená k externí entitu a pak ji přijmou vrátit v odpovědi. 

Hlavním účelem je rozšířit předem připravených entit, ale není omezena pouze na tento typ entity. 

`resolution` Vlastnost může být číslo, řetězec, objekt nebo pole:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamické seznamy předaný v době predikcí

Dynamické seznamy umožňují rozšířit existující entity trénovaného a publikované seznam již v aplikaci LUIS. 

Tuto funkci používejte, když hodnoty seznamu entity musí se pravidelně mění. Tato funkce umožňuje rozšířit seznam již trénovaného a publikované entity:

* V době dotazu požadavku koncového bodu předpovědi.
* Pro jeden požadavek.

Seznam entit může být v aplikaci LUIS prázdné ale musí existovat. Seznam entit v aplikaci LUIS se nezmění, ale možnost predikce v koncovém bodě je rozšířit až 2 seznamy s přibližně 1 000 položek.

### <a name="dynamic-list-json-request-body"></a>Seznam dynamického obsahu požadavku JSON

Poslat v textu následující JSON do seznamu přidat nový dílčí seznam s synonym a předpovídat entitou seznamu pro text `LUIS`, se `POST` žádost o předpověď dotazu:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
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

Předpověď odpovědi obsahuje této entity seznamu, všechny ostatní prediktivní entity, protože je definována v požadavku. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset přejmenován na datetimeReference

**Ve verzi V2**, `timezoneOffset` [parametr](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) se posílá ve žádost o předpověď jako parametru řetězce dotazu, bez ohledu na to v případě požadavku je odeslaný jako požadavek GET nebo POST. 

**Ve verzi 3**, stejná funkčnost zajištěna pomocí parametru text příspěvku `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Označení umístění entit v projevy

**Ve verzi V2**, entita byla označena v utterance s `startIndex` a `endIndex`. 

**Ve verzi 3**, je označena entity `startIndex` a `entityLength`.

## <a name="deprecation"></a>Vyřazení 

Rozhraní API V2 nebude přestanou používat po dobu alespoň 9 měsíců po V3 ve verzi preview. 

## <a name="next-steps"></a>Další postup

Použijte dokumentaci rozhraní V3 API aktualizovat existující REST volání LUIS [koncový bod](https://aka.ms/luis-api-v3) rozhraní API. 