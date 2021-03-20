---
title: Definice schématu aplikace
description: Aplikace LUIS je reprezentovaná v rámci `.json` nebo `.lu` zahrnuje všechny záměry, entity, příklad projevy, funkce a nastavení.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: db4fd52dad82542f20e58ebb3b8526c5be7f2f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327319"
---
# <a name="app-schema-definition"></a>Definice schématu aplikace

Aplikace LUIS je reprezentovaná v rámci `.json` nebo `.lu` zahrnuje všechny záměry, entity, příklad projevy, funkce a nastavení.

## <a name="format"></a>Formát

Při importu a exportu aplikace vyberte možnost `.json` nebo `.lu` .

|Formát|Informace|
|--|--|
|`.json`| Standardní programovací formát|
|`.lu`|Podporováno [bot Builder nástroji](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)rozhraní bot Framework.|

## <a name="version-7x"></a>Verze 7. x

* Přesun na verzi 7. x, entity jsou reprezentovány jako vnořené entity strojového učení.
* Podpora vytváření vnořených entit strojového učení s `enableNestedChildren` vlastností u následujících rozhraní API pro vytváření obsahu:
    * [Přidání popisku](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Přidat popisek dávky](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Zkontrolovat popisky](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Navrhnout dotazy na koncový bod pro entity](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Navrhnout dotazy koncového bodu pro záměry](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

|  – element                  | Komentář                              |
|--------------------------|--------------------------------------|
| "hierarchické": [],     | Zastaralé, použijte [entity strojového učení](luis-concept-entity-types.md).   |
| "složené": [],        | Zastaralé, použijte [entity strojového učení](luis-concept-entity-types.md). Odkaz na [složenou entitu](reference-entity-composite.md) |
| "closedLists": [],       | [Seznam](reference-entity-list.md) odkazů na entity, které se primárně používají jako funkce entit.    |
| "versionId": "0,1",      | Verze aplikace LUIS|
| "název": "priklad-App";   | Název aplikace LUIS |
| "desc": "",              | Volitelný popis aplikace LUIS  |
| "Culture": "en-US",      | [Jazyk](luis-language-support.md) aplikace, ovlivňuje základní funkce, jako jsou předem připravené entity, strojové učení a provádějících tokenizaci.  |
| "tokenizerVersion": "1.0.0", | [Provádějících tokenizaci](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Entita Pattern.any](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [Entiay regulárního výrazu](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [Seznamy frází (funkce)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  Zastaralé, použijte [entity strojového učení](luis-concept-entity-types.md). |
| "vzory": [],          |  [Vzorce zlepšují přesnost předpovědi](luis-concept-patterns.md) pomocí [syntaxe vzoru](reference-pattern-syntax.md)   |
| "nastavení": []           | [Nastavení aplikace](luis-reference-application-settings.md)|

## <a name="version-6x"></a>Verze 6. x

* Přesun do verze 6. x použijte novou [entitu strojového učení](reference-entity-machine-learned-entity.md) , která bude představovat vaše entity.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Verze 4. x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Další kroky

* Migrace na [rozhraní API pro vytváření obsahu V3](luis-migration-authoring-entities.md)