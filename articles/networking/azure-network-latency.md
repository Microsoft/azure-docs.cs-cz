---
title: Statistiky latence oodezvy sítě Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o statistikách latence odezvy mezi oblastmi Azure.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082930"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistiky latence oodezvy sítě Azure

Azure průběžně monitoruje latenci (rychlost) hlavních oblastí své sítě pomocí interních monitorovacích nástrojů a měření shromážděných [službou ThousandEyes](https://thousandeyes.com), která je službou syntetického monitorování třetí strany.

## <a name="how-are-the-measurements-collected"></a>Jak se měří?

Měření latence se shromažďují od agentů ThousandEyes, hostovaných v cloudových oblastech Azure po celém světě, které mezi sebou nepřetržitě odesílají síťové sondy v intervalech 1 minuty. Měsíční statistiky latence jsou odvozeny z průměrování shromážděných vzorků za měsíc.

## <a name="february-2020-round-trip-latency-figures"></a>Únor 2020 údaje o latenci zpáteční cesty

Měsíční průměrné doby odezvy mezi oblastmi Azure za posledních 29 dní (končící 29. února 2020) jsou uvedeny níže. Následující měření jsou poháněna [ThousandEyes](https://thousandeyes.com).

[![Statistiky latence mezi oblastmi Azure](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Další kroky

Další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
