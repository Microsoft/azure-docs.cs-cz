---
title: Doporučené postupy pro nastavení sítě pro migraci úloh do Azure | Dokumentace Microsoftu
description: Po migraci do Azure, získejte doporučené postupy pro nastavení sítě pro přenášená zatížení.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 5cbe0c5e86a1dfd8cc3cb374f0ae7e1c67ae9d77
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828294"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Osvědčené postupy pro nastavení sítě pro úlohy migrovat do Azure

Jako je plánování a návrh pro migraci, kromě migrace, jedním z nejdůležitějších kroků je návrh a implementaci sítí Azure. Tento článek popisuje osvědčené postupy pro sítě při migraci do implementace IaaS a PaaS v Azure.

> [!IMPORTANT]
> Osvědčené postupy a názory, které jsou popsané v tomto článku jsou založené na platformě Azure a služby funkce dostupné v době zápisu. Funkce a možnosti v průběhu času měnit. Ne všechna doporučení může být pro vaše nasazení, takže vyberte ty, které vám vyhovují.


## <a name="design-virtual-networks"></a>Návrh virtuální sítě

Azure poskytuje virtuální sítě (Vnet):
- Prostředky Azure, komunikovat soukromě, přímo a bezpečně s jinými přes virtuální sítě.
- Ve virtuálních sítích můžete nakonfigurovat koncový bod připojení pro virtuální počítače a služby, které vyžadují internetovou komunikaci.
- Virtuální sítě se o logickou izolaci cloudu Azure, který je vyhrazen pro vaše předplatné.
- Můžete implementovat více virtuálními sítěmi v rámci každého předplatného Azure a oblastí Azure.
- Každá virtuální síť je izolovaná od jiných virtuálních sítí.
- Virtuální sítě může obsahovat privátní a veřejné IP adresy definované v [RFC 1918](https://tools.ietf.org/html/rfc1918), vyjádřené v zápisu CIDR. Veřejné IP adresy nejsou přímo dostupné z Internetu.
- Virtuální sítě můžete připojit k sobě navzájem pomocí VNet peering. Připojené virtuální sítě může být v jedné nebo několika oblastech. Proto prostředky v jedné virtuální síti můžou připojit k prostředkům v jiných virtuálních sítí.
- Ve výchozím nastavení směruje Azure provoz mezi podsítěmi v rámci virtuální sítě, připojené virtuální sítě, místními sítěmi a Internetem.


Existuje několik věcí, které musíte zvážit při plánování topologie sítě VNet, včetně způsobu uspořádání IP adresu mezery, jak implementovat síť centra s paprsky, jak segmentovat do podsítí virtuální sítě, nastavení DNS a implementujte zóny dostupnosti Azure.

## <a name="best-practice-plan-ip-addressing"></a>Osvědčený postup: Naplánujte IP adresování

Při vytváření virtuální sítě jako součást vaší migrace, je potřeba naplánovat adresní prostor vaší virtuální síti IP.

- Byste měli přiřadit adresní prostor, který není větší než rozsah CIDR /16 pro každou virtuální síť. Virtuální sítě umožňují používání 65536 IP adresy a přiřazování předponu menší než/16 by vést ke ztrátě IP adres. Je důležité, abyste odpad IP adres, i v případě, že jsou v rozsahy privátních, které jsou definovány v dokumentu RFC 1918.
- Adresní prostor virtuální sítě se nesmí překrývat s rozsahy adres místní sítě.
- Překlad síťových adres (NAT) se nesmí používat.
- Překrývající se adresy může způsobit sítě, které nemůže být připojen a směrování, která nebude fungovat správně. Pokud sítě se překrývají, bude potřeba změnit návrh sítě, nebo použijte překlad síťových adres (NAT).

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) virtuálních sítí Azure.
- [Čtení](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) sítě – nejčastější dotazy.
- [Další informace o](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) omezení sítě.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Osvědčený postup: Implementace síťové topologie centra s paprsky

Síťové topologie centra s paprsky izoluje úlohy při sdílení služeb, jako je identity a zabezpečení.
- Centrum je virtuální síť Azure, který funguje jako ústřední bod připojení.
- Paprsky jsou virtuální sítě, které se připojit k virtuální síti centra pomocí VNet peering.
- Sdílené služby jsou nasazené v centru, zatímco individuální úlohy jsou nasazené jako paprsky. 

Zvažte použití těchto zdrojů:
- Implementace hvězdicové topologii v Azure centralizuje běžné služby jako připojení k místním sítím, brány firewall a izolaci mezi virtuálními sítěmi. Virtuální síť centra poskytuje centrální bod připojení k místním sítím a místo, kde můžete hostitele služby používají ve úlohami hostovanými ve virtuálních sítích paprsků.
- Hvězdicové konfigurace se obvykle používá ve velkých podnicích. Menší sítě může zvážit jednodušší návrh a Šetřete na náklady a složitost.
- Virtuální sítě paprsků slouží k izolaci úloh, pomocí každého paprsku se spravují odděleně od ostatních paprsků. Každá úloha může obsahovat několik vrstev a více podsítí, které jsou propojeny se nástroje pro vyrovnávání zatížení Azure.
- Střed a paprsek virtuální sítě se dá implementovat v různých skupinách prostředků a dokonce i v různých předplatných. Po vytvoření partnerského vztahu virtuálních sítí v různých předplatných, dá se přidružit k tenantů Azure Active Directory (AD) stejného nebo jiného, předplatná. To umožňuje decentralizovanou správu každé úlohy při sdílení služeb udržovaných v centrální síti.

![Správa změn](./media/migrate-best-practices-networking/hub-spoke.png)
*hvězdicové topologie*

**Víc se uč:**

- [Přečtěte si informace o](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) hvězdicové topologii.
- Získejte doporučení pro síť pro provoz Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm#network-recommendations) a [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm#network-recommendations) virtuálních počítačů.
- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) partnerský vztah virtuální sítě.


## <a name="best-practice-design-subnets"></a>Osvědčený postup: Návrh podsítě

K zajištění izolace v síti VNet, segmentovat do jedné nebo několika podsítí a přidělení části adresního prostoru virtuální sítě ke každé podsíti.
- Můžete vytvořit více podsítí v rámci každé virtuální síti.
- Ve výchozím nastavení Azure směrování síťového provozu mezi všemi podsítěmi ve virtuální síti.
- Vaše podsíť rozhodnutí, která jsou založeny na technická a organizační požadavky.  
- Vytvoření podsítě se používá zápis CIDR.
- Při rozhodování o rozsahu sítě pro podsítě, je důležité si uvědomit, že Azure uchovává 5 IP adres z každé podsítě, které nelze použít. Například pokud vytvoříte nejmenší dostupné podsítě minimální velikostí/29 (s osmi IP adresy), Azure si zachovají pět adres, abyste měli tři použitelné adresy, které je možné přiřadit k hostitelům v podsíti.
- Ve většině případů se doporučuje použití velikosti/28 jako nejmenší podsítě.

### <a name="example"></a>Příklad:

Tabulka ukazuje příklad virtuální sítě s adresním prostorem 10.245.16.0/20 segmentovat do podsítí pro plánované migrace.

**Podsíť** | **CIDR** | **Adresy** | **Použití**
--- | --- | --- | ---
DEV-FE – EUS2 | 10.245.16.0/22 | 1019 | Front-endu/webovou vrstvu virtuálních počítačů
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | Virtuální počítače vrstvy aplikace
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | Virtuální počítače databáze

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) navrhování podsítí.
- [Zjistěte, jak](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) fiktivní společnosti (Contoso) přípravy na migraci jejich síťové infrastruktury.


## <a name="best-practice-set-up-a-dns-server"></a>Osvědčený postup: Nastavení serveru DNS

Azure přidá DNS server ve výchozím nastavení při nasazení virtuální sítě. To umožňuje rychle vytvořit virtuální sítě a nasazení prostředků. Tento server DNS však pouze poskytuje služby pro prostředky v této virtuální síti. Pokud chcete propojení více virtuálních sítí nebo připojení k místnímu serveru z virtuální sítě, je nutné další název možnosti řešení. Například můžete potřebovat služby Active Directory k překladu názvů DNS mezi virtuálními sítěmi. K tomuto účelu nasazení vlastní vlastní server DNS v Azure.

- Servery DNS ve virtuální síti, může předat dotazy DNS na rekurzivní překladače v Azure. To umožňuje překládat názvy hostitelů v rámci této virtuální síti. Například řadič domény spuštěný v Azure můžete odpovídat na dotazy DNS pro vlastní domény a předávat všechny ostatní dotazy k Azure.
- Předávání DNS umožňuje virtuálních počítačů určených k místním prostředkům (prostřednictvím řadič domény) a názvy hostitelů poskytuje Azure (s použitím předávání). Poskytuje přístup k rekurzivní překladače v Azure pomocí virtuální IP adresy 168.63.129.16.
- Předávání DNS také umožňuje překlad názvů DNS mezi virtuálními sítěmi a povoluje místních počítačů k překladu názvů hostitelů poskytovaný platformou Azure.
    - Přeložit název hostitele virtuálního počítače, server DNS virtuálního počítače se musí nacházet ve stejné virtuální síti a nakonfigurovat tak, aby dotazy na názvy dopředné hostitele do Azure.
    - Protože přípona DNS se liší v každé virtuální síti, můžete použít pravidla pro podmíněné předávání k odesílání dotazů DNS na správnou virtuální síť pro překlad.
- Pokud používáte vlastní servery DNS, můžete zadat víc serverů DNS pro každou virtuální síť. Můžete také zadat víc serverů DNS na síťové rozhraní (pro Azure Resource Manageru), nebo za cloudové služby (v případě modelu nasazení classic).
- Servery DNS zadaný pro síťové rozhraní nebo cloudové služby mají přednost před serverů DNS má zadaný pro virtuální síť.
- V modelu nasazení Azure Resource Manageru můžete určit servery DNS pro virtuální sítě a síťové rozhraní, ale osvědčeným postupem je použít jenom ve virtuálních sítích.

    ![Servery DNS](./media/migrate-best-practices-networking/dns2.png) *servery DNS pro virtuální síť*

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) překlad názvů při použití vlastního serveru DNS.
- [Další informace o](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) DNS pravidla a omezení pojmenování.


## <a name="best-practice-set-up-availability-zones"></a>Osvědčený postup: Nastavení zón dostupnosti

Zóny dostupnosti zvýšit vysokou dostupnost pro ochranu aplikací a dat z datacenter selhání.

- Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure.
- Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí.
- K zajištění odolnosti proti chybám, je minimálně tří samostatných zón ve všech oblastech, povolené.
- Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center.
- Zónově redundantní služby replikovat napříč zónami dostupnosti pro zajištění ochrany z jediné body selhání aplikace a data. --Se zónami dostupnosti Azure nabízí smlouvu SLA o 99,99 % dostupnosti virtuálního počítače.

    ![Zóna dostupnosti](./media/migrate-best-practices-networking/availability-zone.png) *zóně dostupnosti*

- Můžete naplánovat a sestavit vysokou dostupností do architektury migrace společné umístění výpočetní prostředky, úložiště, sítě a zdroje dat v rámci zóny a replikace v jiné zóně. Služby Azure, které podporují zóny dostupnosti se dělí do dvou kategorií:
    - Oblastmi služby: Přidružte prostředek s konkrétní zóně. Pro příklad virtuálních počítačů spravované disky, IP adresy).
    - Zónově redundantní služby: Prostředek se automaticky replikuje napříč zónami. Například zónově redundantní úložiště, Azure SQL Database.
- Můžete nasadit standardní Azure zatížení vyrovnávaném pomocí úloh přístupem k Internetu nebo aplikačních vrstvách pro zónové odolné proti chybám.

    ![Nástroj pro vyrovnávání zatížení](./media/migrate-best-practices-networking/load-balancer.png) *nástroje pro vyrovnávání zatížení*

**Víc se uč:**
-   [Získejte přehled](https://docs.microsoft.com/azure/availability-zones/az-overview) zón dostupnosti.



## <a name="design-hybrid-cloud-networking"></a>Návrh hybridní cloudové sítě

Pro úspěšnou migraci je velmi důležité pro připojení k Azure v místním podnikovým sítím. Tím se vytvoří připojení vždy na označuje jako síť hybridního cloudu, kde jsou k dispozici služby Azure cloud pro podnikové uživatele. Existují dvě možnosti pro vytvoření tohoto typu sítě:

- **Síť Site-to-site VPN:** Vytvoření připojení site-to-site mezi kompatibilní místní zařízení VPN a službou Azure VPN gateway, který je nasazený ve virtuální síti. Žádné oprávnění místních prostředků můžete přístup k virtuálním sítím. Komunikace Site-to-site se odesílá prostřednictvím šifrovaného tunelu přes internet. 
- **Azure ExpressRoute:** Můžete navázat připojení k Azure ExpressRoute mezi místní sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé a provoz se nebude přenášejí prostřednictvím Internetu.

**Víc se uč:**

- [Další informace](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) o možnostech sítě hybridního cloudu.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Osvědčený postup: Implementace sítě site-to-site VPN s vysokou dostupností

K implementaci site-to-site VPN, nastavení brány sítě VPN v Azure.
- Brána VPN je konkrétní typ brány virtuální sítě, který slouží k posílání šifrovaného provozu mezi virtuální sítí VNet Azure a místním umístěním přes veřejný Internet.
- Bránu sítě VPN můžete použít také k posílání šifrovaného provozu mezi virtuálními sítěmi Azure po síti Microsoftu.
- Každá virtuální síť může mít pouze jednu bránu VPN.
- Můžete vytvořit několik připojení ke stejné bráně VPN. Když vytvoříte několik připojení, všechny tunely VPN sdílejí dostupnou šířku pásma.
- Každá Azure VPN Gateway se skládá ze dvou instancí v konfiguraci aktivní-pohotovostní.
    - Pro plánované údržbě nebo neplánovaném přerušení aktivní instanci dojde k převzetí služeb při selhání a pohotovostní instance automaticky převezme a obnoví site-to-site a připojení VNet-to-VNet. 
    - Přepnutí způsobí, že stejné přerušení.
    - Při plánované údržbě by se spojení mělo obnovit během 10 až 15 sekund.
    - U neplánovaných problémů bude obnovení spojení delší dobu, asi jednu až 1.5 minut v nejhorším případě.
    - Připojení klienta VPN Point-to-site (P2S) k bráně budou odpojeny a uživatelé budou muset znovu připojit z klientských počítačů.

Při nastavování site-to-site VPN, postupujte takto:
 - Budete potřebovat virtuální síť, rozsah adres není překrývají s místní sítí, ke kterým se připojí virtuální privátní síť.
 - Vytvořit podsíť brány v síti.
 - Vytvoření brány VPN, zadejte typ brány (VPN) a určuje, zda je brána na základě zásad nebo založené na směrování. Síť VPN typu RouteBased se doporučuje jako více podporující a budoucnosti.
 - Místní vytvoření brány místní sítě a nakonfigurujte místní zařízení VPN. 
 - Vytvoříte převzetí služeb při selhání připojení VPN typu site-to-site mezi bránou virtuální sítě a místním zařízením. Pomocí sítě VPN založené na trasách umožňuje aktivní pasivní nebo aktivní aktivní připojení k Azure. Založené na trasách také podporuje připojení point-to-site (z jednoho počítače) i připojení site-to-site (z libovolného počítače) současně.
 - Můžete určit SKU brány, kterou chcete použít. Bude to záviset na požadavky úloh, propustnosti, funkcí a smlouvy o úrovni služeb.
 - Border gateway protocol (BGP) je volitelná funkce vám pomůže s Azure ExpressRoute a VPN Gateway založená na trasách rozšířit místní trasy protokolu BGP pro vaše virtuální sítě.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*Site-to-site VPN*
 
**Víc se uč:**

- [Kontrola](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) kompatibilní s místními zařízeními VPN.
- [Získejte přehled](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) bran VPN.
- [Další informace o](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) vysoce dostupných připojení VPN.
- [Další informace o](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) plánování a navrhování bránu VPN.
- [Kontrola](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) nastavení služby VPN gateway.
- [Kontrola](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) SKU brány.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) nastavení protokolu BGP s bránami Azure VPN Gateway.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Osvědčený postup: Konfigurace brány pro VPN Gateway

Při vytváření brány VPN gateway v Azure, musíte použít speciální podsíť s názvem GatewaySubnet. Při vytváření této podsítě Poznámka těchto osvědčených postupů:

- Délka předpony podsítě brány může mít délku maximální předpony 29 (například 10.119.255.248/29). Aktuální doporučení je, že používáte délka předpony 27 (například 10.119.255.224/27).
- Při definování adresní prostor podsítě brány použijte poslední části adresního prostoru virtuální sítě.
- Pokud používáte Azure GatewaySubnet, nikdy neměli nasazovat všechny virtuální počítače nebo jiné zařízení, jako jsou Application Gateway do podsítě brány.
- Nepřiřazovat skupinu zabezpečení sítě (NSG) k této podsíti. To způsobí, že je brána přestane fungovat.

**Víc se uč:**
- [Tento nástroj použijte](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) určit váš adresní prostor IP adres.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Osvědčený postup: Implementace Azure virtuální sítě WAN pro firemní pobočky

Pro více připojení k síti VPN Azure virtuální síť WAN je síťové služby, která poskytuje optimalizované a automatizované větve do větve připojením prostřednictvím Azure.
- Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. To můžete udělat ručně, nebo pomocí upřednostňované poskytovatele zařízení prostřednictvím virtuální sítě WAN partnera.
- Používání preferované poskytovatele zařízení umožňuje použití, připojení a konfigurace správy.
- Azure WAN integrovaný řídicí panel poskytuje okamžitý přehled o řešení problémů, které šetří čas a poskytují snadný způsob, jak sledovat ve velkém měřítku připojení site-to-site. 

**Další informace:**
[Další informace o](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) Azure virtuální sítě WAN.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Osvědčený postup: Implementace pro zásadně důležité připojení ExpressRoute

Službu Azure ExpressRoute umožňuje rozšířit vaše místní infrastrukturu do cloudu Microsoftu tak, že vytvoříte privátní připojení mezi virtuální datové centrum Azure a místní sítí.
- Připojení ExpressRoute může být v síti any-to-any (IP VPN), síť Ethernet typu point-to-point nebo prostřednictvím poskytovatele připojení. Jejich nenavazují přes veřejný internet.
- Připojení ExpressRoute nabízejí vyšší úroveň zabezpečení, spolehlivost a vyšších rychlostí (až 10 GB/s), spolu s konzistentní latence.
- ExpressRoute je užitečné pro virtuální datová centra, jak zákazníci mohou získat výhody pravidla dodržování předpisů, které jsou přidružené k připojení privátní sítě.
- S ExpressRoute Direct můžete připojit přímo k směrovače Microsoftu na 100Gbps pro potřeby větší šířku pásma.
- ExpressRoute používá protokol BGP pro výměnu tras mezi místními sítěmi, instancí Azure a veřejnými adresami Microsoftu.

Nasazení připojení ExpressRoute obvykle zahrnuje zapojení poskytovatele služeb ExpressRoute. Pro rychlý start je běžné zpočátku použití sítě site-to-site VPN k navázání připojení mezi virtuální datové centrum a místním prostředkům a potom migrovat do připojení ExpressRoute, když fyzického propojení se svým poskytovatelem služeb vytvořeno.

**Víc se uč:**
- [Získejte přehled](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) služby ExpressRoute.
- [Další informace o](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) přímo ExpressRoute.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Osvědčený postup: Optimalizace směrování ExpressRoute s komunit protokolu BGP

Pokud máte víc okruhů ExpressRoute, máte více než jednu cestu, jak se připojit k Microsoftu. V důsledku toho neoptimální směrování se může stát a provozu mohou použít delší cestu pro přístup k síti a Microsoftem. Delší je síťová cesta, tím větší je latence. Latence má přímý vliv na výkon a uživatelské prostředí aplikací.

#### <a name="example"></a>Příklad:

Vezměme si příklad.

- Máte dvě pobočky v USA, jednu v Los Angeles a jednu v New Yorku.
- Vaše pobočky jsou připojené v síti WAN, což může být vlastní páteřní sítě nebo VPN IP poskytovatele služeb.
- Máte dva okruhy ExpressRoute, jeden v oblasti USA – západ a druhý v oblasti USA – východ, které jsou také připojené k síti WAN. Zjevně máte dvě cesty, jak se připojit k síti Microsoftu.


 
**Problém** nyní Představte si, že máte nasazení Azure (například Azure App Service) v oblasti USA – západ i USA – východ.
- Chcete, aby uživatelé pro přístup k jejich nejbližší služeb Azure pro optimální výkon v každé pobočce.
- Proto budete chtít připojit uživatele z Los Angeles k Azure USA – západ a uživatele z New Yorku k Azure USA – východ.
- Tento postup funguje pro uživatele na východním pobřeží, ale ne pro západ. Problém je následujícím způsobem:
    - V každém okruhu ExpressRoute jsme inzerovat obě předpony v Azure USA – východ (23.100.0.0/16) a Azure USA – západ (13.100.0.0/16).
    - Bez znalosti, která předpona je z které oblasti, nejsou předpony zpracovávat odděleně.
    - Vaše síť WAN můžete předpokládat, že obě předpony jsou blíž oblasti USA – východ než USA – západ a proto směrovat uživatele z obou poboček na okruh ExpressRoute v oblasti USA – východ, poskytuje méně než optimální prostředí pro uživatele v pobočce v Los Angeles.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*komunit protokolu BGP neoptimalizované připojení*

**Řešení**

Abyste optimalizovali směrování pro uživatele obou poboček, budete muset vědět, která předpona je z Azure USA – západ a která je z Azure USA – východ. Tyto informace můžete kódovat s použitím hodnoty komunity protokolu BGP.
- Přiřaďte jedinečnou hodnotu komunity protokolu BGP pro každou oblast Azure. Například 12076:51004 pro USA – východ; 12076:51006 pro USA – západ.
- Teď, když je jasné předpony, které patří do které oblasti Azure, můžete nakonfigurovat upřednostňované okruh ExpressRoute.
- Vzhledem k tomu, že při použití protokolu BGP pro výměnu informací o směrování, můžete k ovlivnění směrování hodnotu místní preference protokolu BGP.
- V našem příkladu přiřaďte vyšší hodnotu local preference pro 13.100.0.0/16 v oblasti USA – západ než v oblasti USA – východ a obdobně vyšší hodnotu local preference pro 23.100.0.0/16 v oblasti USA – východ než USA – západ. 
- Tato konfigurace zajišťuje, že při cesty společnosti Microsoft jsou k dispozici, uživatelé v Los Angeles se připojí k Azure USA – západ – západ okruh pomocí i uživatelé New Yorku k Azure USA – východ připojit pomocí – východ okruh. Směrování je optimalizované na obou stranách.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*komunit protokolu BGP optimalizované připojení*


**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) optimalizace směrování

## <a name="securing-vnets"></a>Zabezpečení virtuální sítě

Mezi společnostmi Microsoft a můžete je sdílet odpovědnost za zabezpečení virtuální sítě. Společnost Microsoft poskytuje mnoho síťové funkce, jakož i služby, které pomáhají zabezpečit prostředky. Při navrhování zabezpečení pro virtuální sítě, existuje několik osvědčených postupů, měli byste postupovat podle, včetně implementace hraniční síti, pomocí filtrování skupin a skupin zabezpečení, zabezpečení přístupu k prostředkům a IP adresy a implementaci útoků.

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) osvědčených postupů pro zabezpečení sítě.
- [Zjistěte, jak](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) návrh pro zajištění zabezpečených sítí.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Osvědčený postup: Implementace Azure hraniční síti

I když společnost Microsoft se investuje silně při ochraně cloudové infrastruktury, je nutné také chránit vaše cloudové služby a skupiny prostředků. S více vrstvami přístup k zabezpečení poskytuje nejlepší ochranu. Vložení do hraniční sítě v místě je důležitou součástí strategie je, že ochranu.

- Hraniční síť chrání prostředkům v interní síti z nedůvěryhodné sítě. 
- Je nejkrajnější vrstvy, který je přístupný z Internetu. Obecně nachází se mezi Internetem a podnikovou infrastrukturou, obvykle s určitou formu ochrany na obou stranách. 
- V typické podnikové síťové topologie základní infrastruktury silně přidá na perimetry, s víc vrstvami zabezpečení zařízení. Hranice každou vrstvu se skládá ze zařízení a body vynucení zásad.
- Každá vrstva může obsahovat kombinaci řešení zabezpečení sítě, které zahrnují brány firewall, ochrany před únikem informací s cílem odepření služby (DoS), systémů ochrany zjišťování/vniknutí neoprávněného vniknutí (IDS/IPS) a zařízení VPN.
- Vynucení zásad v hraniční síti můžete použít zásady brány firewall, seznamy řízení přístupu (ACL) nebo konkrétní směrování.
- Protože doručena příchozí provoz z Internetu, má zachytit a zpracovat kombinaci řešení ochrany před mobilními útoky bloku a škodlivého provozu, dovolující oprávněné požadavky na síť.
- Příchozí provoz směrovat přímo k prostředkům v hraniční síti. Prostředek hraniční sítě může komunikovat s jiným prostředkům hlouběji v síti, přesun vpřed provoz do sítě po ověření.

Následující obrázek znázorňuje příklad hraniční síti jedinou podsítí v podnikové síti, s dvěma hranice zabezpečení.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*nasazení hraniční sítě*

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) nasazení hraniční sítě mezi Azure a místním datacentrem.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Osvědčený postup: Filtrování provozu virtuální sítě s použitím skupin zabezpečení sítě

Skupiny zabezpečení sítě (NSG) obsahují více pravidla zabezpečení příchozích a odchozích, které umožňují filtrovat provoz směřující do a z prostředků. Filtrování může být ve zdrojové a cílové IP adresy, portu a protokolu. 
- Skupiny zabezpečení sítě obsahují pravidla zabezpečení, které povolit nebo odepřít příchozí síťové přenosy do (nebo odchozí síťový provoz ze) několik typů prostředků Azure. Pro každé pravidlo můžete určit zdroj a cíl, port a protokol.
- Pravidla skupiny zabezpečení sítě jsou vyhodnocovány podle priority, pomocí informací o pěti řazené kolekce členů (zdroj, zdrojový port, cíl, cílový port a protokol) která povolí nebo zakážou provoz.
- Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku.
- Záznam flow umožňuje stavové existenci NSG. Například pokud chcete zadat odchozí pravidlo zabezpečení na jakoukoli adresu přes port 80, není nutné příchozí pravidlo zabezpečení pro reakci na odchozí provoz. Příchozí pravidlo zabezpečení je potřeba zadat pouze v případě, že se komunikace zahajuje externě.
- Opačně to platí také. Pokud je povolený příchozí provoz přes port, není nutné zadat odchozí pravidlo zabezpečení pro reakci na provoz přes port.
- Existující připojení nepřerušily při odebrání pravidla zabezpečení, který povolen tok. Přenosové toky jsou přerušen, připojení se zastaví a žádný provoz se přenášejí v obou směrech po dobu aspoň pár minut.
- Při vytváření skupin zabezpečení sítě, vytvořte jako málo nejdříve ale tolik, které jsou nezbytné.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Osvědčený postup: Zabezpečené north/south a provoz typu east/west

Při zabezpečení virtuální sítě, je důležité vzít v úvahu vektory útoku.
- Použití pouze skupiny Nsg podsítě se prostředí zjednoduší, ale pouze zabezpečuje provoz do podsítě. To se označuje jako north/south provoz.
- Provoz mezi virtuálními počítači ve stejné podsíti se označuje jako provoz typu east/west.
- Je důležité využívat obě formy ochranu, takže pokud se hacker získá přístup z vnějšku, budete se zastaví při pokusu o připojení počítačů umístěných ve stejné podsíti.

### <a name="use-service-tags-on-nsgs"></a>Použití značek služby na skupiny zabezpečení sítě

Značka služby představuje skupinu předpon IP adres. Použití značku služby pomáhá minimalizovat složitost vytváření pravidla skupiny zabezpečení sítě.
- Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel.
- Společnost Microsoft spravuje předpony adres, které jsou přidružené k značku služby a automaticky značku služby aktualizuje při změně adres.
- Nelze vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté.

Značky služeb vyžadují ruční práce z pravidla přiřazení do skupin služby Azure. Například pokud chcete povolit virtuální síť podsíť obsahující webové servery pro přístup ke službě Azure SQL Database, můžete vytvořit pravidlo odchozí port 1433 a použít **Sql** značka služby.
- To **Sql** značka označuje předpony adres služeb Azure SQL Database a Azure SQL Data Warehouse.
- Pokud zadáte **Sql** jako hodnotu, provoz je povolený nebo zakázaný jazyka SQL.
- Pokud chcete povolit přístup k **Sql** v konkrétní oblasti, můžete zadat tuto oblast. Například pokud chcete povolit přístup pouze ke službě Azure SQL Database v oblasti východní USA, můžete zadat **Sql.EastUS** jako značku služby.
- Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale nepředstavuje konkrétní SQL database nebo serveru.
- Všechny předpony adres reprezentované touto značkou jsou reprezentované také značkou **Internet**.


**Víc se uč:**

- [Přečtěte si informace o](https://docs.microsoft.com/azure/virtual-network/security-overview) skupin zabezpečení sítě.
- [Kontrola](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) značky služeb dostupné pro skupiny zabezpečení sítě.


## <a name="best-practice-use-application-security-groups"></a>Osvědčený postup: Použití skupin zabezpečení aplikací

Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace.

- Můžete seskupit virtuální počítače a definovat zásady zabezpečení sítě na základě skupin zabezpečení aplikací.
- Skupiny zabezpečení aplikací umožňují snadno opakovaně používat zásady zabezpečení ve velkém měřítku bez potřeby ruční údržby explicitních IP adres.
- Skupiny zabezpečení aplikace zpracovávat složitost explicitních IP adres nebo více sad pravidel, tak budete moct soustředit na obchodní logiku. 

### <a name="example"></a>Příklad:

![Skupiny zabezpečení aplikací](./media/migrate-best-practices-networking/asg.png)
*příklad skupiny zabezpečení aplikací*

**Síťové rozhraní** | **Skupiny zabezpečení aplikací**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- V našem příkladu každé síťové rozhraní patří do pouze jednu skupinu zabezpečení aplikace, ale ve skutečnosti rozhraní patří do více skupin, v souladu s omezení v Azure.
- Žádný ze síťových rozhraní nemá přidruženou skupinu NSG. NSG1 je přidružená k obě podsítě a obsahuje následující pravidla.

    **Název pravidla** | **Účel** | **Podrobnosti**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Povolte přenosy z Internetu k webovým serverům. Příchozí provoz z Internetu byl odepřen podle pravidla zabezpečení DenyAllInbound výchozí, takže je potřeba žádná další pravidla pro skupiny zabezpečení aplikací AsgLogic nebo AsgDb. | Priorita: 100<br/><br/> Zdroj: internet<br/><br/> Zdrojový port: *<br/><br/> Cíl: AsgWeb<br/><br/> Cílový port: 80<br/><br/> Protokol: TCP<br/><br/> Přístup: Povolte.
    Deny-Database-All | AllowVNetInBound výchozího pravidla zabezpečení umožňuje veškerou komunikaci mezi prostředky ve stejné virtuální síti, je potřeba toto pravidlo pro odepření provozu ze všech prostředků. | Priorita: 120<br/><br/> Zdroj: *<br/><br/> Zdrojový port: *<br/><br/> Cíl: AsgDb<br/><br/> Cílový port: 1433<br/><br/> Protokol: Vše<br/><br/> Přístup: Odepřete.
    Allow-Database-BusinessLogic | Povolte přenosy z příslušné skupiny zabezpečení aplikace AsgLogic AsgDb skupiny zabezpečení aplikace. Priorita pro toto pravidlo je vyšší než pravidlo odepřít-Database-All a bude zpracována před pravidlo, tak je povolený provoz ze skupiny zabezpečení aplikace AsgLogic a veškerý ostatní provoz je blokován. | Priorita: 110<br/><br/> Zdroj: AsgLogic<br/><br/> Zdrojový port: *<br/><br/> Cíl: AsgDb<br/><br/> Cílový port: 1433<br/><br/> Protokol: TCP<br/><br/> Přístup: Povolte.

- Pravidla určující skupinu zabezpečení aplikace jako zdroj nebo cíl se použijí pouze na síťová rozhraní, která jsou členy příslušné skupiny zabezpečení aplikace. Pokud síťové rozhraní není členem žádné skupiny zabezpečení aplikace, pravidlo se pro něj nepoužije, ani když je k podsíti přiřazená skupina zabezpečení sítě.

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) skupiny zabezpečení aplikací.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Osvědčený postup: Zabezpečený přístup k PaaS pomocí koncových bodů služby virtuální sítě

Koncové body služby virtuální sítě rozšiřují privátní adresní prostor virtuální sítě a identitu do služeb Azure přes přímé připojení.

- Koncové body umožňují zabezpečit důležité prostředky služeb Azure pro vaše virtuální sítě pouze. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.
- Privátní adresní prostor virtuální sítě můžou se překrývat a proto jej nelze použít k jednoznačné identifikaci přenos dat pocházejících z jedné virtuální sítě.
- Jakmile se ve vaší virtuální síti povolené koncové body služby, můžete svázat prostředky služeb Azure tak, že přidáte pravidlo virtuální sítě k prostředkům služby. To poskytuje lepší zabezpečení díky úplnému odebrání veřejného internetového přístupu k prostředkům a povolení provozu pouze z vaší virtuální sítě.

![Koncové body služby](./media/migrate-best-practices-networking/endpoint.png)
*koncové body služby*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) koncové body služby virtuální sítě.


## <a name="best-practice-control-public-ip-addresses"></a>Osvědčený postup: Ovládací prvek veřejné IP adresy

Veřejné IP adresy v Azure lze přidružit virtuálních počítačů, nástroje pro vyrovnávání zatížení, brány application Gateway a bránami VPN.

- Veřejné IP adresy umožňují internetovým prostředkům příchozí k prostředkům Azure komunikovat a prostředkům Azure komunikovat odchozí k Internetu.
- Veřejné IP adresy se vytvářejí s SKU basic nebo standard, s celou řadou rozdíly mezi nimi. Standardní SKU se dají přiřadit k libovolné službě, ale nakonfigurují nejvíce obvykle na virtuálních počítačích, nástroje pro vyrovnávání zatížení a brány application Gateway.
- Je důležité si uvědomit, že základní veřejnou IP adresu nemusí automaticky nakonfiguruje skupiny NSG. Budete muset nakonfigurovat vlastní a přiřadit pravidla pro řízení přístupu. Standardní SKU IP adres máte skupinu zabezpečení sítě a pravidel přiřazené ve výchozím nastavení.
- Jako osvědčený postup by neměl být virtuální počítače nakonfigurované s použitím veřejné IP adresy.
    - Pokud potřebujete otevřít port, mělo by být pouze pro webové služby, jako je port 80 nebo 443.
    - Porty standardní vzdálené správy, jako jsou SSH (22) a protokolu RDP (3389) je třeba nastavit na odepřít, spolu s všech ostatních portech pomocí skupin zabezpečení sítě.
- Je doporučeno umístit virtuální počítače s bránou Azure load balancer nebo aplikace. V případě potřeby přístup k portům pro vzdálenou správu můžete použít v čase přístup k virtuálním počítačům v Azure Security Center.

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) veřejné IP adresy v Azure.
- [Přečtěte si další](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) na just-in-time přístup k virtuálním počítačům v Azure Security Center.


## <a name="leverage-azure-security-features-for-networking"></a>Využijte funkce Azure zabezpečení sítě

Azure má funkce zabezpečení platformy, které se snadno používá a poskytují bohaté protiopatření pro běžné síťovým útokům. Patří mezi ně Brána Firewall služby Azure, brány Firewall webových aplikací a Network Watcher.

## <a name="best-practice-deploy-azure-firewall"></a>Osvědčený postup: Nasazení brány Firewall na Azure

Brány Firewall na Azure je služba zabezpečení spravované sítě založené na cloudu, které chrání prostředky virtuální sítě. Jedná se o plně stavové brány firewall na-jako službu s integrovanou vysokou dostupnost a škálovatelnost cloudu neomezený.

![Koncové body služby](./media/migrate-best-practices-networking/firewall.png)
*Brána Firewall služby Azure*

- V Brána Firewall služby Azure můžete centrálně vytvoření, vynucení a protokolu zásady aplikace a síťové připojení mezi předplatná a virtuálními sítěmi.
- Brány Firewall Azure používá statickou veřejnou IP adresu pro vaše prostředky virtuální sítě mimo brány firewall umožňuje provoz pocházející z vaší virtuální sítě.
- Brány Firewall na Azure je plně integrované s Azure Monitor pro protokolování a analýza.
- Jako osvědčený postup při vytváření pravidel brány Firewall na Azure vytvořte pravidla pomocí značek plně kvalifikovaný název domény.
    - Značku plně kvalifikovaný název domény představuje skupinu plně kvalifikované názvy domén, které jsou spojené s dobře známými službami Microsoftu.
    - Použít značku plně kvalifikovaný název domény Pokud chcete povolit požadované odchozí síťové přenosy přes bránu firewall.
- Například pokud chcete ručně povolit aktualizace Windows síťové přenosy přes bránu firewall, je třeba vytvořit víc pravidel aplikace. Pomocí značek plně kvalifikovaný název domény, vytvoření pravidla pro aplikace a zahrnují značku aktualizace Windows. S tímto pravidlem na místě síťového provozu do koncových bodů služby Microsoft Windows Update probíhá přes bránu firewall.

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/firewall/overview) ze Brána Firewall služby Azure.
- [Další informace o](https://docs.microsoft.com/azure/firewall/fqdn-tags) značky plně kvalifikovaný název domény.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Osvědčený postup: Nasazování firewallu webových aplikací (WAF)

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Zneužití patří útoky prostřednictvím injektáže SQL a útoky skriptování napříč weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace. Centralizovaný firewall webových aplikací pomáhá dělat mnohem jednodušší správu zabezpečení a pomáhá správcům aplikace pomáhalo chránit před hrozbami neoprávněného vniknutí. Firewall webových aplikací může reagovat na ohrožení zabezpečení, rychlejší, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Azure firewall webových aplikací (WAF) je funkce služby Azure application gateway.
- WAF poskytuje centralizovanou ochranu webových aplikací, z běžných zneužitím a ohrožením zabezpečení.
- WAF chrání bez nutnosti upravovat back-end kód.
- To může chránit několik webových aplikací současně za službou application gateway
- WAF je integrovaná s Azure Security Center.
- Můžete přizpůsobit pravidla firewallu webových aplikací a skupin pravidel tak, aby vyhovoval vašim požadavkům aplikace.
- Jako osvědčený postup byste měli použít WAF v popředí u žádné aplikace s přístupem k web, včetně aplikací na virtuálních počítačích Azure nebo služby Azure App Service.

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF.
- [Kontrola](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) WAF omezení a vyloučení.


## <a name="best-practice-implement-azure-network-watcher"></a>Osvědčený postup: Implementace Azure Network Watcher

Azure Network Watcher nabízí nástroje pro monitorování prostředků a komunikace ve virtuální síti Azure. Můžete například monitorovat komunikace mezi virtuálním Počítačem a koncový bod jako jiný virtuální počítač nebo plně kvalifikovaný název domény, zobrazení zdroje a vztahy prostředků ve virtuální síti, nebo diagnostikovat problémy se sítí provoz.

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- Pomocí služby Network Watcher můžete monitorovat a diagnostikovat problémy se sítí bez nutnosti přihlášení do virtuálních počítačů.
- Můžete nastavením upozornění můžete aktivovat zachytávání paketů a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Pokud narazíte na problém, můžete ho prozkoumat podrobněji.
- Jako nejlepší praxe, měli byste použít sledovací proces sítě ke kontrole protokolů toku NSG.
    - Protokoly toků NSG ve službě Network Watcher umožňují zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě.
    - Protokoly toku jsou napsané ve formátu json.
    - Protokoly toku zobrazení odchozí a příchozí toků na základě podle pravidla síťové rozhraní (NIC), na kterém tok vztahuje, 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový port a protokol) a určuje, zda byl provoz povolen nebo odepřen.

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/network-watcher) služby Network Watcher.
- [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) protokoly toků NSG.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Použití nástrojů partnerů na webu Azure Marketplace

Pro složitější topologií sítě můžete použít produkty zabezpečení od partnerů Microsoftu, v konkrétní síťová virtuální zařízení (Nva).

- Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je například Brána firewall, optimalizace sítě WAN nebo jiná síťová funkce.
- Síťová virtuální zařízení slouží k upevnění funkce zabezpečení a sítě VNet. Je možné nasadit s vysokou dostupností brány firewall, narušení ochrany před únikem informací, zjišťování neoprávněných vniknutí, brány firewall webových aplikací (Waf), optimalizace sítě WAN, směrování, Vyrovnávání zatížení, VPN, certifikát správy, služby Active Directory a vícefaktorové ověřování.
- Síťové virtuální zařízení je k dispozici mnoho dodavatelů v [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Osvědčený postup: Implementace brány firewall a síťová virtuální zařízení v Centru sítí

V centru je hraniční síti (s přístupem k Internetu) obvykle spravované přes bránu Firewall Azure, farmy brány firewall nebo brány firewall webových aplikací (Waf). Vezměte v úvahu následující porovnání.

**Typ brány firewall** | **Podrobnosti**
--- | ---
Řešení Waf | Webové aplikace jsou běžné a mají tendenci trpí ohrožení zabezpečení a potenciální zneužití.<br/><br/> Řešení Waf jsou navržené k detekování útoků, které webové aplikace (HTTP/HTTPS), přesněji než obecné brány firewall.<br/><br/> Ve srovnání s technologií tradici brány firewall, řešení Waf mají sadu konkrétní funkce, které chrání před hrozbami interní webové servery.
Brána Azure Firewall | Podobně jako farmy síťové virtuální zařízení brány firewall Brána Firewall služby Azure používá mechanismus správy common a sadu pravidel zabezpečení k ochraně úloh hostovaných v sítích paprsků a pro řízení přístupu k místním sítím.<br/><br/> Brána Firewall Azure nemá škálovatelnost součástí.
Síťové virtuální zařízení brány firewall | Například síťové virtuální zařízení brány Firewall Azure farmy brány firewall mají společného mechanismu správy a sadu pravidel zabezpečení k ochraně úloh hostovaných v sítích paprsků a pro řízení přístupu k místním sítím.<br/><br/> Síťové virtuální zařízení brány firewall můžete ručně škálovat za nástrojem pro vyrovnávání zatížení.<br/><br/> Když síťové virtuální zařízení brány firewall má menší speciální software než brány WAF, má širší rozsah aplikace můžete filtrovat a kontrolovat libovolného typu v příchozího a odchozího provozu.<br/><br/> Pokud chcete používat síťové virtuální zařízení, najdete je na webu Azure Marketplace.

Doporučujeme používat jednu sadu Azure brány firewall (nebo síťová virtuální zařízení) pro přenosy z Internetu, a jinou pro přenosy pocházející místní.
- Použití jenom jedné sady brány firewall pro obě představuje bezpečnostní riziko, protože nabízí neexistuje žádná bezpečnostní hranice mezi těmito dvěma sadami síťových přenosů.
- Pomocí vrstev samostatnou bránu firewall snižuje složitost pravidel pro kontrolu zabezpečení a je jasné, která pravidla platí pro které příchozí žádosti v síti.

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) pomocí síťových virtuálních zařízení ve virtuální síti Azure.

## <a name="next-steps"></a>Další postup 

Přečtěte si doporučené postupy:

- [Osvědčené postupy](migrate-best-practices-security-management.md) pro zabezpečení a správu po migraci.
- [Osvědčené postupy](migrate-best-practices-costs.md) cost Management po migraci.
