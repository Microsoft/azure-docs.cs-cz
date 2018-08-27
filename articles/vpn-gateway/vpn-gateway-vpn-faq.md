---
title: Azure VPN Gateway – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy k branám VPN. Nejčastější dotazy týkající se propojení Microsoft Azure Virtual Network mezi různými místy, připojení s hybridní konfigurací a bran VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 245e5e1048f27fc76141312cf6f9021dc0060ae6
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "39359056"
---
# <a name="vpn-gateway-faq"></a>Nejčastější dotazy k branám VPN

## <a name="connecting"></a>Připojování k virtuálním sítím

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Je možné propojit virtuální sítě v různých oblastech Azure?

Ano. Žádné omezení oblastí se ve skutečnosti neuplatňuje. Jedna virtuální síť může být propojena s jinou virtuální sítí ve stejném oblasti nebo v jiné oblasti Azure. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Je možné propojovat virtuální sítě v rámci různých předplatných?

Ano.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Je možné připojovat se k více serverům z jedné virtuální sítě?

K více serverům se lze připojovat prostřednictvím prostředí Windows PowerShell a rozhraní API REST Azure. Přečtěte si oddíl [Možnosti připojování k více serverům a připojení VNet-to-VNet](#V2VMulti) v nejčastějších dotazech.

### <a name="what-are-my-cross-premises-connection-options"></a>Jaké jsou možnosti připojení mezi různými místy?

Podporovány jsou následující možnosti připojení mezi různými místy:

* Site-to-Site – připojení VPN prostřednictvím protokolu IPsec (IKE v1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo službu RRAS. Další informace naleznete v tématu [Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Point-to-Site – připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo IKE v2. Toto připojení nevyžaduje zařízení VPN. Další informace naleznete v tématu [Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet-to-VNet – tento typ připojení je stejný jako konfigurace Site-to-Site. VNet-to-VNet je připojení k síti VPN prostřednictvím protokolu IPsec (IKE v1 a IKE v2). Nevyžaduje zařízení VPN. Další informace naleznete v tématu [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Multi-Site – varianta konfigurace Site-to-Site, která umožňuje propojit několik místních serverů do virtuální sítě. Další informace najdete v tématu [Multi-Site](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – ExpressRoute je přímé připojení k Azure z vaší sítě WAN, nikoli prostřednictvím připojení VPN přes veřejný internet. Další informace najdete v tématech [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md) a [ExpressRoute – nejčastější dotazy](../expressroute/expressroute-faqs.md).

Další informace o připojeních VPN Gateway najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Jaký je rozdíl mezi připojením Site-to-Site a Point-to-Site?

Konfigurace **Site-to-Site** (tunel VPN IPsec/IKE) jsou mezi místním umístěním a Azure. To znamená, že se můžete z kteréhokoli počítače ve vaší lokalitě připojit k libovolnému virtuálnímu počítači nebo instanci role ve vaší virtuální síti v závislosti na zvoleném způsobu konfigurace směrování a oprávněních. Představuje skvělou možnost připojení mezi různými místy, která je vždy k dispozici a je velmi vhodná pro hybridní konfigurace. Tento typ připojení využívá zařízení sítě VPN s protokolem IPsec (hardwarové nebo softwarové zařízení), které musí být nasazeno v hraniční části sítě. Chcete-li vytvořit tento typ připojení, musíte mít adresu IPv4 přístupnou zvenčí, která není za zařízením NAT.

Konfigurace **Point-to-Site** (VPN přes SSTP) umožňují připojit se z jednoho počítače odkudkoli kamkoli do vaší virtuální sítě. Využívá integrovaného klienta VPN ve Windows. Jako součást konfigurace Point-to-Site nainstalujete certifikát a balíček konfigurace klienta VPN, který obsahuje nastavení umožňující vašemu počítači připojit se ke kterémukoli virtuálnímu počítači nebo instanci role v rámci virtuální sítě. Toto řešení je skvělé, pokud se chcete připojit k virtuální síti, ale nenacházíte se na příslušném místě. Je také vhodné, pokud nemáte přístup k hardwaru sítě VPN nebo adrese IPv4 přístupné zvenčí; obě tyto položky jsou pro připojení Site-to-Site vyžadovány.

Virtuální síť můžete nakonfigurovat tak, aby souběžně používala připojení Site-to-Site i Point-to-Site, pokud pro bránu vytvoříte připojení Site-to-Site s použitím typu sítě VPN založeného na směrování. Typy sítí VPN založené na směrování se v modelu nasazení Classic nazývají dynamické brány.

## <a name="gateways"></a>Brány virtuálních sítí

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Je brána sítě VPN bránou virtuální sítě?

Brána sítě VPN je typem brány virtuální sítě. Brána sítě VPN odesílá šifrovaný provoz mezi virtuální sítí a místním umístěním přes veřejné připojení. Bránu sítě VPN můžete použít také k odesílání provozu mezi virtuálními sítěmi. Při vytváření brány sítě VPN použijete parametr -GatewayType s hodnotou 'Vpn'. Další informace najdete v tématu [Informace o nastavení konfigurace služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Co je to brána založená na zásadách (se statickým směrováním)?

Brány založené na zásadách implementují sítě VPN založené na zásadách. Sítě VPN založené na zásadách šifrují pakety a směrují je do tunelových propojení IPsec na základě kombinací předpon adres mezi vaší místní sítí a virtuální sítí VNet Azure. Zásada (nebo selektor provozu) se většinou v konfiguraci sítě VPN definuje jako přístupový seznam.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Co je to brána založená na směrování (s dynamickým směrováním)?

Brány založené na směrování implementují sítě VPN založené na směrování. Sítě VPN založené na směrování používají ke směrování paketů do příslušných rozhraní tunelových propojení „trasy“ v tabulce předávání IP nebo směrovací tabulce IP. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady nebo selektor provozu pro sítě VPN založené na směrování používají konfiguraci typu any-to-any (se zástupnými znaky).

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Můžu aktualizovat bránu VPN založenou na zásadách tak, aby byla založená na směrování?
Ne. Typ brány virtuální sítě Azure se nedá změnit z brány založené na zásadách na bránu založenou na směrování ani naopak. Brána se musí odstranit a znovu vytvořit a tento proces trvá přibližně 60 minut. IP adresa brány ani předsdílený klíč (PSK) se nezachovají.
1. Odstraňte všechna připojení přidružená k bráně, která se má odstranit.
2. Odstraňte bránu:
* [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
* [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
* [Azure PowerShell – Classic](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. [Vytvoření nové brány požadovaného typu a dokončení nastavení sítě VPN](vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)

### <a name="do-i-need-a-gatewaysubnet"></a>Potřebuji GatewaySubnet?

Ano. Podsíť brány obsahuje IP adresy, které používá služba brány virtuální sítě. Pro virtuální síť je třeba vytvořit podsíť brány, aby bylo možné konfigurovat bránu virtuální sítě. Pro správné fungování všech podsítí brány je nutné, aby měly název GatewaySubnet. Nenastavujte pro podsíť brány jiný název. A v podsíti brány nenasazujte virtuální počítače ani žádná jiná zařízení.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přidělené službě brány. Některé konfigurace vyžadují přidělení více IP adres službám brány než jiné. Ujistěte se, že podsíť brány obsahuje dostatek IP adres, aby se mohla přizpůsobit budoucímu růstu a případným dalším konfiguracím nových připojení. Přestože je tedy možné vytvořit tak malou podsíť brány, jako je /29, doporučujeme vytvořit podsíť brány o velikosti /27 nebo větší (/27, /26, /25 atd.). Podívejte se na požadavky pro konfiguraci, kterou chcete vytvořit, a ověřte, že vaše podsíť brány bude tyto požadavky splňovat.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Je možné nasazovat do podsítě brány virtuální počítače nebo instance role?

Ne.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Je možné získat IP adresu brány VPN předtím, než se vytvoří?

Ne. Nejprve je třeba vytvořit bránu, aby se získala IP adresa. Pokud bránu VPN odstraníte a znovu vytvoříte, IP adresa se změní.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Je možné vyžádat si pro bránu VPN statickou veřejnou IP adresu?

Ne. Podporuje se pouze dynamické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Změna IP adresy brány VPN proběhne pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se veřejná IP adresa brány VPN nezmění. 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Jak se tunelové připojení sítě VPN ověřuje?

Síť VPN Azure používá ověřování PSK (předsdílený klíč). Při vytváření tunelového propojení sítě VPN se vygeneruje předsdílený klíč (PSK). Automaticky generovaný předsdílený klíč můžete nahradit vlastním prostřednictvím rutiny prostředí PowerShell nebo rozhraní API REST pro nastavení předsdíleného klíče.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Je možné použít rozhraní API pro nastavení předsdíleného klíče ke konfiguraci sítě VPN s bránou založenou na zásadách (se statickým směrováním)?

Ano, rozhraní API i rutinu prostředí PowerShell pro nastavení předsdíleného klíče je možné použít ke konfiguraci sítí VPN Azure založených na zásadách (se statickým směrováním) i sítí VPN Azure založených na směrování (s dynamickým směrováním).

### <a name="can-i-use-other-authentication-options"></a>Je možné použít jiné možnosti ověřování?

Ověřování je omezeno na použití předsdílených klíčů (PSK).

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Jak určit provoz, který má procházet bránou VPN?

#### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

* PowerShell: K určení provozu na místní síťové bráně použijte parametr AddressPrefix.
* Azure Portal: Přejděte k položce Brána místní sítě > Konfigurace > Adresní prostor.

#### <a name="classic-deployment-model"></a>Model nasazení Classic

* Azure Portal: Přejděte k položce klasická virtuální síť > připojení k síti VPN > připojení VPN typu Site-to-Site > Název místní lokality > Místní lokalita > Klientský adresní prostor. 

### <a name="can-i-configure-force-tunneling"></a>Je možné konfigurovat vynucené tunelování?

Ano. Informace najdete v tématu [Konfigurace vynuceného tunelování](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Je možné nastavit v Azure vlastní server VPN a používat ho pro připojování k místní síti?

Ano, je možné nasazovat v Azure vlastní servery nebo brány VPN buď z webu Azure Marketplace, nebo vytvořené jako vlastní směrovače sítě VPN. Je třeba nakonfigurovat ve virtuální síti trasy definované uživatelem, aby se zajistilo správné směrování provozu mezi místními sítěmi a podsítěmi virtuálních sítí.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Proč jsou některé porty brány VPN otevřené?

Jsou vyžadovány pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity (včetně zákazníků těchto bran) žádným způsobem ovlivnit činnost těchto koncových bodů.

Brána sítě VPN je v podstatě zařízení s více adresami, přičemž jedna síťová karta je připojena k privátní síti zákazníka a jedna je přístupná z veřejné sítě. Entity infrastruktury Azure nemohou kvůli shodě procházet do privátních sítí zákazníků, takže je pro komunikaci infrastruktury třeba využívat veřejné koncové body. Veřejné koncové body jsou pravidelně kontrolovány auditem zabezpečení Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Další informace o typech brány, požadavcích a propustnosti

Další informace najdete v tématu [Informace o nastavení konfigurace služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="s2s"></a>Připojení typu Site-to-Site a zařízení VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Co je třeba zvážit při výběru zařízení VPN?

Ve spolupráci s dodavateli zařízení jsme ověřili sadu standardních zařízení VPN pro připojení Site-to-Site. Seznam známých kompatibilních zařízení VPN, příslušné pokyny ke konfiguraci nebo ukázky a specifikace zařízení najdete v článku [s popisem zařízení VPN](vpn-gateway-about-vpn-devices.md). Všechna zařízení v řadách zařízení uvedená jako známá kompatibilní by měla spolupracovat s virtuální sítí. Konfiguraci zařízení VPN vám usnadní ukázka konfigurace zařízení nebo odkaz, který odpovídá příslušné řadě zařízení.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Kde najdu nastavení konfigurace zařízení VPN?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Jak upravím ukázky konfigurace zařízení VPN?

Informace o úpravách ukázek konfigurace zařízení najdete v tématu popisujícím [úpravy ukázek](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Kde najdu parametry protokolu IPsec a IKE?

Parametry protokolu IPsec/IKE najdete v popisu [parametrů](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Proč se tunelové připojení sítě VPN založené na zásadách při nečinnosti deaktivuje?

Toto je očekávané chování u bran VPN pracujících na základě zásad (označují se také výrazem statické směrování). Když je přenos přes tunelové propojení nečinný déle než 5 minut, tunelové propojení se deaktivuje. Když se provoz v některém směru obnoví, tunelové propojení se ihned aktivuje znovu.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Je možné používat pro připojení k Azure softwarové sítě VPN?

Konfigurace připojení Site-to-Site pro více míst je podporována u serverů RRAS (Routing and Remote Access) Windows Server 2012.

Jiná softwarová řešení sítě VPN by měla s naší bránou spolupracovat, pokud odpovídají implementacím protokolu IPsec podle průmyslových standardů. Ohledně pokynů ke konfiguraci a podporu se obraťte na výrobce příslušného softwaru.

## <a name="P2S"></a>Point-to-Site s využitím nativního ověřování certifikátů Azure

Tato část se týká modelu nasazení Resource Manager.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>Point-to-Site s využitím ověřování pomocí protokolu RADIUS

Tato část se týká modelu nasazení Resource Manager.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>Připojení typu VNet-to-VNet a Multi-Site

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Je možné používat bránu VPN Azure pro provoz mezi místními servery a jinou virtuální sítí?

**Model nasazení Resource Manager**<br>
Ano. Další informace najdete v článku o [BGP](#bgp).

**Model nasazení Classic**<br>
Provoz prostřednictvím brány VPN Azure s použitím modelu nasazení Classic je možný, je však závislý na staticky definovaných adresních prostorech v souboru konfigurace sítě. Protokol BGP není u virtuálních sítí a bran VPN Azure používajících model nasazení Classic dosud podporován. Bez protokolu BGP je ruční definování adresních prostorů pro přenos velmi náchylné k chybám a nedoporučuje se.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Generuje Azure stejný předsdílený klíč protokolu IPsec/IKE pro všechna připojení k síti VPN pro stejnou virtuální síť?

Ne, Azure ve výchozím nastavení pro různá připojení k síti VPN generuje různé předsdílené klíče. Prostřednictvím rozhraní API REST nebo rutiny prostředí PowerShell pro nastavení předsdíleného klíče však můžete nastavit požadovanou hodnotu klíče. Klíčem MUSÍ být alfanumerický řetězec o délce 1 až 128 znaků.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Je možné dosáhnout větší šířky pásma použitím několika sítí VPN Site-to-Site než v případě jedné virtuální sítě?

Ne, všechna tunelové propojení sítí VPN Point-to-Site sdílejí tutéž bránu VPN Azure a dostupnou šířku pásma.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Je možné nakonfigurovat více tunelových propojení mezi virtuální sítí a místního serverem prostřednictvím sítě VPN pro více serverů?

Ano, ale budete muset nakonfigurovat BGP na obou tunelech ve stejné lokalitě.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Je možné používat sítě VPN Point-to-Site v případě virtuální sítě s několika tunelovými propojeními sítě VPN?

Ano, sítě VPN Point-to-Site (P2S) je možné používat spolu s bránami VPN, které se připojují k několika místním serverům a dalším virtuálním sítím.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Je možné připojit virtuální síť se sítěmi VPN s protokolem IPsec k okruhu ExpressRoute?

Ano, tato možnost je podporována. Další informace najdete v tématu [konfigurace ExpressRoute a připojení VPN typu site-to-site, která mohou existovat vedle sebe](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike"></a>Zásady IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>Připojení mezi místními sítěmi a virtuální počítače

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Pokud se virtuální počítač nachází ve virtuální síti s propojením mezi různými místy, jak se k virtuálnímu počítači připojovat?

Možností je několik. Pokud je pro virtuální počítač povolen protokol RDP, je možné připojit se k virtuálnímu počítači s použitím privátní IP adresy. V takovém případě zadáte privátní IP adresu a port, ke kterému se chcete připojit (obvykle 3389). Je třeba nakonfigurovat příslušný port ve virtuálním počítači pro provoz.

K virtuálnímu počítači se lze připojit pomocí privátní IP adresy i z jiného virtuálního počítače umístěného ve stejné virtuální síti. K virtuálnímu počítači se nelze připojit pomocí protokolu RDP s použitím privátní IP adresy z místa mimo virtuální síť. Pokud je například nakonfigurována virtuální síť typu Point-to-Site, ale z počítače není vytvořeno připojení, nelze se k virtuálnímu počítači připojit pomocí privátní IP adresy.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Pokud se virtuální počítač nachází ve virtuální síti s možností připojení mezi různými místy, prochází tímto připojením veškerý provoz z virtuálního počítače?

Ne. Bránou virtuální sítě prochází pouze s cílovou IP adresou uvedenou v nastavených rozsazích IP adres místní sítě pro příslušnou virtuální síť. Provoz s cílovou IP adresou v příslušné virtuální síti probíhá v rámci této virtuální sítě. Ostatní provoz je odesílán prostřednictvím služby Load Balancer do veřejných sítí nebo prostřednictvím brány VPN Azure, pokud je použito vynucené tunelování.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>Nejčastější dotazy týkající se virtuálních sítí

Další informace o virtuálních sítích najdete v tématu [Nejčastější dotazy týkající se virtuálních sítí](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Další postup

* Další informace o službě VPN Gateway najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).
* Další informace o nastavení konfigurace služby VPN Gateway najdete v tématu [Informace o nastavení konfigurace služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).
