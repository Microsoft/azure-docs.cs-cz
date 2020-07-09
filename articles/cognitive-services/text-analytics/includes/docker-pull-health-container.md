---
title: Vyžádané čtení Docker pro kontejner stavu
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro Analýza textu pro kontejner stavu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108908"
---
Vyplňte a odešlete [formulář žádosti o Cognitive Services kontejnery](https://aka.ms/cognitivegate) , který bude požadovat přístup ke kontejneru.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Pomocí příkazu Docker Login s přihlašovacími údaji, které jste zadali v e-mailu pro registraci, se připojte k našemu soukromému registru kontejnerů pro kontejnery Cognitive Services.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte tuto image kontejneru z našeho soukromého registru kontejnerů.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
