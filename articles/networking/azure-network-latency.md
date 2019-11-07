---
title: Statistika latence sítě Azure v-cestách | Microsoft Docs
description: Přečtěte si o statistikách latence odezvy mezi oblastmi Azure.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587586"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Statistika latence sítě Azure v přenosu

Azure nepřetržitě monitoruje latenci (rychlost) základních oblastí své sítě pomocí nástrojů pro interní monitorování a také měření shromažďovaných službou [ThousandEyes](https://thousandeyes.com), což je služba syntetického monitorování třetí strany.

## <a name="how-are-the-measurements-collected"></a>Jak se shromažďují míry?

Měření latence se shromažďují z ThousandEyesch agentů hostovaných v cloudových oblastech Azure v celém světě, které nepřetržitě posílají testy sítě mezi sebou, v intervalu 1 minut. Statistika měsíční latence se odvozuje z průměrného počtu shromážděných ukázek v měsíci.

## <a name="october-2019-latency-figures"></a>Hodnoty latence v říjnu 2019

Po 31 dní od 31. října 2019 se doba latence měsíčních min a maximální doby odezvy v rámci agregovaných oblastí:

- **5 MS** až **72 MS** pro zacyklení cest v rámci **Severní Amerikach** oblastí.
- **3 MS** až **28 MS** pro výměnu cest v rámci oblastí v **Evropě** .
- **4 MS** až **134 MS** pro výměnu cest mezi oblastmi **Asie** .

Následující měření latence mezi oblastmi využívá [ThousandEyes](https://thousandeyes.com). Jednotka měření v následující tabulce je v milisekundách (MS).

![Statistika latence mezi oblastmi Azure](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>Další kroky
- Přečtěte si o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).