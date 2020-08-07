---
title: Statistika latence sítě Azure v-cestách | Microsoft Docs
description: Přečtěte si o statistikách latence odezvy mezi oblastmi Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847430"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistika latence sítě Azure v přenosu

Azure nepřetržitě monitoruje latenci (rychlost) základních oblastí své sítě pomocí nástrojů pro interní monitorování a také měření shromažďovaných službou [ThousandEyes](https://thousandeyes.com), což je služba syntetického monitorování třetí strany.

## <a name="how-are-the-measurements-collected"></a>Jak se shromažďují míry?

Měření latence se shromažďují od agentů ThousandEyes hostovaných v cloudových oblastech Azure po celém světě, které neustále odesílají testy sítě mezi sebou v intervalu 1 minut. Statistika měsíční latence se odvozuje z průměrného počtu shromážděných ukázek v měsíci.

## <a name="july-2020-round-trip-latency-figures"></a>Hodnoty latence odezvy v červenci 2020

Níže jsou uvedené měsíční průměrné doby odezvy mezi oblastmi Azure za posledních 31 dní (končící 31. července 2020). Následující měření jsou poháněná nástrojem [ThousandEyes](https://thousandeyes.com).

[![Statistika latence mezi oblastmi Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Další kroky

Přečtěte si o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
