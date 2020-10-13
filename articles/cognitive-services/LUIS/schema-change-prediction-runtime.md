---
title: Rozšiřování aplikace za běhu – LUIS
description: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 69e2608fb01ece81f555aae2f3d4a2e4a05cfc90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322797"
---
# <a name="extend-app-at-prediction-runtime"></a>Rozšiřování aplikace v prostředí předpovědi

Schéma aplikace (modely a funkce) je vyškolené a publikované do koncového bodu předpovědi. Tento publikovaný model se používá v modulu runtime předpovědi. K rozšíření předpovědi můžete předat nové informace spolu s utterance uživatele do předpovědi runtime.

Mezi dvě předpovědi běhových součástí schématu patří:
* [Externí entity](#external-entities)
* [Dynamické seznamy](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externí entity

Externí entity poskytují vaší aplikaci LUIS schopnost identifikovat a označovat entity za běhu, které se dají použít jako funkce pro stávající entity. To vám umožní používat vlastní extraktory samostatných a vlastních entit před odesláním dotazů do koncového bodu předpovědi. Vzhledem k tomu, že se jedná o koncový bod předpovědi dotazu, nemusíte model přeškolovat a publikovat.

Klientská aplikace poskytuje vlastní extrakci entit, která spravuje párování entit a určení umístění v rámci utterance odpovídající entity a odeslání těchto informací s požadavkem.

Externí entity jsou mechanismus pro rozšíření libovolného typu entity, zatímco se pořád používají jako signály pro jiné modely.

To je užitečné pro entitu, která má data dostupná pouze v modulu runtime předpovědi pro dotazování. Příklady tohoto typu dat neustále mění data nebo specifická pro jednotlivé uživatele. Entitu kontakt LUIS můžete v seznamu kontaktů uživatele roztáhnout externími informacemi.

Externí entity jsou součástí rozhraní API pro vytváření obsahu v3. Přečtěte si další informace o [migraci](luis-migration-api-v3.md) na tuto verzi.

### <a name="entity-already-exists-in-app"></a>Entita už v aplikaci existuje.

Hodnota `entityName` pro externí entitu, která byla předána tělo požadavku koncového bodu, musí v době odeslání žádosti existovat v vyškolené a publikované aplikaci. Typ entity nezáleží na podporovaných typech.

### <a name="first-turn-in-conversation"></a>Nejprve zapnout konverzaci

Zvažte první utterance v konverzaci robota chatu, kde uživatel zadá následující neúplné informace:

`Send Hazem a new message`

Požadavek z robota chatu na LUIS může předat informace v těle příspěvku, `Hazem` aby se přímo shodoval jako jeden z kontaktů uživatele.

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

V tomto případě se v rámci konverzace používá utterance `him` jako odkaz na `Hazem` . Robot konverzačního chatu v těle příspěvku může mapovat `him` na hodnotu entity extrahovanou z prvního utteranceu, `Hazem` .

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

`preferExternalEntities`Vlastnost Options určuje, že pokud uživatel odešle externí entitu, která se překrývá s předpovězenou entitou se stejným názvem, Luis zvolí entitu předanou nebo entitu existující v modelu.

Zvažte například dotaz `today I'm free` . LUIS `today` se detekuje jako datetimeV2 s následující odpovědí:

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

Pokud `preferExternalEntities` je nastaven na `false` , vrátí Luis odpověď, jako kdyby nebyla odeslána Externí entita.

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

Pokud `preferExternalEntities` je nastaven na `true` , vrátí Luis odpověď, včetně:

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

`resolution`Vlastnost může být číslo, řetězec, objekt nebo pole:

* Praha
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamické seznamy

Dynamické seznamy umožňují rozšiřování existující entity vyškolené a publikované seznamu, která už je v aplikaci LUIS.

Tuto funkci použijte, pokud se hodnoty entit seznamu musí pravidelně měnit. Tato funkce umožňuje rozšířenou již vyškolenou a publikovanou seznam entit:

* V době požadavku koncového bodu předpovědi dotazu.
* Pro jeden požadavek.

Entita seznamu může být v aplikaci LUIS prázdná, ale musí existovat. Entita seznamu v aplikaci LUIS se nezměnila, ale schopnost předpovědi na koncovém bodu se rozšíří tak, aby obsahovala až 2 seznamy s přibližně 1 000 položkami.

### <a name="dynamic-list-json-request-body"></a>Text požadavku JSON dynamického seznamu

Odeslat následující tělo JSON pro přidání nového podseznamu se synonymy do seznamu a předpověď entity seznamu pro text `LUIS` s `POST` požadavkem na předpověď dotazu:

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

## <a name="next-steps"></a>Další kroky

* [Skóre předpovědi](luis-concept-prediction-score.md)
* [Vytváření změn rozhraní API V3](luis-migration-api-v3.md)
