---
title: 'VPN Gateway: Upravte nastavení IP adresy brány: Azure Portal'
description: Tento článek vás provede změnou předpon IP adres pro bránu místní sítě pomocí Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864022"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Úprava nastavení místní síťové brány pomocí Azure Portal

Někdy se nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress změní. V tomto článku se dozvíte, jak upravit nastavení místní síťové brány. Tato nastavení můžete také upravit pomocí jiné metody výběrem jiné možnosti z následujícího seznamu:

Než připojení odstraníte, možná budete chtít stáhnout konfiguraci pro připojení zařízení, aby se získal definovaný PSK. Tímto způsobem ho nemusíte předefinovat na druhé straně.

> [!div class="op_single_selector"]
> * [portál Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Prostředí](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Upravit předpony IP adres

Když upravíte předpony IP adres, postup podle následujících kroků závisí na tom, jestli má brána místní sítě připojení.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Úprava IP adresy brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Když změníte veřejnou IP adresu, postupujte podle kroků uvedených v závislosti na tom, jestli má brána místní sítě připojení.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. Viz [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).
