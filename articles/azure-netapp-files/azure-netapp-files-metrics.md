---
title: Metriky pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje metriky pro soubory Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460428"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriky pro Azure NetApp Files

Soubory Azure NetApp poskytuje metriky přidělené úložiště, skutečné využití úložiště, viposlužby svazku a latence. Analýzou těchto metrik můžete lépe porozumět způsobu využití a výkonu objemu účtů NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriky využití pro fondy kapacit

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Fond přidělený velikosti svazku*  
    Celková objemová kvóta (GiB) v daném fondu kapacity (to znamená součet zřízených velikostí objemů v kapacitním fondu).  
    Tato velikost je velikost, kterou jste vybrali při vytváření svazku.  
- *Velikost spotřebovaného fondu*  
    Celkový počet logických prostor (GiB) použitých napříč svazky ve fondu kapacity.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriky využití svazků

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Velikost spotřebovaného objemu*   
    Celkový logický prostor použitý ve svazku (GiB).  
    Tato velikost zahrnuje logický prostor používaný aktivními systémy souborů a snímky.  
- *Velikost snímku svazku*   
   Přírůstkové logické místo používané snímky ve svazku.  

## <a name="performance-metrics-for-volumes"></a>Metriky výkonu pro svazky

- *Průměrná latence*   
    Průměrná doba čtení ze svazku v milisekundách.
- *AverageWriteLatency*   
    Průměrná doba zápisu ze svazku v milisekundách.
- *ReadIops*   
    Počet čtení na svazek za sekundu.
- *WriteIops*   
    Počet zápisů na svazek za sekundu.

## <a name="next-steps"></a>Další kroky

* [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
