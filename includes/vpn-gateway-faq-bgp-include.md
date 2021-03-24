---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/22/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3877134f8a00cd627909d7f889fd5b104ccbd8b1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863614"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Je protokol BGP podporován ve všech SKU služby Azure VPN Gateway?
Protokol BGP je podporován u všech SKU Azure VPN Gateway s výjimkou úrovně Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Můžu použít protokol BGP se Azure Policy branami VPN?
Ne, protokol BGP je podporován pouze u bran sítě VPN založených na trasách.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Jaká čísla ASN (čísla autonomních systémů) můžu použít?
Pro místní sítě i virtuální sítě Azure můžete použít vlastní veřejný čísla ASN nebo privátní čísla ASN. Nemůžete použít rozsahy rezervované pro Azure nebo IANA.

Následující čísla ASN jsou vyhrazené pro Azure nebo IANA:
* Čísla ASN rezervované pro Azure:
  * Veřejná ASN: 8074, 8075, 12076
  * Soukromá ASN: 65515, 65517, 65518, 65519, 65520
* Čísla ASN [vyhrazený organizací IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
   * 23456, 64496–64511, 65535–65551 a 429496729

Pokud se připojujete ke službě Azure VPN Gateway, nemůžete tyto čísla ASN pro vaše místní zařízení VPN zadat.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Můžu použít 32-bit (4 bajty) čísla ASN?
Ano, VPN Gateway nyní podporuje 32-bit (4 bajty) čísla ASN. Ke konfiguraci pomocí čísla ASN v desítkovém formátu použijte PowerShell, rozhraní příkazového řádku Azure nebo sadu Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Jaké soukromé čísla ASN můžu použít?
Rozsahy použitelných pro soukromé čísla ASN jsou:

* 64512-65514 a 65521-65534

Tyto čísla ASN nejsou rezervovány organizací IANA nebo Azure pro použití, a proto je lze použít k přiřazení ke službě Azure VPN Gateway.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Jaká adresa VPN Gateway použít pro IP adresu partnerského uzlu protokolu BGP?

Ve výchozím nastavení VPN Gateway přiděluje jednu IP adresu z rozsahu *GatewaySubnet* pro brány VPN typu aktivní-pohotovostní nebo dvě IP adresy pro brány VPN typu aktivní-aktivní. Tyto adresy se přiřazují automaticky, když vytvoříte bránu VPN. Můžete získat vlastní IP adresu protokolu BGP přidělenou pomocí prostředí PowerShell nebo umístěním do Azure Portal. V prostředí PowerShell použijte rutinu **Get-AzVirtualNetworkGateway** a vyhledejte vlastnost **bgpPeeringAddress** . V Azure Portal na stránce **Konfigurace brány** vyhledejte v části **Konfigurace vlastnosti ASN protokolu BGP** .

Pokud místní směrovače VPN používají IP adresy **APIPA** (169.254. x. x) jako IP adresy protokolu BGP, musíte v bráně Azure VPN zadat další **IP adresu protokolu BGP pro službu Azure APIPA** . Azure VPN Gateway vybere adresu APIPa, která se má použít s místním partnerským vztahem APIPa protokolu APIPa zadaná v bráně místní sítě, nebo privátní IP adresa pro místní partnerský uzel BGP bez APIPa. Další informace najdete v tématu [Konfigurace protokolu BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Jaké jsou požadavky na IP adresy partnerského uzlu protokolu BGP na zařízení VPN?
Vaše místní adresa partnerského uzlu BGP nesmí být stejná jako veřejná IP adresa vašeho zařízení VPN nebo z adresního prostoru virtuální sítě služby VPN Gateway. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může to být adresa přiřazená k rozhraní zpětné smyčky v zařízení (buď běžná IP adresa, nebo adresa APIPa). Pokud vaše zařízení používá pro protokol BGP adresu APIPa, musíte v bráně Azure VPN Gateway zadat IP adresu APIPa, jak je popsáno v tématu [Konfigurace protokolu BGP](../articles/vpn-gateway/bgp-howto.md). Zadejte tuto adresu v odpovídající bráně místní sítě představující umístění.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Co mám zadat jako předpony mých adres pro bránu místní sítě při použití protokolu BGP?

> [!IMPORTANT]
>
>Jedná se o změnu z dřív dokumentovaného požadavku. Pokud pro připojení používáte protokol BGP, ponechte pole **adresní prostor** prázdné pro odpovídající prostředek místní síťové brány. Azure VPN Gateway přidá trasu hostitele interně k místní IP adrese partnerského uzlu BGP prostřednictvím tunelového propojení IPsec. Do pole **adresní prostor** nepřidávejte trasy/32. Je redundantní a pokud používáte adresu APIPa jako místní IP adresu BGP zařízení VPN, nejde do tohoto pole přidat. Pokud přidáte do pole **adresní prostor** žádné další předpony, přidají se kromě tras zjištěných prostřednictvím protokolu BGP jako statické trasy v bráně Azure VPN.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Můžu použít stejné číslo ASN pro místní sítě VPN i virtuální sítě Azure?
Ne. Pokud je připojujete spolu s protokolem BGP, musíte přiřadit různé čísla ASN mezi místními sítěmi a vašimi virtuálními sítěmi Azure. K bráně Azure VPN Gateway je přiřazeno výchozí číslo ASN 65515, bez ohledu na to, jestli je protokol BGP povolený, nebo ne pro připojení mezi místními sítěmi. Tuto výchozí hodnotu můžete přepsat tak, že při vytváření brány sítě VPN přiřadíte jiné číslo ASN, případně můžete změnit číslo ASN po vytvoření brány. K odpovídajícím bránám místní sítě Azure budete muset přiřadit místní čísla ASN.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Které předpony adres budou služby Azure VPN gateway prezentovat?
Brány inzerují následující trasy na vaše místní zařízení s protokolem BGP:

* Předpony adres vaší virtuální sítě.
* Předpony adres pro každou bránu místní sítě připojenou ke službě Azure VPN Gateway.
* Trasy zjištěné z jiných relací partnerských vztahů protokolu BGP připojených ke službě Azure VPN Gateway s výjimkou výchozí trasy nebo tras, které se překrývají s předponou virtuální sítě.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Kolik předpon je možné inzerovat do Azure VPN Gateway?
Azure VPN Gateway podporuje až 4000 předpon. Pokud počet předpon překročí toto omezení, relace BGP se ukončí.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Mohu inzerovat výchozí cestu (0.0.0.0/0) do bran Azure VPN Gateway?
Ano. Všimněte si, že to vynutí všechny odchozí přenosy virtuální sítě směrem k místní lokalitě. Zabrání taky virtuálním počítačům virtuální sítě přijímat veřejnou komunikaci přímo z Internetu, jako je RDP nebo SSH z Internetu, do virtuálních počítačů.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Můžu inzerovat přesné předpony jako předpony moje virtuální sítě?

Ne. inzerování stejných předpon jako libovolných předpon adres vaší virtuální sítě bude zablokováno nebo filtrováno službou Azure. Můžete ale inzerovat předponu, která je nadmnožinou toho, co máte ve vaší virtuální síti. 

Pokud například vaše virtuální síť používala adresní prostor 10.0.0.0/16, můžete inzerovat 10.0.0.0/8. Nemůžete ale inzerovat 10.0.0.0/16 nebo 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Můžu použít protokol BGP s připojením mezi virtuálními sítěmi?
Ano, protokol BGP můžete použít pro připojení mezi různými místy a připojení mezi virtuálními sítěmi.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Lze u služeb Azure VPN Gateway používat kombinaci připojení pomocí protokolu BGP a bez protokolu BGP?
Ano, u stejné služby Azure VPN Gateway je možné kombinovat připojení pomocí protokolu BGP i bez protokolu BGP.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Podporuje Azure VPN Gateway směrování přenosu BGP?
Ano, směrování přenosu protokolu BGP je podporované, s výjimkou, že brány Azure VPN Gateway Neinzerují výchozí trasy jiným partnerům protokolu BGP. Pokud chcete povolit směrování provozu napříč několika branami Azure VPN, musíte povolit protokol BGP u všech zprostředkujících připojení mezi virtuálními sítěmi. Další informace najdete v tématu [o protokolu BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Můžu mít více než jeden tunel mezi službou Azure VPN Gateway a místní sítí?
Ano, můžete vytvořit více než jedno tunelové propojení VPN typu Site-to-Site (S2S) mezi službou Azure VPN Gateway a místní sítí. Všimněte si, že všechny tyto tunely se počítají na základě celkového počtu tunelů pro brány VPN Azure a musíte povolit protokol BGP u obou tunelů.

Například pokud máte dvě redundantní tunely mezi vaší bránou Azure VPN a jednou z vašich místních sítí, spotřebovávají 2 tunely z celkové kvóty pro vaši bránu Azure VPN.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Můžu mít několik tunelových propojení mezi dvěma virtuálními sítěmi Azure s protokolem BGP?
Ano, ale alespoň jedna z bran virtuální sítě musí být v konfiguraci aktivní–aktivní.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Můžu použít protokol BGP pro S2S VPN v konfiguraci koexistence sítě VPN v Azure ExpressRoute a S2S?
Ano. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Co je třeba přidat do místního zařízení VPN pro relaci partnerského vztahu protokolu BGP?
Přidejte trasu hostitele IP adresy partnerského uzlu protokolu BGP Azure na zařízení VPN. Tato trasa odkazuje na tunelové připojení VPN S2S IPsec. Pokud je třeba IP adresa partnerského uzlu Azure VPN 10.12.255.30, přidáte trasu hostitele pro 10.12.255.30 s rozhraním Next Hop odpovídajícího rozhraní tunelového propojení IPsec na zařízení VPN.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Podporuje Brána virtuální sítě BFD pro připojení S2S s protokolem BGP?
Ne. Rozpoznávání obousměrného předávání (BFD) je protokol, který můžete použít s protokolem BGP ke zjištění rychlejšího výpadku sousedů, než můžete pomocí standardních nečinnosti protokolu BGP. BFD používá časovače s dalšími sekundami, které jsou navržené pro práci v prostředích LAN, ale ne přes veřejná připojení k Internetu nebo na celé síti.

U připojení přes veřejný Internet se určité pakety zpoždění nebo dokonce vyřazené nejsou neobvyklé, takže zavedení těchto agresivních časovačů může být nestabilní. Tato nestabilita může způsobit ztlumení tras pomocí protokolu BGP. Jako alternativu můžete nakonfigurovat místní zařízení s časovači nižšími, než je výchozí interval, 60 sekund "naživu" a 180 – sekundový časovač blokování. Výsledkem je rychlejší konvergence.

### <a name="do-azure-vpn-gateways-initiate-bgp-peering-sessions-or-connections"></a>Spouští Azure VPN Gateway relace nebo připojení partnerských vztahů protokolu BGP?

Brána inicializuje relace partnerských vztahů protokolu BGP s místními IP adresami partnerského uzlu BGP, které jsou zadané v prostředcích brány místní sítě, pomocí privátních IP adres na branách VPN. To je bez ohledu na to, jestli jsou místní IP adresy protokolu BGP v rozsahu APIPa nebo v běžných privátních IP adresách. Pokud vaše místní zařízení VPN používají adresy APIPa jako IP adresy protokolu BGP, je nutné nakonfigurovat mluvčí protokol BGP, aby zahájil připojení.

