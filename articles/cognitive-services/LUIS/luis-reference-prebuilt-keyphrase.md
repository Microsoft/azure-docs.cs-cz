---
title: Keyphrase předem vytvořená entita – LUIS
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje keyphrase předem připravené informace o entitách v Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e55c0453c117c51e5a8e4986631516d3e61ed10b
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677597"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase předem vytvořenou entitu pro aplikaci LUIS
Entita keyPhrase extrahuje různé klíčové fráze z utterance. Do aplikace nemusíte přidávat příklad projevy obsahující keyPhrase. Entita keyPhrase je v [mnoha jazykových verzích](luis-language-support.md#languages-supported) podporována jako součást funkcí pro [analýzu textu](../text-analytics/overview.md) . 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Řešení pro předem vytvořenou entitu keyPhrase

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Následující příklad ukazuje řešení entity **Builtin. keyPhrase** .

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```
#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Následující JSON má parametr `verbose` nastavený na `false`:

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ]
        }
    }
}
```

Následující JSON má parametr `verbose` nastavený na `true`:

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ],
            "$instance": {
                "keyPhrase": [
                    {
                        "type": "builtin.keyPhrase",
                        "text": "educational requirements",
                        "startIndex": 13,
                        "length": 24,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "builtin.keyPhrase",
                        "text": "development",
                        "startIndex": 51,
                        "length": 11,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```
* * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

Přečtěte si o [procentech](luis-reference-prebuilt-percentage.md), [číslech](luis-reference-prebuilt-number.md)a [stáří](luis-reference-prebuilt-age.md) entit.
