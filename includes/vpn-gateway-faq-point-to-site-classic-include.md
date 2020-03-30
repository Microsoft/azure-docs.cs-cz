---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76309029"
---
Tyto nejčastější dotazy platí pro připojení P2S, která používají klasický model nasazení.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Jaké klientské operační systémy je možné používat s připojeními typu Point-to-Site?

Podporovány jsou následující operační systémy:

* Windows 7 (32bitové a 64bitové verze)
* Windows Server 2008 R2 (pouze 64bitové verze)
* Windows 8 (32bitové a 64bitové verze)
* Windows 8.1 (32bitové a 64bitové verze)
* Windows Server 2012 (pouze 64bitové verze)
* Windows Server 2012 R2 (pouze 64bitové verze)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Mohu použít jakýkoli softwarový klient VPN, který podporuje SSTP pro Point-to-Site?

Ne. Podpora je omezena pouze na uvedené verze operačního systému Windows.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Kolik koncových bodů klienta VPN může existovat v konfiguraci point-to-site?

Množství koncových bodů klienta VPN závisí na sku brány a protokolu.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. Stále můžete nahrát až 20 kořenových certifikátů.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Mohu procházet proxy servery a brány firewall pomocí point-to-site?

Ano. K tunelování přes brány firewall používáme protokol SSTP (Secure Socket Tunneling Protocol). Toto tunelové propojení se zobrazí jako připojení HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Pokud restartuji klientský počítač nakonfigurovaný pro připojení Point-to-Site, připojí se síť VPN automaticky znovu?

Ve výchozím nastavení klientský počítač automaticky neobnoví připojení VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Podporuje point-to-site automatické opětovné připojení a DDNS v klientech VPN?

Ne. Automatické opětovné připojení a DDNS nejsou aktuálně podporovány v vpn point-to-site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Mohu mít konfigurace Site-to-Site a Point-to-Site pro stejnou virtuální síť?

Ano. Obě řešení budou fungovat, pokud máte routebased typ VPN pro vaši bránu. Pro model nasazení Classic je potřebná dynamická brána. Nepodporujeme point-to-site pro statické směrování vpn brány nebo brány, které používají **-VpnType PolicyBased** rutina.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Je možné nakonfigurovat klienta Point-to-Site tak, aby se připojoval k několik virtuálním sítím současně?

Ano. Virtuální sítě však nemohou mít překrývající se předpony IP a adresní prostory point-to-site se nesmí překrývat mezi virtuálními sítěmi.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakou propustnost je možné očekávat u připojení typu Site-to-Site nebo Point-to-Site?

Určit přesnou propustnost tunelových propojení sítí VPN je obtížné. IPsec a SSTP jsou kryptograficky náročné protokoly sítě VPN. Propustnost je také omezena latencí a šířkou pásma mezi vašimi prostory a internetem.
