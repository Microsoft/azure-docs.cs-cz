---
title: 'Přidat bránu virtuální sítě k virtuální síti pro ExpressRoute: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: V tomto článku vám pomůžou přidat bránu virtuální sítě k již vytvořené virtuální síti správce prostředků pro ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 04/17/2017
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 8f8d021adae1cb25198c53e486fdb50e42ac211b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097909"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek pomůže přidání, změna velikosti a odstranit bránu virtuální sítě (VNet) pro už existující virtuální síť. Tady jsou kroky pro tuto konfiguraci speciálně pro virtuální sítě vytvořené pomocí modelu nasazení Resource Manageru, který se použije v konfiguraci služby ExpressRoute. Další informace o branách virtuálních sítí a nastavení konfigurace brány pro ExpressRoute najdete v tématu [o branách virtuálních sítí pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením
Ověřte, že máte nainstalovanou nejnovější rutiny prostředí Azure PowerShell. Pokud jste nenainstalovali nejnovější rutiny, musíte udělat před zahájením kroků konfigurace. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Zobrazit [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).

