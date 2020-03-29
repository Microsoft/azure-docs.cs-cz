---
title: Analýza mínění - LUIS
titleSuffix: Azure Cognitive Services
description: Pokud je nakonfigurována analýza mínění, odpověď LUIS json zahrnuje analýzu mínění.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270425"
---
# <a name="sentiment-analysis"></a>Analýza mínění
Pokud je nakonfigurována analýza mínění, odpověď LUIS json zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci [k Analýze textu.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Rozlišení pro sentiment

Data mínění je skóre mezi 1 a 0 označující kladné (blíže k 1) nebo negativní (blíže k 0) mínění dat.

#### <a name="english-language"></a>[Anglický jazyk](#tab/english)

Pokud je `en-us`jazyková verze , odpověď je:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Další jazyky](#tab/other-languages)

Pro všechny ostatní kultury je odpověď:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Další kroky

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

