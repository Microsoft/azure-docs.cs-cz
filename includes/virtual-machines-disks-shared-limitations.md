---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202213"
---
Ve verzi Preview platí, že spravované disky, které mají povolené sdílené disky, podléhají následujícím omezením:

- Aktuálně dostupné jenom pro Premium SSD.
- V současné době se podporuje jenom v Středozápadní USA oblasti.
- Všechny virtuální počítače sdílející disk musí být nasazené ve stejné [skupině umístění pro Proximity](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Dá se povolit jenom na datových discích, ne na discích s operačním systémem.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- Ukládání do mezipaměti hostitele jen pro čtení není pro prémiové SSD s `maxShares>1`k dispozici.
- Skupiny dostupnosti a sady škálování virtuálních počítačů je možné používat jenom s `FaultDomainCount` nastavenou na 1.
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

Pokud se zajímáte o vyzkoušení sdílených disků, [Zaregistrujte si verzi Preview](https://aka.ms/shareddisksignup).
