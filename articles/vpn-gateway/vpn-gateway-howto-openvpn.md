---
title: 'Jak nakonfigurovat OpenVPN na Azure VPN Gateway: PowerShell'
description: Postup konfigurace OpenVPN pro Bránu VPN Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 7505420cc31fe751ecc0c114a89fea0734cbc6cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162403"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurace OpenVPN pro bránu VPN z bodu na pracoviště Azure

Tento článek vám pomůže nastavit **OpenVPN® protokol** na Azure VPN Gateway. Článek předpokládá, že již máte pracovní prostředí point-to-site. Pokud tak neučiníte, vytvořte pomocí pokynů v kroku 1 síť VPN z bodu na web.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. Vytvoření VPN z bodu na web

Pokud ještě nemáte funkční prostředí point-to-site, postupujte podle pokynů k jeho vytvoření. Viz [Vytvoření vpn typu point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) k vytvoření a konfiguraci brány VPN typu point-to-site s nativním ověřováním certifikátů Azure. 

> [!IMPORTANT]
> Základní skladová položka není podporována pro OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. Povolit OpenVPN na bráně

Povolte OpenVPN na vaší bráně. Před spuštěním následujících příkazů se ujistěte, že je brána již nakonfigurována pro bod k webu (IKEv2 nebo SSTP):

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci klientů pro OpenVPN najdete [v tématu Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
