---
title: zahrnout soubor
description: zahrnout soubor
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003670"
---
### <a name="virtual-networks"></a>Virtuální sítě

|  |  |
|---------|---------|
| [Povolené skladové položky Application Gateway](../articles/governance/policy/samples/allowed-app-gate-sku.md) | Vyžaduje, aby aplikační brány používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Blokování partnerských vztahů se sítí ER](../articles/governance/policy/samples/no-peering-er-net.md) | Zakáže přidružení partnerských vztahů k síti v zadané skupině prostředků. Tuto možnost využijete, pokud chcete zabránit připojení k centrálně spravované síťové infrastruktuře. Zadejte název skupiny prostředků, aby se zabránilo přidružení. |
| [Žádná tabulka směrování definovaná uživatelem](../articles/governance/policy/samples/no-user-def-route-table.md)  |Zakáže nasazení virtuálních sítí s tabulkou směrování definovanou uživatelem. |
| [NSG X ve všech podsítích](../articles/governance/policy/samples/nsg-on-subnet.md) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |