---
title: 'Azure ExpressRoute: O šifrování'
description: Další informace o šifrování ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: 7dd9106539b6756d74629ac663241a5b5562cefb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75437045"
---
# <a name="expressroute-encryption"></a>Šifrování ExpressRoute
 
ExpressRoute podporuje několik šifrovacích technologií k zajištění důvěrnosti a integrity dat procházet mezi vaší sítí a sítí společnosti Microsoft.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Nejčastější dotazy k šifrování z bodu do bodu podle macsec
MACsec je [standard IEEE](https://1.ieee802.org/security/802-1ae/). Šifruje data na úrovni řízení přístupu k médiím (MAC) nebo síťové vrstvě 2. Pomocí nástroje MACsec můžete šifrovat fyzická propojení mezi síťovými zařízeními a síťovými zařízeními společnosti Microsoft, když se připojíte k microsoftu prostřednictvím [služby ExpressRoute Direct](expressroute-erdirect-about.md). MACsec je ve výchozím nastavení na portech ExpressRoute Direct zakázán. Můžete přinést vlastní klíč MACsec pro šifrování a uložit jej do [azure key vault](../key-vault/key-vault-overview.md). Vy rozhodujete, kdy klíč otočit. Další časté otázky najdete níže.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Mohu povolit MACsec na okruhu ExpressRoute zřízeného poskytovatelem ExpressRoute?
Ne. MACsec šifruje veškerý provoz na fyzickém spojení s klíčem vlastněným jednou entitou (tj. zákazníkem). Proto je k dispozici pouze na ExpressRoute Direct.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Mohu zašifrovat některé obvody ExpressRoute na portech ExpressRoute Direct a ponechat ostatní obvody na stejných portech nezašifrované? 
Ne. Jakmile je macsec povolen, jsou zašifrovány všechny přenosy řízení sítě, například datový provoz Protokolu BGP a datový provoz zákazníků. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Když povolím/zakážu MACsec nebo aktualizuji klíč MACsec, ztratí moje místní síť připojení k Microsoftu přes ExpressRoute?
Ano. Pro konfiguraci MACsec podporujeme pouze režim předsdíleného klíče. To znamená, že je třeba aktualizovat klíč na svých zařízeních i na zařízeních společnosti Microsoft (prostřednictvím našeho rozhraní API). Tato změna není atomická, takže ztratíte připojení, pokud dojde k neshodě klíčů mezi oběma stranami. Důrazně doporučujeme naplánovat okno údržby pro změnu konfigurace. Chcete-li minimalizovat prostoje, doporučujeme aktualizovat konfiguraci na jednom odkazu ExpressRoute Direct najednou po přepnutí síťového provozu na jiný odkaz.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Bude provoz i nadále proudit, pokud je nesoulad v klíči MACsec mezi mými zařízeními a microsoftem?
Ne. Pokud je nakonfigurován macsec a dojde k neshodě klíčů, ztratíte připojení k microsoftu. Jinými slovy, nebudeme se vrátit k nešifrovanému připojení, které by odhalilo vaše data. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Sníží povolení funkce MACsec v programu ExpressRoute Direct výkon sítě?
MACsec šifrování a dešifrování dochází v hardwaru na směrovačích, které používáme. Na naší straně nemá žádný vliv na výkon. Měli byste se však poradit s dodavatelem sítě pro zařízení, která používáte, a zjistěte, zda má MACsec nějaké důsledky pro výkon.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>které šifrovací sady jsou podporovány pro šifrování?
Podporujeme AES128 a AES256.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Nejčastější dotazy k end-to-end šifrování podle iPsec
IPsec je [standard IETF](https://tools.ietf.org/html/rfc6071). Šifruje data na úrovni internetového protokolu (IP) nebo síťové vrstvy 3. IPsec můžete použít k šifrování koncového připojení mezi místní sítí a virtuální sítí (VNET) v Azure. Další časté otázky najdete níže.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Mohu povolit IPsec kromě MACsec na mých portech ExpressRoute Direct?
Ano. MACsec zabezpečuje fyzická spojení mezi vámi a společností Microsoft. IPsec zabezpečuje koncové připojení mezi vámi a virtuálními sítěmi v Azure. Můžete je povolit nezávisle. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Můžu použít bránu Azure VPN k nastavení tunelového propojení IPsec mezi místní sítí a virtuální sítí Azure?
Ano. Tento tunelový propojení IPsec můžete nastavit přes partnerský vztah microsoftu okruhu ExpressRoute. Postupujte podle našeho [průvodce konfigurací](site-to-site-vpn-over-microsoft-peering.md).
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Můžu použít bránu Azure VPN k nastavení tunelového propojení IPsec přes privátní partnerský vztah Azure?
Pokud přijmete Azure Virtual WAN, můžete [podle těchto kroků](../virtual-wan/vpn-over-expressroute.md) šifrovat připojení od konce do konce. Pokud máte pravidelnou virtuální síť Azure, můžete nasadit bránu VPN jiného výrobce ve virtuální síti a vytvořit tunel iPsec mezi ní a místní bránou VPN.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Jaká je propustnost, kterou získám po povolení protokolu IPsec na mém připojení ExpressRoute?
Pokud se používá brána Azure VPN, zkontrolujte [čísla výkonu zde](../vpn-gateway/vpn-gateway-about-vpngateways.md). Pokud se používá brána VPN jiného výrobce, obraťte se na dodavatele pro čísla výkonu.

## <a name="next-steps"></a>Další kroky
Další informace o konfiguraci macsec naleznete v [tématu Konfigurace macsec.](expressroute-howto-macsec.md)

Další informace o konfiguraci protokolu IPsec naleznete v tématu [Konfigurace protokolu IPsec.](site-to-site-vpn-over-microsoft-peering.md)
