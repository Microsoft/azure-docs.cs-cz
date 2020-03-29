---
title: Docker u kontejneru detekce jazyka
titleSuffix: Azure Cognitive Services
description: Příkaz pro vyžádat docker pro kontejner detekce jazyka
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966690"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker u kontejneru detekce jazyka

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v kontejneru [rozpoznávání jazyků](https://go.microsoft.com/fwlink/?linkid=2018759) v centru Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
