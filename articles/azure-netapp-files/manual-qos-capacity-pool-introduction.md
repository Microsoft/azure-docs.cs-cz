---
title: Ruční fond kapacit technologie QoS Azure NetApp Files | Microsoft Docs
description: Poskytuje Úvod do ručního fondu kapacity QoS a referenčních informací pro další informace.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581121"
---
# <a name="manual-qos-capacity-pool"></a>Fond ručně zřizované kapacity QoS

Tento článek poskytuje Úvod do funkce fondu kapacity technologie QoS (Quality of Service).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Jak se ruční technologie QoS liší od automatické technologie QoS

[Typ QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) je atributem fondu kapacit. Azure NetApp Files poskytuje dva typy QoS fondů kapacity – auto (výchozí) a ruční.  

V *manuálním* fondu kapacity QoS můžete přiřadit kapacitu a propustnost pro svazek nezávisle. Minimální a maximální úrovně propustnosti najdete v tématu [omezení prostředků pro Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits). Celková propustnost všech svazků vytvořených pomocí manuálního fondu kapacity QoS je omezená celkovou propustností fondu. Je určena kombinací velikosti fondu a propustnosti na úrovni služby. 

Ve fondu kapacity *auto* QoS se propustnost automaticky přiřazuje svazkům ve fondu, a to úměrně k kvótě velikosti přiřazené ke svazkům.  

Informace o typech QoS najdete v tématu [hierarchie úložiště Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) a [důležitých informací o výkonu pro Azure NetApp Files](azure-netapp-files-performance-considerations.md) .

## <a name="example-of-using-manual-qos"></a>Příklad použití ruční technologie QoS

Pokud používáte ruční fond kapacit QoS s, například SAP HANAm systémem, databází Oracle nebo jinými úlohami, které vyžadují více svazků, lze fond kapacit použít k vytvoření těchto svazků aplikace.  Každý svazek může poskytovat individuální velikost a propustnost pro splnění požadavků aplikace.  Podrobnosti o výhodách najdete v tématu [Příklady propustnosti svazků v ručním fondu kapacity QoS](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) .  

## <a name="how-to-specify-the-manual-qos-type"></a>Jak zadat ruční typ QoS

Když [vytváříte fond kapacit](azure-netapp-files-set-up-capacity-pool.md), můžete zadat, aby fond kapacit používal ruční typ QoS.  [Existující fond kapacit](manage-manual-qos-capacity-pool.md#change-to-qos) můžete také změnit tak, aby používal ruční typ QoS. 

Nastavení typu kapacity na ruční QoS je trvalá změna. Ruční nástroj pro kapacitu typu QoS nejde převést na fond kapacity auto QoS. 

Použití ručního typu QoS vyžaduje, abyste tuto [funkci zaregistrovali](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Další kroky

* [Správa fondu ručně zřizovaného kapacity QoS](manage-manual-qos-capacity-pool.md)
* [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
* [Hierarchie úložiště](azure-netapp-files-understand-storage-hierarchy.md) 
* [Úrovně služeb pro Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Aspekty výkonu pro Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Omezení prostředků pro službu Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vytvoření svazku NFS](azure-netapp-files-create-volumes.md)
* [Vytvoření svazku SMB](azure-netapp-files-create-volumes-smb.md)
* [Vytvoření svazku se dvěma protokoly](create-volumes-dual-protocol.md)
* [Metriky pro Azure NetApp Files](azure-netapp-files-metrics.md)
* [Řešení potíží s fondy kapacity](troubleshoot-capacity-pools.md)
