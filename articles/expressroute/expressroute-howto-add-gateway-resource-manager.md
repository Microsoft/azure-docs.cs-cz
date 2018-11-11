---
title: 'Přidat bránu virtuální sítě k virtuální síti pro ExpressRoute: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Tento článek vás provede přidání brány virtuální sítě k již vytvořené virtuální síti správce prostředků pro ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 32e49a11b02afedf69e5aa61ca2f626ffe5a125e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239572"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání, změna velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Tady jsou kroky pro tuto konfiguraci speciálně pro virtuální sítě vytvořené pomocí modelu nasazení Resource Manageru, který se použije v konfiguraci služby ExpressRoute. Další informace o branách virtuálních sítí a nastavení konfigurace brány pro ExpressRoute najdete v tématu [o branách virtuálních sítí pro ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Před zahájením
Ověřte, že máte nainstalovanou nejnovější rutiny prostředí Azure PowerShell. Pokud jste nenainstalovali nejnovější rutiny, musíte udělat před zahájením kroků konfigurace. Další informace najdete v článku [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Další postup
Po vytvoření brány virtuální sítě můžete propojit vaši virtuální síť k okruhu ExpressRoute. Zobrazit [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).

