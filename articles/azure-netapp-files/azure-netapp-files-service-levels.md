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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533117"
---
# <a name="service-levels-for-azure-netapp-files"></a>Úrovně služeb pro Azure NetApp Files
Úrovně služeb jsou atributem fondu kapacit. Úrovně služeb jsou definované a rozlišené o povolenou maximální propustnost svazku ve fondu kapacity na základě kvóty, která je přiřazená ke svazku.

## <a name="supported-service-levels"></a>Podporované úrovně služeb

Azure NetApp Files podporuje tři úrovně služeb: *Ultra*, *Premium*a *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    Úroveň úložiště úrovně Ultra poskytuje až 128 MiB/s za propustnost na 1 TiB přidělené kvóty. 

* <a name="Premium"></a>Premium Storage

    Úroveň Premium Storage poskytuje až 64 MiB/s propustnosti na 1 TiB přidělené kvóty. 

* <a name="Standard"></a>Storage úrovně Standard

    Úroveň Standard úložiště poskytuje až 16 MiB/s propustnost na 1 TiB přidělené kvóty.

## <a name="throughput-limits"></a>Omezení propustnosti

Omezení propustnosti svazku je určeno kombinací následujících faktorů:
* Úroveň služby fondu kapacity, do které patří svazek
* Kvóta přiřazená ke svazku  

Tento koncept je znázorněný v následujícím diagramu:

![Ilustrace úrovně služeb](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

V příkladu 1 výše se ke svazku z fondu kapacit s úrovní Premium Storage přiřazeným 2 TiB kvótě přiřadí limit propustnosti 128 MiB/s (2 TiB * 64 MiB/s). Tento scénář se vztahuje bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu svazku.

V příkladu 2 výše se ke skupině kapacity s úrovní úložiště úrovně Premium, která je přiřazená 100 GiB kvóty, přiřadí limit propustnosti 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Tento scénář se vztahuje bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu svazku.

## <a name="next-steps"></a>Další kroky

- [Stránka s cenami Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Model nákladů pro Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- [Smlouva SLA (SLA) pro Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Dynamická změna úrovně služeb svazku](dynamic-change-volume-service-level.md) 