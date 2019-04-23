---
title: Upravit předpony IP adresy brány místní sítě a adresy IP adresa brány sítě VPN | Azure | Prostředí PowerShell | Dokumentace Microsoftu
description: Tento článek vás provede s Změna předpony IP adres pro bránu místní sítě pomocí Powershellu
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
ms.openlocfilehash: 923c478909e0393f36da06802023b3f40ee63a3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419125"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Úprava nastavení místní síťové brány pomocí PowerShellu

Někdy změnit nastavení pro bránu místní sítě AddressPrefix nebo GatewayIPAddress. Tento článek ukazuje, jak upravit nastavení brány místní sítě. Můžete také upravit tato nastavení pomocí jiné metody tak, že vyberete jinou možnost z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Než začnete

Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

## <a name="ipaddprefix"></a>Úprava předpon IP adres

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="gwip"></a>Úprava IP adresy brány

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Další postup

Můžete ověřit připojení k bráně. Zobrazit [ověření připojení brány](vpn-gateway-verify-connection-resource-manager.md).