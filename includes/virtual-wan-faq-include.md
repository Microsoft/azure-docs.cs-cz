---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6fac71572446a80ff00368991d6cb80c572864ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859880"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Je Azure Virtual WAN v GA?

Ano, Azure Virtual WAN je všeobecně dostupná (GA). Virtuální síť WAN se ale skládá z několika funkcí a scénářů. Ve virtuální síti WAN existují funkce nebo scénáře, kde Microsoft používá značku Preview. V těchto případech je konkrétní funkce nebo samotný scénář ve verzi Preview. Pokud nepoužíváte určitou funkci verze Preview, použije se běžná podpora GA. Další informace o podpoře verze Preview najdete v tématu Další informace o [použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzích Preview.

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Musí mít uživatel k používání Azure Virtual WAN rozbočovač a paprsek se zařízeními SD-WAN/VPN?

Služba Virtual WAN poskytuje mnoho funkcí integrovaných v jednom podokně, jako je například připojení Site-to-Site VPN, User/P2S Connectivity, ExpressRoute konektivita, Virtual Network připojení, VPN ExpressRoute vzájemné propojení, přenosné připojení typu VNet-to-VNet, centralizované směrování, Azure Firewall Správce brány firewall, monitorování, šifrování ExpressRoute a mnoho dalších možností. Nemusíte mít všechny tyto případy použití, abyste mohli začít používat virtuální síť WAN. Můžete začít jenom s jedním případem použití. Virtuální architektura sítě WAN je architektura rozbočovače a paprsku s integrovaným škálováním a výkonem v umístění, kde se větví (zařízení VPN/SD-WAN), uživatelé (klienti Azure VPN, openVPN nebo IKEv2), ExpressRoute okruhy, virtuální sítě slouží jako paprsky k virtuálním rozbočovačům. Všechna centra jsou připojená v celé síti sítě WAN, která usnadňuje uživateli používání páteřní sítě Microsoftu pro připojení typu any-to-Any (jakékoli paprsky). Pro centrum a paprsky se zařízeními SD-WAN/VPN je uživatelé mohli ručně nastavit na portálu Azure Virtual WAN nebo použít virtuální síť WAN partner (SD-WAN/VPN) pro nastavení připojení k Azure. Virtuální partneři sítě WAN poskytují automatizaci pro připojení, což je schopnost exportovat informace o zařízení do Azure, stáhnout konfiguraci Azure a navázat připojení ke službě Azure Virtual WAN hub. Pro připojení VPN typu Point-to-site/User podporujeme klienta [Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN nebo IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Můžete zakázat plně protažené rozbočovače ve virtuální síti WAN?

Virtuální síť WAN nabízí dva typy: Basic a Standard. V nástroji Basic Virtual WAN se rozbočovače neukládají do sítě. Ve standardní virtuální síti WAN jsou rozbočovače při prvním nastavení sítě WAN a automaticky připojené k síti. Uživatel nemusí provádět žádnou konkrétní hodnotu. Uživatel také nemusí zakázat ani povolit funkci pro získání rozbočovačů s mřížkou. Virtual WAN poskytuje mnoho možností směrování pro řízení provozu mezi všemi paprsky (virtuálními sítěmi, VPN nebo ExpressRoute). Poskytuje snadné prostředí s kompletními oky a také flexibilitu směrování provozu podle vašich potřeb. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Jak se ve virtuální síti WAN zpracovávají Zóny dostupnosti a odolnost proti chybám?

Virtuální síť WAN je kolekce rozbočovačů a služeb zpřístupněných v rámci centra. Uživatel může mít tolik virtuálních sítí WAN na základě jejich potřeby. Ve virtuálním rozbočovači WAN existuje několik služeb, jako je VPN, ExpressRoute atd. Každá z těchto služeb (kromě Azure Firewall) je nasazena v Zóny dostupnosti oblasti, to znamená, že oblast podporuje Zóny dostupnosti. Pokud se oblast po počátečním nasazení v centru změní na zónu dostupnosti, může uživatel znovu vytvořit brány, které aktivují nasazení zóny dostupnosti. Všechny brány se zřídí v rozbočovači jako aktivní – aktivní, což znamená, že v rámci centra je vestavěná odolnost. Uživatelé se můžou připojit k několika rozbočovačům, pokud chtějí mít odolnost napříč oblastmi. I když je koncept virtuální sítě WAN globální, skutečný prostředek virtuální sítě WAN je Správce prostředků a nasazený v regionu. Pokud by tato virtuální oblast sítě WAN mohla mít problém, všechna centra v této virtuální síti WAN budou dál fungovat, jak je, ale uživatel nebude moct vytvářet nová centra, dokud nebude dostupná tato oblast virtuální sítě WAN.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Jaký klient podporuje službu Azure Virtual WAN User VPN (Point-to-site)?

Virtual WAN podporuje [klienta VPN Azure](https://go.microsoft.com/fwlink/?linkid=2117554), klienta OpenVPN nebo jakéhokoli klienta IKEv2. Ověřování Azure AD je podporované klientem Azure VPN. vyžaduje se minimálně operační systém Windows 10 Client verze 17763,0 nebo vyšší.  OpenVPN klienti můžou podporovat ověřování založené na certifikátech. Po výběru ověřování na základě certifikátu v bráně se zobrazí soubor *. ovpn* , který se stáhne do vašeho zařízení. IKEv2 podporuje ověřování certifikátem a protokolem RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Pro uživatele VPN (Point-to-site) – proč je fond klientů P2S rozdělen do dvou tras?

Každá brána má dvě instance, k rozdělení dojde, aby každá instance brány mohla nezávisle přidělit klientské IP adresy pro připojené klienty a provoz z virtuální sítě se směruje zpátky na správnou instanci brány, aby nedošlo k přesměrování instancí mezi branami.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Návody přidat servery DNS pro klienty P2S?

Existují dvě možnosti, jak přidat servery DNS pro klienty P2S. První způsob je preferovaný, protože přidá vlastní servery DNS do brány místo klienta.

1. K přidání vlastních serverů DNS použijte následující skript prostředí PowerShell. Nahraďte hodnoty pro vaše prostředí.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Nebo pokud používáte klienta Azure VPN pro Windows 10, můžete změnit stažený soubor XML profilu a přidat ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** značky před jeho importem.

   ```powershell
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

Každá Brána VPN P2S pro uživatele má dvě instance a každá instance podporuje až určitých uživatelů při změně jednotky škálování. Škálování jednotka 1-3 podporuje připojení 500, škálování jednotka 4-6 podporuje 1000 připojení, jednotka škálování 7-12 podporuje 5000 připojení a jednotky škálování 13-18 podporuje až 10 000 připojení. 

Například umožňuje, aby uživatel vybírá 1 jednotku škálování. Každá jednotka škálování by znamenala, že je nasazená brána aktivní-aktivní a každá z instancí (v tomto případě 2) by podporovala až 500 připojení. Vzhledem k tomu, že můžete získat připojení 500 × 2 na bránu, neznamená to, že pro tuto jednotku škálování naplánujete 1000 místo 500. V případě, že se překročí doporučený počet připojení, může být nutné obsluhovat instance, aby připojení k nadbytečnému 500 mohlo být přerušeno. Nezapomeňte také naplánovat prostoje v případě, že se rozhodnete škálovat nebo snížit velikost jednotky škálování nebo změnit konfiguraci Point-to-site v bráně VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Jaký je rozdíl mezi bránou virtuální sítě Azure (VPN Gateway) a bránou VPN Azure Virtual WAN?

Virtual WAN poskytuje možnosti připojení typu Site-to-Site ve velkém měřítku a je určená pro zajištění propustnosti, škálovatelnosti a snadného použití. Když připojíte lokalitu k virtuální síti WAN VPN Gateway, liší se od běžné brány virtuální sítě, která používá typ brány VPN. Podobně platí, že když připojujete okruh ExpressRoute k virtuálnímu rozbočovači WAN, používá jiný prostředek pro bránu ExpressRoute, než je obvyklá Brána virtuální sítě, která používá typ brány ExpressRoute. 

Virtuální síť WAN podporuje až 20 GB/s agregovanou propustnost jak pro VPN, tak pro ExpressRoute. Virtuální síť WAN má také automatizaci pro připojení s ekosystémem partnerů pro zařízení s připojením k síti. Zařízení s větví CPE mají vestavěnou automatizaci, která slouží k automatickému zřizování a připojení k Azure Virtual WAN. Tato zařízení jsou dostupná od stále rostoucího ekosystému partnerů pro SD-WAN a VPN. Podívejte se na [seznam upřednostňovaných partnerů](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Jak se virtuální síť WAN liší od brány virtuální sítě Azure?

SÍŤ VPN brány virtuální sítě je omezená na 30 tunelů. Pro připojení byste měli pro rozsáhlé sítě VPN používat Virtual WAN. Můžete připojit až 1 000 připojení větví na oblast (virtuální rozbočovač) s agregací 20 GB/s na jeden hub. Připojení je tunel typu aktivní-aktivní z místního zařízení VPN do virtuálního rozbočovače. Můžete mít jeden rozbočovač na oblast, což znamená, že můžete připojit více než 1 000 větví napříč rozbočovači.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Co je jednotka škálování služby Virtual WAN

Jednotka škálování je jednotka definovaná k výběru agregované propustnosti brány ve virtuálním rozbočovači. 1 jednotka škálování sítě VPN = 500 MB/s. 1 jednotka škálování ExpressRoute = 2 GB/s. Příklad: 10 jednotek škálování sítě VPN by znamenalo 500 MB/s × 10 GB/s.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Které poskytovatele zařízení (virtuální partneři sítě WAN) jsou podporované?

Plně automatizované prostředí Virtual WAN v současnosti podporuje celá řada partnerů. Další informace najdete v tématu [Partneři Virtual WAN](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Jaké jsou kroky automatizace Virtual WAN u partnerů?

Informace o krocích automatizace u partnerů najdete v tématu o [automatizaci Virtual WAN u partnerů](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Musím použít preferované partnerské zařízení?

Ne. Můžete použít libovolné zařízení s podporou VPN, které vyhovuje požadavkům Azure na podporu IKEv2/IKEv1 protokolu IPsec. Virtuální síť WAN má také CPE Partnerská řešení, která automatizují připojení k virtuální síti WAN Azure, což usnadňuje nastavení připojení VPN s protokolem IPsec ve velkém měřítku.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Jak partneři pro Virtual WAN automatizují možnosti připojení s využitím Azure Virtual WAN?

Softwarově definovaná řešení možností připojení obvykle spravují svá pobočková zařízení s využitím kontroleru nebo centra pro zřizování zařízení. Kontroler může k automatizaci připojení k Azure Virtual WAN využívat rozhraní API Azure. Automatizace zahrnuje odesílání informací o větvích, stahování konfigurace Azure, nastavování tunelů IPSec na virtuální rozbočovače Azure a automatické nastavení připojení k vytvoření připojení k virtuální síti WAN na Azure. Když máte stovky větví, připojení pomocí virtuálních sítí WAN CPE je snadné, protože možnosti registrace neberou v platnost nutnost nastavovat, konfigurovat a spravovat rozsáhlé připojení IPsec. Další informace najdete v tématu věnovaném [automatizaci partnerů pro Virtual WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Co když zařízení, které používám, není v seznamu partnerů virtuální sítě WAN? Můžu ho I nadále používat pro připojení k síti VPN Azure Virtual WAN?

Ano, pokud zařízení podporuje protokol IPsec IKEv1 nebo IKEv2. Virtuální partneři sítě WAN automatizují připojení ze zařízení k koncovým bodům VPN Azure. To zahrnuje automatizaci kroků, jako je například nahrání informací o větvích, protokol IPsec a konfigurace a připojení. Vzhledem k tomu, že vaše zařízení nepochází z virtuálního partnerského serveru pro partnery WAN, budete muset provést těžkou zvedání ručního nastavování konfigurace Azure a aktualizace vašeho zařízení, aby se nastavilo připojení pomocí protokolu IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Jak se onboardují noví partneři, kteří nejsou uvedení ve vašem seznamu partnerů pro spouštění?

Všechna rozhraní API Virtual WAN jsou otevřená rozhraní API. Pokud chcete posoudit technickou proveditelnost, přečtěte si dokumentaci k [virtuálním partnerům sítě WAN](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) . Ideální partner je takový, pro jehož zařízení se dá zřídit připojení IKEv1 nebo IKEv2 protokolu IPSec. Jakmile společnost dokončí službu automatizace pro svoje zařízení CPE na základě výše uvedených pokynů pro automatizaci, můžete se na azurevirtualwan@microsoft.com Toto místo připojit [přes partnery]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Pokud jste zákazníkem, který by měl být uveden jako virtuální partner sítě WAN, obraťte se na virtuální síť WAN odesláním e-mailu společnosti azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Jak virtuální síť WAN podporuje zařízení SD-WAN?

Virtuální partneři sítě WAN automatizují připojení IPsec k koncovým bodům VPN Azure. Pokud je adaptér virtuální sítě WAN poskytovatelem SD-WAN, předpokládá se, že řadič SD-WAN spravuje automatizaci a připojení IPsec k koncovým bodům VPN Azure. Pokud zařízení SD-WAN vyžaduje svůj vlastní koncový bod místo Azure VPN pro jakékoli proprietární funkce SD-WAN, můžete koncový bod SD-WAN ve virtuální síti Azure nasadit a koexistovat s Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Kolik zařízení VPN se může připojit k jednomu rozbočovači?

Pro jedno virtuální centrum se podporuje až 1 000 připojení. Každé připojení se skládá ze čtyř odkazů a každé připojení propojení podporuje dvě tunely, které jsou v konfiguraci aktivní-aktivní. Tunely se ukončí v bráně VPN virtuálního rozbočovače Azure. Odkazy reprezentují fyzický odkaz poskytovatele internetových služeb na zařízení pobočky nebo sítě VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Co je připojení ke větvi Azure Virtual WAN?

Připojení z větve nebo zařízení VPN do Azure Virtual WAN není žádné, ale připojení VPN, které se prakticky připojuje k síti VPN a Azure VPN Gateway ve virtuálním rozbočovači.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Může se místní zařízení VPN připojit k více rozbočovačům?

Ano. Tok přenosů, když se začíná, pochází z místního zařízení do nejbližší hraniční sítě Microsoftu a pak do virtuálního centra.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké nové prostředky Resource Manageru?
  
Ano, virtuální síť WAN má nové prostředky Správce prostředků. Další informace najdete v [přehledu](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Můžu při použití Azure Virtual WAN nasadit a použít moje oblíbené síťové virtuální zařízení (ve virtuální síti NVA)?

Ano, virtuální síť svého oblíbeného virtuálního síťového zařízení (NVA) můžete připojit k síti Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Můžu ve virtuálním rozbočovači vytvořit síťové virtuální zařízení?

Síťové virtuální zařízení (síťové virtuální zařízení) se nedá nasadit v rámci virtuálního rozbočovače. Můžete ho ale vytvořit ve virtuální síti paprsku, která je připojená k virtuálnímu rozbočovači, a povolit příslušné směrování pro směrování provozu podle vašich potřeb.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Je možné, že virtuální síť rozbočovače má bránu virtuální sítě?

Ne. Virtuální síť paprsků nemůže mít bránu virtuální sítě, pokud je připojená k virtuálnímu rozbočovači.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Je podpora protokolu BGP v připojení k síti VPN podporovaná?

Ano, BGP se podporuje. Při vytváření sítě VPN můžete zadat parametry protokolu BGP. To znamená, že pro protokol BGP bude povolená jakákoli připojení vytvořená v Azure pro tuto lokalitu.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Jsou pro Virtual WAN k dispozici nějaké informace o cenách nebo licencích?

Ano. Prohlédněte si stránku s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Je možné vytvořit Azure Virtual WAN s využitím šablony Resource Manageru?

Jednoduchá konfigurace jedné virtuální sítě WAN s jedním rozbočovačem a jedním vpnsite se dá vytvořit pomocí [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtuální síť WAN je primárně řízená jako služba založená na REST nebo na portálu.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Můžou virtuální sítě připojení k virtuálnímu rozbočovači komunikovat mezi sebou (V2V tranzit)?

Ano. Standard Virtual WAN podporuje přenosné připojení typu VNet-to-VNet přes virtuální síť WAN, ke které jsou virtuální sítě připojené. V terminologii virtuální sítě WAN se tyto cesty označují jako "místní virtuální síť WAN" pro virtuální sítě připojené k virtuálnímu rozbočovači WAN v rámci jedné oblasti a "globální virtuální síť WAN" pro virtuální sítě připojené prostřednictvím několika virtuálních rozbočovačů WAN ve dvou nebo více oblastech. V některých scénářích může být paprskový virtuální sítě také přímo navzájem partnerským vztahem s použitím [Virtual Network partnerských vztahů](../articles/virtual-network/virtual-network-peering-overview.md) kromě místního nebo globálního přenosu virtuální sítě WAN. V takovém případě má partnerský vztah virtuálních sítí přednost před přenosným připojením přes virtuální síť WAN.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Jsou ve Virtual WAN povolené možnosti připojení mezi jednotlivými pobočkami?

Ano, možnosti připojení mezi jednotlivými pobočkami jsou ve Virtual WAN k dispozici. Větev se koncepčně vztahuje na síť VPN, okruhy ExpressRoute nebo uživatele VPN typu Point-to-site nebo uživatel VPN. Možnost Povolit větev do větve je ve výchozím nastavení povolená a může se nacházet v nastavení konfigurace sítě WAN. To umožňuje větvím a uživatelům sítě VPN připojit se k ostatním větvím VPN a taky může být přenosové připojení povolené mezi uživateli VPN a ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Prochází provoz mezi větví přes Azure Virtual WAN?

Ano.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Vyžaduje virtuální síť WAN ExpressRoute z každé lokality?

Ne. Virtuální síť WAN nevyžaduje ExpressRoute z každé lokality. Vaše lokality můžou být připojené k síti poskytovatele prostřednictvím okruhu ExpressRoute. Pro lokality, které jsou připojené pomocí ExpressRoute k virtuálnímu rozbočovači a také IPsec VPN, do stejného rozbočovače, virtuální rozbočovač zajišťuje přenosové připojení mezi uživatelem VPN a ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Je při používání Azure Virtual WAN k dispozici propustnost sítě nebo omezení připojení?

Propustnost sítě je vázaná na službu ve virtuálním rozbočovači WAN. I když můžete mít tolik virtuálních sítí WAN, kolik jich budete chtít, každá virtuální síť WAN umožňuje 1 rozbočovač na oblast. V každém z nich je agregovaná propustnost sítě VPN až 20 GB/s, ale agregovaná propustnost ExpressRoute je až 20 GB/s a agregovaná propustnost uživatele VPN typu Point-to-site je až 20 GB/s. Směrovač ve virtuálním rozbočovači podporuje až 50 GB/s pro přenosové toky VNet-to-VNet a předpokládá celkový počet úloh 2000 virtuálních počítačů v rámci všech virtuální sítě připojených k jednomu virtuálnímu rozbočovači.

Když se sítě VPN připojí k rozbočovači, provedou připojení. Virtuální síť WAN podporuje až 1000 připojení nebo 2000 tunelů IPsec na virtuální rozbočovač. Když se vzdálení uživatelé připojují k virtuálnímu rozbočovači, připojí se k bráně VPN P2S, která podporuje až 10 000 uživatelů v závislosti na jednotce škálování (šířka pásma) zvolené pro bránu VPN P2S ve virtuálním rozbočovači.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Jaká je celková propustnost sítě VPN tunelu VPN a připojení?

Celková propustnost sítě VPN rozbočovače je až 20 GB/s na základě zvolené jednotky škálování služby VPN Gateway. Propustnost sdílí všechna existující připojení. Každé tunelové propojení v rámci připojení může podporovat až 1 GB/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Nevidím nastavení 20 GB/s pro virtuální rozbočovač na portálu. Návody nakonfigurovat?

Přejděte k bráně VPN uvnitř centra na portálu a kliknutím na jednotku škálování ji změňte na příslušné nastavení.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Umožňuje virtuální síti WAN, aby místní zařízení využilo více poskytovatelů internetových služeb paralelně, nebo je vždy jedním tunelem sítě VPN?

Řešení místních zařízení můžou použít zásady provozu k řízení provozu napříč několika tunely do služby Azure Virtual WAN hub (Brána sítě VPN ve virtuálním rozbočovači).

### <a name="what-is-global-transit-architecture"></a>Co je architektura globálního přenosu?

Informace o globální architektuře přenosů najdete v tématu [globální přenosová architektura sítě a virtuální síť WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Jak se směruje provoz v páteřní síti Azure?

Provoz se řídí vzorem: síťová zařízení – >poskytovatel internetových služeb >hraniční síť Microsoftu – >Microsoft DC (síť VNet) – >Microsoft Network Edge->ISP->pobočky

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Co je při použití tohoto modelu potřeba na jednotlivých lokalitách? Jenom připojení k internetu?

Ano. Připojení k Internetu a fyzické zařízení, které podporuje protokol IPsec, nejlépe z našich integrovaných [virtuálních partnerů WAN](../articles/virtual-wan/virtual-wan-locations-partners.md). Volitelně můžete ručně spravovat konfiguraci a připojení k Azure z preferovaného zařízení.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Návody povolit výchozí trasu (0.0.0.0/0) v připojení (VPN, ExpressRoute nebo Virtual Network)?

Virtuální rozbočovač může rozšířit získanou výchozí trasu na připojení VPN/ExpressRoute typu Site-to-site k síti VPN/, pokud je příznak povoleno u připojení. Tento příznak se zobrazí, když uživatel upraví připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Ve výchozím nastavení je tento příznak zakázán, pokud je lokalita nebo okruh ExpressRoute připojen k rozbočovači. Ve výchozím nastavení je povolená, když se přidá připojení k virtuální síti, které virtuální síť připojí k virtuálnímu rozbočovači. Výchozí trasa nepochází do virtuálního centra WAN. Výchozí trasa je šířena v případě, že ji již služba Virtual WAN hub vyvolala v důsledku nasazení brány firewall v centru nebo v případě, že je povoleno vynucené tunelování na jiném připojeném serveru.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Jak virtuální rozbočovač ve virtuální síti WAN vybere nejlepší cestu pro trasu z více Center

Pokud virtuální rozbočovač zjistí stejnou trasu z několika vzdálených rozbočovačů, pořadí, ve kterém se rozhodne, je následující:

1. Nejdelší shoda předpony.
2. Místní trasy přes interhub.
3. Statické trasy přes protokol BGP: Jedná se o kontext k rozhodnutí, které provádí směrovač virtuálního rozbočovače. Pokud je ale rozhodovací tvůrce Brána VPN, kde lokalita inzeruje trasy přes protokol BGP nebo poskytuje předpony statických adres, můžou se statické trasy upřednostnit přes trasy protokolu BGP.
4. ExpressRoute (ER) prostřednictvím sítě VPN: ER se upřednostňuje přes VPN, pokud je kontext místním rozbočovačem. Přenosová konektivita mezi okruhy ExpressRoute je k dispozici pouze prostřednictvím Global Reach. Proto ve scénářích, kdy je okruh ExpressRoute připojen k jednomu rozbočovači a je k jinému rozbočovači připojen jiný okruh ExpressRoute s připojením VPN, může být síť VPN upřednostňována pro scénáře mezi rozbočovači.
5. JAKO délka cesty.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Umožňuje virtuálnímu rozbočovači WAN připojení mezi okruhy ExpressRoute.

Přenos mezi ER-to-ER je vždy prostřednictvím globálního dosahu. Brány virtuálních rozbočovačů se nasazují v oblastech DC nebo Azure. Pokud se dva okruhy ExpressRoute připojují prostřednictvím globálního dosahu, nepotřebujete, aby provoz dostal všechny možnosti od hraničních směrovačů do řadiče domény virtuálního rozbočovače.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Existuje koncept váhy v okruhech Azure Virtual WAN ExpressRoute nebo připojení k síti VPN.

Pokud je k virtuálnímu rozbočovači připojeno více okruhů ExpressRoute, váha směrování v připojení poskytuje mechanismus pro ExpressRoute ve virtuálním rozbočovači, který upřednostňuje jeden okruh přes druhý. Neexistuje žádný mechanismus pro nastavení váhy pro připojení VPN. Azure vždycky upřednostňuje připojení ExpressRoute přes připojení VPN v rámci jednoho rozbočovače.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Preferuje virtuální síť WAN ExpressRoute přes VPN pro výstup provozu Azure

Ano.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Pokud má virtuální síť WAN okruh ExpressRoute a připojenou lokalitu VPN, co by mohlo způsobit, že bude trasa připojení VPN preferována nad ExpressRoute?

Když je okruh ExpressRoute připojený k virtuálnímu rozbočovači, jsou směrovače Microsoft Edge prvním uzlem pro komunikaci mezi místním prostředím a Azure. Tyto hraniční směrovače komunikují s branami Virtual WAN ExpressRoute, které pak zjišťují trasy od směrovače virtuálního rozbočovače, které řídí všechny trasy mezi všemi branami ve virtuální síti WAN. Směrovače Microsoft Edge zpracovávají ExpressRoute trasy virtuální rozbočovače s vyšší prioritou než trasy zjištěné z místního prostředí. Z nějakého důvodu se v případě, že se připojení VPN stane primárním médiem, aby se zjistily trasy od (například scénáře převzetí služeb při selhání mezi ExpressRoute a VPN), pokud lokalita VPN nemá delší dobu jako délku cesty, bude virtuální rozbočovač dál sdílet sítě VPN s bránou ExpressRoute, což způsobí, že směrovače Microsoft Edge upřednostňují trasy VPN v místních trasách.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Když jsou připojená dvě centra (centrum 1 a 2) a okruh ExpressRoute je připojený k oběma centrům jako motýlek, jaká je cesta k virtuální síti připojené k centru 1, aby se mohla připojit k virtuální síti připojené v centru 2?

Aktuální chování je preferovat cestu k okruhu ExpressRoute přes rozbočovač a rozbočovač pro připojení VNet-to-VNet. Nedoporučuje se to ale v instalaci virtuální sítě WAN. Virtuální tým sítě WAN pracuje na opravě, aby umožnil předvolbu centra pro prostředení v cestě ExpressRoute. Doporučení je pro více okruhů ExpressRoute (různých zprostředkovatelů) pro připojení k jednomu rozbočovači a připojení k rozbočovači, které poskytuje služba Virtual WAN, pro přenos toků mezi oblastmi.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Můžou se centra vytvořit v jiné skupině prostředků ve virtuální síti WAN?
Ano. Tato možnost je aktuálně dostupná jenom přes PowerShell. Virtual WAN Portal přihlásí centra ve stejné skupině prostředků jako samotný prostředek virtuální sítě WAN.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Je podpora protokolu IPv6 ve virtuální síti WAN podporovaná?

Protokol IPv6 není podporován ve virtuálním centru sítě WAN a jeho branách. Pokud máte virtuální síť s podporou protokolů IPv4 a IPv6 a chcete připojit virtuální síť k virtuální síti WAN, tento scénář se momentálně nepodporuje. 

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Jaká je doporučená verze rozhraní API, kterou budou používat skripty, které automatizují různé funkce virtuálních sítí WAN?

Vyžaduje se minimálně verze 05-01-2020 (květen 1 2020). 

### <a name="are-there-any-virtual-wan-limits"></a>Existují nějaká omezení virtuální sítě WAN?

Viz část [omezení virtuální sítě WAN](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) na stránce omezení pro předplatné a službu.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Jaké jsou rozdíly mezi typy virtuálních sítí WAN (Basic a Standard)?

Viz [základní a standardní virtuální sítě WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Ceny najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/virtual-wan/) .
