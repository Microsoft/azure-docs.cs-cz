---
title: Konfigurace klientů OpenVPN pro Azure Virtual WAN
description: Postup konfigurace klientů OpenVPN pro Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490997"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Konfigurace klienta OpenVPN pro Azure Virtual WAN

Tento článek vám pomůže nakonfigurovat **klienty &reg; protokolu OpenVPN** . K připojení prostřednictvím protokolu OpenVPN můžete použít také klienta Azure VPN pro Windows 10. Další pokyny najdete [tady](openvpn-azure-ad-client.md) .

## <a name="before-you-begin"></a>Než začnete

Vytvořte konfiguraci VPN uživatele (Point-to-site). Ujistěte se, že pro typ tunelu vyberete "OpenVPN". Postup najdete v tématu [Vytvoření konfigurace P2S pro Azure Virtual WAN](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Další informace o uživatelské síti VPN (Point-to-site) najdete v tématu [Vytvoření uživatelských připojení VPN](virtual-wan-point-to-site-portal.md).

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**
