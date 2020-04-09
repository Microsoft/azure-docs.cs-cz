---
title: Docker vytáhnout pro kontejner extrakce frází klíče
titleSuffix: Azure Cognitive Services
description: Příkaz pro vytažení klíčových frází pro kontejner extrakce klíčových frází
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876987"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker vytáhnout pro kontejner extrakce frází klíče

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v tématu kontejner [extrakce frází klíče](https://go.microsoft.com/fwlink/?linkid=2018757) v dockerhubu.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
