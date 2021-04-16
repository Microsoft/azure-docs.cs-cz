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
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564440"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Získání Docker pro kontejner Analýza mínění V3

Kontejner mínění Analysis kontejneru v3 je k dispozici v několika jazycích. Ke stažení kontejneru pro balíček English použijte následující příkaz. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Chcete-li stáhnout kontejner pro jiný jazyk, nahraďte `en` jedním z kódů jazyka níže. 

| Analýza textu kontejner | Kód jazyka |
|--|--|
| Čínština (zjednodušená)    |   `zh-hans`   |
| Čínština (tradiční)   |   `zh-hant`   |
| Nizozemština                 |     `nl`      |
| Angličtina               |     `en`      |
| Francouzština                |     `fr`      |
| Němčina                |     `de`      |
| Hindština                 |    `hi`       |
| Italština               |     `it`      |
| Japonština              |     `ja`      |
| Korejština                |     `ko`      |
| Norština (Bokmål)   |     `no`      |
| Portugalština (Brazílie)   |    `pt-BR`    |
| portugalština (Portugalsko) |    `pt-PT`    |
| Španělština               |     `es`      |
| Turečtina               |     `tr`      |

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v tématu [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
