---
title: Analýza mínění – LUIS
titleSuffix: Azure Cognitive Services
description: Pokud je nakonfigurovaná analýza mínění, odpověď LUIS JSON zahrnuje analýzu mínění.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554021"
---
# <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurovaná analýza mínění, odpověď LUIS JSON zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci k [Analýza textu](../text-analytics/index.yml) .

LUIS používá Analýza textu v2. 

Analýza mínění je nakonfigurována při publikování aplikace. Další informace najdete v tématu [o publikování aplikace](./luis-how-to-publish-app.md) .

## <a name="resolution-for-sentiment"></a>Řešení pro mínění

Mínění data jsou skóre mezi 1 a 0 značící kladné (blíže k 1) nebo záporné (navýšení na 0) mínění dat.

#### <a name="english-language"></a>[Anglický jazyk](#tab/english)

Pokud je jazyková verze `en-us` , odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Další jazyky](#tab/other-languages)

Pro všechny ostatní jazykové verze je odpověď:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).
