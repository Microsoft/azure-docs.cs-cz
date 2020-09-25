---
title: Úrovně služeb pro Azure NetApp Files | Microsoft Docs
description: Popisuje výkon propustnosti pro úrovně služby Azure NetApp Files.
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
ms.date: 09/22/2019
ms.author: b-juche
ms.openlocfilehash: 818b3b59b1113875b6486ffe64bc8d2d30d613d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325452"
---
# <a name="service-levels-for-azure-netapp-files"></a>Úrovně služeb pro Azure NetApp Files
Úrovně služeb jsou atributem fondu kapacit. Úrovně služeb jsou definované a rozlišené o povolenou maximální propustnost svazku ve fondu kapacity na základě kvóty, která je přiřazená ke svazku.

## <a name="supported-service-levels"></a>Podporované úrovně služeb

Azure NetApp Files podporuje tři úrovně služeb: *Ultra*, *Premium*a *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    Úroveň úložiště Ultra poskytuje až 128 MiB/s za propustnost na 1 TiBou kapacitu. 

* <a name="Premium"></a>Premium Storage

    Úroveň Premium Storage poskytuje propustnost až 64 MiB/s za 1 TiBou kapacitu. 

* <a name="Standard"></a>Storage úrovně Standard

    Úroveň Standard úložiště poskytuje až 16 MiB/s propustnosti na 1 TiBu zřízené kapacity.

## <a name="throughput-limits"></a>Omezení propustnosti

Omezení propustnosti svazku je určeno kombinací následujících faktorů:
* Úroveň služby fondu kapacity, do které patří svazek
* Kvóta přiřazená ke svazku  
* Typ QoS (*Automatické* nebo *Ruční*) fondu kapacit  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Příklady omezení propustnosti pro svazky ve fondu kapacity auto QoS

Následující diagram ukazuje příklady propustnosti svazků ve fondu kapacity auto QoS:

![Ilustrace úrovně služeb](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* V příkladu 1 výše se k fondu kapacity auto QoS s úrovní Premium Storage, která je přiřazená 2 TiB kvóty, přiřadí limit propustnosti 128 MiB/s (2 TiB * 64 MiB/s). Tento scénář se vztahuje bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu svazku.

* V příkladu 2 výše se k fondu kapacity auto QoS s úrovní Premium Storage, která je přiřazená 100 GiB kvóty, přiřadí limit propustnosti 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Tento scénář se vztahuje bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu svazku.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Příklady omezení propustnosti svazků v ručním fondu kapacity QoS 

Pokud používáte ruční fond kapacit QoS, můžete přiřadit kapacitu a propustnost pro svazek nezávisle. Když vytvoříte svazek v manuálním fondu kapacity QoS, můžete zadat hodnotu propustnosti (MiB/S). Celková propustnost přiřazená svazkům v manuálním fondu kapacity technologie QoS závisí na velikosti fondu a na úrovni služby. Je omezené podle (velikost fondu kapacit v propustnosti TiB x na úrovni služby/TiB). Například fond kapacit 10 TiB s nejvyšší úrovní služeb má celkovou kapacitu propustnosti 1280 MiB/s (10 TiB x 128 MiB/s/TiB), která je pro svazky k dispozici.

Pro SAP HANA systém je možné tento fond kapacit použít k vytvoření následujících svazků. Každý svazek poskytuje individuální velikost a propustnost pro splnění požadavků vaší aplikace:

* SAP HANA objem dat: velikost 4 TB s až 704 MiB/s
* SAP HANA svazek protokolu: velikost 0,5 TB s až 256 MiB/s
* SAP HANA sdílený svazek: velikost 1 TB s až 64 MiB/s.
* SAP HANA svazek zálohy: velikost 4,5 TB s až 256 MiB/s.

Následující diagram znázorňuje scénáře SAP HANAch svazků:

![Scénáře SAP HANA svazků technologie QoS](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Další kroky

- [Stránka s cenami Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- [Smlouva SLA (SLA) pro Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Dynamická změna úrovně služeb svazku](dynamic-change-volume-service-level.md) 
- [Cíle na úrovni služby pro replikaci mezi oblastmi](cross-region-replication-introduction.md#service-level-objectives)