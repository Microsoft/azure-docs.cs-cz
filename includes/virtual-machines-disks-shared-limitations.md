---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4a61399bbb75639fdf4f03da7b77f22a2136adbe
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628872"
---
Povolení sdílených disků je dostupné jenom pro podmnožinu typů disků. Sdílené disky teď můžou povolit jenom disky Ultra a Premium SSD. U každého spravovaného disku, na kterém jsou povolené sdílené disky, platí následující omezení uspořádaná podle typu disku:

### <a name="ultra-disks"></a>Disky Ultra

Disky Ultra mají vlastní samostatný seznam omezení, které se nevztahují ke sdíleným diskům. Omezení pro ultra disk najdete v tématu [použití disků Azure Ultra](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Při sdílení Ultra discích mají tato dodatečná omezení:

- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](/windows-server/failover-clustering/clustering-requirements)

Sdílené disky Ultra jsou k dispozici ve všech oblastech, které ve výchozím nastavení podporují disky Ultra a nevyžadují, abyste se k jejich používání přihlásili.

### <a name="premium-ssds"></a>Disky SSD úrovně Premium

- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- Dá se povolit jenom na datových discích, ne na discích s operačním systémem.
- Mezipaměť dostupnosti hostitele **jen pro čtení** není k dispozici pro prémiové SSD s `maxShares>1` .
- Pro prémiové SSD s nástrojem není k dispozici shlukování disku `maxShares>1` .
- Při používání skupin dostupnosti a sady škálování virtuálních počítačů se sdílenými disky Azure není pro sdílený datový disk vynutilo [přidružení domén chyb úložiště](../articles/virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) s doménou selhání virtuálního počítače.
- Při použití [skupin umístění blízkosti (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)musí být všechny virtuální počítače sdílející disk součástí stejného PPG.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](/windows-server/failover-clustering/clustering-requirements)
- Podpora Azure Site Recovery ještě není k dispozici.
- Azure Backup je k dispozici prostřednictvím služby [Azure disk Backup (Preview)](../articles/backup/disk-backup-overview.md).

#### <a name="regional-availability"></a>Regionální dostupnost

Shared Premium SSD jsou dostupné ve všech oblastech, které jsou dostupné pro správu disků.