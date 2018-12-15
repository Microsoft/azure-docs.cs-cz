---
title: Vysvětlení hierarchie úložiště služby Azure NetApp Files | Microsoft Docs
description: Popisuje hierarchii úložiště, včetně účtů, fondů kapacity a svazků služby Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: fa498c39123e09c212466c900e6000c0138db467
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413094"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Vysvětlení hierarchie úložiště služby Azure NetApp Files

Před vytvořením svazku ve službě Azure NetApp Files musíte zakoupit a nastavit fond pro zřízenou kapacitu.  Pokud chcete nastavit fond kapacity, musíte mít účet NetApp. Pochopení hierarchie úložiště pomáhá nastavit a spravovat prostředky Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Účty NetApp

- Účet NetApp slouží jako správní seskupení základních fondů kapacity.  
- Účet NetApp není stejný jako obecný účet úložiště Azure. 
- Účet NetApp se vztahuje na oblast.   
- V rámci jedné oblasti můžete mít víc účtů NetApp, ale každý účet NetApp se váže pouze k jedné oblasti.

## <a name="capacity_pools"></a>Fondy kapacit

- Fond kapacity se měří podle jeho zřízené kapacity.  
- Kapacita je zajištěný nástrojem pevné skladové položky, které jste zakoupili (například kapacitou 4 TB).
- Minimální velikost pro jednu kapacitu fondu je 4 TB a maximální velikost je 500 TB. 
- Fond kapacity může mít jenom jednu úroveň služeb.  
  V současné době je k dispozici jenom úroveň služeb Premium.
- Každá kapacita fondu může patřit do pouze jeden účet NetApp. Však může mít několik fondů kapacity v rámci účtu NetApp.  
- Fond kapacity se nedá mezi účty NetApp přesouvat.   
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) nejde přesunout fond kapacity 1 z účtu NetApp oblasti USA – východ do účtu NetApp oblasti USA – západ 2.  

## <a name="volumes"></a>Svazky

- Svazek se měří podle spotřeby kapacity logické a je škálovatelný. Minimální velikost pro samostatný svazek je 100 GB a maximální velikost je 92 TiB.
- Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.
-   V jedné oblasti může mít maximálně 100 svazky na předplatné Azure. 
- Každý svazek patří jenom do jednoho fondu, ale fond může obsahovat několik svazků. 
- V rámci stejného účtu NetApp můžete přesunout svazek mezi fondy.    
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) můžete přesunout svazky z fondu kapacity 1 do fondu kapacity 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptuální diagram hierarchie úložiště 
Následující příklad znázorňuje vztahy předplatného Azure, účtů NetApp, fondů kapacity a svazků.   

![Konceptuální diagram hierarchie úložiště](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Další postup

[Registrace pro soubory Azure NetApp](azure-netapp-files-register.md)
