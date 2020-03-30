---
title: 'Azure ExpressRoute: Přidání brány do virtuální sítě: PowerShell'
description: Tento článek vám pomůže přidat bránu virtuální sítě do již vytvořené virtuální sítě Správce prostředků pro ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037434"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Správce prostředků – portál Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vám pomůže přidat, změnit velikost a odebrat bránu virtuální sítě (VNet) pro již existující virtuální síť. Kroky pro tuto konfiguraci platí pro virtuální sítě, které byly vytvořeny pomocí modelu nasazení Správce prostředků pro konfiguraci ExpressRoute. Další informace naleznete v [tématu O branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Před zahájením

### <a name="working-with-powershell"></a>Práce s PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Seznam odkazů na konfiguraci

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete virtuální síť propojit s okruhem ExpressRoute. Viz [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md).
