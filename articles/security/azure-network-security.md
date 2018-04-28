---
title: Zabezpečení sítě Azure | Microsoft Docs
description: Další informace o cloudové výpočetní služby, které zahrnují široký výběr výpočetních instancích & služby, které je možné škálovat nahoru či dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: cc64ef8d820db6a072b708323eb110d62ed0a83c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-network-security"></a>Zabezpečení sítě Azure

Víme, že je zabezpečení úlohy, jeden v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepší důvodů, proč používat Azure pro vaše aplikace a služby je využít výhod Azure širokou škálu zabezpečení nástroje a možnosti. Tyto nástroje a možnosti pomoct, aby bylo možné vytvořit zabezpečený řešení na platformě Azure.

Microsoft Azure poskytuje utajení, integrita a dostupnost dat zákazníka, a zároveň umožnit transparentní odpovědnosti za. Vám pomůžou lépe pochopit kolekce ovládacích prvků zabezpečení sítě, která je implementována v rámci Microsoft Azure z hlediska zákazníka, je zapsán tohoto článku, "Azure Network Security", k poskytování komplexní pohled na síti kontrolních mechanismů zabezpečení v Microsoft Azure.

Tento dokument je určený k budete informování o širokou škálu ovládací prvky sítě, které můžete konfigurovat pro zvýšení zabezpečení řešení, které můžete nasadit v Azure. Pokud vás zajímá Microsoft nemá k zabezpečení síťových prostředcích infrastruktury ze samotné platformě Azure, najdete v části zabezpečení Azure v [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Platforma Azure

Azure je platforma služby veřejného cloudu, která podporuje široký výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení.  Může probíhat Linux kontejnery s integrace Dockeru; vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js; sestavení back EndY pro iOS, Android a Windows zařízení. Cloudové služby Azure podporují stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Pokud sestavení nebo IT prostředky se mají migrovat, poskytovatele služeb veřejného cloudu, můžete se spoléhat na dané organizace dalo k ochraně vašich aplikací a dat se službami a ovládací prvky poskytují ke správě zabezpečení vaše cloudové prostředky.

Infrastruktury Azure a k aplikacím pro hostování miliony zákazníků současně slouží ze zařízení a poskytuje trustworthy foundation, na kterém může podnikům splňovat požadavky jejich zabezpečení. Azure navíc poskytuje rozsáhlou sadu možností konfigurovat zabezpečení a umožňuje řídit tak, aby zabezpečení ke splnění jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit.

## <a name="abstract"></a>Abstraktní

Veřejné cloudové služby společnosti Microsoft poskytování služeb flexibilně škálovatelné a infrastruktury, podnikové úrovni funkce a mnoho možností pro hybridní připojení. Můžete přístup k těmto službám prostřednictvím Internetu nebo s Azure ExpressRoute, které poskytuje připojení k privátní síti. Platforma Microsoft Azure umožňuje bezproblémově rozšířit vaši infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho můžete povolit třetím stranám rozšířené možnosti prostřednictvím nabídky zabezpečení služeb a virtuálních zařízení.

Síťové služby Azure maximalizovat flexibilitu, dostupnosti, odolnost proti chybám, zabezpečení a integrity záměrné. Tento dokument poskytuje podrobné informace o síťové funkce Azure a informace o tom, jak zákazníci mohou používat funkce nativní zabezpečení Azure k ochraně svých prostředků informací.

Určený cílové skupiny pro tento dokument White Paper patří:

- Technické správci, správci sítě a vývojáři, kteří hledají řešení zabezpečení dostupným a podporovaným v Azure.

-   Malé a střední podniky nebo obchodní proces vedení, kteří chtějí získat základní přehled Azure síťové technologie a služby, které jsou relevantní diskuze kolem zabezpečení sítě ve veřejném cloudu Azure.

## <a name="azure-networking-big-picture"></a>Velký obrázek sítě Azure
Microsoft Azure obsahuje robustní síťové infrastruktury pro podporu aplikací a služeb požadavky na připojení. Připojení k síti je možné mezi prostředky, které jsou umístěné v Azure, mezi místními a Azure hostovaným prostředkům a do a z Internetu a Azure.

![Velký obrázek sítě Azure](media/azure-network-security/azure-network-security-fig-1.png)

[Azure síťové infrastruktury](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umožňuje bezpečně se vzájemně připojovat prostředky Azure s virtuálními sítěmi (virtuální sítě). Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace sítě cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě může připojit k místní sítě.

Azure podporuje vyhrazené propojení WAN připojení k síti na pracovišti a virtuální síť Azure s [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Propojení mezi Azure a váš web používá vyhrazené připojení, které se nepřenášejí prostřednictvím veřejného Internetu. Pokud vaše aplikace Azure běží v několik datových center, můžete použít [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pro směrování požadavků od uživatelů inteligentně napříč instancí aplikace. Můžete také směrovat provoz do služby nejsou spuštěny v Azure, pokud jsou přístupné z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Zobrazení Enterprise Azure síťovými součástmi
Azure má mnoho síťových součástí, které jsou relevantní pro diskusí zabezpečení sítě. jsme popisují tyto síťovými součástmi a zaměřit se na problémy se zabezpečením s nimi spojeny.

> [!Note]
> Ne všechny aspekty Azure sítě jsou popsány – probereme pouze ty, které se považuje za hrají v plánování a navrhování infrastruktury zabezpečené sítě kolem služby a aplikace, které můžete nasadit v Azure.

V tomto dokumentu bude titulní následující Azure sítě s podnikovými:

-   Základní připojení k síti

-   Hybridní připojení

-   Ovládací prvky zabezpečení

-   Ověření sítě

### <a name="basic-network-connectivity"></a>Základní připojení k síti

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) služby umožňuje bezpečně se vzájemně připojovat prostředky Azure s virtuálními sítěmi (VNet). Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace vyhrazený pro vaše předplatné Azure síťové infrastruktury. Můžete také propojení virtuálních sítí v navzájem a vaše místní sítě pomocí sítě site-to-site VPN a vyhrazené [linky WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Základní připojení k síti](media/azure-network-security/azure-network-security-fig-2.png)

S tím, že používáte virtuální počítače na hostitelských serverech v Azure, otázka se, jak tyto virtuální počítače připojit k síti. Odpověď je, že virtuální počítače připojit k [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Virtuální sítě Azure jsou jako virtuální sítě, můžete použít místní s vlastní řešení virtualizace platformy, jako je například Microsoft Hyper-V nebo VMware.

#### <a name="intra-vnet-connectivity"></a>Připojení Intra-VNet

Virtuální sítě můžete připojit k sobě navzájem, povolení prostředky připojenými k buď virtuální sítě spolu vzájemně komunikovat virtuální sítě. Propojení virtuálních sítí k sobě navzájem, můžete použít jednu nebo obě z následujících možností:

- **Partnerský vztah:** umožňuje prostředky připojenými k jiné sítě Azure Vnet v rámci stejné oblasti Azure pro komunikaci mezi sebou. Šířka pásma a čekací doba mezi virtuální sítí je stejný, jako kdyby prostředky byly připojené ke stejné síti VNet. Další informace o partnerském vztahu [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Partnerské vztahy](media/azure-network-security/azure-network-security-fig-3.png)

- **Připojení VNet-to-VNet:** umožňuje prostředky připojenými k jiné službě Azure VNet v rámci umístění Azure stejný nebo jiný. Na rozdíl od partnerského vztahu, šířka pásma je omezená mezi virtuálními sítěmi, protože provoz musí procházet skrz bránu VPN Azure.

![Připojení VNet-to-VNet](media/azure-network-security/azure-network-security-fig-4.png)


Další informace o připojení virtuální sítě pomocí připojení VNet-to-VNet, najdete [konfigurace článku připojení VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Virtuální síť Azure možnosti:

Jak vidíte, virtuální síť Azure poskytuje virtuální počítače pro připojení k síti, aby se můžete připojit k dalším síťovým prostředkům zabezpečené způsobem. Základní připojení je však jenom začátek. Následující funkce služby Azure Virtual Network vystavení vlastností zabezpečení ve virtuální síti Azure:

-   Izolace

-   Připojení k internetu

-   Připojení prostředků Azure.

-   Připojení virtuální sítě

-   Místní připojení

-   Filtrování provozu

-   Směrování

**Izolace**

Virtuální sítě jsou [izolované](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) od sebe navzájem. Můžete vytvořit samostatné virtuálních sítí patřících pro vývoj, testování a produkci používat stejné [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) bloky adres. Naopak můžete vytvořit více virtuálních sítí, použít jiné bloky adres CIDR a společně připojení sítě. Virtuální síť můžete rozdělit do několika podsítí.

Azure poskytuje interní překlad adres pro virtuální počítače a [cloudové služby](https://azure.microsoft.com/services/cloud-services/) instance rolí připojené k virtuální síti. Volitelně můžete nakonfigurovat virtuální síť použít vlastní servery DNS, místo použití Azure interní překlad adres.

Můžete implementovat více virtuálních sítí v rámci každé Azure [předplatné](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) a Azure [oblast](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Každý virtuální sítě je izolovaná od jiných virtuálních sítí. Pro každý virtuální síť můžete:

-   Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Prostředky Azure přiřadí připojené k virtuální síti privátní IP adresy z adresního prostoru, můžete přiřadit.

-   Segment sítě VNet do jedné nebo více podsítí a přidělovat část adresní prostor virtuální sítě pro každou podsíť.

-   Můžete použít Azure překlad nebo zadejte vlastní server DNS pro použití prostředky, které jsou připojené k virtuální síti. Další informace o překladu názvů v virtuální sítě, najdete [překlad názvů pro virtuální počítače a cloudové služby](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Připojení k Internetu**

Všechny [Azure virtuální počítače (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) a instance rolí cloudové služby připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům. (VM) a instancí rolí cloudové služby připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

Všechny prostředky, které jsou připojené k virtuální síti mají ve výchozím nastavení odchozí připojení k Internetu. Privátní IP adresu prostředku je zdrojová síťová adresa přeložit (překládat pomocí SNAT) na veřejnou IP adresu pomocí infrastruktury Azure. Implementací vlastní směrování a filtrování provozu, můžete změnit výchozí připojení. Další informace o odchozí připojení k Internetu, přečtěte si [pochopení odchozí připojení v Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pro komunikaci příchozí prostředky Azure z Internetu, nebo k Internetu bez překládat pomocí SNAT odchozí komunikaci, musí prostředek přiřazenou veřejnou adresu IP. Další informace o veřejné IP adresy, přečtěte si [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Připojení prostředků Azure.**

[Prostředky Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) například cloudových služeb a virtuálních počítačů může být připojen k stejnou virtuální síť. Prostředky můžete připojit k sobě navzájem pomocí privátních IP adres, i když jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuálními sítěmi a místními sítěmi, takže není nutné konfigurovat a spravovat trasy.

Několik prostředků Azure můžete připojit k virtuální síti, jako jsou virtuální počítače (VM), cloudové služby, prostředí App Service a sady škálování virtuálního počítače. Virtuální počítače připojit k podsítí v rámci virtuální sítě přes síťové rozhraní (NIC). Další informace o síťové karty, najdete [síťových rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Připojení virtuální sítě**

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) lze připojit k sobě navzájem, povolení prostředky připojenými k žádné virtuální sítě pro komunikaci s jakýmikoli prostředky na ostatní virtuální sítě.

Virtuální sítě můžete připojit k sobě navzájem, povolení prostředky připojenými k buď virtuální sítě spolu vzájemně komunikovat virtuální sítě. Propojení virtuálních sítí k sobě navzájem, můžete použít jednu nebo obě z následujících možností:

- **Partnerský vztah:** umožňuje prostředky připojenými k jiné sítě Azure Vnet v rámci stejné oblasti Azure pro komunikaci mezi sebou. Šířka pásma a čekací doba mezi virtuální sítě je stejný jako kdyby byly prostředky připojené k stejné VNet.To Další informace o partnerském vztahu, přečtěte si [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Připojení VNet-to-VNet:** umožňuje prostředky připojenými k jiné službě Azure VNet v rámci umístění Azure stejný nebo jiný. Na rozdíl od partnerského vztahu, šířka pásma je omezená mezi virtuálními sítěmi, protože provoz musí procházet skrz bránu VPN Azure. Další informace o připojení virtuální sítě pomocí připojení VNet-to-VNet. Další informace, přečtěte si [konfigurace připojení typu VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Místní připojení**

Virtuální sítě může být připojen k [místní](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sítě prostřednictvím privátní sítě připojení mezi vaší sítí a Azure, nebo připojení site-to-site VPN přes Internet.

Do místní sítě lze připojit k virtuální síti pomocí libovolnou kombinaci těchto možností:

- **Point-to-site virtuální privátní sítě (VPN):** navázat mezi jeden počítač připojený k síti a virtuální sítě. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Připojení k poskytování šifrovanou komunikaci prostřednictvím Internetu mezi Počítačem a virtuální sítě používá protokol SSTP. Latence pro síť VPN point-to-site nepředvídatelným, protože přenosy dat prochází přes Internet.

- **Site-to-site VPN:** mezi zařízení VPN a služby Azure VPN Gateway. Tento typ připojení umožňuje jakémukoli prostředku místní autorizaci pro přístup k virtuální síti. Připojení je VPN pomocí protokolu IPsec/IKE, která poskytuje šifrovanou komunikaci prostřednictvím Internetu mezi místní zařízení a brána Azure VPN. Latence pro připojení site-to-site nepředvídatelným, protože přenosy dat prochází přes Internet.

- **Azure ExpressRoute:** Vytváří se mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Provoz neprochází Internetu. Latence pro připojení typu ExpressRoute je předvídatelný, protože provoz není procházení Internetu. Další informace o všech předchozích možností připojení, přečtěte si [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrování přenosu**

Instance role virtuálního počítače a cloudové služby [přenos v síti](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) je možné filtrovat příchozí a odchozí zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupin zabezpečení (NSG) sítě:** každou NSG může obsahovat více pravidel příchozí a odchozí zabezpečení, které umožňují filtrovat provoz ve zdrojové a cílové IP adresy, portu a protokolu. Můžete použít skupinu NSG pro každý síťový adaptér ve virtuálním počítači. Můžete taky použít skupinu NSG k podsíti síťový adaptér nebo jiných prostředků Azure je připojen k. Další informace o skupinách Nsg, najdete [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtuální síťová zařízení:** zařízení virtuální sítě je virtuální počítač spuštěný software, který provádí síťové funkce, jako je například Brána firewall. Zobrazte seznam dostupných NVAs v Azure Marketplace. NVAs jsou také k dispozici, které poskytují optimalizace sítě WAN a jiných síťových přenosů funkce. NVAs jsou obvykle používány s uživatelem nebo trasy protokolu BGP. Hodnocení chyb zabezpečení můžete také použít k filtrování provozu mezi virtuálními sítěmi.

**Směrování**

Volitelně můžete přepsat výchozí Azure směrování konfiguraci vlastních tras, nebo pomocí trasy protokolu BGP prostřednictvím brány sítě.

Azure vytvoří směrovací tabulky, které umožňují prostředky připojenými k žádné podsíti v žádné virtuální sítě pro komunikaci mezi sebou, ve výchozím nastavení. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Trasy definované uživatelem:** můžete vytvořit vlastní směrovací tabulky s trasami, které tuto kontrolu, kde provoz se směruje na pro každou podsíť. Další informace o trasy definované uživatelem, přečtěte si [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Trasy protokolu BGP:** Pokud virtuální sítě se připojit k místní síti pomocí připojení k Azure VPN Gateway nebo ExpressRoute, můžete rozšířit směrování BGP do vaší virtuální sítě.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybridní připojení k Internetu: připojení k místní síti
Do místní sítě lze připojit k virtuální síti pomocí libovolnou kombinaci těchto možností:

-   Připojení k internetu

-   Síť VPN Point-to-site (P2S VPN)

-   VPN typu Site-to-Site (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Připojení k Internetu

Jak naznačuje název připojení k Internetu, zpřístupní úlohy z Internetu, tak, že můžete zpřístupnit různé veřejných koncových bodů pro procesy, které za provozu ve virtuální síti. Tyto úlohy mohou být vystaveny pomocí [nástroj pro vyrovnávání zatížení internetového](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) nebo jednoduše přiřadit veřejnou IP adresu do virtuálního počítače. Tímto způsobem bude možné pro všechno, co je na Internetu, aby bylo možné dosáhnout tomto virtuálním počítači, poskytuje bránu firewall hostitele, [skupin zabezpečení (NSG) sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), a [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) povolit, aby dojít.

V tomto scénáři může zveřejnit aplikaci, která musí být veřejné k Internetu a budou moci připojovat k němu z kdekoli nebo z určitých umístění v závislosti na konfiguraci úlohy.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Point-to-Site VPN nebo VPN typu Site-to-Site
Tyto dva, které patří do stejné kategorii. Obě potřebují mít bránu VPN virtuální síť a můžete připojit se pomocí buď klienta VPN pro pracovní stanice v rámci [KonfiguracePoint-to-Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) nebo můžete konfigurovat místní [zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) moct ukončit site-to-site VPN. Tímto způsobem místní zařízení může připojit k prostředkům v rámci virtuální sítě.

Konfigurace Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení jednotlivých klientských počítačů k virtuální síti. P2S je připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol).

![Point-to-Site VPN](media/azure-network-security/azure-network-security-fig-5.png)

Připojení point-to-Site jsou užitečné, pokud se chcete připojit k virtuální síti ze vzdáleného umístění, například z domova nebo centrum konference, nebo pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení typu P2S nevyžadují zařízení VPN ani veřejnou IP adresu. Připojení VPN se navazuje ze strany klientského počítače. Proto P2S se nedoporučuje způsob, jak se připojit k Azure v případě, že je nutné z mnoha místní zařízení a počítačů trvalé připojení k síti Azure.

![Site-to-Site VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Další informace o připojení Point-to-Site najdete v tématu [Point-to-Site DM v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2).

Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Toto připojení probíhá přes Internet a umožňuje informace "tunel" uvnitř šifrované odkaz mezi vaší sítí a Azure. Site-to-site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. Tunelové propojení šifrování se provádí pomocí [režimu tunelového připojení IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Při site-to-site VPN je důvěryhodné, spolehlivým a zavedených technologie, provoz v rámci tunelu procházení Internetu. Kromě toho šířka pásma je relativně omezená na maximálně přibližně 200 MB/s.

Pokud budete potřebovat výjimečných úroveň zabezpečení a výkonu pro připojení mezi různými místy, doporučujeme použít Azure ExpressRoute pro připojení mezi různými místy. ExpressRoute je vyhrazené sítě WAN propojení mezi místní umístění nebo poskytovatele hostingu serveru Exchange. Protože se jedná o telco připojení, vaše data není přenášet přes Internet a proto není viditelné na potenciální rizika spojená s internetovou komunikaci.

> [!Note]
> Další informace o branách VPN najdete v tématu [brány VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Vyhrazené propojení WAN
Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do Azure přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

![ Vyhrazené propojení WAN](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Informace o tom, jak připojit síť k Microsoftu pomocí ExpressRoute najdete v tématu [modelů připojení ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) a [technický přehled ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Jako s možnostmi site-to-site VPN, ExpressRoute vám také umožní připojit se k prostředkům, které nejsou nezbytně jenom jeden virtuální sítě. V závislosti na SKU, ve skutečnosti může připojit k 10 virtuálních sítí. Pokud máte [doplněk premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), je možné, v závislosti na šířku pásma, připojení k virtuálním sítím až 100. Další informace o co tyto typy připojení vzhled líbí, přečtěte si [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Ovládací prvky zabezpečení
Virtuální síť Azure poskytuje zabezpečené, logickou síť, která je izolovaná od jiných virtuálních sítí a podporuje mnoho ovládací prvky zabezpečení, která se používá ve vaší místní sítě. Zákazníci vytvořit své vlastní struktura pomocí: podsítě – budou používat svoje vlastní rozsah privátních IP adres, konfigurace směrovacích tabulek, skupin zabezpečení sítě a seznamy řízení přístupu (ACL), bran a virtuálních zařízení spuštění jejich úloh v cloudu.

Následují kontrolní mechanismy zabezpečení, které můžete použít na virtuálních sítí Azure:

-   Ovládací prvky pro přístup k síti

-   Trasy definované uživatelem

-   Síťové zařízení zabezpečení

-   Application Gateway

-   Brány Firewall Azure webových aplikací

-   Řízení dostupnost sítě

#### <a name="network-access-controls"></a>Ovládací prvky pro přístup k síti
Zatímco Azure Virtual Network (VNet) je základním kamenem Azure model pro sítě a poskytuje izolace a ochrany, [skupiny zabezpečení sítě (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) jsou hlavní nástroj slouží k vynucení a řídit pravidla pro provoz sítě na úrovni sítě.

![ Ovládací prvky pro přístup k síti](media/azure-network-security/azure-network-security-fig-8.png)


Můžete řídit přístup k povolení nebo odepření komunikace mezi zatížení v rámci virtuální sítě, ze systémů v sítích zákazníka prostřednictvím připojení mezi různými místy, nebo přímé komunikaci v síti Internet.

V diagramu virtuálních sítí a skupin Nsg nacházet v určité vrstvy v Azure celkového zabezpečení zásobníku, kde skupiny Nsg, UDR a síťových virtuálních zařízení slouží k vytvoření hranice zabezpečení k ochraně nasazení aplikací v síti chráněné.

Skupiny Nsg slouží k vyhodnocení, provoz 5-n-tice (a používají pravidla, které nakonfigurujete pro NSG):

-   [Zdrojové a cílové IP adresy](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Zdrojový a cílový port](https://technet.microsoft.com/library/dd197515)

-   Protokol: [protokol Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) nebo [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

To znamená, že můžete řídit přístup mezi jeden virtuální počítač a skupinu virtuálních počítačů nebo jeden virtuální počítač do jiného jeden virtuální počítač, nebo mezi celý podsítě. Znovu mějte na paměti, že toto je jednoduchá stavových paketů filtrování, kontroly paketů není úplná. Neexistuje žádné ověřovací protokol nebo ID úrovně sítě nebo IP adresy funkci skupinu zabezpečení sítě.

Skupina NSG obsahuje některá vestavěné pravidla, které byste měli vědět. Jsou to:

-   **Povolují veškerý provoz v rámci konkrétní virtuální sítě:** všechny virtuální počítače ve stejné virtuální sítě Azure můžete komunikaci mezi sebou.

-   **Povolit Azure rozložení zátěže pro příchozí:** toto pravidlo umožňuje přenosy z jakékoli zdrojové adresy na všechny cílové adresy pro vyrovnávání zatížení Azure.

-   **Odepřít všechny příchozí:** toto pravidlo blokuje veškerý provoz sourcing z Internetu, které mají explicitně povolená.

-   **Povolují veškerý provoz odchozí k Internetu:** toto pravidlo umožňuje virtuální počítače, aby inicializoval připojení k Internetu. Pokud nechcete, aby byla tato připojení spustil, budete muset vytvořit pravidlo na blokování tato připojení nebo vynutit vynucené tunelování.

#### <a name="system-routes-and-user-defined-routes"></a>Systémové trasy a trasy definované uživatelem

Když přidáte virtuální počítače (VM) k virtuální síti (VNet) v Azure, si všimnete, že jsou virtuální počítače moci komunikovat navzájem v síti automaticky. Není nutné určit bránu, ani když jsou virtuální počítače v různých podsítích.

Totéž platí pro komunikaci z virtuálních počítačů do veřejného internetu, a dokonce i do vaší místní sítě, pokud je dostupné hybridní připojení z Azure do vlastního datacentra.

![Systémové trasy](media/azure-network-security/azure-network-security-fig-9.png)

Tento tok komunikace je možný díky tomu, že Azure pomocí řady systémových tras definuje toky provozu IP. Systémové trasy řídí tok komunikace v těchto scénářích:

-   Ze stejné podsítě.

-   Z jedné podsítě do jiné v rámci jedné virtuální sítě.

-   Z virtuálních počítačů do internetu.

-   Z jedné virtuální sítě do jiné prostřednictvím brány sítě VPN.

-   Z virtuální sítě k jiné virtuální síti prostřednictvím sítě VNet partnerského vztahu ([služby řetězení](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Z virtuální sítě do místní sítě prostřednictvím brány sítě VPN.

Mnoho podniků má striktní zabezpečení a dodržování předpisů požadavky, které vyžadují místní kontroly všech síťových paketů vynutit konkrétní zásady. Azure poskytuje mechanismus názvem [vynuceného tunelování](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) který směruje provoz z virtuálních počítačů k místnímu vytvořením vlastní trasy nebo [protokol BGP (Border Gateway)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) oznámení o inzerovaném programu prostřednictvím ExpressRoute nebo VPN.

Vynucené tunelování v Azure je nakonfigurován pomocí virtuální síti trasy definované uživatelem (UDR). Přesměrování přenosů na místní web je vyjádřen jako výchozí směrování k bráně Azure VPN.

V následující části jsou uvedené aktuální omezení směrovací tabulku a trasy pro virtuální síť Azure:

-   Každá podsíť virtuální sítě má integrovanou, směrovací tabulky systému. Systémovou tabulku směrování má tyto tři skupiny tras:

 -  **Místní virtuální síť trasy:** přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti

 - **Pro místní trasy:** k Azure VPN gateway

 -  **Výchozí trasu:** přímo k Internetu. Dojde ke ztrátě paketů určené na privátní IP adresy, které nejsou pokryty předchozí dva trasy.

-   S vydáním trasy definované uživatelem můžete vytvořit směrovací tabulku, která chcete přidat výchozí trasu a pak přidružit do směrovací tabulky do dané podsítě virtuální sítě povolit vynucené tunelování na těchto podsítí.

-   Budete muset nastavit "výchozí web" mezi místní lokality mezi různými místy připojený k virtuální síti.

-   Vynucené tunelování musí být přidruženy k virtuální síti, která má dynamické směrování brána sítě VPN (není statická brána).

- ExpressRoute vynuceného tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho je ve inzeruje výchozí trasu prostřednictvím relace partnerského vztahu ExpressRoute BGP povolen.

> [!Note]
> Další informace najdete v tématu [dokumentace ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Další informace.

#### <a name="network-security-appliances"></a>Sítě zabezpečovací zařízení
Při skupiny zabezpečení sítě a trasy definované uživatelem může poskytnout míru zabezpečení sítě v síti a přenosu vrstvy [OSI model](https://en.wikipedia.org/wiki/OSI_model), budou existovat být situace, kdy má být nebo muset povolit zabezpečení s vyšší úrovní sadu síťových protokolů. V takových situacích doporučujeme nasadit virtuální sítě zabezpečovací zařízení poskytovaných Azure partnery.

![Sítě zabezpečovací zařízení](./media/azure-network-security/azure-network-security-fig-10.png)

Síť Azure zabezpečovací zařízení zlepšit zabezpečení virtuální sítě a síťových funkcí a jsou k dispozici od více dodavatelů prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com). Tyto virtuální zabezpečovací zařízení je možné nasadit zajistit:

-   Vysoce dostupné brány firewall

-   Prevence vniknutí

-   Zjišťování neoprávněných vniknutí

-   Brány firewall systému webové aplikace (WAFs)

-   Optimalizace sítě WAN

-   Směrování

-   Vyrovnávání zatížení

-   Síť VPN

-   Správa certifikátů

-   Active Directory

-   Vícefaktorové ověřování

#### <a name="application-gateway"></a>Application Gateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) je vyhrazené virtuální zařízení poskytující doručení řadič aplikace (ADC) jako služba.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Aplikační brána umožňuje optimalizovat výkon webové farmy a dostupnost přesměrováním zátěže procesoru náročné ukončení protokolu SSL pro službu application gateway (snižování zátěže-protokolu SSL). Poskytuje také další vrstvy 7 směrování funkcí, včetně:

-   Kruhové dotazování distribuce příchozí provoz

-   Spřažení na základě souboru cookie relace

-   Na základě cestu směrování adres URL

-   Umožňuje hostování více webů za jeden Application Gateway


A [brány firewall webových aplikací (firewall webových aplikací)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) je k dispozici jako součást služby application gateway. To poskytuje ochranu k webovým aplikacím z běžných chyb zabezpečení webové a zneužití. Application Gateway můžete nakonfigurovat jako internetovou bránu, interní pouze brány nebo obojí.

Aplikace brány firewall webových aplikací můžete spustit v režimu zjišťování nebo prevence. Běžné případ použití je pro správce, aby běžel v režimu detekce sledovat provoz pro škodlivé vzorce. Po zjištění potenciálních zneužití, zapnutí do režimu prevence blokování podezřelé příchozí provoz.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Kromě toho aplikace brány firewall webových aplikací umožňuje monitorovat webové aplikace před útoky pomocí protokolu v reálném čase firewall webových aplikací, které jsou integrovány s [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) a [Azure Security Center](https://azure.microsoft.com/services/security-center/) snadno sledovat trendy a sledovat výstrahy firewall webových aplikací.

Formátovaný protokolu JSON přejde přímo na účet úložiště zákazníka. Mít plnou kontrolu nad tyto protokoly a můžete použít vlastní zásady uchovávání informací.

Tyto protokoly můžete také ingestování do vlastní analytics systém pomocí [integrace se službou Azure protokolu](https://aka.ms/AzLog). Protokoly firewall webových aplikací taky jsou integrované s [analýzy protokolů](../log-analytics/log-analytics-overview.md) , analýzy protokolů můžete použít k provedení sofistikované podrobných dotazy.

#### <a name="azure-web-application-firewall-waf"></a>Brány firewall Azure webových aplikací (firewall webových aplikací)

Webové aplikace jsou stále cíle nebezpečné útoky, které využívají běžné známých chyb zabezpečení, například vkládání, křížové útoky skriptování a další útoky, které se zobrazují v SQL [OWASP prvních 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Zabránění takové zneužití v aplikaci vyžaduje přísných Údržba, opravy a monitorování v několika vrstev topologie aplikace.

 ![Brány Firewall Azure webových aplikací (firewall webových aplikací)](./media/azure-network-security/azure-network-security-fig-13.png)

Centralizované [brány firewall webových aplikací (firewall webových aplikací)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) může chránit proti útokům na web a zjednodušuje správu zabezpečení bez nutnosti změny aplikace.

Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

#### <a name="network-availability-controls"></a>Ovládací prvky dostupnost sítě

Existují různé možnosti distribuce síťového provozu pomocí systému Microsoft Azure. Tyto možnosti fungují různě, obsahují jiné sady funkcí a podporují různé scénáře. Lze je používat jednotlivě nebo je kombinovat.

Ovládací prvky dostupnosti sítě jsou následující:

-   Nástroj pro vyrovnávání zatížení Azure

-   Application Gateway

-   Traffic Manager

**Azure nástroj pro vyrovnávání zatížení**

Vaše aplikace přináší vysoké dostupnosti a výkonu v síti. Je Vyrovnávání zatížení vrstvy 4 (TCP, UDP), která distribuuje příchozí komunikaci mezi pořádku instancí služby definované v sadě s vyrovnáváním zatížení.

 ![Nástroj pro vyrovnávání zatížení Azure](media/azure-network-security/azure-network-security-fig-14.png)


Azure nástroj pro vyrovnávání zatížení můžete nakonfigurovat:

-   Načtěte vyrovnávání příchozí přenosy z Internetu k virtuálním počítačům. Tato konfigurace se označuje jako [Vyrovnávání zatížení internetového](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Přenosy Vyrovnávání zatížení mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudové služby nebo mezi místními počítači a virtuální počítače ve virtuální síti mezi různými místy. Tato konfigurace se označuje jako [Vyrovnávání zatížení pro vnitřní](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Předat dál externích přenosů pro konkrétní virtuální počítač.

Všechny prostředky v cloudu potřebovat veřejnou IP adresu, která být dosažitelný z Internetu. Infrastruktura cloudu v Azure používá směrovat IP adresy pro její prostředky. Azure používá překlad síťových adres (NAT) s veřejné IP adresy pro komunikaci k Internetu.

 **Aplikační brány**

 Application Gateway pracuje na aplikační vrstvu (7 vrstvy v zásobníku odkaz OSI sítě). Funguje jako služba reverzních proxy serverů, ukončuje připojení klienta a předává požadavky do back-endových koncových bodů.

 **Správce provozu**

Microsoft Azure Traffic Manager umožňuje řízení distribuce provozu generovaného uživateli pro koncové body služby v různých datových centrech. Koncové body služby, které jsou podporovány nástrojem Traffic Manager zahrnují virtuálních počítačích Azure, webové aplikace a cloudových služeb. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure.

Traffic Manager používá v systému DNS (Domain Name) pro přesměrování požadavků klientů na základě nejvhodnější koncový bod [metodu směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) a stav koncových bodů. Traffic Manager poskytuje celou řadu metod směrování provozu tak, aby vyhovovala potřebám jiné aplikace, koncový bod stavu [monitorování](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)a automatické převzetí služeb při selhání. Správce provozu je odolný proti selhání, včetně selhání celé oblasti Azure.

Azure Traffic Manager umožňuje řízení distribuce provozu napříč koncovými body vaší aplikace. Koncový bod je všechny internetové služby hostované uvnitř nebo mimo Azure.

Správce provozu přináší dvě klíčové výhody:

-   Distribuce přenos v závislosti na jednu z několika [metody směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Nepřetržité monitorování stavu koncový bod](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) a automatické převzetí služeb při selhání koncových bodů.

Když klient se pokusí připojit ke službě, se musí nejprve přeložit název DNS služby na adresu IP. Klient pak připojí k tuto IP adresu pro přístup ke službě. Přesměrovat klienty na konkrétní službu koncové body na základě pravidel metodu směrování provozu Traffic Manageru pomocí služby DNS. Klienti připojovat přímo k vybrané koncový bod. Traffic Manager není serveru proxy nebo brána. Správce provozu nezná provozu mezi klientem a služby předávání.

### <a name="azure-network-validation"></a>Ověření síť Azure

Ověření síť Azure, je potřeba zajistit, že síť Azure pracuje jako je nakonfigurován a lze provést ověření pomocí služby a funkce, které jsou k dispozici pro monitorování sítě. Sledovací proces sítě Azure, dostanete nadbytku protokolování a diagnostické funkce, které můžete umožnit příslušným Statistika zjistit stav a výkon sítě. Tyto možnosti jsou dostupné přes portál, prostředí PowerShell, rozhraní příkazového řádku, Rest API a sady SDK.

Zabezpečení provozu Azure se odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure. Zabezpečení provozu Azure je založený na rozhraní, které zahrnuje poznatky získané při různých možnostech, které jsou jedinečné pro společnosti Microsoft, včetně Microsoft SDL Security Development Lifecycle (), programu Microsoft zabezpečení odpovědi Centre a hloubkové povědomí o povahu hrozeb kybernetického zabezpečení.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure resource Manageru

Uživatelé a procesy, které provozují Microsoft Azure jsou možná nejdůležitější funkce zabezpečení platformy. Tato část popisuje funkce společnosti Microsoft globální infrastruktuře datacenter, které pomůžou vylepšit a spravovat zabezpečení, kontinuitu a ochrany osobních údajů.

Infrastrukturu pro vaše aplikace obvykle tvoří celá řada komponent, může být virtuální počítač, účet úložiště a virtuální sítě, nebo webové aplikace, databáze, databázový server a služby třetích stran. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. Azure Resource Manager umožňuje pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manager**

Resource Manager poskytuje několik výhod:

-   Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

-   Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

-   Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

-   Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

-   Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

-   Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

-   Zobrazením náklady pro skupinu prostředků, které sdílejí značky můžete zpřehlednit fakturaci vaší organizace.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste využívali dřívější model nasazení a chcete se dozvědět o změnách, přečtěte si článek věnovaný [nasazení Resource Manageru a klasickému nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Síť Azure protokolování a monitorování

Azure nabízí celou řadu nástrojů pro monitorování, zabránit, zjišťování a reakce na události zabezpečení sítě. Některé z nejúčinnějších nástrojů, které jsou k dispozici v této oblasti patří:

-   Network Watcher

-   Úrovně monitorování sítě prostředků

-   Log Analytics

### <a name="network-watcher"></a>Sledovací proces sítě

[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – na základě scénáře monitorování se poskytuje s funkcí v sledovací proces sítě. Tato služba obsahuje zachytáváním paketů, další směrování, IP tok ověření, zobrazení skupiny zabezpečení, tok protokolů NSG. Scénář úrovně monitorování poskytuje pohledu koncové síťových prostředků, na rozdíl od monitorování jednotlivých síťových prostředků.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Sledovací proces sítě je místní služba, která umožňuje sledovat a diagnostikovat podmínky na úrovni scénář sítě, do a z Azure. Diagnostika sítě a k dispozici sledovací proces sítě vizualizace nástroje vám pomůžou pochopit, diagnostikovat a získáte přehled o k síti v Azure.

Sledovací proces sítě aktuálně má následující možnosti:

#### <a name="topology"></a>Topologie

[Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) vrátí graf síťovým prostředkům ve virtuální síti. Graf znázorňuje propojení mezi prostředky představující koncové síťové připojení. Na portálu vrátí topologie objektů prostředků na podle základ virtuální sítě. Vztahy, jsou použité v ukázkách linkami mezi prostředky mimo oblast sledovací proces sítě, i když v prostředku skupiny se nezobrazí. Prostředky, vrátí se v zobrazení portálu, jsou podmnožinou síťové součásti, které vykreslovacích. Pokud chcete zobrazit úplný seznam síťové prostředky, můžete použít [prostředí PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) nebo [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Jako prostředky jsou vráceny připojení mezi jejich jsou modelovat v dva vztahy.

- **Členství ve skupině** -virtuální síť obsahuje podsíť, která obsahuje síťový adaptér.

- **Související** – síťový adaptér A je přidružený virtuální počítač.

#### <a name="variable-packet-capture"></a>Zachytáváním paketů proměnné

Sledovací proces sítě [zachytáváním paketů proměnné](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) vám umožní vytvořit relace zachytávání paketů sledovat provoz do a z virtuálního počítače. Pomáhá zachytávání paketů při diagnostice sítě anomálií reaktivně a proactivity. Jiné účely zahrnují shromažďování statistiku sítě, získá informace o síti vniknutí, k ladění komunikaci klienta se serverem a mnoho dalšího.

Zachytáváním paketů je rozšíření virtuálního počítače, který se spustil vzdáleně přes sledovací proces sítě. Tato funkce snižuje zátěž spuštěných zachytáváním paketů ručně na požadovaný virtuální počítač, který úspora času. Zachytáváním paketů můžete spustit prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Je jeden příklad, jak můžete spustit zachytáváním paketů s výstrahami virtuálního počítače.

#### <a name="ip-flow-verify"></a>Ověřte toku IP

[Ověřte IP toky](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) zkontroluje, jestli je paket povolen nebo odepřen do nebo z virtuálního počítače na základě informací o 5 řazené kolekce členů. Tyto informace se skládá z směr, protokol, místní IP, vzdálené IP, místního portu a vzdáleného portu. Pokud paketu je zakázané skupiny zabezpečení, je vrácen název pravidla, které odepřen paketu. Zatímco můžete vybrat všechny zdrojové i cílové adresy IP, tato funkce vám pomůže správci rychle diagnostikovat problémy s připojením z nebo na Internet a z nebo na místním prostředí.

Toky IP ověřte cílem síťové rozhraní virtuálního počítače. Tok přenosů dat je pak ověřit podle nakonfigurovaného nastavení do nebo z rozhraní sítě. Tato možnost je užitečná při potvrzení, pokud pravidla v skupinu zabezpečení sítě blokuje příchozí nebo odchozí provoz nebo z virtuálního počítače.

#### <a name="next-hop"></a>Další směrování

Určuje, [další segment](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pro pakety směrovány v prostředcích infrastruktury sítě Azure, takže umožňuje diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem. Přenosy z virtuálního počítače se odesílají do cílového umístění v závislosti na účinné postupy spojené s síťový adaptér. Další směrování získá typ dalšího směrování a IP adresa z paketu z konkrétní virtuální počítač a síťový adaptér. To pomůže určete, jestli je paket směrovat do cílového umístění nebo je provoz probíhá černým dírkového.

Další směrování také vrátí hodnotu směrovací tabulka přidružené k dalším místě směrování. Při dotazování další směrování, pokud trasy, která je definována jako trasy definované uživatelem, bude vrácen danou trasu. V opačném případě vrátí další segment "Systémová trasa".

#### <a name="security-group-view"></a>zobrazení skupiny zabezpečení

Získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači. Skupiny zabezpečení sítě na úrovni podsítě nebo na úrovni seskupování souvisejí. Když přidružené na úrovni podsítě, bude se vztahovat na všechny instance virtuálního počítače v podsíti. Síť [zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) vrátí nakonfigurované skupiny Nsg a pravidel, které jsou přidružené úrovni síťových Adaptérů a podsítě pro virtuální počítač poskytuje přehled o konfiguraci. Kromě toho pravidla efektivní zabezpečení jsou vráceny pro jednotlivé síťové adaptéry ve virtuálním počítači. Zobrazení pomocí skupiny zabezpečení sítě, můžete vyhodnotit virtuální počítač chyb zabezpečení sítě, jako je například otevřené porty. Můžete také ověřit, pokud vaše skupina zabezpečení sítě funguje podle očekávání, na základě [srovnání nakonfigurované a pravidla efektivní zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Protokolování toku NSG

 Tok protokoly pro skupinu zabezpečení sítě umožňují zaznamenat protokoly související s přenosy, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informací o 5-n-tice – zdrojové adresy IP, cílovou IP adresu, zdrojový Port, cílový Port a protokol.

[Skupina zabezpečení sítě toku protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkce sledovací proces sítě, která vám umožní zobrazit informace o příchozí a odchozí provoz IP prostřednictvím skupinu zabezpečení sítě.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Brána virtuální sítě a odstraňování problémů s připojením

Sledovací proces sítě obsahuje řadu funkcí ve vztahu k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. [Řešení potíží s prostředků](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) nelze volat pomocí prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Při volání, sledovací proces sítě kontroluje stav brány virtuální sítě nebo připojení a vrátí nalezených výsledcích.

Tato část vás provede úloh jiný správy, které jsou aktuálně dostupné pro řešení potíží s prostředků.

-   [Řešení potíží s bránu virtuální sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Vyřešte potíže připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limity předplatného sítě

[Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytují podrobnosti o využití jednotlivých síťových prostředků v předplatném v oblasti pro maximální počet prostředků, které jsou k dispozici.

#### <a name="configuring-diagnostics-log"></a>Konfiguraci diagnostiky protokolu

Poskytuje sledovací proces sítě [diagnostické protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) zobrazení. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťových prostředků, snadno a rychle.

### <a name="network-resource-level-monitoring"></a>Úrovně monitorování sítě prostředků

Následující funkce jsou k dispozici pro monitorování na úrovni prostředků:

#### <a name="audit-log"></a>Protokol auditu

Operace provedené v rámci konfigurace sítí přihlášeni. Tyto protokoly auditu je nezbytné k navázání různých compliances. Tyto protokoly můžete zobrazit na portálu Azure nebo pomocí nástroje Microsoft, jako je Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku a Rest API.

> [!Note]
> Další informace o protokolů auditu najdete v tématu [auditovat operace s Resource Managerem](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Protokoly auditu jsou k dispozici pro operace udělat na všechny síťové prostředky.


#### <a name="metrics"></a>Metriky

Metriky jsou měření výkonu a čítače shromážděných za období. Metriky jsou aktuálně dostupné pro službu Application Gateway. Metriky lze použít k aktivaci výstrahy podle prahovou hodnotu. Ve výchozím nastavení služba Azure Application Gateway monitoruje stav všechny prostředky v jeho fond back-end a automaticky odebere jakémukoli prostředku, považoval za poškozený z fondu. Aplikační brána pokračuje v monitorování není v pořádku instancí a přidá je zpět do fondu back-end v pořádku, jakmile jsou k dispozici a reagovat na sondy stavu. Aplikační brána odešle sondy stavu s stejný port, který je definován v nastavení HTTP back-end. Tato konfigurace zajistí, že tato kontrola je testování stejný port, který zákazníky se používají pro připojení k back-end.

> [!Note]
> V tématu [Application Diagnostics brány](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) zobrazíte použití metriky pro vytvoření výstrahy.

#### <a name="diagnostic-logs"></a>Diagnostické protokoly

Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště, odeslané do centra událostí, nebo analýzy protokolů. Tyto protokoly poskytují přehled o stavu prostředku. Tyto protokoly můžete zobrazit v nástrojů, jako je Power BI a analýzy protokolů. Další postup zobrazení diagnostických protokolů najdete [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnostické protokoly jsou k dispozici pro [nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Sledovací proces sítě poskytuje že diagnostické protokoly zobrazení. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťových prostředků, snadno a rychle.

### <a name="log-analytics"></a>Log Analytics

[Analýza protokolu](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je služba v Azure, která monitoruje své cloudové a místní prostředí k udržování své dostupnosti a výkonu. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

Analýzy protokolů nabízí následující řešení pro monitorování vaší sítí:

-   Sledování výkonu sítě (NPM)

-   Analýza Azure Application Gateway

-   Skupina zabezpečení sítě Azure analytics

#### <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)
[Sledování výkonu sítě](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) řešení pro správu je síť řešení monitorování, které sleduje stav, dostupnosti a dostupnosti sítě.

Se používá k monitorování připojení mezi:

-   veřejný cloud a místní

-   datových center a umístění uživatele (firemních pobočkách)

-   podsítě hostování různé úrovně víceúrovňových aplikací.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Analýza brány Azure aplikace v analýzy protokolů

Tyto protokoly jsou podporovány pro Application Gateway:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Pro Application Gateway se podporují následující metriky:

-   propustnost 5 minut

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Analytické údaje skupiny zabezpečení sítě Azure v analýzy protokolů

Tyto protokoly jsou podporovány pro [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** obsahuje položky, pro které skupina NSG pravidla, použije se pro virtuální počítače a instance rolí na základě adresy MAC. Stav pro tato pravidla se shromažďují každých 60 sekund.

- **NetworkSecurityGroupRuleCounter:** obsahuje položky pro jednotlivé skupiny NSG počet použití pravidla se aplikuje zakázat nebo povolit provoz.

## <a name="next-steps"></a>Další postup
Další informace o zabezpečení načtením některá témata s našimi podrobné zabezpečení:

-   [Analýzy protokolů pro skupinu zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Sítě inovací, které jednotka přerušení cloudu](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Sítě přepínač softwaru této zajišťuje globální cloudu Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Jak Microsoft sestavení jeho rychlé a spolehlivé globální sítě](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Osvětlení až inovací sítě](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
