---
title: Úrovně služby pro Azure NetApp Files | Dokumentace Microsoftu
description: Popisuje propustnost výkonu pro úrovně služeb Azure NetApp souborů.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523110"
---
# <a name="service-levels-for-azure-netapp-files"></a>Úrovně služeb pro Azure NetApp Files
Úrovně služeb se atribut kapacity fondu. Úrovně služby jsou definovány a odlišit použitím povolený maximální propustnost pro svazku ve fondu kapacitu podle kvóty, které je přiřazeno ke svazku.

## <a name="supported-service-levels"></a>Podporované služby úrovně

Služba soubory Azure NetApp podporuje tři úrovně služeb: *Ultra*, *Premium*, a *standardní*. 

* <a name="Ultra"></a>Ultra úložiště

    Úroveň Ultra úložiště poskytuje až 128 MiB/s propustnost za 1 TiB kvóty přiřazené. 

* <a name="Premium"></a>Storage úrovně Premium

    Storage úrovně Premium se poskytuje až 64 MiB/s propustnost za 1 TiB kvóty přiřazené. 

* <a name="Standard"></a>Storage úrovně Standard

    Vrstvy úložiště úrovně Standard poskytuje až 16 MiB/s propustnost za 1 TiB kvóty přiřazené.

## <a name="throughput-limits"></a>Omezení propustnosti

Limit propustnosti pro svazek se určuje podle kombinace následující faktory:
* Úrovně služby kapacity fondu, ke kterému patří svazku
* Kvóta přiřadí svazku  

Tento koncept je znázorněn v následujícím diagramu:

![Obrázek úrovně služby](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

V příkladu výše 1, svazek z kapacity fondu s úrovní Premium storage, který je přiřazen 2 TB kvóty přiřadí propustnost maximálně 128 MiB/s (2 TB * 64 MiB/s). Tento scénář platí bez ohledu na velikost kapacity fondu nebo spotřeby skutečné velikosti.

V příkladu 2 výše, bude svazek z kapacity fondu úložiště na úrovni Premium, které je přiřazeno 100 GB kvóty přiřazena omezení propustnosti 6.25 MiB/s (0.09765625 TiB * 64 MiB/s). Tento scénář platí bez ohledu na velikost kapacity fondu nebo spotřeby skutečné velikosti.

## <a name="next-steps"></a>Další postup

- Najdete v článku [souborů NetApp Azure stránce s cenami](https://azure.microsoft.com/pricing/details/storage/netapp/) za cenu úrovní různých služeb
- Zobrazit [modelu nákladů pro soubory Azure NetApp](azure-netapp-files-cost-model.md) pro výpočet spotřebu kapacity v kapacitě fondu 
- [Nastavení fondu kapacity](azure-netapp-files-set-up-capacity-pool.md)