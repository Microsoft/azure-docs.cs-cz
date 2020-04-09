---
title: Docker utažení pro kontejner analýzy mínění
titleSuffix: Azure Cognitive Services
description: Příkaz vyžádat docker pro kontejner Analýzy mínění
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876981"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker utažení pro kontejner analýzy mínění

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v kontejneru [analýzy mínění](https://go.microsoft.com/fwlink/?linkid=2018654) v centru Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
