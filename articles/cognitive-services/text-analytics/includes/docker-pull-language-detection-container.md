---
title: Vyžádané čtení Docker pro kontejner Rozpoznávání jazyka
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Rozpoznávání jazyka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051271"
---
## <a name="pull-the-language-detection-container"></a>Vyžádání kontejneru Rozpoznávání jazyka

Image kontejneru pro Analýza textu jsou k dispozici na Container Registry Microsoft.

| Kontejner | Název Container Registry/úložiště/image |
|-----------|------------|
| Rozpoznávání jazyka | `mcr.microsoft.com/azure-cognitive-services/language` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [rozpoznávání jazyka](https://go.microsoft.com/fwlink/?linkid=2018759) v Docker Hub.


### <a name="docker-pull-for-the-language-detection-container"></a>Vyžádané čtení Docker pro kontejner Rozpoznávání jazyka

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
