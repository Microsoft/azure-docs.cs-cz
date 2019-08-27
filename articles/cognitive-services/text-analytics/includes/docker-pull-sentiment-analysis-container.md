---
title: Vyžádané čtení Docker pro kontejner Analýza mínění
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Analýza mínění
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051393"
---
## <a name="pull-the-sentiment-analysis-container"></a>Vyžádání kontejneru Analýza mínění

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

| Kontejner | Název Container Registry/úložiště/image |
|-----------|------------|
| Analýza mínění | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [Analýza mínění](https://go.microsoft.com/fwlink/?linkid=2018654) v Docker Hub.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Vyžádané čtení Docker pro kontejner Analýza mínění

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
