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
ms.date: 08/06/2019
ms.author: b-juche
ms.openlocfilehash: 9a56fb27fdf9e196291942041d68b249d7f16648
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839249"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriky pro Azure NetApp Files

Azure NetApp Files poskytuje metriky přiděleného úložiště, skutečné využití úložiště, propustnost svazku, IOPS a latenci. Díky analýze těchto metrik můžete získat lepší informace o způsobu využití a výkonu svých účtů NetApp.  

## <a name="capacity_pools"></a>Metriky využití pro fondy kapacit

<!-- 
- *Volume pool allocated size*  
    This is the size (GiB) of the provisioned capacity pool.  
--> 
- *Využití přiděleného fondu svazků*  
    Toto je celková kvóta svazku (GiB) v daném fondu kapacit (tj. celkový počet zřízených velikostí svazků ve fondu kapacit). Toto je velikost, kterou jste vybrali při vytváření svazku.  
- *Celková logická velikost fondu svazků*  
    Toto je celkový počet logických prostorů (GiB) používaných napříč svazky ve fondu kapacit.  
<!-- 
- *Volume pool total snapshot size*  
    This is the total of incremental logical space used by the snapshots.  
-->

## <a name="volumes"></a>Metriky využití pro svazky

<!-- 
- *Volume allocated size*   
    This is the volume size (quota) provisioned in GiB.  
--> 
- *Logická velikost svazku*   
    Toto je celkové logické místo použité ve svazku (GiB). Tato velikost zahrnuje logický prostor používaný aktivními systémy souborů a snímky.  
- *Velikost snímku svazku*   
    Toto je přírůstkový logický prostor používaný snímky ve svazku.  

## <a name="next-steps"></a>Další postup

* [Pochopení hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
