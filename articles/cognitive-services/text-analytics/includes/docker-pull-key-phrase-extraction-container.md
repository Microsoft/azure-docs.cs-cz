---
title: Vyžádané čtení Docker pro kontejner Extrakce klíčových frází
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro kontejner Extrakce klíčových frází
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876987"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Vyžádané čtení Docker pro kontejner Extrakce klíčových frází

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757) v Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
