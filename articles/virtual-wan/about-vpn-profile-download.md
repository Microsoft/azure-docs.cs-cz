---
title: 'Azure Virtual WAN: profily klientů VPN uživatele'
description: To vám pomůže pracovat se souborem profilu klienta.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980911"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Práce se soubory profilů klienta VPN uživatele

Soubory profilu obsahují informace potřebné ke konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace, které jsou nezbytné pro profil klienta VPN uživatele.

## <a name="download-the-profile"></a>Stáhnout profil

K stažení souboru ZIP profilu klienta můžete použít postup v článku [stažení profilů](global-hub-profile.md) .

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn* , který je potřeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete v tématu [Konfigurace klientů OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti VPN uživatele sítě WAN najdete v tématu [vytvoření připojení k síti VPN uživatele](virtual-wan-point-to-site-portal.md).
