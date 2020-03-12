---
title: Latence připojení uživatele k virtuální ploše Windows – Azure
description: Latence připojení pro uživatele virtuálních klientů Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128192"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Určení latence připojení uživatele ve virtuální ploše Windows

Virtuální klient Windows je globálně dostupný. Správci můžou vytvářet virtuální počítače v libovolné oblasti Azure, které chtějí. Latence připojení se bude lišit v závislosti na umístění uživatelů a virtuálních počítačů. Služba Windows Virtual Desktop Service se průběžně zavede do nových geografických oblastí, aby se zlepšila latence. 
 
[Nástroj Estimator Experience pro virtuální počítače s Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) vám pomůže určit nejlepší umístění pro optimalizaci latence vašich virtuálních počítačů. Doporučujeme, abyste tento nástroj používali každých dvou až tří měsíců, abyste se ujistili, že se optimální umístění nezměnilo, protože se virtuální plocha Windows vyřadí do nových oblastí. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Virtuální plocha Windows používá Traffic Manager Azure, která kontroluje umístění serveru DNS uživatele, aby našel nejbližší instanci služby Virtual Desktop systému Windows. Před volbou umístění pro virtuální počítače doporučujeme správcům zkontrolovat umístění serveru DNS daného uživatele.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit nejlepší umístění pro optimální latenci, přečtěte si [Nástroj Estimator pro Windows Virtual Desktop Experience](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Cenové tarify najdete v tématu [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Pokud chcete začít s nasazením virtuálního počítače s Windows, přečtěte si [náš kurz](tenant-setup-azure-active-directory.md).