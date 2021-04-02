---
title: Vyžádané čtení Docker pro kontejner Rozpoznávání jazyka
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Rozpoznávání jazyka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906036"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Vyžádané čtení Docker pro kontejner Rozpoznávání jazyka

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [rozpoznávání jazyka](https://go.microsoft.com/fwlink/?linkid=2018759) v Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
