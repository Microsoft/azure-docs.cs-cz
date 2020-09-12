---
title: 'Jak nakonfigurovat OpenVPN v Azure VPN Gateway: PowerShell'
description: Naučte se používat PowerShell k povolení protokolu OpenVPN v Azure VPN Gateway pro prostředí Point-to-site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 57bc53c28866b3f16e742c27b6a1600bfe6f44ab
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418819"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurace OpenVPN pro Azure Point-to-Site VPN Gateway

Tento článek vám pomůže nastavit **Protokol OpenVPN®** v Azure VPN Gateway. Článek předpokládá, že již máte funkční prostředí typu Point-to-site. Pokud to neuděláte, postupujte podle pokynů v kroku 1 a vytvořte síť VPN typu Point-to-site.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. vytvoření sítě VPN typu Point-to-site

Pokud ještě nemáte funkční prostředí Point-to-site, postupujte podle pokynů a vytvořte si ho. V tématu [vytvoření sítě VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) můžete vytvořit a nakonfigurovat BRÁNu VPN typu Point-to-site s nativním ověřováním certifikátů Azure. 

> [!IMPORTANT]
> Základní skladová položka není pro OpenVPN podporovaná.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. povolení OpenVPN v bráně

Povolte v bráně OpenVPN. Před spuštěním následujících příkazů se ujistěte, že je brána už nakonfigurovaná pro protokol IKEv2 nebo SSTP:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat klienty pro OpenVPN, přečtěte si téma [Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
