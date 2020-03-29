---
title: 'Azure ExpressRoute: Přidání brány do virtuální sítě: klasické'
description: Nakonfigurujte bránu virtuální sítě pro virtuální síť klasického nasazení pomocí prostředí PowerShell pro konfiguraci ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74928051"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí PowerShellu (klasického)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky pro přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro již existující virtuální síť. Kroky pro tuto konfiguraci jsou speciálně pro virtuální sítě, které byly vytvořeny pomocí **klasického modelu nasazení** a které se použijí v konfiguraci ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Před zahájením
Ověřte, že jste nainstalovali rutiny Prostředí Azure PowerShell potřebné pro tuto konfiguraci.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě můžete virtuální síť propojit s okruhem ExpressRoute. Viz [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md).

