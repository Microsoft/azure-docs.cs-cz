---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471676"
---
Ve verzi Preview podléhají spravované disky, které mají povolené sdílené disky, následující omezení:

- V současné době k dispozici pouze s prémiovými SSD.
- V současné době podporována pouze v oblasti západní střední USA.
- Všechny virtuální počítače sdílející disk musí být nasazeny ve stejných [skupinách umístění bezkontaktní .](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Lze povolit pouze na datových discích, nikoli na discích operačního systému.
- V některých verzích clusteru s podporou převzetí služeb při selhání systému Windows Server lze použít pouze základní disky, podrobnosti naleznete v [tématu Požadavky na hardware clustering u převzetí služeb při selhání a možnosti úložiště](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Ukládání hostitelů jen pro čtení není k dispozici `maxShares>1`pro prémiové ssd disy s .
- Skupiny dostupnosti a škálovací sady `FaultDomainCount` virtuálních strojů lze použít pouze s nastavenou na 1.
- Podpora Azure Backup a Azure Site Recovery ještě není dostupná.

Pokud máte zájem o vyzkoušení sdílených disků, [zaregistrujte se do naší verze Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
