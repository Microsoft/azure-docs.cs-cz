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
ms.openlocfilehash: 2b30c176cf3c9dd31ae3efa85d308b3f89bd4dbe
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734305"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Potřebuje uživatel mít rozbočovač a mluvil se zařízeními SD-WAN/VPN, aby mohl používat Azure Virtual WAN?

Virtuální síť WAN poskytuje mnoho funkcí integrovaných do jediného podokna skla, jako je připojení VPN site/site-to-site, připojení k internetu/P2S, připojení ExpressRoute, připojení virtuální sítě, propojení VPN ExpressRoute, přenosná připojení virtuální sítě k virtuální síti, centralizované směrování, zabezpečení azure firewall a správce brány firewall, monitorování, šifrování ExpressRoute a mnoho dalších funkcí. Nemusíte mít všechny tyto případy použití začít používat virtuální WAN. Můžete jednoduše začít s jedním případem použití. Architektura Virtuální WAN je architektura rozbočovače a paprsku s integrovaným škálovatelem a výkonem, kde pobočky (zařízení VPN/SD-WAN), uživatelé (klienti VPN Azure, klienti openVPN nebo IKEv2), obvody ExpressRoute, virtuální sítě slouží jako paprsky virtuálního centra. Všechny rozbočovače jsou připojeny v plné síti ve standardní virtuální síti WAN, což uživateli usnadňuje použití páteřní sítě Microsoftu pro připojení libovolného k libovolnému (libovolnému paprsku). Pro rozbočovač a mluvil s SD-WAN/VPN zařízení, uživatelé můžete buď ručně nastavit na portálu Azure Virtual WAN nebo pomocí virtuální WAN Partner CPE (SD-WAN/VPN) nastavit připojení k Azure. Partneři virtuální sítě WAN poskytují automatizaci pro připojení, což je možnost exportovat informace o zařízení do Azure, stáhnout konfiguraci Azure a navázat připojení k centru Azure Virtual WAN. Pro připojení VPN point-to-site/User podporujeme [klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554), klienta OpenVPN nebo IKEv2. 

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Jakého klienta podporuje síť Azure Virtual WAN User VPN (Point-to-site)?

Virtual WAN podporuje [klienta Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554), klienta OpenVPN nebo libovolného klienta IKEv2. Azure AD ověřování je podporované s Azure VPN Client.A minimálně Windows 10 klientského operačního systému verze 17763.0 nebo vyšší je vyžadováno.  OpenVPN klient (y) může podporovat ověřování na základě certifikátu. Jakmile je na bráně vybráno ověřování založené na certifikátu, uvidíte soubor .ovpn, který chcete stáhnout do vašeho zařízení. Certifikát i radius auth je podporován i IKEv2. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Pro uživatele VPN (Point-to-site)- Proč je fond klientů P2S rozdělen do dvou tras?

Každá brána má dvě instance, rozdělení se stane tak, aby každá instance brány můžete nezávisle přidělit IP adresy klienta pro připojené klienty a provoz z virtuální sítě je směrován zpět do instance správné brány, aby se zabránilo směrování instance mezi bránami.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Jak přidám servery DNS pro klienty P2S?

Existují dvě možnosti přidání serverů DNS pro klienty P2S.

1. Otevření lístku podpory u Microsoftu a jejich přidání serverů DNS do centra
2. Nebo pokud používáte klientazure VPN pro Windows 10, můžete upravit stažený soubor XML profilu a přidat ** \<dnsserver>\<dnsserver> \</dnsserver>\</dnsservers>** značky před jeho importem.

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

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Pro uživatele VPN (Point-to-site)- kolik klientů je podporováno?

Každá brána VPN P2S uživatele má dvě instance a každá instance podporuje až určité uživatele, jak se mění jednotka škálování. Jednotka škálování 1-3 podporuje připojení 500, jednotka Scale 4-6 podporuje 1000 připojení, jednotka Scale 7-10 podporuje připojení 5000 a jednotka 11+ podporuje až 10 000 připojení. Jako příklad řekněme, že uživatel zvolí jednotku 1 měřítka. Každá jednotka škálování by znamenala aktivní aktivní bránu nasazenou a každá instance (v tomto případě 2) by podporovala až 500 připojení. Vzhledem k tomu, že můžete získat připojení 500 * 2 na bránu, neznamená to, že plánujete 1000 namísto 500 pro tuto jednotku škálování, protože instance může být nutné opravit, během kterého může být připojení pro extra 500 přerušeno, pokud překročíte doporučený počet připojení.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (vpn gateway) a bránou Azure Virtual WAN VPN?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Když připojíte web k bráně virtuální sítě WAN VPN, liší se od běžné brány virtuální sítě, která používá typ brány "VPN". Podobně když připojíte okruh ExpressRoute k rozbočovači Virtuální WAN, použije pro bránu ExpressRoute jiný prostředek než běžná brána virtuální sítě, která používá typ brány ExpressRoute. Virtuální síť WAN podporuje agregoční propustnost až 20 Gb/s jak pro VPN, tak pro ExpressRoute. Virtuální WAN má také automatizaci pro připojení s ekosystémem partnerů cpe pobočkových zařízení. Zařízení pobočky CPE mají integrovanou automatizaci, která automaticky zřaží a připojuje se k virtuální síti Azure WAN. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Podívejte se na [seznam upřednostňovaných partnerů](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Jak se virtuální síť WAN liší od brány virtuální sítě Azure?

Virtuální síť brána VPN je omezena na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Můžete připojit až 1 000 větev připojení na oblast (virtuální rozbočovač) s agregací 20 Gb/s na rozbočovač. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. Můžete mít jeden rozbočovač na oblast, což znamená, že můžete připojit více než 1 000 větví napříč rozbočovači.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co je jednotka škálování virtuální brány WAN
Jednotka škálování je jednotka definovaná k vyskladnění agregační propustnost brány ve virtuálním rozbočovači. 1 měřítku jednotky VPN = 500 Mbps . 1 jednotka měřítka ExpressRoute = 2 Gb/s. Příklad: 10 měřítku jednotky VPN by znamenalo 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Kteří poskytovatelé zařízení (partneři virtuální sítě WAN) jsou podporováni?

Plně automatizované prostředí Virtual WAN v současnosti podporuje celá řada partnerů. Další informace najdete v tématu [Partneři Virtual WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jaké jsou kroky automatizace Virtual WAN u partnerů?

Informace o krocích automatizace u partnerů najdete v tématu o [automatizaci Virtual WAN u partnerů](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům Azure na podporu IKEv2/IKEv1 protokolu IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Automatizace zahrnuje nahrávání informací o větvi, stahování konfigurace Azure, nastavení tunelových propojení IPSec do virtuálních center Azure a automatické nastavení připojení z větvení zařízení do virtuální sítě Azure WAN. Pokud máte stovky poboček, připojení pomocí virtual WAN CPE Partners je snadné, protože možnost ipřipojení vyžaduje nastavení, konfiguraci a správu připojení k rozsáhlému protokolu IPsec. Další informace najdete v tématu věnovaném [automatizaci partnerů pro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).


### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak virtual WAN podporuje zařízení SD-WAN?

Virtuální partneři WAN automatizují připojení Protokolu IPsec ke koncovým bodům Azure VPN. Pokud je partner virtuální sítě WAN zprostředkovatelem SD-WAN, znamená to, že řadič SD-WAN spravuje automatizaci a připojení IPsec ke koncovým bodům Azure VPN. Pokud zařízení SD-WAN vyžaduje vlastní koncový bod namísto Azure VPN pro všechny proprietární funkce SD-WAN, můžete nasadit koncový bod SD-WAN ve virtuální síti Azure a koexistovat s Azure Virtual WAN.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Mění Virtual WAN stávající funkce připojení?

Stávající funkce připojení Azure se nemění.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, Virtual WAN zavádí nové prostředky Resource Manageru. Další informace najdete v [přehledu](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kolik zařízení VPN se může připojit k jednomu rozbočovači?

Na virtuální rozbočovač je podporováno až 1 000 připojení. Každé připojení se skládá ze čtyř propojení a každé propojení podporuje dva tunelové propojení, které jsou v konfiguraci aktivní aktivní. Tunely končí v azure virtuální rozbočovač vpngateway.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Může se místní zařízení VPN připojit k více rozbočovačům?

Ano. Tok provozu při zahájení je z místního zařízení na nejbližší okraj sítě Společnosti Microsoft a potom do virtuálního rozbočovače.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, virtuální síť svého oblíbeného virtuálního síťového zařízení (NVA) můžete připojit k síti Azure Virtual WAN. Virtuální síť virtuálního síťového zařízení nejprve připojte k rozbočovači pomocí připojení k centrální virtuální síti. Potom vytvořte virtuální trasu rozbočovače s dalším směrováním směřujícím na virtuální zařízení. Na tabulku směrování virtuálního rozbočovače můžete použít více tras. Všechny paprsky připojené k virtuální síti virtuálního síťového zařízení musí být navíc připojené k virtuálnímu rozbočovači kvůli zajištění šíření tras hvězdicové virtuální sítě do místních systémů.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Můžu ve virtuálním rozbočovači vytvořit síťové virtuální zařízení?

Síťové virtuální zařízení (NVA) nelze nasadit uvnitř virtuálního rozbočovače. Můžete ji však vytvořit ve virtuální síti s paprskem, která je připojená k virtuálnímu rozbočovači a povolit trasu v rozbočovači k přímému provozu pro cílovou virtuální síť prostřednictvím IP adresy Síťového virtuálního zařízení (síťové karty).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Může virtuální síť pro paprsky mít bránu virtuální sítě?

Ne. Virtuální síť s paprskem nemůže mít bránu virtuální sítě, pokud je připojena k virtuálnímu rozbočovači.

### <a name="is-there-support-for-bgp"></a>Podporuje se BGP?

Ano, BGP se podporuje. Při vytváření webu VPN můžete zadat parametry Protokolu BGP. To bude znamenat, že všechna připojení vytvořená v Azure pro tuto lokalitu budou povolena pro BGP. Navíc pokud jste měli virtuální síť s nva a pokud tato virtuální síť NVA byla připojena k virtuální wan rozbočovač, aby bylo zajištěno, že trasy z virtuální sítě NVA jsou inzerovány odpovídajícím způsobem, paprsky, které jsou připojeny k virtuální síti NVA musí zakázat BGP. Kromě toho připojte tyto virtuální sítě pro paprsky k virtuální virtuální síti rozbočovače, abyste zajistili, že trasy virtuální sítě paprsku se rozšíří do místních systémů.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Dá se ve virtuálním rozbočovači směrovat provoz s využitím UDR?

Ano, provoz můžete směrovat do virtuální sítě pomocí směrovací tabulky virtuálního rozbočovače. To umožňuje nastavit trasy pro cílové virtuální sítě v Azure prostřednictvím konkrétní IP adresy (obvykle síťové virtuální sítě).

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?

Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Jak vypočítám cenu rozbočovače?

* Zaplatili byste za služby v centru. Řekněme například, že máte 10 větví nebo místní zařízení, která vyžadují připojení k Virtuální síti Azure, by znamenalo připojení ke koncovým bodům VPN v centru. Řekněme, že se jedná o VPN 1 měřítku jednotky = 500 Mbps, to je účtováno na $ 0.361 / hod. Každé připojení je zpoplatněno ve výši 0,05 USD/hod. U 10 připojení by celkový poplatek za službu/hod činil 0,361 USD + $.5/hod. Platí poplatky za data za provoz opouštějící Azure.

* Za příplatek za rozbočovač. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Pokud jste měli ExpressRoute bránu kvůli ExpressRoute okruhy připojení k virtuálnímu rozbočovači, pak byste platit za jednotkovou cenu stupnice. Každá jednotka škálování v ER má 2 Gb/s a každá připojovací jednotka se účtuje stejnou rychlostí jako jednotka připojení VPN.

* Pokud jste měli paprskové vnety připojené k rozbočovači, poplatky za partnerský vztah na vyčovávacích zařízení Paprsky paprsku stále platí. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Všechna virtuální rozhraní WAN rozhraní API jsou otevřená rozhraní API. Můžete si projít dokumentaci a posoudit technickou proveditelnost. Máte-li jakékoli otázky, pošlete e-mail na adresu azurevirtualwan@microsoft.com. Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co když zařízení, které používám, není v seznamu partnerů virtuální sítě WAN? Můžu ho pořád používat k připojení k Azure Virtual WAN VPN?

Ano, pokud zařízení podporuje IPsec IKEv1 nebo IKEv2. Virtuální wan partneři automatizují připojení ze zařízení do koncových bodů Azure VPN. To znamená automatizaci kroků, jako je "nahrávání informací větev", "IPsec a konfigurace" a "připojení". Vzhledem k tomu, že vaše zařízení není z ekosystému partnerů virtuální sítě WAN, budete muset provést těžkou práci s ručním převzetím konfigurace Azure a aktualizací zařízení pro nastavení připojení IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchou konfiguraci jedné virtuální sítě WAN s jedním rozbočovačem a jedním vpnsite lze vytvořit pomocí [šablony rychlého startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtuální WAN je především služba REST nebo portálem řízená.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Podporuje se pro Azure Virtual WAN globální VNet Peering? 

Virtuální síť můžete připojit v jiné oblasti, než je vaše virtuální SÍŤ WAN.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Můžou virtuální sítě pro paprsky připojené k virtuálnímu rozbočovači vzájemně komunikovat (V2V Transit)?

Ano. Standardní virtuální síť WAN podporuje přenosité připojení virtuálnísítě k virtuálnísíti prostřednictvím rozbočovače Virtuální síť WAN, ke kterému jsou virtuální sítě připojeny. V terminologii virtuální sítě WAN označujeme tyto cesty jako "místní přenos virtuální virtuální sítě WAN" pro virtuální sítě připojené k virtuálnímu wan hubu v rámci jedné oblasti a "globální přenos virtuální virtuální sítě WAN" pro virtuální sítě připojené přes více virtuálních rozbočovačů WAN ve dvou nebo více oblastech. Přenos virtuální sítě podporuje až 3 Gb/s propustnost během veřejné verze Preview. Propustnost se rozšíří, když globální tranzit přejde GA.

Poznámka: V současné době V2V tranzit náhled vyžaduje VPN GW, které mají být nasazeny ve virtuálním centru pro aktivaci směrovací prvky, které mají být spuštěny. Tato VPN GW se nepoužívá pro tranzitní cestu V2V. Toto je známé omezení a bude odstraněno v době V2V GA. Bránu VPN můžete odstranit v rozbočovačích poté, co je plně spuštěna, protože není potřebná pro funkci přenosu V2V. 

Pro některé scénáře paprskové virtuální sítě lze také přímo peered s sebou pomocí [partnerského vztahu virtuální sítě](../articles/virtual-network/virtual-network-peering-overview.md) kromě místní nebo globální virtuální sítě virtuální sítě virtuální sítě tranzitu. V tomto případě má partnerský vztah vsíti přednost před přenositým připojením prostřednictvím centra Virtuální síť WAN. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co je pobočkové připojení k virtuální síti Azure WAN?

Připojení z větvení zařízení do Azure Virtual WAN podporuje až čtyři odkazy. Odkaz je fyzické připojení odkaz na pobočku umístění (například: ATT, Verizon atd.). Každé připojení propojení se skládá ze dvou aktivních/aktivních tunelů IPsec.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou dostupné v síti Virtual WAN u připojení VPN a u připojení VPN na ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází provoz mezi větvemi prostřednictvím virtuální sítě Azure WAN?

Ano.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje virtuální síť WAN expressroute z každého webu?

Ne, Virtual WAN nevyžaduje ExpressRoute z každé lokality. Používá standardní připojení iPsec site-to-site prostřednictvím internetových odkazů ze zařízení do centra Azure Virtual WAN. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. Pro weby, které jsou připojeny pomocí ExpressRoute ve virtuálním rozbočovači, weby mohou mít větev větve ní provoz mezi VPN a ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Platí při použití Azure Virtual WAN nějaké omezení propustnosti sítě?

Počet poboček je omezen na 1000 připojení na rozbočovač/oblast a celkem 20 Gb/s v rozbočovači. Můžete mít 1 rozbočovač na oblast.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Kolik připojení VPN podporuje centrum Virtual WAN?

Centrum Azure Virtual WAN může podporovat až 1 000 připojení S2S, 10 000 připojení P2S a 4 připojení ExpressRoute současně.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaká je celková propustnost VPN tunelového propojení VPN a připojení?

Celková propustnost centra VPN je až 20 Gb/s na základě zvolené jednotky škálování. Propustnost je sdílena všemi existujícími připojeními. Každý tunel ve spojení může podporovat až 1 Gb/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>Nevidím nastavení 20 Gb/s pro virtuální rozbočovač na portálu. Jak to nakonfiguruji?

Přejděte do brány VPN uvnitř centra na portálu a kliknutím na jednotku měřítka ji změňte na příslušné nastavení.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje virtuální síť WAN místnímu zařízení paralelní využití více poskytovatelů služeb LPS nebo je to vždy jeden tunel VPN?

Připojení přicházející do virtuální sítě WAN VPN je vždy aktivní aktivní tunelové propojení (pro odolnost proti chybám v rámci stejného rozbočovače nebo oblasti) pomocí odkazu, který je k dispozici na větvi. Tímto odkazem může být odkaz pro isp v místní pobočce. Virtuální WAN 'VPNSite' poskytuje možnost přidat informace o odkazu na webu. Pokud máte více poskytovatelů služeb Internetových služeb ve větvi a každý z poskytovatelů služeb Internetových služeb poskytl odkaz, lze tyto informace nastavit v informacích o webu VPN v Azure. Správa převzetí služeb při selhání mezi isp ve větvi je však zcela operace směrování zaměřené na pobočky.

### <a name="what-is-global-transit-architecture"></a>Co je globální architektura tranzitu?

Informace o globální architektuře přenosu naleznete v [tématu Global transit network architecture and Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Provoz se řídí vzorem: pobočkové zařízení ->ISP->síť Microsoft >síti Microsoft DC (hub VNet)->zařízení pobočky Microsoft network >pobočk >a

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k internetu a fyzické zařízení, které podporuje IPsec, nejlépe od našich integrovaných [partnerů Virtual WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). Volitelně můžete ručně spravovat konfiguraci a připojení k Azure z vašeho upřednostňovaného zařízení.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Jak povolím výchozí trasu (0.0.0.0/0) v připojení (VPN, ExpressRoute nebo virtuální síť):

Virtuální rozbočovač může šířit naučenou výchozí trasu do připojení VPN/ExpressRoute mezi lokalitami, pokud je příznak v připojení povolen. Tento příznak je viditelný, když uživatel ueviduje připojení k virtuální síti, připojení VPN nebo připojení ExpressRoute. Ve výchozím nastavení je tento příznak zakázán, pokud je lokalita nebo okruh ExpressRoute připojen k rozbočovači. Je ve výchozím nastavení povolena, když se přidá připojení k virtuální síti pro připojení virtuální sítě k virtuálnímu rozbočovači. Výchozí trasa nepochází z rozbočovače Virtuální WAN. Výchozí trasa je šířena, pokud se již zjistilo, že centrum Virtual WAN v důsledku nasazení brány firewall v rozbočovači nebo pokud je povoleno vynucené tunelové propojení jiné připojené lokality.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Jak virtuální rozbočovač ve virtuální malze vymezuje nejlepší cestu pro trasu z více rozbočovačů

Pokud se virtuální centrum naučí stejnou trasu z více vzdálených rozbočovačů, pořadí, ve kterém se rozhodne, je následující:
1) Původ trasy a) Síťové trasy – předpony virtuální sítě přímo naučné branami virtuálního rozbočovače b) Hub RouteTable (staticky nakonfigurované trasy) c) BGP d) InterHub trasy
2)  Metrika trasy : Virtuální síť WAN upřednostňuje expressroute před VPN. Peer ExpressRoute mají vyšší váhu ve srovnání s partnerem VPN
3)  Délka cesty AS

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jaké jsou rozdíly mezi virtuálními typy WAN (základní a standardní)?

Typ WAN Basic umožňuje vytvořit základní rozbočovač (Skladová položka = základní). Typ WAN standardu umožňuje vytvořit standardní rozbočovač (Skladová položka = standardní). Základní rozbočovače jsou omezeny na funkce VPN site-to-site. Standardní rozbočovače umožňují mít ExpressRoute, Uživatel VPN (P2S), úplné mřížky rozbočovače a vnet-k-virtuální sítě tranzitu přes rozbočovače. Platíte základní poplatek ve výši $ 0.25 / hod pro standardní rozbočovače a poplatek za zpracování dat pro tranzit přes rozbočovače během připojení k virtuální síti k virtuální síti, stejně jako zpracování dat pro provoz rozbočovače do rozbočovače. Další informace naleznete v [tématu Základní a standardní virtuální soubory WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Ceny najdete na stránce [Ceny.](https://azure.microsoft.com/pricing/details/virtual-wan/)
