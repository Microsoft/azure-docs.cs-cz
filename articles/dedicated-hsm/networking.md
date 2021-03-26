---
title: Požadavky na síť – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Přehled důležitých informací o sítích týkajících se vyhrazených nasazení HSM v Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5365ba8c4fbc07c487dd40cfcdc9d566990c493c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607033"
---
# <a name="azure-dedicated-hsm-networking"></a>Vyhrazené sítě HSM v Azure

Vyhrazený modul HARDWAROVÉho zabezpečení Azure vyžaduje vysoce zabezpečené síťové prostředí. To platí bez ohledu na to, jestli se z cloudu Azure zpátky do IT prostředí (místní), pomocí distribuovaných aplikací nebo scénářů s vysokou dostupností. Sítě Azure tuto službu nabízí a existují čtyři různé oblasti, které je potřeba řešit.

- Vytváření zařízení HSM v rámci vaší Virtual Network (VNet) v Azure
- Připojení místních prostředků ke cloudovým prostředkům pro konfiguraci a správu zařízení HSM
- Vytváření a propojení virtuálních sítí pro připojení prostředků aplikací a zařízení HSM
- Propojení virtuálních sítí v různých oblastech pro vzájemnou komunikaci a také pro zajištění scénářů s vysokou dostupností

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuální síť pro vyhrazené HSM

Vyhrazené HSM jsou integrované do Virtual Network a umisťují se do vlastní privátní sítě Customers v Azure. To umožňuje přístup k zařízením z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.  
Další informace o integraci služeb Azure do virtuální sítě a funkcí, které poskytuje, najdete v dokumentaci ke [službám Virtual Network pro Azure](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Virtuální sítě

Než zřídíte vyhrazené zařízení HSM, zákazníci nejdřív budou muset vytvořit Virtual Network v Azure nebo použít jiný, který už existuje v předplatném pro zákazníky. Virtuální síť definuje hraniční zabezpečení pro vyhrazené zařízení HSM. Další informace o vytváření virtuálních sítí najdete v tématu [dokumentace k virtuální síti](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsítě

Podsítě segmentují virtuální síť do samostatných adresních prostorů použitelných prostředky Azure, které do nich umístíte. Vyhrazené HSM se nasadí do podsítě ve virtuální síti. Každé vyhrazené zařízení HSM, které je nasazeno v podsíti zákazníka, obdrží privátní IP adresu z této podsítě. Podsíť, ve které je nasazené zařízení HSM, musí být explicitně delegovaná na službu: Microsoft. HardwareSecurityModules/dedicatedHSMs. Tím je uděleno určité oprávnění ke službě HSM pro nasazení do podsítě. Delegování na vyhrazené HSM ukládá určitá omezení zásad v podsíti. V delegovaných podsítích nejsou aktuálně podporovány skupiny zabezpečení sítě (skupin zabezpečení sítě) a trasy User-Defined (udr). V důsledku toho se po delegování podsítě na vyhrazené HSM dá použít jenom k nasazení prostředků HSM. Nasazení jakýchkoli dalších zákaznických prostředků do podsítě se nezdaří.


### <a name="expressroute-gateway"></a>ExpressRoute bránu

Požadavek aktuální architektury je konfigurací brány ER v podsíti zákazníků, kde je nutné umístit zařízení HSM, aby bylo možné povolit integraci zařízení HSM do Azure. Tuto bránu ER nejde využít k připojení místních umístění k zařízením HSM pro zákazníky v Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Připojení místního prostředí k Azure

Při vytváření cloudových prostředků je obvyklým požadavkem pro privátní připojení zpátky k místním IT prostředkům. V případě vyhrazeného modulu HARDWAROVÉho zabezpečení (HSM) bude mít klientský software HSM konfiguraci zařízení HSM a také aktivity, jako jsou například zálohy a protokoly o přijetí změn z HSM k analýze. Klíčový bod rozhodování je povaha připojení, protože existují možnosti.  Nejpružnější možností je síť VPN typu Site-to-site, protože se bude pravděpodobně jednat o více místních prostředků, které vyžadují zabezpečenou komunikaci s prostředky (včetně HSM) v cloudu Azure. Tato akce vyžaduje, aby organizace zákazníka měla k usnadnění připojení zařízení VPN. Připojení VPN typu Point-to-site lze použít, pokud je k dispozici pouze jeden místní koncový bod, například jedna pracovní stanice pro správu.
Další informace o možnostech připojení najdete v tématu [VPN Gateway možností plánování](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> V současné době ExpressRoute není možnost připojení k místním prostředkům. Měla by se také poznamenat, že brána ExpressRoute použitá jak je popsaná výše, není pro připojení k místní infrastruktuře.

### <a name="point-to-site-vpn"></a>SÍŤ VPN typu Point-to-site

Virtuální privátní síť typu Point-to-site je nejjednodušší forma zabezpečeného připojení k jednomu koncovému bodu v místním prostředí. To může být důležité, pokud máte v úmyslu mít pro vyhrazené HSM založené na Azure jenom jednu pracovní stanici pro správu.

### <a name="site-to-site-vpn"></a>Site-to-site VPN

Virtuální privátní síť typu Site-to-site umožňuje zabezpečenou komunikaci mezi vyhrazenými HSM založenými na Azure a místním prostředím. Důvodem je, že se jedná o zálohovací zařízení pro místní modul hardwarového zabezpečení (HSM), které vyžaduje připojení mezi dvěma pro spuštění zálohování.

## <a name="connecting-virtual-networks"></a>Připojení virtuálních sítí

Typická architektura nasazení pro vyhrazený modul HARDWAROVÉho zabezpečení spustí jednu virtuální síť a odpovídající podsíť, ve které se vytvoří a zřídí zařízení HSM. V rámci stejné oblasti může být pro součásti aplikací, které by používaly vyhrazený modul HARDWAROVÉho zabezpečení, také další virtuální sítě a podsítě. Pokud chcete povolit komunikaci mezi těmito sítěmi, používáme Virtual Network partnerský vztah.

### <a name="virtual-network-peering"></a>Peering virtuálních sítí

Pokud je v oblasti více virtuálních sítí, které potřebují přístup k prostředkům dalších zdrojů, můžete k vytváření zabezpečených komunikačních kanálů mezi nimi použít Virtual Network partnerský vztah.  Partnerský vztah virtuálních sítí poskytuje nejen zabezpečenou komunikaci, ale taky zajišťuje připojení s nízkou latencí a velkou šířkou pásma mezi prostředky v Azure.

![partnerský vztah k síti](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Připojení přes oblasti Azure

Zařízení HSM mají schopnost přes knihovny softwaru přesměrovat provoz do alternativního HSM. Přesměrování provozu je užitečné v případě, že dojde ke ztrátě zařízení nebo k výpadku zařízení. Scénáře selhání na regionální úrovni je možné zmírnit nasazením HSM do jiných oblastí a povolením komunikace mezi virtuálními sítěmi v různých oblastech.

### <a name="cross-region-ha-using-vpn-gateway"></a>HA v různých oblastech pomocí brány VPN

U globálně distribuovaných aplikací nebo pro regionální scénáře převzetí služeb při selhání s vysokou dostupností je potřeba propojit virtuální sítě napříč různými oblastmi. S vyhrazeným modulem HARDWAROVÉho zabezpečení Azure je možné dosáhnout vysoké dostupnosti pomocí VPN Gateway, která poskytuje zabezpečené tunelové propojení mezi oběma virtuálními sítěmi. Další informace o připojeních VNet-to-VNet pomocí VPN Gateway naleznete v článku s názvem [co je VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Globální partnerské vztahy virtuálních sítí nejsou v současné době k dispozici ve scénářích připojení pro různé oblasti s vyhrazeným HSM a měli byste místo toho použít službu VPN Gateway. 

![Diagram znázorňuje dvě oblasti, které jsou propojeny dvěma branami V P N. Každá oblast obsahuje partnerské virtuální sítě.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Omezení sítě
> [!NOTE]
> Omezení vyhrazené služby HSM pomocí delegování podsítě je zavedená omezení, která by se měla vzít v úvahu při návrhu cílové síťové architektury pro nasazení HSM. Použití delegování podsítě znamená skupin zabezpečení sítě, udr a globální partnerský vztah virtuálních sítí není pro vyhrazený modul HSM podporován. Následující části poskytují nápovědu k alternativním technikům pro dosažení stejného nebo podobného výsledku pro tyto funkce. 

Síťová karta HSM, která se nachází ve vyhrazené virtuální síti HSM, nemůže používat skupiny zabezpečení sítě ani trasy definované uživatelem. To znamená, že není možné nastavit zásady výchozího a odepření z hlediska vyhrazené virtuální sítě HSM a že jiné segmenty sítě musí být allowlisted, aby získaly přístup k vyhrazené službě HSM. 

Přidání řešení proxy síťové virtuální zařízení (Network Virtual Spotřebičs) taky umožňuje logicky umístit síťové virtuální zařízení bránu firewall v centru přenosu/DMZ, aby bylo možné logicky umístit před síťovou kartu HSM a tím zajistit potřebnou alternativu pro skupin zabezpečení sítě a udr.

### <a name="solution-architecture"></a>Architektura řešení
Tento návrh sítě vyžaduje následující prvky:
1.  Síť VNet průjezdu nebo DMZ centra s vrstvou proxy serveru síťové virtuální zařízení. V ideálním případě jsou k dispozici dvě nebo více síťová virtuální zařízení. 
2.  Okruh ExpressRoute s povoleným privátním partnerským vztahem a připojení k virtuální síti centra přenosů.
3.  Síť VNet peering mezi virtuální sítí centra přenosů a vyhrazenou virtuální sítí HSM.
4.  Bránu firewall nebo Azure Firewall síťové virtuální zařízení můžete nasadit jako možnost DMZ služby v centru. 
5.  Další úlohy virtuální sítě je možné navázat na síť virtuální sítě rozbočovače. Klient identita Gemalto může získat přístup k vyhrazené službě HSM prostřednictvím virtuální sítě centra.

![Diagram znázorňuje virtuální síť centra DMZ s úrovní proxy serveru síťové virtuální zařízení pro NSG a UDR alternativní řešení.](media/networking/network-architecture.png)

Vzhledem k tomu, že přidání řešení proxy serveru síťové virtuální zařízení umožňuje logicky umístit síťové virtuální ZAŘÍZENÍou bránu firewall do centra přenosu/DMZ, takže bude poskytovat potřebné zásady výchozího nastavení. V našem příkladu budeme pro tento účel používat Azure Firewall a bude potřeba, aby byly zavedeny následující prvky:
1. Azure Firewall nasazené do podsítě "AzureFirewallSubnet" ve virtuální síti centra DMZ
2. Směrovací tabulka s UDR, která směruje provoz směřující do privátního koncového bodu Azure interního nástroje do Azure Firewall. Tato směrovací tabulka se použije pro GatewaySubnet, kde se nachází virtuální brána Customer ExpressRoute.
3. Pravidla zabezpečení sítě v rámci AzureFirewall, která umožňují předávání mezi důvěryhodným zdrojovým rozsahem a soukromým koncovým bodem Azure IBL naslouchá na portu TCP 1792. Tato logika zabezpečení přidá do vyhrazené služby HSM zásadu "výchozí zamítnutí". To znamená, že do vyhrazené služby HSM budou povoleny pouze důvěryhodné rozsahy IP adres. Všechny ostatní rozsahy budou zahozeny.  
4. Směrovací tabulka s UDR, která směruje provoz do Prem do Azure Firewall. Tato směrovací tabulka se použije pro podsíť proxy serveru síťové virtuální zařízení. 
5. NSG, který se použije pro podsíť proxy síťové virtuální zařízení pro důvěřování pouze rozsahu podsítě Azure Firewall jako zdroj, a povoluje předávání jenom pro přesměrování na IP adresu síťové karty HSM přes port TCP 1792. 

> [!NOTE]
> Vzhledem k tomu, že vrstva proxy serveru síťové virtuální zařízení bude SNAT IP adresou klienta v případě, že se předává síťovému adaptéru HSM, není mezi virtuální sítí HSM a virtuální sítí centra DMZ vyžadována žádná udr.  

### <a name="alternative-to-udrs"></a>Alternativa k udr
Výše zmíněné řešení úrovně síťové virtuální zařízení funguje jako alternativa k udr. Je potřeba si uvědomit několik důležitých bodů.
1.  U síťové virtuální zařízení by měl být nakonfigurován překlad síťových adres, aby bylo možné správně směrovat přenosy vrácených dat.
2. Zákazníci by měli zakázat nastavení IP adresy klienta v konfiguraci Luna HSM, aby používali VNA pro překlad adres (NAT). Následující příkazy Servce jako příklad.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Nasaďte udr pro přenosy příchozích dat do vrstvy síťové virtuální zařízení. 
4. V případě jednotlivých návrhů nebudou podsítě HSM iniciovat požadavek na odchozí připojení do vrstvy platformy.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternativa k použití globálních VNET Peering
K dispozici je několik architektur, které můžete použít jako alternativu k globálním partnerským vztahům virtuální sítě.
1.  Použití [připojení VPN Gateway VNet-to-vnet](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 
2.  Připojte virtuální síť HSM k jiné virtuální síti pomocí okruhu ER. To funguje nejlépe, když je vyžadována přímá místní cesta nebo virtuální síť VPN. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>HSM s přímým připojením Route Express
![Diagram znázorňuje modul HARDWAROVÉho zabezpečení s přímým připojením tras.](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy](faq.md)
- [Možnosti podpory](supportability.md)
- [Vysoká dostupnost](high-availability.md)
- [Fyzické zabezpečení](physical-security.md)
- [Monitorování](monitoring.md)
- [Architektura nasazení](deployment-architecture.md)
