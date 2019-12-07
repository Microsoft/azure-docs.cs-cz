---
title: Statistika latence sítě Azure v-cestách | Microsoft Docs
description: Přečtěte si o statistikách latence odezvy mezi oblastmi Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893077"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistika latence sítě Azure v přenosu

Azure nepřetržitě monitoruje latenci (rychlost) základních oblastí své sítě pomocí nástrojů pro interní monitorování a také měření shromažďovaných službou [ThousandEyes](https://thousandeyes.com), což je služba syntetického monitorování třetí strany.

## <a name="how-are-the-measurements-collected"></a>Jak se shromažďují míry?

Měření latence se shromažďují od agentů ThousandEyes hostovaných v cloudových oblastech Azure po celém světě, které neustále odesílají testy sítě mezi sebou v intervalu 1 minut. Statistika měsíční latence se odvozuje z průměrného počtu shromážděných ukázek v měsíci.

## <a name="november-2019-latency-figures"></a>Hodnoty latence v listopadu 2019

**Listopadová aktualizace:** Přidali jsme 3 oblasti.

* Norsko – východ
* Norsko – západ
* Austrálie

Níže jsou uvedené měsíční průměrné doby odezvy mezi oblastmi Azure za posledních 30 dní (končící 30. listopadu 2019). Následující měření jsou poháněná nástrojem [ThousandEyes](https://thousandeyes.com).

![Statistika latence mezi oblastmi Azure](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
