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
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90906015"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Vyžádané čtení Docker pro kontejner Extrakce klíčových frází

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte image kontejneru z Microsoft Container Registry.

Úplný popis dostupných značek pro kontejnery Analýza textu najdete v kontejneru [extrakce klíčových frází](https://go.microsoft.com/fwlink/?linkid=2018757) v Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
