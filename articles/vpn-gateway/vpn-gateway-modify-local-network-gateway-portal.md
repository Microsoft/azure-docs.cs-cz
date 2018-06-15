---
title: Úprava předpon adres IP brány místní sítě a adresa brány VPN | Azure | Portál | Microsoft Docs
description: Tento článek vás provede změna předpony IP adresy pro bránu místní sítě pomocí portálu Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: bdd6f90fe97408bd45a72adf58bfdc190207de46
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23883821"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Upravit nastavení brány místní sítě pomocí portálu Azure

Někdy se změnit nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak upravit nastavení brány místní sítě. Můžete také upravit tato nastavení pomocí různých metod výběrem jinou možnost z následujícího seznamu:

> [!div class="op_single_selector"]
> * [portál Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Úprava předpon adres IP

Když upravíte předpony IP adres, kroky, které budete postupovat podle závisí na tom, jestli má bránu místní sítě připojení.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Upravit IP adresu brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Když změníte veřejnou IP adresu, kroky, které budete postupovat podle závisí na tom, jestli má bránu místní sítě připojení.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Další kroky

Můžete ověřit připojení brány. V tématu [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).