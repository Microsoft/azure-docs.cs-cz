---
title: Konfigurace tunelového připojení VPN Always-On
titleSuffix: Azure Virtual WAN
description: Postup konfigurace tunelového připojení zařízení VPN pro virtuální síť WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983689"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurace tunelového připojení zařízení VPN typu Always On pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Musíte vytvořit konfiguraci Point-to-site a upravit přiřazení virtuálního rozbočovače. Pokyny najdete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Vytvoření centra s bránou P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Konfigurace tunelového připojení zařízení

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, spusťte následující příkaz:

![Snímek obrazovky se zobrazí okno PowerShellu, ve kterém se spustí příkaz Remove-VpnConnection-Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).