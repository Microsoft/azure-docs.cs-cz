---
title: Úprava předpon adres IP brány místní sítě a adresa brány VPN | Azure | Prostředí PowerShell | Microsoft Docs
description: Tento článek vás provede procesem Změna předpony IP adresy pro bránu místní sítě pomocí prostředí PowerShell
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 2a095b96a8c352abeca72640d37c0d629b447763
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23885102"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Úprava nastavení místní síťové brány pomocí PowerShellu

Někdy se změnit nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak upravit nastavení brány místní sítě. Můžete také upravit tato nastavení pomocí různých metod výběrem jinou možnost z následujícího seznamu:

> [!div class="op_single_selector"]
> * [portál Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

## <a name="ipaddprefix"></a>Úprava předpon adres IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Upravit IP adresu brány

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. V tématu [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).