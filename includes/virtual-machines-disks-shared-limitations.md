---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008326"
---
Ve verzi Preview je povolení sdílených disků dostupné jenom pro podmnožinu typů disků. Sdílené disky teď můžou povolit jenom disky Ultra a Premium SSD. U každého spravovaného disku, na kterém jsou povolené sdílené disky, platí následující omezení uspořádaná podle typu disku:

### <a name="ultra-disks"></a>Disky Ultra

Disky Ultra mají vlastní samostatný seznam omezení, které se nevztahují ke sdíleným diskům. Omezení pro ultra disk najdete v tématu [použití disků Azure Ultra](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Při sdílení Ultra discích mají tato dodatečná omezení:

- V současné době se podporuje jenom v Západní USA.
- V současné době omezena na Azure Resource Manager nebo podporu sady SDK.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

### <a name="premium-ssds"></a>Disky SSD úrovně Premium

- V současné době se podporuje jenom v Středozápadní USA oblasti.
- Všechny virtuální počítače sdílející disk musí být nasazené ve stejné [skupině umístění pro Proximity](../articles/virtual-machines/windows/proximity-placement-groups.md).
- Dá se povolit jenom na datových discích, ne na discích s operačním systémem.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- Mezipaměť dostupnosti hostitele jen pro čtení není k dispozici `maxShares>1`pro prémiové SSD s.
- Skupiny dostupnosti a sady škálování virtuálních počítačů je možné používat jenom s `FaultDomainCount` nastavením na 1.
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

Pokud se zajímáte o vyzkoušení sdílených disků, [Zaregistrujte si verzi Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
