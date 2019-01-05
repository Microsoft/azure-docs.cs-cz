---
title: Omezení prostředků pro soubory Azure NetApp | Dokumentace Microsoftu
description: Popisuje omezení pro souborů NetApp Azure prostředky, včetně omezení pro fondy kapacitu, svazky a delegované podsítě.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056870"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Omezení prostředků pro soubory Azure NetApp
Principy omezení prostředků pro soubory Azure NetApp umožňuje správu svazků.

## <a name="capacity_pools"></a>Fondy kapacit

- Minimální velikost pro jednu kapacitu fondu je 4 TB a maximální velikost je 500 TB. 
- Každá kapacita fondu může patřit do pouze jeden účet NetApp. Však může mít několik fondů kapacity v rámci účtu NetApp.  

## <a name="volumes"></a>Svazky

- Minimální velikost pro samostatný svazek je 100 GB a maximální velikost je 92 TiB.
- V jedné oblasti může mít maximálně 100 svazky na předplatné Azure.  

## <a name="delegated_subnet"></a>Delegované podsítě 

V každé Azure Virtual Network (Vnet) je možné jenom jednu podsíť delegovat do služby soubory Azure NetApp.

## <a name="next-steps"></a>Další postup

[Vysvětlení hierarchii úložiště souborů Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md)
