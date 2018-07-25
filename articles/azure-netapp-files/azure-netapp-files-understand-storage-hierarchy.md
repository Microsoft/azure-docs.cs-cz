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
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010986"
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
- Kapacita je zřízena pomocí pevných skladových položek, které jste zakoupili (například 4TB kapacita).
- Fond kapacity může mít jenom jednu úroveň služeb.  
  V současné době je k dispozici jenom úroveň služeb Premium.
- Každý fond kapacity patří pouze k jednomu účtu NetApp.  
- Fond kapacity se nedá mezi účty NetApp přesouvat.   
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) nejde přesunout fond kapacity 1 z účtu NetApp oblasti USA – východ do účtu NetApp oblasti USA – západ 2.  

## <a name="volumes"></a>Svazky

- Svazek se měří podle spotřeby logické kapacity a je škálovatelný až do 100 TB na jeden svazek.
- Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.
- Každý svazek patří jenom do jednoho fondu, ale fond může obsahovat několik svazků. 
- V rámci stejného účtu NetApp můžete přesunout svazek mezi fondy.    
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) můžete přesunout svazky z fondu kapacity 1 do fondu kapacity 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptuální diagram hierarchie úložiště 
Následující příklad znázorňuje vztahy předplatného Azure, účtů NetApp, fondů kapacity a svazků.   

![Konceptuální diagram hierarchie úložiště](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Další kroky

1. [Vytvoření účtu NetApp](azure-netapp-files-create-netapp-account.md)
2. [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
3. [Vytvoření svazku pro Azure NetApp Files](azure-netapp-files-create-volumes.md)
4. [Konfigurace zásad exportu pro svazek (volitelné)](azure-netapp-files-configure-export-policy.md)