---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944517"
---
Povolení sdílených disků je dostupné jenom pro podmnožinu typů disků. Sdílené disky teď můžou povolit jenom disky Ultra a Premium SSD. U každého spravovaného disku, na kterém jsou povolené sdílené disky, platí následující omezení uspořádaná podle typu disku:

### <a name="ultra-disks"></a>Disky Ultra

Disky Ultra mají vlastní samostatný seznam omezení, které se nevztahují ke sdíleným diskům. Omezení pro ultra disk najdete v tématu [použití disků Azure Ultra](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Při sdílení Ultra discích mají tato dodatečná omezení:

- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

Sdílené disky Ultra jsou k dispozici ve všech oblastech, které ve výchozím nastavení podporují disky Ultra a nevyžadují, abyste se k jejich používání přihlásili.

### <a name="premium-ssds"></a>Disky SSD úrovně Premium

- V současné době se podporuje jenom v [podmnožině oblastí](#regional-availability).
- V současné době omezena na Azure Resource Manager nebo podporu sady SDK. 
- Dá se povolit jenom na datových discích, ne na discích s operačním systémem.
- Mezipaměť dostupnosti hostitele **jen pro čtení** není k dispozici pro prémiové SSD s `maxShares>1` .
- Pro prémiové SSD s nástrojem není k dispozici shlukování disku `maxShares>1` .
- Při používání skupin dostupnosti a sady škálování virtuálních počítačů se sdílenými disky Azure není pro sdílený datový disk vynutilo [přidružení domén chyb úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) s doménou selhání virtuálního počítače.
- Při použití [skupin umístění blízkosti (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)musí být všechny virtuální počítače sdílející disk součástí stejného PPG.
- V některých verzích clusteru s podporou převzetí služeb při selhání Windows serveru se dají použít jenom základní disky. Podrobnosti najdete v tématu [požadavky na hardware clusteringu s podporou převzetí služeb při selhání a možnosti](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

#### <a name="regional-availability"></a>Regionální dostupnost

Shared Premium SSD se podporuje jenom v těchto oblastech:

- East US
- USA – východ 2
- USA – západ
- Západní USA 2
- USA – středozápad
- Středojižní USA
- USA – středosever
- Střední USA
- Západní Evropa
- Severní Evropa
- Jižní Korea – střed
- Střední Kanada
- Kanada – východ
- Japonsko – východ
- Japonsko – západ
- USA (Gov) – Virginia
- USA (Gov) – Arizona

Pokud vás zajímá, že budete chtít vyzkoušet Shared Premium SSD, [Zaregistrujte si přístup](https://aka.ms/AzureSharedDiskGASignUp).