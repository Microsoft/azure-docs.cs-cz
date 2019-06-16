---
title: 'Jak nakonfigurovat OpenVPN ve službě Azure VPN Gateway: Prostředí PowerShell | Dokumentace Microsoftu'
description: Postup konfigurace OpenVPN pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 609c2ef91fafe0ae955252a594292d861e772f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002962"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Konfigurace OpenVPN pro bránu Azure VPN point-to-site

Tento článek vám pomůže nastavit **OpenVPN® protokol** ve službě Azure VPN Gateway. Tento článek předpokládá, že jste již pracovní prostředí point-to-site. Pokud ho nevidíte, postupujte podle pokynů v kroku 1 k vytvoření sítě VPN point-to-site.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="vnet"></a>1. Vytvoření sítě VPN point-to-site

Pokud ještě nemáte funkční prostředí point-to-site, postupujte podle pokynů a vytvořte si ho. Zobrazit [vytvořit síť point-to-site VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) vytváření a konfiguraci brány VPN typu point-to-site s ověřováním pomocí nativního certifikátu Azure. 

> [!IMPORTANT]
> Základní SKU se nepodporuje pro OpenVPN.

## <a name="enable"></a>2. Povolit OpenVPN na bráně

Povolte OpenVPN na bráně. Ujistěte se, že brána je už nakonfigurovaná pro point-to-site (protokol IKEv2 nebo SSTP) před spuštěním následujících příkazů:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Další postup

Konfigurování klientů pro OpenVPN, naleznete v tématu [klienty nakonfigurovat OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
