---
title: Požadavky na kontejnery a doporučení
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877966"
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
