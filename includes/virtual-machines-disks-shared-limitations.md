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
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471676"
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

Pokud se zajímáte o vyzkoušení sdílených disků, [Zaregistrujte si verzi Preview](https://aka.ms/AzureSharedDiskPreviewSignUp).
