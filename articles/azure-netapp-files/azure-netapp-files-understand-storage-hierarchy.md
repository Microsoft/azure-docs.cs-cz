---
title: Jaká je hierarchie úložišť souborů Azure NetApp | Dokumenty společnosti Microsoft
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
ms.date: 02/27/2020
ms.author: b-juche
ms.openlocfilehash: 70d3a2a501952a5e20b1ff8e99f48f4d7aefce8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78163960"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Jaká je hierarchie úložišť souborů Azure NetApp

Před vytvořením svazku ve službě Azure NetApp Files musíte zakoupit a nastavit fond pro zřízenou kapacitu.  Pokud chcete nastavit fond kapacity, musíte mít účet NetApp. Pochopení hierarchie úložiště pomáhá nastavit a spravovat prostředky Azure NetApp Files.

> [!IMPORTANT] 
> Soubory Azure NetApp aktuálně nepodporuje migraci prostředků mezi předplatnými.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Účty NetApp

- Účet NetApp slouží jako správní seskupení základních fondů kapacity.  
- Účet NetApp není stejný jako obecný účet úložiště Azure. 
- Účet NetApp se vztahuje na oblast.   
- V rámci jedné oblasti můžete mít víc účtů NetApp, ale každý účet NetApp se váže pouze k jedné oblasti.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Fondy kapacit

- Fond kapacity se měří podle jeho zřízené kapacity.  
- Kapacitu zřdí pevné sku, které jste zakoupili (například kapacita 4 TiB).
- Fond kapacity může mít jenom jednu úroveň služeb.  
- Každý fond kapacity může patřit pouze k jednomu účtu NetApp. V rámci účtu NetApp však můžete mít více fondů kapacity.  
- Fond kapacity se nedá mezi účty NetApp přesouvat.   
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) nejde přesunout fond kapacity 1 z účtu NetApp oblasti USA – východ do účtu NetApp oblasti USA – západ 2.  
- Fond kapacity nelze odstranit, dokud nebudou odstraněny všechny svazky v rámci fondu kapacity.

## <a name="volumes"></a><a name="volumes"></a>Svazky

- Objem se měří podle spotřeby logické kapacity a je škálovatelný. 
- Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.
- Každý svazek patří jenom do jednoho fondu, ale fond může obsahovat několik svazků. 
- Svazek nelze přesunout mezi fondy kapacit. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Například v [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) níže nelze přesunout svazky z kapacity fondu 1 kapacity fondu 2.
- Svazek nelze odstranit, dokud nebudou odstraněny všechny jeho snímky.

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptuální diagram hierarchie úložiště 
Následující příklad znázorňuje vztahy předplatného Azure, účtů NetApp, fondů kapacity a svazků.   

![Konceptuální diagram hierarchie úložiště](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Další kroky

- [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrace pro Azure NetApp Files](azure-netapp-files-register.md)
