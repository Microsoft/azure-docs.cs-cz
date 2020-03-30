---
title: Úrovně služeb pro soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje výkon propustnost pro úrovně služeb souborů Azure NetApp.
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
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832584"
---
# <a name="service-levels-for-azure-netapp-files"></a>Úrovně služeb pro Azure NetApp Files
Úrovně služeb jsou atributem fondu kapacity. Úrovně služeb jsou definovány a rozlišeny povolenou maximální propustností pro objem ve fondu kapacity na základě kvóty, která je přiřazena k svazku.

## <a name="supported-service-levels"></a>Podporované úrovně služeb

Soubory Azure NetApp podporují tři úrovně služeb: *Ultra*, *Premium*a *Standard*. 

* <a name="Ultra"></a>Ultra úložiště

    Úroveň úložiště Ultra poskytuje až 128 MiB/s propustnostna na 1 TiB kvóty svazku přiřazena. 

* <a name="Premium"></a>Premium Storage

    Úroveň úložiště Premium poskytuje až 64 MiB/s propustnostna na 1 TiB kvóty svazku přiřazena. 

* <a name="Standard"></a>Standardní úložiště

    Úroveň úložiště Standard poskytuje až 16 MiB/s propustnostna na 1 TiB kvóty svazku přiřazena.

## <a name="throughput-limits"></a>Omezení propustnosti

Limit propustnost pro objem je určen kombinací následujících faktorů:
* Úroveň služeb fondu kapacity, do kterého svazek patří
* Kvóta přiřazená svazku  

Tento koncept je znázorněn na obrázku níže:

![Ilustrace na úrovni služby](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

V příkladu 1 výše bude svazku z fondu kapacity s úrovní úložiště Premium, kterému je přiřazena 2 TiB kvóty, přiřazen limit propustnosti 128 MiB/s (2 TiB * 64 MiB/s). Tento scénář platí bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu objemu.

V příkladu 2 výše bude svazku z fondu kapacity s úrovní úložiště Premium, kterému je přiřazeno 100 GB kvóty, přiřazen limit propustnosti 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Tento scénář platí bez ohledu na velikost fondu kapacity nebo skutečnou spotřebu objemu.

## <a name="next-steps"></a>Další kroky

- Ceny různých úrovní služeb najdete na [stránce s cenami souborů Azure NetApp.](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Viz [Nákladový model pro soubory Azure NetApp](azure-netapp-files-cost-model.md) pro výpočet spotřeby kapacity ve fondu kapacity 
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)
- Viz [Smlouva o úrovni služeb (SLA) pro soubory Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp/)