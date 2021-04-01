---
title: 'Azure ExpressRoute: informace o šifrování'
description: Přečtěte si o šifrování ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899866"
---
# <a name="expressroute-encryption"></a>Šifrování ExpressRoute
 
ExpressRoute podporuje několik technologií šifrování, které zajišťují důvěrnost a integritu dat mezi vaší sítí a sítí Microsoftu.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Šifrování Point-to-Point pomocí MACsec – Nejčastější dotazy
MACsec je [Standard IEEE](https://1.ieee802.org/security/802-1ae/). Šifruje data na úrovni MAC (Media Access Control) nebo síťové vrstvě 2. Pomocí MACsec můžete šifrovat fyzické odkazy mezi síťovými zařízeními a síťovými zařízeními Microsoftu, když se připojíte k Microsoftu prostřednictvím [ExpressRoute Direct](expressroute-erdirect-about.md). MACsec je ve výchozím nastavení zakázaný u portů ExpressRoute Direct. Zavedete si vlastní klíč MACsec pro šifrování a uložíte ho do [Azure Key Vault](../key-vault/general/overview.md). Určíte, kdy se má klíč otočit. Další informace najdete v dalších nejčastějších dotazech.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Můžu povolit MACsec v okruhu ExpressRoute zřízeném poskytovatelem ExpressRoute?
No. MACsec šifruje veškerý provoz na fyzickém propojení s klíčem, který patří jedné entitě (tj. zákazník). Proto je k dispozici pouze na ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Je možné zašifrovat některé okruhy ExpressRoute na mých portech ExpressRoute Direct a ponechat jiné okruhy na stejných portech bez šifrování? 
No. Jakmile je MACsec povolený veškerý provoz řízení sítě, například přenos dat protokolu BGP a přenosy dat zákazníků, se zašifrují. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Když povolíte nebo zakážete MACsec nebo aktualizujete MACsec klíč, ztratí se v místní síti připojení k Microsoftu přes ExpressRoute?
Ano. V případě konfigurace MACsec podporujeme jenom režim Pre-Shared Key. Znamená to, že potřebujete aktualizovat klíč jak na svých zařízeních, tak na Microsoftu (přes naše rozhraní API). Tato změna není atomická, takže ztratíte připojení, když dojde k neshodě klíče mezi dvěma stranami. Důrazně doporučujeme, abyste naplánovali časové období údržby pro změnu konfigurace. Abychom minimalizovali prostoje, doporučujeme, abyste po přepnutí síťového provozu na jiný odkaz aktualizovali konfiguraci na jednom odkazu ExpressRoute Direct v čase.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Bude provoz pokračovat v toku, pokud dojde k neshodě klíče MACsec mezi zařízeními a společností Microsoft?
No. Pokud je MACsec nakonfigurovaný a dojde k neshodě klíčů, ztratíte připojení k Microsoftu. Jinými slovy nebudeme vracet k nezašifrovanému připojení, které zveřejňuje vaše data. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Povolí MACsec na ExpressRoute přímé snížení výkonu sítě?
K šifrování a dešifrování MACsec dochází v hardwaru na směrovačích, které používáme. Na naší straně není dopad na výkon. Měli byste si však u zařízení, která používáte, kontrolovat u dodavatele sítě a zjistit, zda má MACsec nějaké snížení výkonu.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Které šifrovací sady jsou podporované pro šifrování?
Podporujeme jenom verzi AES-128 a AES-256 pro [Rozšířené číslování paketů](https://1.ieee802.org/security/802-1aebw/) . V konfiguraci MACsec na zařízení je taky potřeba zakázat [identifikátor sci (Secure Channel Identifier)](https://wikipedia.org/wiki/IEEE_802.1AE) . 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Komplexní šifrování pomocí protokolu IPsec – Nejčastější dotazy
IPsec je [Standard IETF](https://tools.ietf.org/html/rfc6071). Šifruje data na úrovni Internet Protocol (IP) nebo síťové vrstvy 3. Protokol IPsec můžete použít k šifrování komplexního připojení mezi vaší místní sítí a virtuální sítí (VNET) v Azure. Další informace najdete v dalších nejčastějších dotazech.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Je možné povolit protokol IPsec kromě MACsec na ExpressRoute přímých portech?
Ano. MACsec zabezpečuje fyzické připojení mezi vámi a společností Microsoft. IPsec zabezpečuje komplexní připojení mezi vámi a vašimi virtuálními sítěmi v Azure. Můžete je povolit nezávisle. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Můžu použít Azure VPN Gateway k nastavení tunelu IPsec přes privátní partnerský vztah Azure?
Ano. Pokud přijmete Azure Virtual WAN, můžete postupovat podle [těchto kroků](../virtual-wan/vpn-over-expressroute.md) k zašifrování kompletního připojení. Pokud máte běžnou virtuální síť Azure, můžete postupovat podle [těchto kroků](../vpn-gateway/site-to-site-vpn-private-peering.md) a vytvořit tunel IPSec mezi službou Azure VPN Gateway a místní bránou VPN.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Jaká je propustnost, po povolení protokolu IPsec na ExpressRoute připojení?
Pokud se používá Azure VPN Gateway, podívejte se na toto [číslo výkonu](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pokud se používá brána sítě VPN jiného výrobce, obraťte se na dodavatele s číslem výkonu.

## <a name="next-steps"></a>Další kroky
Další informace o konfiguraci MACsec najdete v tématu [Konfigurace MACsec](expressroute-howto-macsec.md) .

Další informace o konfiguraci protokolu IPsec najdete v tématu [Konfigurace protokolu IPSec](site-to-site-vpn-over-microsoft-peering.md) .
