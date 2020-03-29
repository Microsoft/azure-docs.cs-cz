---
title: Docker utažení pro kontejner analýzy mínění
titleSuffix: Azure Cognitive Services
description: Příkaz vyžádat docker pro kontejner Analýzy mínění
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966721"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker utažení pro kontejner analýzy mínění

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v kontejneru [analýzy mínění](https://go.microsoft.com/fwlink/?linkid=2018654) v centru Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
