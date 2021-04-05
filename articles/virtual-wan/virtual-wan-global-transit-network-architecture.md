---
title: 'Architektura: globální přenosová architektura sítě'
titleSuffix: Azure Virtual WAN
description: Přečtěte si, jak Azure Virtual WAN umožňuje globální přenosovou architekturu, protože umožňuje všudypřítomný připojení mezi cloudové úlohy.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: cfb75b6383d8ca449b4bc54b9d21cb16b3a4ad40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428179"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globální přenosová architektura sítě a virtuální síť WAN

Moderní podniky vyžadují připojení všudypřítomný mezi aplikacemi, daty a uživateli distribuovanými na Hyper-v cloudu a místním prostředím. Podniková architektura pro globální tranzitní sítě se přijímá v rámci konsolidace, připojení a řízení moderního a celosvětového podnikového provozu zaměřeného na Cloud.

Architektura globální přenosové sítě je založená na klasickém modelu připojení hub a hvězdicové sítě, ve kterém cloudová hostovaná síť "centrum" umožňuje přenosné připojení mezi koncovými body, které mohou být distribuovány napříč různými typy "paprsků".

V tomto modelu může být paprsek:
* Virtuální síť (virtuální sítě)
* Lokalita fyzické pobočky
* Vzdálený uživatel
* Internet

![střed a paprskový](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Obrázek 1: globální přenosová centra a síť paprsků**

Obrázek 1 znázorňuje logické zobrazení globální tranzitní sítě, kde geograficky distribuované uživatele, fyzické lokality a virtuální sítě jsou propojeny prostřednictvím síťového centra hostovaného v cloudu. Tato architektura umožňuje v rámci logického připojení mezi koncovými body sítě logický přenos s jedním směrováním.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globální tranzitní síť s virtuální sítí WAN

Azure Virtual WAN je cloudová síťová služba spravovaná Microsoftem. Všechny síťové součásti, ze kterých se tato služba skládá, je hostována a spravována společností Microsoft. Další informace o virtuální síti WAN najdete v článku [Přehled virtuálních sítí WAN](virtual-wan-about.md) .

Azure Virtual WAN umožňuje globální přenosovou architekturu sítě díky tomu, že umožňuje všudypřítomný připojení mezi globálně distribuovanými sadami cloudových úloh v virtuální sítě, pobočkových lokalitách, SaaS a PaaS aplikacemi a uživateli.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Obrázek 2: globální tranzitní síť a virtuální síť WAN**

V architektuře Azure Virtual WAN se virtuální rozbočovače sítě WAN zřídí v oblastech Azure, ke kterým se můžete rozhodnout připojit své větve, virtuální sítě a vzdálené uživatele. Fyzické větve jsou připojené k centru podle úrovně Premium nebo Standard ExpressRoute nebo site-to-VPN, virtuální sítě jsou připojené k rozbočovači podle připojení virtuální sítě a vzdálení uživatelé se můžou přímo připojit k centru pomocí uživatelské sítě VPN (VPN typu Point-to-site). Virtuální síť WAN taky podporuje připojení virtuální sítě mezi oblastmi, kde virtuální síť v jedné oblasti může být připojená k virtuálnímu rozbočovači WAN v jiné oblasti.

Virtuální síť WAN můžete vytvořit tak, že vytvoříte jedno virtuální centrum sítě WAN v oblasti s největším počtem paprsků (větví, virtuální sítě, uživatelů) a potom propojíte paprsky, které jsou v jiných oblastech, do centra. Tato možnost je vhodná v případě, že podniková technologie je převážně v jedné oblasti s několika vzdálenými paprsky.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Připojení k rozbočovači

Podnikové cloudové nároky můžou zahrnovat víc cloudových oblastí a je optimální (latence) pro přístup ke cloudu z oblasti nejbližší jejich fyzické lokalitě a uživatelům. Jedním z klíčových zásad globální architektury přenosové sítě je umožnění připojení mezi oblastmi mezi všemi koncovými body cloudu a místními sítěmi. To znamená, že provoz z větve, která je připojená ke cloudu v jedné oblasti, může získat přístup k jiné větvi nebo virtuální síti v jiné oblasti pomocí připojení typu hub, které povoluje [globální síť Azure](https://azure.microsoft.com/global-infrastructure/global-network/).

![mezi oblastmi](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Obrázek 3: připojení mezi oblastmi virtuálních sítí WAN**

Pokud je v jedné virtuální síti WAN povolené víc rozbočovačů, rozbočovače se automaticky propojí prostřednictvím odkazů z centra na hub. tím se tak umožní globální připojení mezi větvemi a virtuální sítě distribuovanými napříč několika oblastmi. 

Kromě toho je možné přidružit centra, která jsou součástí stejné virtuální sítě WAN, k různým místním přístupům a zásadám zabezpečení. Další informace najdete v části [zabezpečení a řízení zásad](#security) dále v tomto článku.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Jakékoli připojení

Globální přenosová architektura sítě umožňuje jakékoli připojení prostřednictvím virtuálních rozbočovačů WAN. Tato architektura eliminuje nebo omezuje nutnost připojení celé sítě nebo částečné sítě mezi paprsky, které jsou složitější pro sestavování a údržbu. Kromě toho je snazší konfigurovat a udržovat řízení směrování v sítích s rozbočovačem a s mřížkou.

Připojení typu any-to-Any (v kontextu globální architektury) umožňuje podnik s globálně distribuovanými uživateli, pobočkami, datacentry, virtuální sítě a aplikacemi, aby se vzájemně připojovali přes "transitní" centra. Azure Virtual WAN funguje jako globální tranzitní systém.

![libovolný](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Obrázek 4: cesty provozu virtuální sítě WAN**

Azure Virtual WAN podporuje následující cesty pro připojení globálního přenosu. Písmena v závorkách se mapují na obrázek 4.

* Větev do virtuální sítě (a)
* Větev do větve (b)
  * ExpressRoute Global Reach a virtuální síť WAN
* Vzdálený uživatel – virtuální síť (c)
* Vzdálený uživatel-do větve (d)
* VNet-to-VNet (e)
* Větev do větve – hub (f)
* Větvení do sítě VNet – hub (g)
* VNet-to-hub-hub (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Mezi větvemi (a) a mezi různými oblastmi (g) mezi virtuálními sítěmi

Větev-to-VNet je primární cesta podporovaná službou Azure Virtual WAN. Tato cesta umožňuje připojit větve k podnikovým úlohám Azure IAAS nasazeným v Azure virtuální sítě. Větve se dají připojit k virtuální síti WAN přes ExpressRoute nebo VPN typu Site-to-site. Přenos dat do virtuální sítě, která jsou připojená k virtuálním rozbočovačům sítě WAN prostřednictvím připojení virtuální sítě. Pro virtuální síť WAN není vyžadován explicitní [přenos brány](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) , protože virtuální síť WAN automaticky povoluje přenos brány do sítě. Informace o tom, jak připojit SD-WAN CPE k virtuální síti WAN, najdete v článku věnovaném [virtuálním partnerům WAN](virtual-wan-configure-automation-providers.md) .

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach a virtuální síť WAN

ExpressRoute je soukromý a odolný způsob, jak připojit vaše místní sítě k Microsoft Cloud. Virtuální síť WAN podporuje připojení okruhu Express Route. Připojení lokality pobočky k virtuální síti WAN pomocí Express Route vyžaduje 1) okruh úrovně Premium nebo Standard okruh 2), aby byl v umístění s povoleným Global Reach.

ExpressRoute Global Reach je doplňková funkce pro ExpressRoute. Pomocí Global Reach můžete propojit okruhy ExpressRoute dohromady a vytvořit tak soukromou síť mezi místními sítěmi. Větve, které jsou připojené k Azure Virtual WAN pomocí ExpressRoute, vyžadují vzájemnou komunikaci ExpressRoute Global Reach.

V tomto modelu se může každá větev, která je připojená k virtuálnímu rozbočovači WAN pomocí ExpressRoute, připojit k virtuální sítě pomocí cesty k virtuální síti. Provoz z větve do větve nebude přenášet do služby hub, protože ExpressRoute Global Reach umožňuje lepší cestu přes Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Mezioblast mezi větvemi (b) a mezi větvemi (f)

Větve se dají připojit ke službě Azure Virtual WAN pomocí okruhů ExpressRoute a připojení VPN typu Site-to-site. Větve můžete připojit k virtuálnímu rozbočovači WAN, který je v oblasti nejbližší k této větvi.

Tato možnost umožňuje podnikům využít páteřní síť Azure pro připojení větví. I když je tato možnost k dispozici, měli byste zvážit výhody propojení větví přes Azure Virtual WAN vs. pomocí privátní sítě WAN.  

> [!NOTE]
> Zakázání připojení mezi větvemi ve virtuální síti WAN – virtuální síť WAN se dá nakonfigurovat tak, aby se zakázalo připojení mezi větvemi. Tento konfiguračním zablokuje šíření tras mezi sítěmi VPN (S2S a P2S) a připojenými weby Express Route. Tato konfigurace nebude mít vliv na směrování mezi virtuálními sítěmi a propogation a připojení VNet-to-VNet. Pokud chcete nakonfigurovat toto nastavení pomocí webu Azure Portal: v nabídce konfigurace virtuální sítě WAN vyberte nastavení: větev-do-větev – zakázáno. 

### <a name="remote-user-to-vnet-c"></a>Vzdálený uživatel – virtuální síť (c)

Můžete povolit přímý zabezpečený vzdálený přístup k Azure pomocí připojení typu Point-to-site od vzdáleného klienta uživatele k virtuální síti WAN. Podnikoví vzdálení uživatelé už nemusí hairpin do cloudu pomocí podnikové sítě VPN.

### <a name="remote-user-to-branch-d"></a>Vzdálený uživatel-do větve (d)

Cesta vzdáleného uživatele k větvi umožňuje vzdáleným uživatelům, kteří používají připojení Point-to-site k Azure, přistupovat k místním úlohám a aplikacím prostřednictvím přenosu v cloudu. Tato cesta dává vzdáleným uživatelům flexibilitu při přístupu k úlohám, které jsou nasazené v Azure i v místním prostředí. Podniky můžou povolit centrální cloudovou službu vzdáleného přístupu založenou na Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Tranzitní síť VNet-to-VNet (e) a více oblastí VNet-to-VNet (h)

Průjezd VNet-to-VNet umožňuje, aby se virtuální sítě vzájemně připojovaly, aby bylo možné propojit vícevrstvé aplikace, které jsou implementované v rámci více virtuální sítě. Volitelně můžete vzájemně propojit virtuální sítě prostřednictvím partnerského vztahu virtuálních sítí a to může být vhodné pro některé scénáře, kdy není nutné přenos prostřednictvím centra VWAN.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Vynucené tunelování a výchozí trasa ve službě Azure Virtual WAN

Vynucené tunelování se dá povolit tak, že v virtuální síti WAN nakonfigurujeme možnost Povolit výchozí trasu pro připojení VPN, ExpressRoute nebo Virtual Network.

Virtuální rozbočovač šíří naučenou výchozí trasu k síti VPN/ExpressRoute připojení typu Site-to-site, pokud je možnost Povolit výchozí příznak povoleno u připojení. 

Tento příznak se zobrazí, když uživatel upraví připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Ve výchozím nastavení je tento příznak zakázán, pokud je lokalita nebo okruh ExpressRoute připojen k rozbočovači. Ve výchozím nastavení je povolená, když se přidá připojení k virtuální síti, které virtuální síť připojí k virtuálnímu rozbočovači. Výchozí trasa nepochází do virtuálního centra WAN. Výchozí trasa je šířena v případě, že ji již služba Virtual WAN hub vyvolala v důsledku nasazení brány firewall v centru nebo v případě, že je povoleno vynucené tunelování na jiném připojeném serveru.

## <a name="security-and-policy-control"></a><a name="security"></a>Zabezpečení a řízení zásad

Rozbočovače Azure Virtual WAN propojování všech koncových bodů sítě v rámci hybridní sítě a potenciálně uvidí veškerý tranzitní síťový provoz. Virtuální rozbočovače WAN je možné převést na zabezpečená virtuální centra tím, že nasadíte Azure Firewall do Center VWAN a povolíte cloudové zabezpečení, přístup a řízení zásad. Orchestrace bran Azure firewall ve virtuálních sítích WAN může provádět Azure Firewall Manager.

[Azure firewall Manager](../firewall-manager/index.yml) poskytuje možnosti pro správu a škálování zabezpečení pro globální přenosové sítě. Azure Firewall Manager nabízí možnost centrálně spravovat směrování, správu globálních zásad a rozšířené služby internetového zabezpečení prostřednictvím třetích stran spolu s Azure Firewall.

![zabezpečené virtuální centrum s Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Obrázek 5: zabezpečené virtuální centrum s Azure Firewall**

Azure Firewall k virtuální síti WAN podporuje následující cesty globálního připojení pro zabezpečené průjezdy. Písmena v závorkách se mapují na obrázek 5.

* Zabezpečený přenos VNet-to-VNet (e)
* Síť VNet-to-Internet nebo služba zabezpečení třetích stran (i)
* Služba zabezpečení z více stran na Internet nebo pro službu zabezpečení třetí strany (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Zabezpečený přenos VNet-to-VNet (e)

Zabezpečený průjezd typu VNet-to-VNet umožňuje, aby se virtuální sítě vzájemně připojovaly prostřednictvím Azure Firewall ve virtuálním centru WAN.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Síť VNet-to-Internet nebo služba zabezpečení třetích stran (i)

Připojení typu VNet-to-Internet umožňuje virtuální sítě připojit se k Internetu prostřednictvím Azure Firewall ve virtuálním centru WAN. Provoz do Internetu prostřednictvím podporovaných služeb zabezpečení třetích stran neprovádí tok prostřednictvím Azure Firewall. Pomocí správce Azure Firewall můžete nakonfigurovat cestu VNet-to-Internet prostřednictvím podporované služby zabezpečení třetí strany.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Služba zabezpečení z více stran na Internet nebo pro službu zabezpečení třetí strany (j)
Síť s připojením k Internetu umožňuje větví připojit se k Internetu prostřednictvím Azure Firewall ve virtuálním centru WAN. Provoz do Internetu prostřednictvím podporovaných služeb zabezpečení třetích stran neprovádí tok prostřednictvím Azure Firewall. Pomocí správce Azure Firewall můžete nakonfigurovat cestu k síti Internet prostřednictvím podporované služby zabezpečení třetí strany. 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>Zabezpečený tranzitní přechod mezi jednotlivými větvemi (f)

Větve je možné připojit k zabezpečenému virtuálnímu rozbočovači pomocí Azure Firewall s využitím okruhů ExpressRoute a připojení VPN typu Site-to-site. Větve můžete připojit k virtuálnímu rozbočovači WAN, který je v oblasti nejbližší k této větvi.

Tato možnost umožňuje podnikům využít páteřní síť Azure pro připojení větví. I když je tato možnost k dispozici, měli byste zvážit výhody propojení větví přes Azure Virtual WAN vs. pomocí privátní sítě WAN.  

> [!NOTE]
> V současné době není podporováno souběžné zpracování provozu přes bránu firewall. Provoz mezi rozbočovači bude směrován do správné větve v rámci zabezpečeného virtuálního rozbočovače, ale přenosy budou obejít Azure Firewall v každém centru.

### <a name="branch-to-vnet-secured-transit-g"></a>Zabezpečený tranzitní větev mezi virtuálními sítěmi (g)

Zabezpečený přenos větví na virtuální síť umožňuje větvím komunikovat s virtuálními sítěmi ve stejné oblasti jako virtuální síť WAN a také v jiné virtuální síti připojené k jinému virtuálnímu rozbočovači WAN v jiné oblasti.

> [!NOTE]
> Mezi centrem s bránou firewall se aktuálně nepodporují. Provoz mezi rozbočovači se přesune přímo z Azure Firewall v každém centru.  Přenos prostřednictvím připojení určeného pro virtuální síť ve stejné oblasti bude zpracován Azure Firewall v zabezpečeném centru.


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>Návody povolit výchozí trasu (0.0.0.0/0) v zabezpečeném virtuálním centru

Azure Firewall nasazené ve virtuálním centru sítě WAN (zabezpečený virtuální rozbočovač) se dá nakonfigurovat jako výchozí směrovač pro Internet nebo důvěryhodného poskytovatele zabezpečení pro všechny větve (připojené přes VPN nebo Express Route), paprskový virtuální sítě a uživatele (připojené přes P2S VPN). Tato konfigurace se musí provádět pomocí správce Azure Firewall.  Pokud chcete nakonfigurovat veškerý provoz z větví (včetně uživatelů) a také virtuální sítě do Internetu prostřednictvím Azure Firewall, přečtěte si téma směrování provozu do vašeho rozbočovače. 

Jedná se o konfiguraci dvou kroků:

1. Nakonfigurujte směrování internetového provozu pomocí zabezpečené nabídky nastavení tras virtuálního rozbočovače. Nakonfigurujte virtuální sítě a větve, které mohou odesílat přenosy na Internet přes bránu firewall.

2. Nakonfigurujte, která připojení (virtuální síť a větev) můžou směrovat provoz na Internet (0.0.0.0/0) prostřednictvím Azure FW v centru nebo důvěryhodném poskytovateli zabezpečení. Tento krok zajistí, aby se výchozí trasa rozšířila na vybrané větve a virtuální sítě připojené k virtuální síti WAN prostřednictvím připojení. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>Vynucení tunelového přenosu do místní brány firewall v zabezpečeném virtuálním centru

Pokud se už virtuálním rozbočovačem z jedné z větví (lokalit VPN nebo ER) objevila výchozí trasa (přes BGP), bude tato výchozí trasa přepsaná výchozí trasou získanou z nastavení Azure Firewall Manageru. V takovém případě budou všechny přenosy, které vstupují do centra z virtuální sítě a větví určených k Internetu, směrovány na Azure Firewall nebo důvěryhodného poskytovatele zabezpečení.

> [!NOTE]
> V současné době není k dispozici možnost vybrat místní bránu firewall nebo Azure Firewall (a důvěryhodného poskytovatele zabezpečení) pro internetový vázaný provoz pocházející z virtuální sítě, větví nebo uživatelů. Výchozí trasa získaná z nastavení Azure Firewall Manageru je vždy upřednostňována nad výchozí trasou získanou z jedné z větví.


## <a name="next-steps"></a>Další kroky

Vytvořte připojení pomocí virtuální sítě WAN a nasaďte Azure Firewall v VWANch rozbočovačích.

* [Připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
* [Připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager pro nasazení nástroje Azure FW v VWAN](../firewall-manager/index.yml)
