---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6054fe5f71f54794d4974a71cdfd61a7959534ff
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92082177"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Je protokol BGP podporován ve všech SKU služby Azure VPN Gateway?
Protokol BGP je podporován u všech SKU Azure VPN Gateway s výjimkou úrovně Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Je možné protokol BGP používat se službami Azure VPN Gateway se zásadami?
Ne, protokol BGP je podporován pouze u služeb VPN Gateway se směrováním.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>Jaká čísla ASN (čísla autonomního systému) můžu použít?
Můžete využít vlastní veřejný čísla ASN nebo privátní čísla ASN pro místní sítě i virtuální sítě Azure **s výjimkou** rozsahů, které jsou rezervované pro Azure nebo IANA:

> [!IMPORTANT]
>
> Následující čísla ASN jsou vyhrazené pro Azure nebo IANA:
> * Čísla ASN rezervované pro Azure:
>    * Veřejná ASN: 8074, 8075, 12076
>    * Soukromá ASN: 65515, 65517, 65518, 65519, 65520
> * Čísla ASN [vyhrazený organizací IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496–64511, 65535–65551 a 429496729
>
> Při připojování ke službě Azure VPN Gateway nemůžete zadat tyto čísla ASN pro místní zařízení VPN.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Můžu použít 32-bit (4 bajty) čísla ASN (čísla autonomního systému)?
Ano, brány VPN Azure teď podporují 32-bit (4 bajty) čísla ASN. Pomocí PowerShellu/CLI/sady SDK můžete nakonfigurovat použití čísla ASN v desítkovém formátu.

### <a name="what-private-asns-can-i-use"></a>Jaké soukromé čísla ASN můžu použít?
Rozsah použitelných privátních čísla ASN, které lze použít, jsou tyto:

* 64512-65514, 65521-65534

Tyto čísla ASN nejsou vyhrazené organizací IANA nebo Azure pro použití, a proto je můžete použít k přiřazení VPN Gateway k Azure.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Jakou adresu služba Azure VPN Gateway používá pro IP adresu partnera BGP?

* Ve výchozím nastavení brána Azure VPN Gateway přidělí jednu IP adresu z rozsahu GatewaySubnet pro brány VPN typu aktivní-pohotovostní nebo dvě IP adresy pro brány VPN typu aktivní-aktivní. Tyto adresy se přiřazují automaticky, když vytvoříte bránu VPN. Můžete získat vlastní IP adresy protokolu BGP přidělené pomocí prostředí PowerShell (Get-AzVirtualNetworkGateway, vyhledat vlastnost "bgpPeeringAddress") nebo v Azure Portal (pod vlastností "konfigurace ASN protokolu BGP" na stránce Konfigurace brány).

* Pokud místní směrovače VPN používají IP adresy **APIPA** (169.254. x. x) jako IP adresy protokolu BGP, musíte v bráně Azure VPN zadat další **IP adresu protokolu BGP pro službu Azure APIPA** . Azure VPN Gateway vybere adresu APIPa pro použití s místním partnerským vztahem protokolu APIPa, který je zadaný v bráně místní sítě, nebo privátní IP adresa pro místní partnerský uzel BGP bez APIPa. Další informace najdete v tématu [Konfigurace protokolu BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Jaké jsou požadavky na IP adresu partnera BGP v zařízení VPN?
Vaše místní adresa partnerského uzlu BGP **nesmí** být stejná jako veřejná IP adresa vašeho zařízení VPN nebo z adresního prostoru virtuální sítě VPN Gateway. Jako místní adresu partnera BGP v zařízení VPN použijte jinou IP adresu. Může se jednat o adresu přiřazenou k rozhraní zpětné smyčky v zařízení, buď k běžné IP adrese, nebo k adrese APIPa. Pokud vaše zařízení používá pro protokol BGP adresu APIPa, musíte v bráně VPN Azure zadat IP adresu APIPa, jak je popsáno v tématu [Konfigurace protokolu BGP](../articles/vpn-gateway/bgp-howto.md). Adresu zadejte v odpovídající bráně místní sítě reprezentující umístění.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Co je třeba zadat jako předpony adres pro bránu místní sítě při použití protokolu BGP?

> [!IMPORTANT]
>
>Jedná se o změnu z dřív dokumentovaného požadavku. Pokud pro připojení používáte protokol BGP, měli byste ponechat pole **adresní prostor** ***prázdné*** pro odpovídající prostředek místní síťové brány. Azure VPN Gateway interně přidá trasu hostitele k místní IP adrese partnerského uzlu BGP přes tunelové propojení IPsec. Do pole adresní **prostor nepřidávejte trasy** /32. Je redundantní a pokud používáte adresu APIPa jako místní IP adresu BGP zařízení VPN, nelze ji přidat do tohoto pole. Pokud do pole adresní prostor přidáte jakékoli jiné předpony, přidají se kromě tras zjištěných prostřednictvím protokolu BGP jako **statické trasy** v bráně Azure VPN.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Je možné používat stejné číslo ASN pro místní sítě VPN a sítě Azure VNet?
Ne, pokud pomocí protokolu BGP vzájemně propojujete místní sítě a sítě Azure VNet, je nutné pro ně přiřadit různá čísla ASN. Službám Azure VPN Gateway je přiřazeno výchozí číslo ASN 65515, ať už je protokol BGP povolen pro připojení mezi místními sítěmi, či nikoliv. Toto výchozí nastavení můžete změnit přiřazením jiného čísla ASN při vytváření služby VPN Gateway nebo můžete číslo ASN změnit po vytvoření služby brány. Místní čísla ASN je nutné přiřadit odpovídajícím bránám místních sítí Azure.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Které předpony adres budou služby Azure VPN gateway prezentovat?
Služby Azure VPN Gateway budou místním zařízením BGP prezentovat následující směrování:

* předpony adres sítí VNet
* předpony Azure pro jednotlivé brány místních sítí připojené ke službě Azure VPN Gateway
* směrování převzatá z jiných relací vytvoření partnerského vztahu protokolu BGP připojených ke službě Azure VPN Gateway, **kromě výchozího směrování nebo směrování překrytých jinými předponami sítě VNet**

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Kolik předpon je možné inzerovat službě Azure VPN Gateway?
Podporujeme až 4000 předpon. Pokud počet předpon překročí toto omezení, relace BGP se ukončí.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Mohu inzerovat výchozí cestu (0.0.0.0/0) do bran Azure VPN Gateway?
Yes.

Všimněte si, že tato akce vynutí všechny přenosy odchozích dat virtuální sítě směrem k místní lokalitě a zabrání virtuálním počítačům virtuální sítě v přijetí veřejné komunikace přímo z Internetu, jako je RDP nebo SSH z Internetu k virtuálním počítačům.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Mohu inzerovat přesné předpony jako předpony mé virtuální sítě?

Ne, inzerování stejných předpon jako předpon adres virtuální sítě bude blokováno nebo filtrováno platformou Azure. Můžete ale inzerovat předponu, která je nadmnožinou toho, co máte ve své virtuální síti. 

Například když vaše virtuální síť používá adresní prostor 10.0.0.0/16, můžete inzerovat 10.0.0.0/8. Nemůžete ale inzerovat 10.0.0.0/16 nebo 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Je možné použít protokol BGP u připojení mezi sítěmi VNet?
Ano, protokol BGP lze použít pro připojení mezi místními sítěmi i připojení mezi sítěmi VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Lze u služeb Azure VPN Gateway používat kombinaci připojení pomocí protokolu BGP a bez protokolu BGP?
Ano, u stejné služby Azure VPN Gateway je možné kombinovat připojení pomocí protokolu BGP i bez protokolu BGP.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Podporuje služba Azure VPN Gateway směrování provozu pomocí protokolu BGP?
Ano, směrování provozu pomocí protokolu BGP je podporováno, pouze s tou výjimkou, že služby Azure VPN Gateway **NEBUDOU** prezentovat výchozí směrování ostatním partnerům BGP. Pokud chcete povolit směrování provozu mezi více různými službami Azure VPN Gateway, je nutné povolit protokol BGP ve všech zprostředkujících připojení mezi sítěmi VNet. Další informace najdete v tématu [o protokolu BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Je možné mít víc než jeden tunel mezi službou Azure VPN Gateway a místní sítí?
Ano, můžete vytvořit více než jeden tunel VPN S2S mezi službou Azure VPN Gateway a místní sítí. Všimněte si, že všechny tyto tunely se započítávají do celkového počtu tunelů pro vaše brány Azure VPN a musíte protokol BGP povolit u obou tunelů.

Pokud máte například dvě redundantní tunely mezi vaší bránou Azure VPN a jednou z vašich místních sítí, budou spotřebovávat 2 tunely z celkové kvóty pro vaši bránu Azure VPN.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Mohu mít více různých tunelů mezi dvěma sítěmi Azure VNet s protokolem BGP?
Ano, ale alespoň jedna z bran virtuální sítě musí být v konfiguraci aktivní–aktivní.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Můžu použít protokol BGP pro S2S VPN v konfiguraci koexistence sítě VPN ExpressRoute/S2S?
Yes. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Co je třeba přidat do místního zařízení VPN pro relaci partnerského vztahu protokolu BGP?
Do zařízení VPN je nutné přidat směrování hostitele IP adresy partnera BGP Azure odkazující na tunel VPN IPsec S2S. Pokud je například IP adresa partnera BGP Azure 10.12.255.30, je nutné přidat směrování hostitele pro adresu 10.12.255.30 s rozhraním nexthop odpovídajícího rozhraní tunelu IPsec ve vašem zařízení VPN.

### <a name="does-the-virtual-network-gateway-support-bidirectional-forwarding-detection-bfd-for-site-to-site-connections-with-bgp"></a>Podporuje Virtual Network Gateway pro připojení Site-to-site s protokolem BGP i zjišťování obousměrného předávání (BFD)?
Ne. Rozpoznávání obousměrného předávání (BFD) je protokol, který lze použít společně s protokolem BGP ke zjištění rychlejšího výpadku sousedů, než použití standardních udržení protokolu BGP. BFD používá časovače s dalšími sekundami, které jsou navržené pro práci v prostředích LAN, ale ne přes veřejná připojení k Internetu nebo na celé síti. U připojení přes veřejný Internet se některé pakety zpoždění nebo dokonce vyřazené nejsou neobvyklé, takže zavedení těchto agresivních časovačů by vedlo k nestabilitě, která může způsobit nestabilitu těchto tras. Jako alternativu můžete nakonfigurovat místní zařízení s časovači nižšími než s výchozím intervalem 60 – sekundu a časovačem podržením 180-Second pro dobu rychlejší konvergence.
