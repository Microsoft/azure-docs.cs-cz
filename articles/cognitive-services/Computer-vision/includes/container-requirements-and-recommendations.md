---
title: Požadavky na kontejnery a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481763"
---
> [!NOTE]
> Požadavky a doporučení jsou založeny na srovnávacích testech s jedním požadavkem za sekundu pomocí 8 MB obrázku naskenovaného obchodního dopisu, který obsahuje 29 řádků a celkem 803 znaků.

Následující tabulka popisuje minimální a doporučené přidělení prostředků pro každý kontejner pro čtení.

| Kontejner | Minimální | Doporučené |Tps<br>(Minimální, maximální)|
|-----------|---------|-------------|--|
| Čtení | 1 jádra, 8 GB paměti, 0,24 TPS | 8 jader, 16 GB paměti, 1,17 TPS | 0.24, 1.17 |

* Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.
* TPS - transakce za sekundu.

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.
