---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812666"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Z oficiálního webu si stáhněte klienta OpenVPN a nainstalujte ho.
1. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace sítě VPN uživatele v Azure Portal nebo New-AzureRmVpnClientConfiguration v PowerShellu.
1. Rozbalte profil. V Poznámkovém bloku otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN.
1. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. Ve formátovaných certifikátech PEM můžete otevřít soubor. cer a zkopírovat ho do klíče Base64 mezi hlavičkami certifikátů. Postup najdete v tématu [Postup exportu certifikátu pro získání kódovaného veřejného klíče.](../articles/virtual-wan/certificates-point-to-site.md)
1. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Postup najdete v tématu [postup extrakce privátního klíče.](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
1. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
1. Na hlavním panelu systému klikněte pravým tlačítkem na ikonu OpenVPN a vyberte **připojit**.

##### <a name="ikev2"></a>IKEv2

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86.
1. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**a potom na **přesto spustit**.
1. V klientském počítači přejděte na **nastavení sítě** a vyberte **síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.
1. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů najdete v tématu věnovaném [generování certifikátů](../articles/virtual-wan/certificates-point-to-site.md). Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
