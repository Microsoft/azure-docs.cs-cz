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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "76309029"
---
Tyto nejčastější dotazy se vztahují na připojení P2S, která používají model nasazení Classic.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Jaké klientské operační systémy je možné používat s připojeními typu Point-to-Site?

Podporovány jsou následující operační systémy:

* Windows 7 (32bitové a 64bitové verze)
* Windows Server 2008 R2 (pouze 64bitové verze)
* Windows 8 (32bitové a 64bitové verze)
* Windows 8.1 (32bitové a 64bitové verze)
* Windows Server 2012 (pouze 64bitové verze)
* Windows Server 2012 R2 (pouze 64bitové verze)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Můžu použít libovolného softwarového klienta sítě VPN, který podporuje SSTP pro připojení Point-to-site?

No. Podpora je omezená jenom na uvedené verze operačního systému Windows.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Kolik koncových bodů klienta VPN může existovat v konfiguraci typu Point-to-site?

Počet koncových bodů klienta VPN závisí na SKU brány a protokolu.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. Můžete i nadále nahrávat až 20 kořenových certifikátů.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Je možné procházet proxy a brány firewall pomocí Point-to-site?

Ano. K tunelu používáme protokol SSTP (Secure Socket Tunneling Protocol) prostřednictvím bran firewall. Toto tunelové propojení se zobrazí jako připojení HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Pokud restartuji klientský počítač nakonfigurovaný pro připojení Point-to-Site, připojí se síť VPN automaticky znovu?

Ve výchozím nastavení klientský počítač automaticky znovu nevytvoří připojení k síti VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Podporuje Point-to-site automatické obnovení připojení a DDNS na klientech VPN?

No. Automatické opětovné připojení a DDNS se v tuto chvíli nepodporují u sítí VPN typu Point-to-site.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Můžu mít pro stejnou virtuální síť konfigurace site-to-site a Point-to-site?

Ano. Obě řešení budou fungovat, pokud pro bránu používáte typ VPN RouteBased. Pro model nasazení Classic je potřebná dynamická brána. Nepodporujeme připojení Point-to-site pro brány VPN se statickým směrováním nebo brány, které používají rutinu **-VpnType PolicyBased** .

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Je možné nakonfigurovat klienta Point-to-Site tak, aby se připojoval k několik virtuálním sítím současně?

Ano. Virtuální sítě ale nemůžou mít překrývající se předpony IP adres a adresní prostory Point-to-site se nesmí překrývat mezi virtuálními sítěmi.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakou propustnost je možné očekávat u připojení typu Site-to-Site nebo Point-to-Site?

Určit přesnou propustnost tunelových propojení sítí VPN je obtížné. IPsec a SSTP jsou kryptograficky náročné protokoly sítě VPN. Propustnost je také omezená latencí a šířkou pásma mezi vaším místním prostředím a internetem.
