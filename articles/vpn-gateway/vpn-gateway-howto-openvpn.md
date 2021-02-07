---
title: Jak nakonfigurovat OpenVPN v Azure VPN Gateway
description: Naučte se používat PowerShell k povolení protokolu OpenVPN v Azure VPN Gateway pro prostředí Point-to-site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 34f24b8fbdb28e1b1f73e9db428c510d3f4661ce
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804835"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurace OpenVPN pro Azure Point-to-Site VPN Gateway

Tento článek vám pomůže nastavit **Protokol OpenVPN®** v Azure VPN Gateway. Můžete použít buď portál, nebo pokyny pro PowerShell.

## <a name="prerequisites"></a>Požadavky

* Článek předpokládá, že již máte funkční prostředí typu Point-to-site. Pokud to neuděláte, vytvořte ho pomocí jedné z následujících metod.

  * [Portál – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)

* Ověřte, že vaše brána VPN nepoužívá základní SKU. Základní skladová položka není pro OpenVPN podporovaná.

## <a name="portal"></a>Portál

1. Na portálu přejděte na **bránu virtuální sítě – > konfiguraci Point-to-site**.
1. V poli **Typ tunelového propojení** vyberte v rozevíracím seznamu možnost **OpenVPN (SSL)** nebo **IKEv2 a OpenVPN (SSL)** .

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Z rozevíracího seznamu vyberte OpenVPN SSL.":::
1. Uložte změny a pokračujte dalšími **kroky**.

Povolte v bráně OpenVPN.

1. V bráně povolte OpenVPN pomocí následujícího příkladu:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Pokračujte **dalšími kroky**.

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat klienty pro OpenVPN, přečtěte si téma [Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
