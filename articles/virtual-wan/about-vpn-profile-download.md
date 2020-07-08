---
title: 'Azure Virtual WAN: profily klientů VPN uživatele'
description: To vám pomůže pracovat se souborem profilu klienta.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: c64e7988094612077131029547682c7ae3d25c98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753148"
---
# <a name="working-with-user-vpn-client-profiles"></a>Práce s profily klienta VPN uživatele

Stažený soubor profilu obsahuje informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN uživatele.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace klientů OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti VPN uživatele sítě WAN najdete v tématu [vytvoření připojení k síti VPN uživatele](virtual-wan-point-to-site-portal.md).
