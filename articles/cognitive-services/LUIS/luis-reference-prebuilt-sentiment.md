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
ms.date: 07/01/2020
ms.openlocfilehash: 2e8e5a127741625fde7910aaabd421836148fc35
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018663"
---
# <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurovaná analýza mínění, odpověď LUIS JSON zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci k [Analýza textu](../text-analytics/index.yml) .

LUIS používá Analýza textu v2. 

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