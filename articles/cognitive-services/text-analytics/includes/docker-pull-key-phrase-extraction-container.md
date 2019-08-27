---
title: Vyžádané čtení Docker pro kontejner Extrakce klíčových frází
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Extrakce klíčových frází
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051294"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Vyžádání kontejneru Extrakce klíčových frází

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

| Kontejner | Název Container Registry/úložiště/image |
|-----------|------------|
| Extrakce klíčových frází | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757) v Docker Hub.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Vyžádané čtení Docker pro kontejner Extrakce klíčových frází

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
