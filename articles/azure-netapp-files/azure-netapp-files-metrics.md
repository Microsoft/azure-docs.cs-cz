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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848790"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriky pro Azure NetApp Files

Azure NetApp Files poskytuje metriky přiděleného úložiště, skutečné využití úložiště, propustnost svazku, IOPS a latenci. Díky analýze těchto metrik můžete získat lepší informace o způsobu využití a výkonu svých účtů NetApp.  

## <a name="capacity_pools"></a>Metriky využití pro fondy kapacit

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Využití přiděleného fondu svazků*  
    Celková kvóta svazku (GiB) v daném fondu kapacity (tj. celkový počet zřízených velikostí svazků ve fondu kapacit)  
    Toto je velikost, kterou jste vybrali při vytváření svazku.  
- *Celková logická velikost fondu svazků*  
    Celkový počet logických prostorů (GiB) používaných napříč svazky ve fondu kapacity  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>Metriky využití pro svazky

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Logická velikost svazku*   
    Celkové logické místo použité ve svazku (GiB)  
    Tato velikost zahrnuje logický prostor používaný aktivními systémy souborů a snímky.  
- *Velikost snímku svazku*   
   Přírůstkový logický prostor používaný snímky ve svazku  

## <a name="performance-metrics-for-volumes"></a>Metriky výkonu pro svazky

- *AverageReadLatency*   
    Průměrná doba čtení ze svazku v milisekundách
- *AverageWriteLatency*   
    Průměrná doba zápisu ze svazku v milisekundách
- *ReadIops*   
    Počet čtení na svazek za sekundu
- *WriteIops*   
    Počet zápisů na svazek za sekundu

## <a name="next-steps"></a>Další postup

* [Pochopení hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
