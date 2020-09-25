---
title: Hierarchie úložiště Azure NetApp Files | Microsoft Docs
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278324"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Hierarchie úložiště Azure NetApp Files

Před vytvořením svazku ve službě Azure NetApp Files musíte zakoupit a nastavit fond pro zřízenou kapacitu.  Pokud chcete nastavit fond kapacity, musíte mít účet NetApp. Pochopení hierarchie úložiště pomáhá nastavit a spravovat prostředky Azure NetApp Files.

> [!IMPORTANT] 
> Azure NetApp Files aktuálně nepodporuje migraci prostředků mezi předplatnými.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Účty NetApp

- Účet NetApp slouží jako správní seskupení základních fondů kapacity.  
- Účet NetApp není stejný jako obecný účet úložiště Azure. 
- Účet NetApp se vztahuje na oblast.   
- V rámci jedné oblasti můžete mít víc účtů NetApp, ale každý účet NetApp se váže pouze k jedné oblasti.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Fondy kapacit

Porozumět tomu, jak fondy kapacity fungují, vám pomůžou vybrat správné typy fondů kapacity pro vaše potřeby úložiště. 

### <a name="general-rules-of-capacity-pools"></a>Obecná pravidla fondů kapacity

- Fond kapacity se měří podle jeho zřízené kapacity.   
    Další informace najdete v tématu [typy QoS](#qos_types) .  
- Kapacita se zřizuje v pevných SKU, které jste si koupili (například na 4 TiB kapacitě).
- Fond kapacity může mít jenom jednu úroveň služeb.  
- Každý fond kapacit může patřit pouze k jednomu účtu NetApp. V rámci účtu NetApp ale můžete mít několik fondů kapacit.  
- Fond kapacity se nedá mezi účty NetApp přesouvat.   
  Například v následujícím [konceptuálním diagramu hierarchie úložiště](#conceptual_diagram_of_storage_hierarchy) nejde přesunout fond kapacity 1 z účtu NetApp oblasti USA – východ do účtu NetApp oblasti USA – západ 2.  
- Fond kapacit nejde odstranit, dokud se neodstraní všechny svazky v rámci fondu kapacit.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Typy technologie QoS (Quality of Service) pro fondy kapacit

Typ QoS je atributem fondu kapacit. Azure NetApp Files poskytuje dva typy QoS fondů kapacity. 

- *Automaticky (nebo automaticky)* Typ QoS  

    Když vytváříte fond kapacit, výchozí typ QoS je automatický.

    Ve fondu kapacity auto QoS se propustnost automaticky přiřazuje svazkům ve fondu, a to úměrně k kvótě velikosti přiřazené ke svazkům. 

    Maximální propustnost přidělená svazku závisí na úrovni služby fondu kapacity a velikosti kvóty svazku. Příklad výpočtu najdete v tématu [úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md) .

- <a name="manual_qos_type"></a>*Ruční* Typ QoS  

     > [!IMPORTANT] 
     > Použití ručního typu QoS pro fond kapacit vyžaduje registraci.  Viz [Správa ručního fondu kapacity QoS](manage-manual-qos-capacity-pool.md).  

    Máte možnost použít pro fond kapacit ruční typ QoS.

    V manuálním fondu kapacity QoS můžete přiřadit kapacitu a propustnost pro svazek nezávisle. Celková propustnost všech svazků vytvořených pomocí manuálního fondu kapacity QoS je omezená celkovou propustností fondu.  Je určena kombinací velikosti fondu a propustnosti na úrovni služby. 

    Například fond kapacit 4 TiB s úrovní Ultra Service úrovně Standard má celkovou kapacitu propustnosti 512 MiB/s (4 TiB x 128 MiB/s/TiB) dostupné pro svazky.


## <a name="volumes"></a><a name="volumes"></a>Svazků

- Svazek se měří podle spotřeby logické kapacity a je škálovatelný. 
- Spotřeba kapacity svazku se počítá proti zřízené kapacitě příslušného fondu.
- Využití propustnosti svazku se počítá podle propustnosti fondu, který je k dispozici. Podívejte se na téma [Ruční typ QoS](#manual_qos_type).
- Každý svazek patří jenom do jednoho fondu, ale fond může obsahovat několik svazků. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptuální diagram hierarchie úložiště 
Následující příklad znázorňuje vztahy předplatného Azure, účtů NetApp, fondů kapacity a svazků.   

![Konceptuální diagram hierarchie úložiště](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Další kroky

- [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrace služby Azure NetApp Files](azure-netapp-files-register.md)
- [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Aspekty výkonu pro Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- [Správa ručního fondu kapacity QoS](manage-manual-qos-capacity-pool.md)
