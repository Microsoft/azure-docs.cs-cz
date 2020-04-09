---
title: Spustit příklad kontejneru příkazu spuštění dockeru
titleSuffix: Azure Cognitive Services
description: Příkaz Spustit docker pro kontejner Analýzy mínění
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e67f65d252be0ea638d3b5fa241d9413e76f1a98
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876980"
---
Chcete-li spustit kontejner *Analýzy mínění,* proveďte následující `docker run` příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *analýzy mínění* z bitové kopie kontejneru.
* Přiděluje jedno jádro procesoru a 4 gigabajty (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.