---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f6175a797b14077cafacaca1f2fd48f36e945d9e
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424991"
---
Povolení sdílených disků je dostupné jenom pro podmnožinu typů disků. Sdílené disky teď můžou povolit jenom disky Ultra a Premium SSD. U každého spravovaného disku, na kterém jsou povolené sdílené disky, platí následující omezení uspořádaná podle typu disku:

### <a name="ultra-disks"></a>Disky Ultra

Disky Ultra mají vlastní samostatný seznam omezení, které se nevztahují ke sdíleným diskům. Omezení pro ultra disk najdete v tématu [použití disků Azure Ultra](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Při sdílení Ultra discích mají tato dodatečná omezení:

- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

Sdílené disky Ultra jsou k dispozici ve všech oblastech, které ve výchozím nastavení podporují disky Ultra a nevyžadují, abyste se k jejich používání přihlásili.

### <a name="premium-ssds"></a>Disky SSD úrovně Premium

- V současné době se podporuje jenom v Středozápadní USA oblasti.
- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- Dá se povolit jenom na datových discích, ne na discích s operačním systémem.
- Mezipaměť dostupnosti hostitele **jen pro čtení** není k dispozici pro prémiové SSD s `maxShares>1` .
- Pro prémiové SSD s nástrojem není k dispozici shlukování disku `maxShares>1` .
- Při používání skupin dostupnosti a sady škálování virtuálních počítačů se sdílenými disky Azure není pro sdílený datový disk vynutilo [přidružení domén chyb úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) s doménou selhání virtuálního počítače.
- Při použití [skupin umístění blízkosti (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)musí být všechny virtuální počítače sdílející disk součástí stejného PPG.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

Pokud vás zajímá, že budete chtít vyzkoušet Shared Premium SSD, [Zaregistrujte si přístup](https://aka.ms/AzureSharedDiskGASignUp).