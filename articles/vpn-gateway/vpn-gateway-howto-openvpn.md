---
title: Jak nakonfigurovat OpenVPN pro Azure VPN Gateway
description: Naučte se, jak povolit protokol OpenVPN v Azure VPN Gateway pro prostředí Point-to-site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584036"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>Konfigurace OpenVPN pro brány VPN typu Point-to-site

Tento článek vám pomůže nastavit **Protokol OpenVPN®** v Azure VPN Gateway. Můžete použít buď portál, nebo pokyny pro PowerShell.

## <a name="prerequisites"></a>Předpoklady

* Článek předpokládá, že již máte funkční prostředí typu Point-to-site. Pokud to neuděláte, vytvořte ho pomocí jedné z následujících metod.

  * [Portál – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell – vytvoření Point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)

* Ověřte, že vaše brána VPN nepoužívá základní SKU. Základní skladová položka není pro OpenVPN podporovaná.

## <a name="portal"></a>Portál

1. Na portálu přejděte na **bránu virtuální sítě – > konfiguraci Point-to-site**.
1. V poli **Typ tunelu** vyberte **OpenVPN (SSL)** z rozevíracího seznamu.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Z rozevíracího seznamu vyberte OpenVPN SSL.":::
1. Uložte změny a pokračujte dalšími **kroky**.

## <a name="powershell"></a>PowerShell

1. V bráně povolte OpenVPN pomocí následujícího příkladu:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Pokračujte **dalšími kroky**.

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat klienty pro OpenVPN, přečtěte si téma [Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
