---
title: Metriky pro Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files poskytuje metriky přiděleného úložiště, skutečné využití úložiště, Volume IOPS a latenci. Pomocí těchto metrik můžete pochopit využití a výkon.
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
ms.date: 10/07/2020
ms.author: b-juche
ms.openlocfilehash: 80746d7cef4bb0a70c6bb9eb6ff2e053ea6c7a0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91824686"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriky pro Azure NetApp Files

Azure NetApp Files poskytuje metriky přiděleného úložiště, skutečné využití úložiště, Volume IOPS a latenci. Díky analýze těchto metrik můžete získat lepší informace o způsobu využití a výkonu svých účtů NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metriky využití pro fondy kapacit

- *Velikost přiděleného fondu*   
    Zřízená velikost fondu.

- *Fond přidělený velikosti svazku*  
    Celková kvóta svazku (GiB) v daném fondu kapacity (tj. celkový počet zřízených velikostí svazků ve fondu kapacit).  
    Tato velikost je velikost, kterou jste zvolili při vytváření svazku.  

- *Velikost spotřebovaného fondu*  
    Celkový počet logických prostorů (GiB) používaných napříč svazky ve fondu kapacit.  

- *Celková velikost snímku pro fond*    
    Součet velikosti snímku ze všech svazků ve fondu.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metriky využití pro svazky

<!-- ANF-5023: fixed version: 2020.08, 2020.09
- *Percentage Volume Consumed Size*    
    The percentage of the volume consumed, including snapshots.  
-->
- *Velikost přiděleného svazku*   
    Zřízená velikost svazku
- *Velikost kvóty svazku*    
    Velikost kvóty (GiB), se kterou je svazek zřízený.   
- *Velikost spotřebovaného svazku*   
    Logická velikost svazku (využitých bajtů).  
    Tato velikost zahrnuje logický prostor používaný aktivními systémy souborů a snímky.  
- *Velikost snímku svazku*   
   Velikost všech snímků ve svazku.  

## <a name="performance-metrics-for-volumes"></a>Metriky výkonu pro svazky

- *Průměrná latence čtení*   
    Průměrná doba čtení ze svazku v milisekundách
- *Průměrná latence zápisu*   
    Průměrná doba zápisu ze svazku v milisekundách
- *Čtení IOPS*   
    Počet čtení na svazek za sekundu.
- *Zápis IOPS*   
    Počet zápisů na svazek za sekundu.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Metriky replikace svazků

- *Je stav replikace svazku v pořádku*   
    Podmínka vztahu replikace. Dobrý stav je označený `1` . Stav není v pořádku je označený `0` .

- *Je přenos replikace svazků*    
    Určuje, jestli je stav replikace svazku "přenos". 
 
- *Prodleva při replikaci svazků*   
    Množství času v sekundách, po které se data na zrcadle prodlevy za zdrojem. 

- *Doba trvání posledního přenosu replikace svazku*   
    Doba v sekundách, kterou trvalo dokončení posledního přenosu. 

- *Velikost posledního přenosu replikace svazku*    
    Celkový počet bajtů přenesených jako součást posledního přenosu. 

- *Průběh replikace svazků*    
    Celkové množství přenesených dat pro aktuální operaci přenosu. 

- *Celkový přenos replikace svazků*   
    Kumulativní přenesené bajty pro relaci. 

## <a name="next-steps"></a>Další kroky

* [Vysvětlení hierarchie úložiště služby Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
