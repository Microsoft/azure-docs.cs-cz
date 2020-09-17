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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707777"
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

- *Celková velikost snímku fondu*    
    Součet velikosti snímku všech svazků ve fondu.

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

- *Průměrná latence čtení*   
    Průměrná doba čtení ze svazku v milisekundách
- *Průměrná latence zápisu*   
    Průměrná doba zápisu ze svazku v milisekundách
- *Čtení IOPS*   
    Počet čtení na svazek za sekundu.
- *Zápis IOPS*   
    Počet zápisů na svazek za sekundu.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Metriky replikace svazků

- *Je stav replikace svazku v pořádku*   
    Podmínka vztahu replikace. 

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
