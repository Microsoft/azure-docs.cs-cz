---
title: Vyžádané čtení Docker pro kontejner Analýza mínění
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Analýza mínění
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588368"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Získání Docker pro kontejner Analýza mínění V3

Kontejner mínění Analysis kontejneru v3 je k dispozici v několika jazycích. Ke stažení kontejneru pro balíček English použijte následující příkaz. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Chcete-li stáhnout kontejner pro jiný jazyk, nahraďte `en` jedním z kódů jazyka níže. 

| Analýza textu kontejner | Kód jazyka |
|--|--|
| Angličtina | `en` |
| Španělština | `es` |
| Francouzština | `fr` |
| Italština | `it` |
| Němčina | `de` |
| Čínština (zjednodušená) | `zh` |
| Čínština (tradiční) | `zht` |
| Japonština | `ja` |
| Portugalština | `pt` |
| Nizozemština | `nl` |

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v tématu [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).