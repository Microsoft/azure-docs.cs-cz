---
title: Konfigurace tunelového připojení typu Always-On
titleSuffix: Azure VPN Gateway
description: Postup konfigurace tunelu Always On VPN pro VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9036992a354b41cd75735e5ccd895d7287a00e91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371717"
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
