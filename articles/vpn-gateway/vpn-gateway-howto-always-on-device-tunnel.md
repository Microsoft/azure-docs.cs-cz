---
title: Konfigurace tunelového připojení typu Always-On
titleSuffix: Azure VPN Gateway
description: Naučte se, jak používat brány se systémem Windows 10, abyste navázali a nakonfigurovali trvalé tunely zařízení do Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: e64d2223740942fe35ae8a730303bee8893c2489
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035599"
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
