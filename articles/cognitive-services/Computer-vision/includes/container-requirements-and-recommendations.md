---
title: Požadavky na kontejner a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481763"
---
> [!NOTE]
> Požadavky a doporučení jsou založené na srovnávacích testech s jednou žádostí za sekundu pomocí 8 MB naskenovaného obchodního dopisu obsahujícího 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

| Kontejner | Minimální | Doporučené |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Čtení | 1 jádra, 8 GB paměti, 0,24 TPS | 8 jader, 16 GB paměti, 1,17 TPS | 0,24, 1,17 |

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu.

Základní a paměť odpovídají nastavení `--cpus` a `--memory`, které se používají jako součást příkazu `docker run`.
