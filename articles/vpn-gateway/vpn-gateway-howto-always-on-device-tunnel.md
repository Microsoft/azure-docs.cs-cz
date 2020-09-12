---
title: Konfigurace tunelového připojení typu Always-On
titleSuffix: Azure VPN Gateway
description: Naučte se, jak používat brány se systémem Windows 10, abyste navázali a nakonfigurovali trvalé tunely zařízení do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: ab951375fdf9c5317e2c3a2f67271666d0987142
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435843"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Konfigurace tunelu zařízení VPN AlwaysOn

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Konfigurace brány

Nakonfigurujte bránu VPN na používání protokolu IKEv2 a ověřování založeného na certifikátech pomocí článku [Konfigurace připojení VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="configure-the-device-tunnel"></a>Konfigurace tunelového připojení zařízení

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, spusťte následující příkaz:

![Vyčištění](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Řešení potíží najdete v tématu [problémy s připojením Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) .
