---
title: Latence připojení uživatelů virtuální plochy Windows – Azure
description: Latence připojení pro uživatele virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128192"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Určení latence připojení uživatelů ve virtuální ploše Windows

Virtuální plocha systému Windows je globálně dostupná. Správci můžou vytvářet virtuální počítače (VM) v libovolné oblasti Azure, kterou chtějí. Latence připojení se bude lišit v závislosti na umístění uživatelů a virtuálních počítačů. Služby Windows Virtual Desktop budou průběžně zavádět do nových zeměpisných oblastí, aby se zlepšila latence. 
 
[Nástroj Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) vám pomůže určit nejlepší umístění pro optimalizaci latence virtuálních počítačů. Doporučujeme používat nástroj každé dva až tři měsíce, abyste se ujistili, že se optimální umístění nezměnilo, protože se virtuální plocha Windows zavádí do nových oblastí. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Virtuální plocha Windows používá Správce provozu Azure, který kontroluje umístění serveru DNS uživatele a vyhledá nejbližší instanci služby Windows Virtual Desktop. Doporučujeme správcům zkontrolovat umístění serveru DNS uživatele před výběrem umístění pro virtuální servery.

## <a name="next-steps"></a>Další kroky

- Chcete-li zkontrolovat nejlepší umístění pro optimální latenci, podívejte se na [nástroj Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Informace o cenových plánech naleznete v [tématu Ceny virtuální plochy Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Chcete-li začít s nasazením virtuální plochy windows, podívejte se na [náš kurz](tenant-setup-azure-active-directory.md).