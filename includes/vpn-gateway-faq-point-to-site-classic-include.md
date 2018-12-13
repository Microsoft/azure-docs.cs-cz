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
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323662"
---
Tyto nejčastější dotazy se týká připojení typu P2S, které používají model nasazení classic.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Jaké klientské operační systémy je možné používat s připojeními typu Point-to-Site?

Podporovány jsou následující operační systémy:

* Windows 7 (32bitové a 64bitové verze)
* Windows Server 2008 R2 (pouze 64bitové verze)
* Windows 8 (32bitové a 64bitové verze)
* Windows 8.1 (32bitové a 64bitové verze)
* Windows Server 2012 (pouze 64bitové verze)
* Windows Server 2012 R2 (pouze 64bitové verze)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Můžete použít libovolného softwarového klienta sítě VPN, který podporuje protokol SSTP pro Point-to-Site?

Ne. Podpora je omezena pouze na uvedené verze operačních systémů Windows.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Kolik koncových bodů klienta VPN může existovat v konfiguraci Point-to-Site?

Až 128 VPN klienti mohou připojit k virtuální síti ve stejnou dobu.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Je možné používat vlastní interní kořenové certifikační autority infrastruktury veřejných klíčů pro připojení Point-to-Site?

Ano. Dříve bylo možné používat pouze kořenové certifikáty podepsané svým držitelem. Stále můžete nahrát až 20 kořenových certifikátů.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Je možné procházet proxy servery a brány firewall pomocí Point-to-Site?

Ano. Pro tunelové propojení prostřednictvím brány firewall používáme Secure Socket Tunneling Protocol (SSTP). Toto tunelové propojení se zobrazí jako připojení HTTPS.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Pokud restartuji klientský počítač nakonfigurovaný pro připojení Point-to-Site, připojí se síť VPN automaticky znovu?

Ve výchozím klientském počítači nebude znovu vytvořit připojení k síti VPN automaticky.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Nemá Point-to-Site automatické obnovení připojení a DDNS u klientů VPN?

Ne. Automatické obnovení připojení a DDNS se aktuálně nepodporují v Point-to-Site VPN.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Může mít Site-to-Site a konfigurace Point-to-Site pro stejnou virtuální síť?

Ano. Obě řešení budou funkční, pokud máte pro bránu typu RouteBased VPN. Pro model nasazení Classic je potřebná dynamická brána. Není podpora Point-to-Site pro brány VPN se statickým směrováním ani brány používající **- VpnType PolicyBased** rutiny.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Je možné nakonfigurovat klienta Point-to-Site tak, aby se připojoval k několik virtuálním sítím současně?

Ano. Nicméně virtuální sítě nemůže mít překrývající se předpony IP a nesmí se překrývat adresní prostory Point-to-Site mezi virtuálními sítěmi.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakou propustnost je možné očekávat u připojení typu Site-to-Site nebo Point-to-Site?

Určit přesnou propustnost tunelových propojení sítí VPN je obtížné. IPsec a SSTP jsou kryptograficky náročné protokoly sítě VPN. Propustnost je také omezena latencí a šířkou pásma mezi vaší lokalitou a Internetem.
