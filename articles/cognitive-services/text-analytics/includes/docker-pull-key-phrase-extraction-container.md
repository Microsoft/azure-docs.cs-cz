---
title: Docker vytáhnout pro kontejner extrakce frází klíče
titleSuffix: Azure Cognitive Services
description: Příkaz pro vytažení klíčových frází pro kontejner extrakce klíčových frází
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966772"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker vytáhnout pro kontejner extrakce frází klíče

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v tématu kontejner [extrakce frází klíče](https://go.microsoft.com/fwlink/?linkid=2018757) v dockerhubu.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
