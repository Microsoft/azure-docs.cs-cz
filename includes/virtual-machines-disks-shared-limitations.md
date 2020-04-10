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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008326"
---
Ve verzi Preview je povolení sdílených disků k dispozici pouze pro podmnožinu typů disků. V současné době mohou sdílené disky povolit pouze ultra disky a prémiové disky SSD. Na každý spravovaný disk, který má povolené sdílené disky, se vztahují následující omezení uspořádaná podle typu disku:

### <a name="ultra-disks"></a>Disky Ultra

Ultra disky mají svůj vlastní samostatný seznam omezení, které nesouvisí se sdílenými disky. Omezení ultra disku najdete v části [Použití disků Azure ultra](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Při sdílení ultra disků mají následující další omezení:

- V současné době podporována pouze v západní USA.
- Momentálně omezená na Azure Resource Manager nebo podporu sady SDK.
- V některých verzích clusteru s podporou převzetí služeb při selhání systému Windows Server lze použít pouze základní disky, podrobnosti naleznete v [tématu Požadavky na hardware clustering u převzetí služeb při selhání a možnosti úložiště](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

### <a name="premium-ssds"></a>Disky SSD úrovně Premium

- V současné době podporována pouze v oblasti západní střední USA.
- Všechny virtuální počítače sdílející disk musí být nasazeny ve stejných [skupinách umístění bezkontaktní .](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Lze povolit pouze na datových discích, nikoli na discích operačního systému.
- V některých verzích clusteru s podporou převzetí služeb při selhání systému Windows Server lze použít pouze základní disky, podrobnosti naleznete v [tématu Požadavky na hardware clustering u převzetí služeb při selhání a možnosti úložiště](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Ukládání hostitelů jen pro čtení není k dispozici `maxShares>1`pro prémiové ssd disy s .
- Skupiny dostupnosti a škálovací sady `FaultDomainCount` virtuálních strojů lze použít pouze s nastavenou na 1.
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

Pokud máte zájem o vyzkoušení sdílených disků, [zaregistrujte se do naší verze Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
