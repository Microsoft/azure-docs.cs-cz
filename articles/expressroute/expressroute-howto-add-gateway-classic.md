---
title: 'Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell: classic: Azure | Microsoft Docs'
description: Konfigurace brány virtuální sítě pro nasazení classic modelu virtuální sítě pomocí prostředí PowerShell konfigurace ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: 195a38fa45f1c514a93980e777fb0d8238aa3f3f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850634"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Konfigurace brány virtuální sítě pro ExpressRoute pomocí prostředí PowerShell (klasické)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic – prostředí PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Tento článek vás provede kroky k přidání, změně velikosti a odebrání brány virtuální sítě (VNet) pro už existující virtuální síť. Kroky pro tuto konfiguraci jsou speciálně určené pro virtuální sítě vytvořené pomocí **modelu nasazení classic** a který bude použít v konfiguraci ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Před zahájením
Ověřte, zda jste nainstalovali rutin prostředí Azure PowerShell, které jsou potřebné pro tuto konfiguraci (1.0.2 nebo novější). Pokud jste nenainstalovali rutiny, budete muset udělat před zahájením kroky konfigurace. Další informace o instalaci prostředí Azure PowerShell najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Další kroky
Po vytvoření brány virtuální sítě, můžete se propojit virtuální sítě k okruhu ExpressRoute. V tématu [propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-classic.md).

