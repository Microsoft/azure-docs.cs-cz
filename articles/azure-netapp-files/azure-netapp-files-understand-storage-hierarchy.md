---
title: Co je hierarchii úložiště souborů NetApp Azure | Dokumentace Microsoftu
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
ms.topic: overview
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522873"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Co je hierarchii úložiště souborů NetApp Azure

Před vytvořením svazku ve službě Azure NetApp Files musíte zakoupit a nastavit fond pro zřízenou kapacitu.  Pokud chcete nastavit fond kapacity, musíte mít účet NetApp. Pochopení hierarchie úložiště pomáhá nastavit a spravovat prostředky Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Účty NetApp

- Účet NetApp slouží jako správní seskupení základních fondů kapacity.  
- Účet NetApp není stejný jako obecný účet úložiště Azure. 
- Účet NetApp se vztahuje na oblast.   
- V rámci jedné oblasti můžete mít víc účtů NetApp, ale každý účet NetApp se váže pouze k jedné oblasti.

## <a name="capacity_pools"></a>Fondy kapacit

- Fond kapacity se měří podle jeho zřízené kapacity.  
- Kapacita je zajištěný nástrojem pevné skladové položky, které jste zakoupili (například kapacitou 4 TB).
- Fond kapacity může mít jenom jednu úroveň služeb.  
- Každá kapacita fondu může patřit do pouze jeden účet NetApp. Však může mít několik fondů kapacity v rámci účtu NetApp.  
- Fond kapacity se nedá mezi účty NetApp přesouvat.   
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) nejde přesunout fond kapacity 1 z účtu NetApp oblasti USA – východ do účtu NetApp oblasti USA – západ 2.  
- Kapacita fondu nejde odstranit, dokud se odstranily všechny svazky v rámci kapacity fondu.

## <a name="volumes"></a>Svazky

- Svazek se měří podle spotřeby kapacity logické a je škálovatelný. 
- Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.
- Každý svazek patří jenom do jednoho fondu, ale fond může obsahovat několik svazků. 
- Svazek se nedají přesouvat mezi fondy kapacity. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Například v [koncepční diagram hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) níže, nelze přesunout svazky z kapacity fondu 1 na 2 kapacity fondu.
- Svazek nejde odstranit, dokud její snímky se odstranily.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptuální diagram hierarchie úložiště 
Následující příklad znázorňuje vztahy předplatného Azure, účtů NetApp, fondů kapacity a svazků.   

![Konceptuální diagram hierarchie úložiště](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Další postup

- [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrace pro soubory Azure NetApp](azure-netapp-files-register.md)
