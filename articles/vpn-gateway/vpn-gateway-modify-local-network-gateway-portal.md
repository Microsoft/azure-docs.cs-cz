---
title: Upravit předpony IP adresy brány místní sítě a adresy IP adresa brány sítě VPN | Azure | Portál | Dokumentace Microsoftu
description: Tento článek vás provede změna předpony IP adres pro bránu místní sítě pomocí webu Azure portal.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651932"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Úprava nastavení místní síťové brány pomocí webu Azure portal

Někdy změnit nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak upravit nastavení brány místní sítě. Můžete také upravit tato nastavení pomocí jiné metody tak, že vyberete jinou možnost z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>Úprava předpon IP adres

Pokud upravíte předpony IP adres, kroky, které sledujete závisí na toho, jestli má připojení k bráně místní sítě.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Úprava IP adresy brány

Pokud zařízení VPN, ke kterému se chcete připojit, změnilo svou veřejnou IP adresu, musíte upravit bránu místní sítě, aby odrážela tuto změnu. Při změně veřejné IP adresy, kroky, které sledujete závisí na toho, jestli má připojení k bráně místní sítě.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Další postup

Můžete ověřit připojení k bráně. Zobrazit [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).