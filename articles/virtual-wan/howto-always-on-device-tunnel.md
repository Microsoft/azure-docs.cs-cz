---
title: Konfigurace tunelového připojení typu Always-On
titleSuffix: Azure Virtual WAN
description: Postup konfigurace tunelového připojení zařízení VPN pro virtuální síť WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e911bf6e3736c931ca5c1563ab42f52ecb5cf3c1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84750584"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurace tunelového připojení zařízení VPN typu Always On pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Musíte vytvořit konfiguraci Point-to-site a upravit přiřazení virtuálního rozbočovače. Pokyny najdete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Upravit přiřazení centra](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurace tunelového připojení zařízení

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Postup odebrání profilu

Chcete-li odebrat profil, spusťte následující příkaz:

![Vyčištění](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).