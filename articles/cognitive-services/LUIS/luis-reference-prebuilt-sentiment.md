---
title: Analýza mínění – LUIS
titleSuffix: Azure Cognitive Services
description: Pokud je nakonfigurovaná analýza mínění, LUIS odpověď json zahrnuje analýzu subjektivního hodnocení.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270425"
---
# <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurovaná analýza mínění, LUIS odpověď json zahrnuje analýzu subjektivního hodnocení. Další informace o analýze mínění najdete v dokumentaci k [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .


## <a name="resolution-for-sentiment"></a>Řešení pro mínění

Je skóre mezi 1 a 0 označující pozitivní mínění data (blíže 1) ani na zápornou (blíže 0) mínění data.

#### <a name="english-language"></a>[Anglický jazyk](#tab/english)

Když je jazyková verze `en-us`, odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Jiné jazyky](#tab/other-languages)

Pro všechny jiné jazykové verze odpověď je:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

