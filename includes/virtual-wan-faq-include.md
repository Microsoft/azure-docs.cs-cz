---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/24/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 827a2d6dc8a3622c17cdbcdfb179a3ea0f434f6f
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006476"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Musí mít uživatel k používání Azure Virtual WAN rozbočovač a paprsek se zařízeními SD-WAN/VPN?

Služba Virtual WAN poskytuje mnoho funkcí integrovaných v jednom podokně, jako je připojení Site-to-Site VPN, User/P2S Connectivity, ExpressRoute konektivita, Virtual Network konektivita, VPN ExpressRoute vzájemné propojení, VNET to Transitive tranzitivní připojení, centralizované směrování, brána firewall Azure a Správce brány firewall, monitorování, šifrování ExpressRoute a mnoho dalších možností. Nemusíte mít všechny tyto případy použití, abyste mohli začít používat virtuální síť WAN. Můžete jednoduše začít jenom s jedním případem použití. Virtuální architektura WAN je architektura rozbočovače a paprsku s integrovaným škálováním a výkonem, kde se větví (zařízení VPN/SD-WAN), uživatelé (klienti Azure VPN, openVPN nebo IKEv2), ExpressRoute okruhy, virtuální sítě slouží jako paprsky virtuálních rozbočovačů. Všechna centra jsou připojená v celé síti sítě WAN, která usnadňuje uživateli používání páteřní sítě Microsoftu pro připojení typu any-to-Any (jakékoli paprsky). Pro centrum a paprsky se zařízeními SD-WAN/VPN je uživatelé mohli ručně nastavit na portálu Azure Virtual WAN nebo použít virtuální síť WAN partner (SD-WAN/VPN) pro nastavení připojení k Azure. Virtuální partneři sítě WAN poskytují automatizaci pro připojení, což je schopnost exportovat informace o zařízení do Azure, stáhnout konfiguraci Azure a navázat připojení ke službě Azure Virtual WAN hub. Pro připojení VPN typu Point-to-site/User podporujeme klienta [Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN nebo IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Jaký klient podporuje službu Azure Virtual WAN User VPN (Point-to-site)?

Virtual WAN podporuje [klienta VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554), klienta OpenVPN nebo jakéhokoli klienta IKEv2. Ověřování Azure AD je podporované klientem Azure VPN. vyžaduje se minimálně operační systém Windows 10 Client verze 17763,0 nebo vyšší.  OpenVPN klienti můžou podporovat ověřování na základě certifikátů. Po výběru ověřování na základě certifikátu v bráně se zobrazí soubor. ovpn, který se stáhne do vašeho zařízení. Protokol IKEv2 podporuje certifikát i ověřování pomocí protokolu RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Pro uživatele VPN (Point-to-site) – proč je fond klientů P2S rozdělen do dvou tras?

Každá brána má dvě instance, k rozdělení dojde, aby každá instance brány mohla nezávisle přidělit klientské IP adresy pro připojené klienty a provoz z virtuální sítě se směruje zpátky na správnou instanci brány, aby nedošlo k přesměrování instancí mezi branami.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Návody přidat servery DNS pro klienty P2S?

Existují dvě možnosti, jak přidat servery DNS pro klienty P2S.

1. Otevřete lístek podpory u Microsoftu a přidejte své servery DNS do centra.
2. Nebo pokud používáte klienta Azure VPN pro Windows 10, můžete před jeho importem upravit stažený soubor XML s profilem a přidat ** \<dnsservers>\<serveru DNS> \</DNSServer>\</dnsservers>** značky.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Pro uživatele VPN (Point-to-site) – kolik klientů se podporuje?

Každá Brána VPN P2S pro uživatele má dvě instance a každá instance podporuje až určitých uživatelů při změně jednotky škálování. Jednotka škálování 1-3 podporuje připojení 500, škálování jednotka 4-6 podporuje 1000 připojení, jednotka škálování 7-12 podporuje 5000 připojení a jednotky škálování 13-20 podporuje připojení až 10 000. Například umožňuje, aby uživatel vybírá 1 jednotku škálování. Každá jednotka škálování by znamenala, že je nasazená brána aktivní-aktivní a každá z instancí (v tomto případě 2) by podporovala připojení až 500. Vzhledem k tomu, že můžete získat připojení 500 × 2 na bránu, neznamená to, že pro tuto jednotku škálování naplánujete 1000 namísto 500, protože při překročení doporučeného počtu připojení může dojít k přerušení připojení k dodatečnému 500. Nezapomeňte také naplánovat prostoje v případě, že se rozhodnete horizontální navýšení nebo snížení kapacity na jednotce škálování nebo změnit konfiguraci Point-to-site v bráně VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a bránou VPN Azure Virtual WAN?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Když připojíte lokalitu k virtuální síti WAN VPN Gateway, liší se od běžné brány virtuální sítě, která používá typ brány VPN. Podobně platí, že když připojujete okruh ExpressRoute k virtuálnímu rozbočovači WAN, používá jiný prostředek pro bránu ExpressRoute, než je obvyklá Brána virtuální sítě, která používá typ brány ExpressRoute. Virtuální síť WAN podporuje až 20 GB/s agregovanou propustnost jak pro VPN, tak pro ExpressRoute. Virtuální síť WAN má také automatizaci pro připojení s ekosystémem partnerů pro zařízení s připojením k síti. Zařízení s větví CPE mají vestavěnou automatizaci, která automaticky zřídí a připojuje se k Azure Virtual WAN. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Podívejte se na [seznam upřednostňovaných partnerů](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Jak se virtuální síť WAN liší od brány virtuální sítě Azure?

SÍŤ VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Můžete připojit až 1 000 připojení větví na oblast (virtuální rozbočovač) s agregací 20 GB/s na jeden hub. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. Můžete mít jeden rozbočovač na oblast, což znamená, že můžete připojit více než 1 000 větví napříč rozbočovači.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co je jednotka škálování služby Virtual WAN
Jednotka škálování je jednotka definovaná k výběru agregované propustnosti brány ve virtuálním rozbočovači. 1 jednotka škálování sítě VPN = 500 MB/s. 1 jednotka škálování ExpressRoute = 2 GB/s. Příklad: 10 jednotek škálování sítě VPN by znamenalo 500 MB/s × 10 GB/s.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Které poskytovatele zařízení (virtuální partneři sítě WAN) jsou podporované?

Plně automatizované prostředí Virtual WAN v současnosti podporuje celá řada partnerů. Další informace najdete v tématu [Partneři Virtual WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jaké jsou kroky automatizace Virtual WAN u partnerů?

Informace o krocích automatizace u partnerů najdete v tématu o [automatizaci Virtual WAN u partnerů](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům Azure na podporu IKEv2/IKEv1 protokolu IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Automatizace zahrnuje odesílání informací o větvích, stahování konfigurace Azure, nastavování tunelů IPSec na virtuální rozbočovače Azure a automatické nastavení připojení k vytvoření připojení k virtuální síti WAN na Azure. Když máte stovky větví, připojení pomocí virtuálních sítí WAN CPE je snadné, protože možnosti registrace neberou v platnost nutnost nastavovat, konfigurovat a spravovat rozsáhlé připojení IPsec. Další informace najdete v tématu věnovaném [automatizaci partnerů pro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak virtuální síť WAN podporuje zařízení SD-WAN?

Virtuální partneři sítě WAN automatizují připojení IPsec k koncovým bodům VPN Azure. Pokud je adaptér virtuální sítě WAN poskytovatelem SD-WAN, předpokládá se, že řadič SD-WAN spravuje automatizaci a připojení IPsec k koncovým bodům VPN Azure. Pokud zařízení SD-WAN vyžaduje svůj vlastní koncový bod místo Azure VPN pro jakékoli proprietární funkce SD-WAN, můžete koncový bod SD-WAN ve virtuální síti Azure nasadit a koexistovat s Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Mění Virtual WAN stávající funkce připojení?

Stávající funkce připojení Azure se nemění.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, Virtual WAN zavádí nové prostředky Resource Manageru. Další informace najdete v [přehledu](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kolik zařízení VPN se může připojit k jednomu rozbočovači?

Pro jedno virtuální centrum se podporuje až 1 000 připojení. Každé připojení se skládá ze čtyř odkazů a každé připojení propojení podporuje dvě tunely, které jsou v konfiguraci aktivní-aktivní. Tunely se ukončí ve virtuálním rozbočovači Azure vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Může se místní zařízení VPN připojit k více rozbočovačům?

Ano. Tok přenosů, když se začíná, pochází z místního zařízení do nejbližší hraniční sítě Microsoftu a pak do virtuálního centra.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, virtuální síť svého oblíbeného virtuálního síťového zařízení (NVA) můžete připojit k síti Azure Virtual WAN. Virtuální síť virtuálního síťového zařízení nejprve připojte k rozbočovači pomocí připojení k centrální virtuální síti. Pak vytvořte trasu virtuálního rozbočovače s dalším segmentem směrování odkazujícím na virtuální zařízení. V tabulce směrování virtuálního rozbočovače můžete použít několik tras. Všechny paprsky připojené k virtuální síti virtuálního síťového zařízení musí být navíc připojené k virtuálnímu rozbočovači kvůli zajištění šíření tras hvězdicové virtuální sítě do místních systémů.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Můžu ve virtuálním rozbočovači vytvořit síťové virtuální zařízení?

Síťové virtuální zařízení (síťové virtuální zařízení) se nedá nasadit v rámci virtuálního rozbočovače. Můžete ho ale vytvořit ve virtuální síti paprsku, která je připojená k virtuálnímu rozbočovači, a povolit trase v centru pro směrování provozu pro cílovou virtuální síť prostřednictvím IP adresy síťové virtuální zařízení (síťové karty).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Je možné, že virtuální síť rozbočovače má bránu virtuální sítě?

Ne. Virtuální síť paprsků nemůže mít bránu virtuální sítě, pokud je připojená k virtuálnímu rozbočovači.

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Při vytváření sítě VPN můžete zadat parametry protokolu BGP. To znamená, že pro protokol BGP bude povolená jakákoli připojení vytvořená v Azure pro tuto lokalitu. Pokud jste navíc používali virtuální síť s síťové virtuální zařízení a pokud byla tato virtuální síť síťové virtuální zařízení připojená k virtuálnímu rozbočovači WAN, aby bylo zajištěno, že se trasy z virtuální sítě síťové virtuální zařízení inzerují vhodně, paprsky připojené ke síťové virtuální zařízení virtuální síti musí protokol BGP zakázat. Kromě toho připojte tyto paprsky virtuální sítě k virtuální síti virtuálního centra a zajistěte, aby byly trasy sítě rozbočovače šířeny do místních systémů.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Ano, provoz můžete směrovat do virtuální sítě pomocí směrovací tabulky virtuálního rozbočovače. To vám umožní nastavit trasy pro cílové virtuální sítě v Azure prostřednictvím konkrétní IP adresy (obvykle síťové karty síťové virtuální zařízení).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?

Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Návody vypočítat cenu centra?

* Platíte za služby v centru. Řekněme například, že máte 10 větví nebo místní zařízení vyžadující připojení k Azure Virtual WAN by znamenalo připojení k koncovým bodům VPN v centru. To znamená, že se jedná o síť VPN s 1 jednotkou škálování = 500 MB/s, účtuje se za $0.361/hod. Každé připojení se účtuje za $0,05/hod. U 10 připojení by celková cena za službu/HR byla $0,361 + $. 5/hod. Poplatky za přenosy dat, které opouští Azure, se použijí.

* Účtuje se další poplatek za střed. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Pokud jste ExpressRoute bránu z důvodu ExpressRoute okruhů připojujících se k virtuálnímu rozbočovači, měli byste platit za jednotkovou cenu škálování. Každá jednotka škálování v ER je 2 GB/s a každá jednotka připojení se účtuje stejnou sazbou jako jednotka připojení VPN.

* Pokud jste se připojili k rozbočovači virtuální sítě, účtují se poplatky za partnerské vztahy na paprskovém virtuální sítě. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Všechna rozhraní API Virtual WAN jsou otevřená rozhraní API. Můžete si projít dokumentaci a vyhodnotit technickou proveditelnost. Pokud máte nějaké dotazy, pošlete e-mail na azurevirtualwan@microsoft.comadresu. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co když zařízení, které používám, není v seznamu partnerů virtuální sítě WAN? Můžu ho I nadále používat pro připojení k síti VPN Azure Virtual WAN?

Ano, pokud zařízení podporuje protokol IPsec IKEv1 nebo IKEv2. Virtuální partneři sítě WAN automatizují připojení ze zařízení k koncovým bodům VPN Azure. To zahrnuje automatizaci kroků, jako je například nahrání informací o větvích, protokol IPsec a konfigurace a připojení. Vzhledem k tomu, že vaše zařízení nepochází z virtuálního partnerského serveru pro partnery WAN, budete muset provést těžkou zvedání ručního nastavování konfigurace Azure a aktualizace vašeho zařízení, aby se nastavilo připojení pomocí protokolu IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchá konfigurace jedné virtuální sítě WAN s jedním rozbočovačem a jedním vpnsite se dá vytvořit pomocí [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtuální síť WAN je primárně řízená jako služba založená na REST nebo na portálu.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

Virtuální síť můžete připojit v jiné oblasti, než je vaše virtuální síť WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Můžou virtuální sítě připojení k virtuálnímu rozbočovači komunikovat mezi sebou (V2V tranzit)?

Ano. Standard Virtual WAN podporuje přenos přes síť VNet přes síť VNet přes virtuální síť WAN, ke které jsou virtuální sítě připojené. V terminologii virtuální sítě WAN se tyto cesty označují jako "místní virtuální síť WAN" pro virtuální sítě připojené k virtuálnímu rozbočovači WAN v rámci jedné oblasti a "globální virtuální síť WAN" pro virtuální sítě připojené prostřednictvím několika virtuálních rozbočovačů WAN ve dvou nebo více oblastech. Transitní virtuální sítě podporuje až 3 GB/s propustnost během veřejné verze Preview. Propustnost se rozšíří, když globální přenos dostane GA.

Poznámka: V2V Transit Preview vyžaduje, aby byl ve virtuálním rozbočovači nasazený rozhraní VPN GS, aby bylo možné aktivovat elementy směrování, které se mají spustit. Tento GS VPN se nepoužívá pro cestu k přenosu V2V. Toto je známé omezení a v době V2V GA se odebere. VPN Gateway můžete odstranit v středech až po jejich úplném spuštění, protože není potřeba pro funkce přenosu V2V. 

V některých scénářích může být paprskový virtuální sítě také přímo navzájem partnerským vztahem s použitím [Virtual Network partnerských vztahů](../articles/virtual-network/virtual-network-peering-overview.md) kromě místního nebo globálního přenosu virtuální sítě WAN. V takovém případě má partnerský vztah virtuálních sítí přednost před přenosným připojením přes virtuální síť WAN. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co je připojení ke větvi Azure Virtual WAN?

Připojení ze zařízení z pobočky do Azure Virtual WAN podporuje až čtyři odkazy. Odkaz je odkaz na fyzické připojení ve větvi umístění (například ATT, Verizon atd.). Každé připojení propojení se skládá ze dvou tunelů aktivní/aktivní IPsec.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou dostupné v síti Virtual WAN u připojení VPN a u připojení VPN na ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází provoz mezi větví přes Azure Virtual WAN?

Ano.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje virtuální síť WAN ExpressRoute z každé lokality?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Používá standardní připojení typu Site-to-site s protokolem IPsec prostřednictvím internetových odkazů ze zařízení do služby Azure Virtual WAN hub. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. Pro lokality, které jsou připojené pomocí ExpressRoute ve virtuálním centru, můžou lokality mít mezi sítí VPN a ExpressRoute tok přenosů mezi větvemi.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Počet větví je omezený na 1000 připojení na střed a oblast a celkem 20 GB/s v centru. Můžete mít 1 rozbočovač na oblast.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Kolik připojení k síti VPN podporuje virtuální centrum WAN?

Rozbočovač Azure Virtual WAN může podporovat až 1 000 připojení S2S, 10 000 připojení P2S a 4 připojení ExpressRoute současně.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaká je celková propustnost sítě VPN tunelu VPN a připojení?

Celková propustnost sítě VPN rozbočovače je až 20 GB/s na základě zvolené jednotky škálování. Propustnost sdílí všechna existující připojení. Každé tunelové propojení v rámci připojení může podporovat až 1 GB/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Na portálu se nezobrazuje nastavení 20 GB pro virtuální rozbočovač. Návody nakonfigurovat?

Přejděte k bráně VPN uvnitř centra na portálu a kliknutím na jednotku škálování ji změňte na příslušné nastavení.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje virtuální síti WAN, aby místní zařízení využilo více poskytovatelů internetových služeb paralelně, nebo je vždy jedním tunelem sítě VPN?
Řešení místních zařízení můžou použít zásady provozu pro řízení provozu napříč několika tunely do Azure.


### <a name="what-is-global-transit-architecture"></a>Co je architektura globálního přenosu?

Informace o globální architektuře přenosů najdete v tématu [globální přenosová architektura sítě a virtuální síť WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Provoz se řídí vzorem: síťová zařízení – >poskytovatel internetových služeb >hraniční síť Microsoftu – >Microsoft DC (síť VNet) – >Microsoft Network Edge->ISP->pobočky

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k Internetu a fyzické zařízení, které podporuje protokol IPsec, nejlépe z našich integrovaných [virtuálních partnerů WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). Volitelně můžete ručně spravovat konfiguraci a připojení k Azure z preferovaného zařízení.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Návody povolit výchozí trasu (0.0.0.0/0) v připojení (VPN, ExpressRoute nebo Virtual Network):

Virtuální rozbočovač může rozšířit získanou výchozí trasu na připojení VPN/ExpressRoute typu Site-to-site k síti VPN/, pokud je příznak povoleno u připojení. Tento příznak se zobrazí, když uživatel upraví připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Ve výchozím nastavení je tento příznak zakázán, pokud je lokalita nebo okruh ExpressRoute připojen k rozbočovači. Ve výchozím nastavení je povolená, když se přidá připojení k virtuální síti, které virtuální síť připojí k virtuálnímu rozbočovači. Výchozí trasa nepochází do virtuálního centra WAN. Výchozí trasa je šířena v případě, že ji již služba Virtual WAN hub vyvolala v důsledku nasazení brány firewall v centru nebo v případě, že je povoleno vynucené tunelování na jiném připojeném serveru.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Jak virtuální rozbočovač ve virtuální síti WAN vybere nejlepší cestu pro trasu z více Center

Pokud virtuální rozbočovač zjistí stejnou trasu z několika vzdálených rozbočovačů, pořadí, ve kterém se rozhodne, je následující.
1) Směrování trasy a) síťové trasy – předpony virtuální sítě se přímo učí pomocí bran služby Virtual hub b) trasy centra (staticky nakonfigurované trasy) c) protokol BGP d) InterHub trasy.
2)  Metrika tras: virtuální síť WAN upřednostňuje ExpressRoute přes VPN. Partnerský uzel ExpressRoute má ve srovnání s partnerským vztahem VPN vyšší váhu.
3)  JAKO délka cesty

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Je podpora protokolu IPv6 ve virtuální síti WAN podporovaná?

Protokol IPv6 není podporován ve virtuálním centru sítě WAN a jeho branách. Pokud máte virtuální síť, která má podporu protokolu IPv6, a chcete připojit virtuální síť k virtuální síti WAN, tento scénář není také podporován. 

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jaké jsou rozdíly mezi typy virtuálních sítí WAN (Basic a Standard)?

Typ sítě typu "základní" umožňuje vytvořit základní centrum (SKU = Basic). Standardní typ sítě WAN umožňuje vytvořit standardní centrum (SKU = Standard). Základní centra jsou omezená na funkce VPN typu Site-to-site. Centra Standard umožňují, aby v centrech bylo ExpressRouteo, uživatelské VPN (P2S), celá mřížka a přenos VNet-to-VNet. Platíte základní poplatek za $0,25/hod pro standardní centra a poplatek za zpracování dat pro přenos přes rozbočovače během připojení typu VNet-to-VNet a také zpracování dat pro rozbočovače do centrálního provozu. Další informace najdete v tématu [základní a standardní virtuální sítě WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Ceny najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/) .
