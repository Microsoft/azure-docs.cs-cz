---
title: Spustit příklad kontejneru příkazu spuštění dockeru
titleSuffix: Azure Cognitive Services
description: Příkaz Spuštění Dockeru pro kontejner Zjišťování jazyka
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1f6bfbf3a15f17c3cfd0487cdd0bbb535ffc31ae
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876979"
---
Chcete-li spustit kontejner zjišťování `docker run` *jazyka,* proveďte následující příkaz.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Tento příkaz:

* Spustí kontejner *rozpoznávání jazyka* z bitové kopie kontejneru.
* Přiděluje jedno jádro procesoru a 4 gigabajty (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači.