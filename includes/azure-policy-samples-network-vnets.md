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
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318215"
---
### <a name="virtual-networks"></a>Virtuální sítě

|  |  |
|---------|---------|
| [Povolené skladové položky Application Gateway](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Vyžaduje, aby aplikační brány používaly schválenou skladovou položku. Zadejte pole schválených skladových položek. |
| [Povolené skladové položky brány virtuální sítě](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Vyžaduje, aby brány virtuálních sítí používaly schválený typ skladové položky. Zadejte pole schválených skladových položek. |
| [Povolené skladové položky nástroje pro vyrovnávání zatížení](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Vyžaduje, aby nástroje pro vyrovnávání zatížení virtuálních sítí používaly schválený typ skladové položky. Zadejte pole schválených skladových položek. |
| [Bez síťového peeringu k síti ExpressRoute](../articles/governance/policy/samples/no-peering-express-route-network.md) | Zakáže přidružení partnerských vztahů k síti v zadané skupině prostředků. Tuto možnost využijete, pokud chcete zabránit připojení k centrálně spravované síťové infrastruktuře. Zadejte název skupiny prostředků, aby se zabránilo přidružení. |
| [Žádná tabulka směrování definovaná uživatelem](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Zakáže nasazení virtuálních sítí s tabulkou směrování definovanou uživatelem. |
| [NSG X ve všech podsítích](../articles/governance/policy/samples/nsg-on-subnet.md) | Vyžaduje, aby se ve všech virtuálních podsítích používala konkrétní skupina zabezpečení sítě. Zadejte ID skupiny zabezpečení sítě, která se má použít. |
| [Použití schválené podsítě pro síťová rozhraní virtuálních počítačů](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou podsíť. Zadejte ID schválené podsítě. |
| [Použití schválené virtuální sítě pro síťová rozhraní virtuálních počítačů](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Vyžaduje, aby síťová rozhraní používala schválenou virtuální síť. Zadáte ID schválené virtuální sítě. |