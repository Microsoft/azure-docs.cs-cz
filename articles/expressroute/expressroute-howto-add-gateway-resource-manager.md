---
title: 'Přidáte bránu virtuální sítě k virtuální síti pro ExpressRoute: PowerShell: Azure | Dokumentace Microsoftu'
description: V tomto článku vám pomůžou přidat bránu virtuální sítě k již vytvořené virtuální síti správce prostředků pro ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3c91fd6140b460d29b33e7d9b1fabafbbcf99422
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406214"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek pomůže přidání, změna velikosti a odstranit bránu virtuální sítě (VNet) pro už existující virtuální síť. Postup pro tuto konfiguraci se vztahuje na virtuální sítě vytvořené pomocí modelu nasazení Resource Manageru pro konfiguraci služby ExpressRoute. Další informace najdete v tématu [o branách virtuálních sítí pro ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Před zahájením

### <a name="working-with-powershell"></a>Práce s využitím Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Přehled konfigurace

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Zobrazit [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).