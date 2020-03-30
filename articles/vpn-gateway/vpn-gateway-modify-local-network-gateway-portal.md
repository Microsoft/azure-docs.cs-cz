---
title: 'Brána VPN: Změna nastavení IP adres brány: Portál Azure'
description: Tento článek vás provede změnou předpony IP adres pro bránu místní sítě pomocí portálu Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864022"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Úprava nastavení brány místní sítě pomocí portálu Azure

Někdy se změní nastavení místní síťové brány AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak změnit nastavení brány místní sítě. Tato nastavení můžete také upravit jinou metodou výběrem jiné možnosti z následujícího seznamu:

Před odstraněním připojení můžete stáhnout konfiguraci pro vaše připojovací zařízení, abyste získali definovanou psk. Tímto způsobem, nemusíte předefinovat na druhé straně.

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Změna předpon IP adres

Při úpravě předpon IP adres závisí postup, který provedete, na tom, zda má brána místní sítě připojení.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Změna IP adresy brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Změníte-li veřejnou IP adresu, postup, který provedete, závisí na tom, zda má brána místní sítě připojení.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [Ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).
