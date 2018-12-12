---
title: 'Konfigurace brány virtuální sítě pro ExpressRoute - classic: Azure Powershellu | Dokumentace Microsoftu'
description: Konfigurace brány virtuální sítě pro nasazení classic model virtuální sítě pomocí prostředí PowerShell pro konfiguraci služby ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 11/05/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 101e03d07a15e9058ef236a575251b052017db32
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083259"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurace brány virtuální sítě pro ExpressRoute přes PowerShell (classic)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Tady jsou kroky pro tuto konfiguraci pro virtuální sítě, které byly vytvořeny pomocí **modelu nasazení classic** a, který se použije v konfiguraci služby ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Před zahájením
Ověřte, že máte nainstalovanou rutin Azure Powershellu, které jsou potřebné pro tuto konfiguraci (verze 1.0.2 nebo novější). Pokud jste nenainstalovali rutiny, budete muset udělat před zahájením kroků konfigurace. Další informace o instalaci Azure Powershellu najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Zobrazit [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

