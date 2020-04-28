---
title: 'Azure ExpressRoute: přidání brány do virtuální sítě: PowerShell'
description: Tento článek vám pomůže přidat bránu virtuální sítě do již vytvořené Správce prostředků virtuální sítě pro ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74037434"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Správce prostředků – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasický – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vám pomůže přidat, změnit jeho velikost a odebrat bránu virtuální sítě (VNet) pro již existující virtuální síť. Kroky pro tuto konfiguraci platí pro virtuální sítě, které byly vytvořeny pomocí modelu nasazení Správce prostředků pro konfiguraci ExpressRoute. Další informace najdete v tématu [informace o branách virtuální sítě pro ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Před zahájením

### <a name="working-with-powershell"></a>Práce s prostředím PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Seznam odkazů konfigurace

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete připojit virtuální síť k okruhu ExpressRoute. Viz [propojení Virtual Network k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).
