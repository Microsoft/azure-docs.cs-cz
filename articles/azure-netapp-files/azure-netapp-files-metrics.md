---
title: Metriky pro Azure NetApp Files | Microsoft Docs
description: Popisuje metriky pro Azure NetApp Files.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460428"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriky pro Azure NetApp Files

Azure NetApp Files poskytuje metriky přiděleného úložiště, skutečné využití úložiště, Volume IOPS a latenci. Díky analýze těchto metrik můžete získat lepší informace o způsobu využití a výkonu svých účtů NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriky využití pro fondy kapacit

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Fond přidělený velikosti svazku*  
    Celková kvóta svazku (GiB) v daném fondu kapacity (tj. celkový počet zřízených velikostí svazků ve fondu kapacit).  
    Tato velikost je velikost, kterou jste zvolili při vytváření svazku.  
- *Velikost spotřebovaného fondu*  
    Celkový počet logických prostorů (GiB) používaných napříč svazky ve fondu kapacit.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriky využití pro svazky

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Velikost spotřebovaného svazku*   
    Celkové logické místo použité ve svazku (GiB).  
    Tato velikost zahrnuje logický prostor používaný aktivními systémy souborů a snímky.  
- *Velikost snímku svazku*   
   Přírůstkový logický prostor používaný snímky ve svazku.  

## <a name="performance-metrics-for-volumes"></a>Metriky výkonu pro svazky

- *AverageReadLatency*   
    Průměrná doba čtení ze svazku v milisekundách
- *AverageWriteLatency*   
    Průměrná doba zápisu ze svazku v milisekundách
- *ReadIops*   
    Počet čtení na svazek za sekundu.
- *WriteIops*   
    Počet zápisů na svazek za sekundu.

## <a name="next-steps"></a>Další kroky

* [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
