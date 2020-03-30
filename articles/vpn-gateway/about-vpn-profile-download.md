---
title: 'Brána Azure VPN: O profilech klientů VPN P2S'
description: To vám pomůže pracovat se souborem profilu klienta
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528502"
---
# <a name="about-p2s-vpn-client-profiles"></a>O profilech klientů Vpn P2S

Stažený soubor profilu obsahuje informace, které jsou nezbytné pro konfiguraci připojení VPN. Tento článek vám pomůže získat a pochopit informace nezbytné pro profil klienta VPN.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* **Složka OpenVPN** obsahuje profil *ovpn,* který je třeba upravit tak, aby zahrnoval klíč a certifikát. Další informace najdete [v tématu Konfigurace klientů OpenVPN pro Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Tato složka nebude k dispozici v souboru zip, pokud je v bráně VPN vybráno ověřování Azure AD. Místo toho bude azurevpnconfig.xml ve složce AzureVPN.

## <a name="next-steps"></a>Další kroky

Další informace o bodu na web naleznete v tématu [Informace o tématu point-to-site](point-to-site-about.md).
