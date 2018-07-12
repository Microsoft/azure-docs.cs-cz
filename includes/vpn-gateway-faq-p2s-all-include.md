---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 199998f193f375137d499913e9ce966f85ed863c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727529"
---
### <a name="supportedclientos"></a>Které klientské operační systémy je možné používat s připojeními typu Point-to-Site?

Podporovány jsou následující operační systémy:

* Windows 7 (32bitové a 64bitové verze)
* Windows Server 2008 R2 (pouze 64bitové verze)
* Windows 8.1 (32bitové a 64bitové verze)
* Windows Server 2012 (pouze 64bitové verze)
* Windows Server 2012 R2 (pouze 64bitové verze)
* Windows Server 2016 (pouze 64bitové verze)
* Windows 10
* Mac OS X verze 10.11 (El Capitan)
* Mac OS X verze 10.12 (Sierra)
* Linux (StrongSwan)
* iOS

>[!NOTE]
>[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]
>

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Kolik koncových bodů klienta VPN je možné mít v konfiguraci připojení Point-to-Site?

Podporujeme až 128 klientů VPN, kteří se mohou připojit k virtuální síti současně.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Je možné procházet proxy servery a brány firewall s využitím schopnosti Point-to-Site?

Azure podporuje dva typy možností Point-to-site VPN:

* SSTP (Secure Socket Tunneling Protocol). SSTP je proprietární řešení Microsoftu založené na SSL, které umožňuje pronikat branami firewall, protože většina bran firewall otevírá port TCP 443, který SSL používá.

* IKEv2 VPN. IKEv2 VPN řešení IPsec VPN založené na standardech, které využívá porty UDP 500 a 4500 a protokol IP č. 50. Brány firewall tyto porty neotvírají vždycky, takže je možné, že IKEv2 VPN nebude moct procházet servery proxy a branami firewall.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Pokud restartuji klientský počítač nakonfigurovaný pro připojení Point-to-Site, připojí se síť VPN automaticky znovu?

Ve výchozím nastavení nebude klientský počítač vytvářet připojení k síti VPN automaticky znovu.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Podporuje připojení Point-to-Site automatické připojení a DDNS u klientů sítě VPN?

Automatické opětné připojení a DDNS se u sítí VPN s připojením Point-to-Site aktuálně nepodporují.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Je možné současně používat konfigurace Site-to-Site a Point-to-Site pro stejnou virtuální síť?

Ano. Pro model nasazení Resource Manageru musíte mít bránu typu VPN RouteBased. Pro model nasazení Classic je potřebná dynamická brána. Připojení Point-to-Site se pro brány VPN se statickým směrováním ani pro brány VPN PolicyBased nepodporuje.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Je možné nakonfigurovat klienta Point-to-Site tak, aby se připojoval k několik virtuálním sítím současně?

Ne. Klient typu Point-to-Site se může připojit jenom k prostředkům ve virtuální síti, ve které je umístěná brána virtuální sítě.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakou propustnost je možné očekávat u připojení typu Site-to-Site nebo Point-to-Site?

Určit přesnou propustnost tunelových propojení sítí VPN je obtížné. IPsec a SSTP jsou kryptograficky náročné protokoly sítě VPN. Propustnost je také omezena latencí a šířkou pásma mezi vaší lokalitou a internetem. U služby VPN Gateway s připojeními Point-to-Site VPN jenom typu IKEv2 bude celková propustnost, kterou můžete očekávat, záviset na skladové jednotce Gateway. Další informace o propustnosti najdete v části [Skladové jednotky (SKU) brány](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Je možné pro připojení Point-to-Site použít libovolného softwarového klienta sítě VPN, pokud podporuje protokol SSTP a/nebo IKEv2?

Ne. Pro SSTP můžete použít jenom nativního klienta VPN v systému Windows a pro IKEv2 nativního klienta VPN v systému Mac. Podrobnosti najdete v seznamu podporovaných klientských operačních systémů.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Podporuje Azure IKEv2 VPN s Windows?

IKEv2 se podporuje v systémech Windows 10 a Server 2016. Pokud ale chcete používat IKEv2, musíte nainstalovat aktualizace a nastavit hodnotu klíče registru v místním prostředí. Verze operačního systému starší než Windows 10 se nepodporují a mohou používat jenom SSTP.

Postup přípravy systému Windows 10 nebo Server 2016 na IKEv2:

1. Nainstalujte aktualizaci.

  | Verze operačního systému | Datum | Číslo/odkaz |
  |---|---|---|---|
  | Windows Server 2016<br>Windows 10 verze 1607 | 17. ledna 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
  | Windows 10 verze 1703 | 17. ledna 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
  |  |  |  |  |

2. Nastavte hodnotu klíče registru. Vytvořte nebo nastavte klíč REG_DWORD HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload v registru na hodnotu 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Co se stane, když se pro připojení P2S VPN nakonfiguruje SSTP i IKEv2?

Když nakonfigurujete SSTP a IKEv2 ve smíšeném prostředí (které se skládá ze zařízení se systémy Windows a Mac), klient VPN systému Windows vždycky nejdřív zkusí využít tunel IKEv2, ale pokud připojení IKEv2 není úspěšné, vrátí se k SSTP. MacOSX se bude připojovat jenom prostřednictvím protokolu IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Které další platformy (mimo Windows a Mac) Azure podporuje pro P2S VPN?

Pro P2S VPN Azure podporuje Windows, Mac a Linux.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Už mám nasazenou Azure VPN Gateway. Můžu na ní povolit RADIUS, případně IKEv2 VPN?

Ano, tyto funkce můžete na už nasazených bránách povolit pomocí PowerShellu nebo webu Azure Portal za předpokladu, že použitá jednotka SKU brány podporuje RADIUS a/nebo IKEv2. Například SKU VPN Gateway Basic nepodporuje RADIUS ani IKEv2.
