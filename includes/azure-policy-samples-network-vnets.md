---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664482"
---
### <a name="virtual-networks"></a>Virtuální sítě

|  |  |
|---------|---------|
| [Povolené skladové položky Application Gateway](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | Vyžaduje, aby aplikační brány používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Blokování partnerských vztahů se sítí ER](../articles/azure-policy/scripts/no-peering-er-net.md) | Zakáže přidružení partnerských vztahů k síti v zadané skupině prostředků. Tuto možnost využijete, pokud chcete zabránit připojení k centrálně spravované síťové infrastruktuře. Zadejte název skupiny prostředků, aby se zabránilo přidružení. |
| [Žádná tabulka směrování definovaná uživatelem](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Zakáže nasazení virtuálních sítí s tabulkou směrování definovanou uživatelem. |
| [NSG X ve všech podsítích](../articles/azure-policy/scripts/nsg-on-subnet.md) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |