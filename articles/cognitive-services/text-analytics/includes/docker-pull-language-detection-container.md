---
title: Docker u kontejneru detekce jazyka
titleSuffix: Azure Cognitive Services
description: Příkaz pro vyžádat docker pro kontejner detekce jazyka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876985"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker u kontejneru detekce jazyka

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou kopii kontejneru z registru kontejnerů společnosti Microsoft.

Úplný popis dostupných značek pro kontejnery Analýzy textu najdete v kontejneru [rozpoznávání jazyků](https://go.microsoft.com/fwlink/?linkid=2018759) v centru Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
