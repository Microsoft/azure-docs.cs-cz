---
title: Definice schématu aplikace
description: Aplikace LUIS je reprezentovaná v rámci `.json` nebo `.lu` zahrnuje všechny záměry, entity, příklad projevy, funkce a nastavení.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: dbbeb4eacbe8e07cf080b3a1527ca39c2b9a5991
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684357"
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
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

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