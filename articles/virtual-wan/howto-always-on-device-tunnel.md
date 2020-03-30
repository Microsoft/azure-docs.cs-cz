---
title: Konfigurace tunelového propojení VPN s nepřetržitým připojením
titleSuffix: Azure Virtual WAN
description: Postup konfigurace tunelového propojení zařízení Always On VPN pro virtuální síť WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: cherylmc
ms.openlocfilehash: e3eea639eaa52c07e877476e9215144e916618d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502851"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Konfigurace tunelového propojení zařízení ALWAYS On VPN pro virtuální síť WAN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Požadavky

Je nutné vytvořit konfiguraci bodu k webu a upravit přiřazení virtuálního centra. Pokyny naleznete v následujících částech:

* [Vytvoření konfigurace P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Úprava přiřazení centra](virtual-wan-point-to-site-portal.md#edit)

## <a name="configure-the-device-tunnel"></a>Konfigurace tunelového propojení zařízení

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Odebrání profilu

Chcete-li profil odebrat, spusťte následující příkaz:

![Vyčištění](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Další kroky

Další informace o virtuální paměti WAN naleznete v [nejčastějších dotazech](virtual-wan-faq.md).