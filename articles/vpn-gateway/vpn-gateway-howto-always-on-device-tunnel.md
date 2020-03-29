---
title: Konfigurace tunelového propojení VPN s nepřetržitým připojením
titleSuffix: Azure VPN Gateway
description: Postup konfigurace tunelového propojení VPN always on pro bránu VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371717"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurace tunelu zařízení VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurace brány

Nakonfigurujte bránu VPN tak, aby používala protokol IKEv2 a ověřování založené na certifikátech pomocí článku [Konfigurace připojení VPN typu Point-to-Site.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

## <a name="configure-the-device-tunnel"></a>Konfigurace tunelového propojení zařízení

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Odebrání profilu

Chcete-li profil odebrat, spusťte následující příkaz:

![Vyčištění](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Řešení potíží najdete v [tématu Problémy s připojením z bodu na pracoviště Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
