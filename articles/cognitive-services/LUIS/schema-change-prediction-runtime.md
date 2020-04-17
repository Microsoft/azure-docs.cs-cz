---
title: Rozšířit aplikaci za běhu – LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538575"
---
# <a name="extend-app-at-prediction-runtime"></a>Rozšíření aplikace za běhu předpovědi

Schéma aplikace (modely a funkce) je trénované a publikované do koncového bodu předpověď. Tento publikovaný model se používá v době běhu předpověď. Můžete předat nové informace, spolu s promluvy uživatele, do běhu předpověď rozšířit předpověď.

Dvě změny schématu běhu předpověď patří:
* [Externí subjekty](#external-entities)
* [Dynamické seznamy](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Externí subjekty

Externí entity poskytují vaší aplikaci LUIS možnost identifikovat a označovat entity za běhu, které lze použít jako funkce pro existující entity. To umožňuje použít vlastní samostatné a vlastní entity extraktory před odesláním dotazů do koncového bodu předpověď. Vzhledem k tomu, že se to provádí v koncovém bodě předpovědi dotazu, není nutné přeškolit a publikovat model.

Klientská aplikace poskytuje vlastní entitu extraktor tím, že spravuje entity odpovídající a určení umístění v rámci utterance této odpovídající entity a potom odesláním těchto informací s požadavkem.

Externí entity jsou mechanismem pro rozšíření libovolného typu entity, zatímco jsou stále používány jako signály pro jiné modely.

To je užitečné pro entitu, která má data k dispozici pouze za běhu předpověď dotazu. Příklady tohoto typu dat neustále mění data nebo konkrétní na uživatele. Můžete rozšířit kontaktní entitu LUIS s externími informacemi ze seznamu kontaktů uživatele.

Externí entity jsou součástí rozhraní API pro vytváření V3. Přečtěte si další informace o [migraci](luis-migration-api-v3.md) na tuto verzi.

### <a name="entity-already-exists-in-app"></a>Entita již v aplikaci existuje.

Hodnota pro `entityName` externí entitu, předané v těle požadavku koncového bodu POST, musí již existovat v trénované a publikované aplikace v době, kdy je požadavek. Na typu entity nezáleží, jsou podporovány všechny typy.

### <a name="first-turn-in-conversation"></a>První obrat v konverzaci

Zvažte první utterance v konverzaci chatovací robot, kde uživatel zadá následující neúplné informace:

`Send Hazem a new message`

Požadavek z chatu robotluis můžete předat informace `Hazem` v těle POST o tak, že je přímo uzavřeno jako jeden z kontaktů uživatele.

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

Předpověď odpověď zahrnuje tuto externí entitu, se všemi ostatními předpovídané entity, protože je definována v požadavku.

### <a name="second-turn-in-conversation"></a>Druhý tah v konverzaci

Další uživatel utterance do chatu bot používá více vágní termín:

`Send him a calendar reminder for the party.`

V tomto pořadí konverzace utterance `him` používá jako `Hazem`odkaz na . Konverzační chatovací robot v těle POST `him` může mapovat na hodnotu entity `Hazem`extrahované z první utterance .

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

Předpověď odpověď zahrnuje tuto externí entitu, se všemi ostatními předpovídané entity, protože je definována v požadavku.

### <a name="override-existing-model-predictions"></a>Přepsat existující předpovědi modelu

Vlastnost `preferExternalEntities` options určuje, že pokud uživatel odešle externí entitu, která se překrývá s předpokládanou entitou se stejným názvem, služba LUIS vybere předanou entitu nebo entitu existující v modelu.

Zvažte například `today I'm free`dotaz . Služba LUIS `today` zjistí jako datetimeV2 s následující odpovědí:

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

Pokud `preferExternalEntities` je nastavena na `false`, LUIS vrátí odpověď, jako kdyby externí entita nebyla odeslána.

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

Pokud `preferExternalEntities` je nastavena na `true`, LUIS vrátí odpověď, včetně:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Řešení

Volitelné _optional_ `resolution` vlastnost vrátí v odpovědi předpověď, což umožňuje předat metadata spojená s externí entitou, pak ji obdrží zpět v odpovědi.

Primárním účelem je rozšíření předem sestavených entit, ale není omezeno na tento typ entity.

Vlastnost `resolution` může být číslo, řetězec, objekt nebo pole:

* "Dallas"
* {"text": "hodnota"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dynamické seznamy

Dynamické seznamy umožňují rozšířit existující trénované a publikované seznam entity, již v aplikaci LUIS.

Tuto funkci použijte v případě, že hodnoty entit seznamu se musí pravidelně měnit. Tato funkce umožňuje rozšířit již vyškolenou a publikovanou entitu seznamu:

* V době požadavku koncového bodu předpověď dotazu.
* Pro jednu žádost.

Entita seznamu může být prázdná v aplikaci LUIS, ale musí existovat. Entita seznamu v aplikaci LUIS se nezmění, ale schopnost předpověď v koncovém bodě je rozšířena tak, aby zahrnovala až 2 seznamy s přibližně 1 000 položkami.

### <a name="dynamic-list-json-request-body"></a>Dynamický seznam tělo požadavku JSON

Odešlete následující tělo JSON přidat nový podseznam se synonymy do seznamu a `LUIS`předpovědět entitu seznamu pro text, , s požadavkem předpověď dotazu: `POST`

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

Odpověď předpověď zahrnuje tuto entitu seznamu se všemi ostatními předpovídanými entitami, protože je definována v požadavku.

## <a name="next-steps"></a>Další kroky

* [Skóre předpovědi](luis-concept-prediction-score.md)
* [Vytváření změn rozhraní API V3](luis-migration-api-v3.md)
