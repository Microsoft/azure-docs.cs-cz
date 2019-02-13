---
title: Zabezpečení sítě Azure | Dokumentace Microsoftu
description: Přečtěte si o cloudové výpočetní služby, které zahrnují široké škály výpočetních instancí a služby, které je možné škálovat směrem nahoru a dolů automaticky podle potřeb vaší aplikace nebo enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: a405583503b75a64dda2bf277a4a50be4e926d28
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111284"
---
# <a name="azure-network-security"></a>Zabezpečení sítě Azure

Víme, že je zabezpečení stavbách v cloudu a jak důležité je, že zjistíte přesné a aktuální informace o zabezpečení Azure. Jedním z nejlepších důvody pro použití Azure pro vaše aplikace a služby je využít výhod Azure širokou škálu nástrojů zabezpečení a možnosti. Tyto nástroje a možnosti nápovědy umožňují vytvářet bezpečná řešení na platformě Azure.

Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat, a zároveň umožnit transparentní zodpovědnost. Vám pomůžou lépe pochopit kolekce kontrolní mechanismy zabezpečení sítě, která je implementovaná v rámci Microsoft Azure z pohledu zákazníka, tento článek, "Azure Network Security", je určen k poskytování komplexní pohled na síti kontrolních mechanismů pro zabezpečení k dispozici s Microsoft Azure.

Tento dokument je určený pro vás informovat o širokou škálu kontrolních mechanismů sítě, které můžete nakonfigurovat k vylepšení zabezpečení řešení, které nasazují v Azure. Pokud vás zajímají co společnost Microsoft k zabezpečení síťových prostředků infrastruktury samotné platformy Azure, najdete v části zabezpečení Azure v [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Platforma Azure

Azure je platforma služby veřejného cloudu, který podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení.  Může probíhat kontejnery Linuxu s integrací Dockeru; Vytvářejte aplikace pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js, Tvořte back EndY pro iOS, Android a Windows zařízení. Azure cloud services podporují stejné technologie miliony vývojářů a IT specialisty už spoléhají a kterým důvěřují.

Při sestavení nebo migraci prostředků IT k poskytovateli služby veřejného cloudu, jste závislí na schopnosti této organizace k ochraně vašich aplikací a dat s ovládacími prvky poskytují ke správě zabezpečení vašich cloudových prostředků a služeb.

Infrastruktura Azure je navržená ze zařízení do aplikace pro hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro na kterém můžou podniky vyhovuje jejich požadavkům na zabezpečení. Azure navíc poskytuje rozsáhlou sadu možností konfigurovatelné zabezpečení a umožňuje jim tak, aby zabezpečení jedinečným požadavkům vaší organizace nasazení můžete přizpůsobit.

## <a name="abstract"></a>Abstraktní

Veřejné cloudové služby Microsoftu poskytovat vysoce škálovatelné služby a infrastruktury, možnosti na podnikové úrovni a spoustu možností pro hybridní připojení. Můžete k těmto službám prostřednictvím Internetu nebo s využitím Azure ExpressRoute, které poskytuje připojení privátní sítě. Platforma Microsoft Azure umožňuje bezproblémově rozšířit vaši infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Kromě toho můžete povolit třetím stranám poznat široké možnosti tím, že nabízí zabezpečení služeb a virtuálních zařízení.

Síťové služby Azure, maximalizovat flexibilitu, dostupnost, odolnost proti chybám, zabezpečení a integrity návrhu. Tento dokument white paper poskytuje podrobné informace o síťových funkcí Azure a informace o tom, jak zákazníci mohou používat funkce nativní zabezpečení Azure k ochraně svých prostředků informací.

Zamýšlenou cílovou skupinou pro tento dokument White Paper patří:

- Technické správci, správci sítě a vývojáři, kteří hledají řešení zabezpečení dostupné a podporované v Azure.

-   Malé a střední podniky nebo vedoucí proces pracovníky, kteří chtějí získat základní přehled Azure síťové technologie a služby, které jsou relevantní v diskuzích k žádostem kolem zabezpečení sítě ve veřejném cloudu Azure.

## <a name="azure-networking-big-picture"></a>Celkový přehled sítě Azure
Microsoft Azure obsahuje robustní síťovou infrastrukturu pro podporu aplikací a požadavky na připojení služby. Připojení k síti je možné mezi prostředky umístěné v Azure, mezi místním a hostované v Azure prostředky a do a z Internetu a z Azure.

![Celkový přehled sítě Azure](media/azure-network-security/azure-network-security-fig-1.png)

[Azure síťovou infrastrukturu](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umožňuje zabezpečené propojení prostředků Azure mezi sebou s virtuálními sítěmi (Vnet). Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť se o logickou izolaci sítě cloudu Azure vyhrazenou pro vaše předplatné. Propojení virtuálních sítí na vaše místní sítě.

Azure podporuje vyhrazená propojení WAN připojení k vaší místní sítí a Azure Virtual Network s [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Propojení mezi Azure a webu používá vyhrazené připojení, které neprochází přes veřejný Internet. Pokud vaše aplikace Azure běží v několika datových centrech, můžete použít [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pro směrování požadavků od uživatelů inteligentně mezi instancemi aplikace. Můžete také směrovat provoz do služeb není spuštěná v Azure, pokud jsou přístupné z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Zobrazení Enterprise Azure síťových součástí
Azure nabízí mnoho síťové součásti, které jsou relevantní pro diskuse zabezpečení sítě. jsme popisují tyto síťové komponenty a zaměřit se na problémy se zabezpečením související s nimi.

> [!Note]
> Ne všechny aspekty sítí Azure jsou popsány – probereme pouze ty, které jsou považovány za pivotal v plánování a navrhování infrastruktury zabezpečené sítě kolem služby a aplikace, které nasazují v Azure.

V tomto dokumentu bude titulní následující sítě podnikové funkce Azure:

-   Základní připojení k síti

-   Hybridní připojení

-   Ovládací prvky zabezpečení

-   Ověřování sítě

### <a name="basic-network-connectivity"></a>Základní připojení k síti

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) služba umožňuje zabezpečené propojení prostředků Azure mezi sebou s virtuálními sítěmi (VNet). Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť se o logickou izolaci vyhrazených pro vaše předplatné Azure síťové infrastruktury. Můžete také propojení virtuálních sítí mezi sebou a vašich místních sítí pomocí připojení site-to-site VPN a vyhrazené [připojení WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Základní připojení k síti](media/azure-network-security/azure-network-security-fig-2.png)

S vědomím, že používáte virtuální počítače na hostitelských serverech v Azure, je na otázku, jak tyto virtuální počítače připojit k síti. Odpověď je, že virtuální počítače připojit k [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Jsou virtuální sítě Azure jako virtuální sítě pomocí místních svoje vlastní řešení virtualizace platformy, jako je například Microsoft Hyper-V nebo VMware.

#### <a name="intra-vnet-connectivity"></a>Připojení uvnitř virtuální sítě

Propojení virtuálních sítí mezi sebou, povolení prostředkům připojeným k jedné virtuální sítě ve virtuálních sítích komunikovat mezi sebou. Propojení virtuálních sítí mezi sebou můžete použít jednu nebo obě z následujících možností:

- **Vytvoření partnerského vztahu:** Umožňuje prostředkům připojeným k jiné virtuální sítě Azure ve stejném umístění Azure ke komunikaci mezi sebou. Na šířku pásma a latence ve virtuální síti je stejný jako v případě, že prostředky, které byly připojené ke stejné virtuální síti. Další informace o partnerských vztazích najdete v článku [partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Partnerské vztahy](media/azure-network-security/azure-network-security-fig-3.png)

- **Připojení VNet-to-VNet:** Umožňuje prostředkům připojeným k jiné virtuální síti Azure v rámci stejného nebo jiného umístění Azure. Na rozdíl od vytvoření partnerského vztahu, šířka pásma je omezená mezi virtuálními sítěmi, protože toku provozu prostřednictvím služby Azure VPN Gateway.

![Připojení VNet-to-VNet](media/azure-network-security/azure-network-security-fig-4.png)


Další informace o propojení virtuálních sítí pomocí připojení VNet-to-VNet najdete v článku [konfigurace článku připojení VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funkce virtuální síť Azure:

Jak je vidět, Azure Virtual Network poskytuje virtuálním počítačům připojovat k síti, tak, aby mohli připojit k jiným síťovým prostředkům zabezpečeným způsobem. Základní připojení je však jenom začátek. Následující funkce služby Azure Virtual Network vystavit vlastnosti zabezpečení služby Azure Virtual Network:

-   Izolace

-   Připojení k internetu

-   Připojení prostředků Azure

-   Připojení k virtuální síti

-   Místní připojení

-   Filtrování provozu

-   Směrování

**Izolace**

Virtuální sítě nacházejí [izolované](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) od sebe. Můžete vytvořit samostatné virtuální sítě pro vývoj, testování a produkci, které používají stejné [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) bloky adres. Naopak můžete vytvořit více virtuálními sítěmi, použít různé bloky adres CIDR a propojit sítě. Virtuální síť můžete rozdělit do několika podsítí.

Azure poskytuje interní překlad adres pro virtuální počítače a [Cloud Services](https://azure.microsoft.com/services/cloud-services/) instance rolí připojené k virtuální síti. Volitelně můžete nakonfigurovat virtuální síť, použijte vlastní servery DNS, namísto použití Azure interní překlad adres.

Můžete implementovat více virtuálními sítěmi v rámci každé [předplatné](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) a Azure [oblasti](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Každá virtuální síť je izolovaná od jiných virtuálních sítí. Pro každou virtuální síť můžete:

-   Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Azure přiřazuje prostředkům připojené k virtuální síti privátní IP adresu z adresního prostoru, můžete přiřadit.

-   Virtuální síť segmentovat do jedné nebo několika podsítí a přidělení části adresního prostoru virtuální sítě ke každé podsíti.

-   Použít překlad názvů poskytovaných Azure nebo zadat vlastní server DNS pro použití podle prostředků, které jsou připojené k virtuální síti. Další informace o překladu ve virtuálních sítích najdete v článku [překlad názvů pro virtuální počítače a Cloud Services](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Připojení k Internetu**

Všechny [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) a instance rolí cloudové služby připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům. (VM) a instance rolí cloudové služby připojené k virtuální síti mají přístup k Internetu, ve výchozím nastavení. Podle potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

Všechny prostředky, které jsou připojené k virtuální síti mají ve výchozím nastavení odchozího připojení k Internetu. Privátní IP adresu prostředku je zdrojové síťové adresy přeložit (SNAT) na veřejnou IP adresu infrastrukturou Azure. Výchozí připojení můžete změnit prostřednictvím implementace vlastního směrování a filtrování provozu. Další informace o odchozích připojení k Internetu, přečtěte si [Principy odchozích připojení v Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ke komunikaci příchozí k prostředkům Azure z Internetu nebo k Internetu bez SNAT odchozí komunikaci, zdroj musí přiřadit veřejnou IP adresu. Další informace o veřejné IP adresy, [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Připojení prostředků Azure**

[Prostředky Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jako virtuální počítače a cloudové služby jde připojit k stejné virtuální síti. Prostředky můžete připojit k sobě navzájem pomocí privátních IP adres, i když jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuálních sítích a sítích na pracovišti, takže není nutné konfigurovat a spravovat směrování.

Několik prostředků Azure můžete připojit k virtuální síti, jako jsou Virtual Machines (VM), Cloud Services, App Service Environment a Škálovací sady virtuálních počítačů. Virtuální počítače připojit k podsíti ve virtuální síti prostřednictvím síťového rozhraní (NIC). Další informace o síťových karet najdete v článku [síťová rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Připojení k virtuální síti**

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jde připojit k sobě navzájem, povolení prostředkům připojeným k žádné virtuální sítě ke komunikaci s jakýmikoli prostředky na jiné virtuální sítě.

Propojení virtuálních sítí mezi sebou, povolení prostředkům připojeným k jedné virtuální sítě ve virtuálních sítích komunikovat mezi sebou. Propojení virtuálních sítí mezi sebou můžete použít jednu nebo obě z následujících možností:

- **Vytvoření partnerského vztahu:** Umožňuje prostředkům připojeným k jiné virtuální sítě Azure ve stejném umístění Azure ke komunikaci mezi sebou. Na šířku pásma a latencí mezi virtuálními sítěmi je stejný jako v případě, že prostředky, které byly připojené k stejné VNet.To Další informace o vytvoření partnerského vztahu, přečtěte si [partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Připojení VNet-to-VNet:** Umožňuje prostředkům připojeným k jiné virtuální síti Azure v rámci stejného nebo jiného umístění Azure. Na rozdíl od vytvoření partnerského vztahu, šířka pásma je omezená mezi virtuálními sítěmi, protože toku provozu prostřednictvím služby Azure VPN Gateway. Další informace o propojení virtuálních sítí pomocí připojení VNet-to-VNet. Další informace najdete v článku [konfigurace připojení typu VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Místní připojení**

Virtuální sítě může být připojen k [místní](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sítě přes privátní připojení mezi vaší sítí a Azure nebo prostřednictvím připojení VPN typu site-to-site přes Internet.

Vaše místní sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

- **Point-to-site virtuální privátní sítě (VPN):** Vytváří se mezi jeden počítač připojený k síti a ve virtuální síti. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Připojení používá protokol SSTP poskytnout šifrovanou komunikaci prostřednictvím Internetu mezi Počítačem a virtuální síť. Latence pro sítě VPN point-to-site nepředvídatelné, protože provoz prochází Internetem.

- **Síť Site-to-site VPN:** Vytváří se mezi zařízením VPN a službou Azure VPN Gateway. Tento typ připojení povoluje všem prostředkům v místním autorizaci pro přístup k virtuální síti. Připojení je protokolu IPsec/IKE VPN, poskytující šifrovanou komunikaci prostřednictvím Internetu mezi místním zařízením a bránou Azure VPN. Latence pro připojení site-to-site nepředvídatelné, protože provoz prochází Internetem.

- **Azure ExpressRoute:** Vytváří se mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Přenosy neprocházejí přes Internet. Latence pro připojení ExpressRoute je předvídatelné, protože provoz nebude procházení Internetu. Další informace o všech předchozích možností připojení, přečtěte si [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrování přenosu**

Instance rolí virtuálních počítačů a cloudových služeb [přenos v síti](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) by šlo filtrovat příchozí a odchozí podle zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupiny zabezpečení sítě (NSG):** Každá skupina zabezpečení sítě může obsahovat více příchozích a odchozích pravidel zabezpečení, které umožňují filtrovat provoz ve zdrojové a cílové IP adresy, portu a protokolu. Skupina zabezpečení sítě můžete použít ke každému síťovému rozhraní ve virtuálním počítači. Můžete také použít skupinu NSG k podsíti, síťové rozhraní nebo jiné prostředky Azure, které je připojený k. Další informace o skupinách Nsg najdete v článku [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Síťová virtuální zařízení:** Síťové virtuální zařízení je virtuální počítač spuštěný software, který provádí síťovou funkci, jako je brána firewall. Zobrazení seznamu dostupných síťových virtuálních zařízení v Azure Marketplace. Síťová virtuální zařízení jsou také k dispozici, které poskytují optimalizace sítě WAN a jiných síťových přenosů. Síťová virtuální zařízení jsou obvykle používány s uživatelem nebo trasy protokolu BGP. Síťové virtuální zařízení můžete také použít k filtrování provozu mezi virtuálními sítěmi.

**Směrování**

Volitelně můžete přepsat výchozí Azure pro směrování konfiguraci vlastních tras, nebo pomocí trasy protokolu BGP přes bránu sítě.

Azure vytvoří směrovací tabulky, které umožňují prostředkům připojeným k žádné podsíti v libovolné virtuální síti komunikovat mezi sebou, ve výchozím nastavení. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Trasy definované uživatelem:** Nemůžete vytvářet vlastní směrovací tabulky s trasami tento ovládací prvek, kde provoz se směruje do pro každou podsíť. Další informace o trasách definovaných uživatelem najdete v článku [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Trasy protokolu BGP:** Je-li propojit vaši virtuální síť k místní síti pomocí Azure VPN Gateway nebo ExpressRoute, můžete rozšířit trasy protokolu BGP pro vaše virtuální sítě.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybridní připojení k Internetu: Připojení k místní síti
Vaše místní sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

-   Připojení k internetu

-   VPN typu Point-to-site (P2S VPN)

-   VPN typu Site-to-Site (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Připojení k Internetu

Jak název napovídá, připojení k Internetu zpřístupňuje své pracovní zatížení z Internetu, tím, že můžete zveřejnit různé veřejné koncové body speciálně přizpůsobené úlohám umístěných ve virtuální síti. Tyto úlohy můžou být přístupné přes [internetového nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) nebo jednoduše přiřadíte veřejnou IP adresu k virtuálnímu počítači. Tímto způsobem je možné pro všechno, co je na Internetu, abychom mohli být schopný připojit tento virtuální počítač v zadané hostitelské brány firewall [zabezpečení skupiny (NSG) sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), a [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) povolit, aby stát.

V tomto scénáři může zveřejnit aplikaci, kterou je potřeba zveřejnit na Internetu a být schopná připojit se k němu z odkudkoli nebo z konkrétních míst v závislosti na konfiguraci vašich úloh.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Point-to-Site VPN nebo VPN typu Site-to-Site
Tyto dvě, které patří do stejné kategorie. Oba musí mít bránu VPN virtuální sítě a lze k němu připojíte pomocí klienta VPN pro pracovní stanice v rámci [KonfiguracePoint-to-Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) nebo můžete nakonfigurovat místní [zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)mohli k ukončení sítě VPN site-to-site. Tímto způsobem na místních zařízeních může připojit k prostředkům v rámci virtuální sítě.

Konfigurace Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení jednotlivých klientských počítačů k virtuální síti. P2S je připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol).

![Point-to-Site VPN](media/azure-network-security/azure-network-security-fig-5.png)

Připojení point-to-Site jsou užitečná, pokud chcete připojit k virtuální síti ze vzdáleného umístění, například z domova nebo center konference, nebo pokud máte pouze několik klientů, které je potřeba připojit k virtuální síti.

Připojení typu P2S nevyžadují zařízení VPN ani veřejnou IP adresu. Připojení VPN se navazuje ze strany klientského počítače. Proto P2S se nedoporučuje způsob připojení k Azure, potřebujete trvalé připojení z mnoha různých zařízení a počítače k síti Azure.

![Site-to-Site VPN](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Další informace o připojení Point-to-Site najdete v článku [Point-to-Site DM v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2).

Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Toto připojení probíhá přes Internet a umožňuje informace "tunelového propojení" uvnitř šifrované propojení mezi vaší sítí a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. Šifrování tunelové propojení se provádí pomocí [režimu tunelového propojení IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Site-to-site VPN je důvěryhodné, spolehlivé a navážete technologie, provoz v rámci tunelu procházení Internetu. Kromě toho šířky pásma je poměrně omezen na maximálně o 200 MB/s.

Pokud budete potřebovat výjimečných úroveň zabezpečení nebo výkon pro vaše připojení mezi různými místy, doporučujeme použít Azure ExpressRoute pro vaše připojení mezi místními sítěmi. ExpressRoute je vyhrazené sítě WAN propojení mezi místním umístěním nebo poskytovatele hostingu serveru Exchange. Protože se jedná telco připojení, vaše data nelze procházet přes Internet a proto není vystavený potenciální rizika spojená s internetové komunikace.

> [!Note]
> Další informace o branách VPN najdete v tématu [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Vyhrazená propojení WAN
Microsoft Azure ExpressRoute umožňuje rozšířit vaše místní sítě do Azure přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

![ Vyhrazená propojení WAN](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Informace o tom, jak připojit vaší sítě do Microsoftu pomocí ExpressRoute najdete v tématu [modely připojení ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) a [technický přehled ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Jako s možnostmi VPN typu site-to-site, ExpressRoute vám také umožní připojit se k prostředkům, které nejsou nutně pouze jednu virtuální síť. Ve skutečnosti v závislosti na SKU, se můžete připojit k virtuálním sítím 10. Pokud máte [doplněk premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), je to možné, v závislosti na šířku pásma připojení až 100 virtuálními sítěmi. Další informace o co tyto typy připojení vzhled, jako je, přečtěte si [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Kontrolní mechanismy zabezpečení
Azure Virtual Network poskytuje zabezpečené a logické síti, která je oddělená od jiných virtuálních sítí a podporuje mnoho ovládacích prvků zabezpečení, které používáte na vaše místní sítě. Zákazníci s vytvořením vlastní struktury: podsítě –, použijte vlastní rozsah privátních IP adres, konfigurace směrovacích tabulek, skupiny zabezpečení sítě a seznamy řízení přístupu (ACL), bran a virtuálních zařízení ke spuštění svých úloh v cloudu.

Následují kontrolní mechanismy zabezpečení, kterými můžete na Azure Virtual Network:

-   Řízení přístupu k síti

-   Trasy definované uživatelem

-   Zařízení pro zabezpečení sítě

-   Application Gateway

-   Firewall webových aplikací

-   Řízení dostupnost sítě

#### <a name="network-access-controls"></a>Řízení přístupu k síti
Zatímco Azure Virtual Network (VNet) je základním kamenem model sítí Azure a poskytuje izolaci a ochranu, [skupiny zabezpečení sítě (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) jsou hlavní nástroj použijete k vynucení a řídit pravidla pro provoz sítě na úroveň sítě.

![ Řízení přístupu k síti](media/azure-network-security/azure-network-security-fig-8.png)


Můžete řídit přístup povolením nebo odepřením komunikace mezi úlohami v rámci virtuální sítě, ze systémů v sítích zákazníků prostřednictvím připojení mezi místními sítěmi, nebo nasměrovat internetovou komunikaci.

V diagramu virtuální sítě a skupin zabezpečení sítě jsou umístěny v konkrétní vrstvy v Azure celkové zabezpečení zásobníku, kde skupin zabezpečení sítě, směrování definovaného uživatelem a síťových virtuálních zařízení slouží k vytvoření hranic zabezpečení k ochraně nasazení aplikací v síti chráněné.

Skupiny Nsg 5-řazené kolekce členů se používá k vyhodnocení provozu (a se používají v pravidlech, která můžete nakonfigurovat pro skupiny zabezpečení sítě):

-   [Zdrojová a cílová IP adresa](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Zdrojový a cílový port](https://technet.microsoft.com/library/dd197515)

-   Protokol: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) nebo [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

To znamená, že se že můžete řídit přístup mezi jeden virtuální počítač a skupinu virtuálních počítačů nebo jeden virtuální počítač k jinému virtuálnímu počítači jeden, nebo mezi celé podsítě. Znovu mějte na paměti, že je to jednoduché stavové filtrování paketů, kontrola paketu není úplná. Není žádný protokol ověřování nebo ID úrovně sítě nebo IP adresy schopností ve skupině zabezpečení sítě.

Skupina zabezpečení sítě obsahuje některá vestavěné pravidla, které byste měli vědět. Jsou to:

-   **Povolení veškerého provozu v rámci konkrétní virtuální sítě:** Všechny virtuální počítače ve stejné virtuální síti Azure můžete komunikovat mezi sebou.

-   **Povolit příchozí Vyrovnávání zatížení Azure:** toto pravidlo povoluje provoz z jakékoli zdrojové adresy na jakoukoli adresu v cílovém pro Azure load balancer.

-   **Odmítnout všechny příchozí:** toto pravidlo blokuje veškeré přenosy z Internetu, explicitně povolující sourcing.

-   **Povolení veškerého provozu odchozího k Internetu:** Toto pravidlo umožňuje virtuálním počítačům, aby inicializoval připojení k Internetu. Pokud nechcete, aby se tato připojení inicializované, musíte vytvořit pravidlo na blokování tato připojení nebo vynutit vynucené tunelování.

#### <a name="system-routes-and-user-defined-routes"></a>Systémové trasy a trasy definované uživatelem

Když přidáte virtuální počítače (VM) k virtuální síti (VNet) v Azure, si všimnete, že se virtuální počítače vzájemně komunikovat v síti automaticky. Není nutné určit bránu, ani když jsou virtuální počítače v různých podsítích.

Totéž platí pro komunikaci z virtuálních počítačů do veřejného internetu, a dokonce i do vaší místní sítě, pokud je dostupné hybridní připojení z Azure do vlastního datacentra.

![Systémové trasy](media/azure-network-security/azure-network-security-fig-9.png)

Tento tok komunikace je možný díky tomu, že Azure pomocí řady systémových tras definuje toky provozu IP. Systémové trasy řídí tok komunikace v těchto scénářích:

-   Ze stejné podsítě.

-   Z jedné podsítě do jiné v rámci jedné virtuální sítě.

-   Z virtuálních počítačů do internetu.

-   Z jedné virtuální sítě do jiné prostřednictvím brány sítě VPN.

-   Z virtuální sítě k jiné virtuální síti prostřednictvím partnerského vztahu virtuálních sítí ([řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Z virtuální sítě do místní sítě prostřednictvím brány sítě VPN.

Mnoho podniků má striktní bezpečnostní a požadavky na dodržování předpisů, které vyžadují místní kontroly všech síťových paketů vynucovat určité zásady. Azure poskytuje mechanismus, který volá [vynucené tunelování](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) , který směruje provoz z virtuálních počítačů k místnímu vytvořením vlastní trasy nebo [protokol BGP (Border Gateway)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) oznámení o inzerovaných programech prostřednictvím ExpressRoute nebo VPN.

Vynucené tunelování v Azure se konfiguruje prostřednictvím virtuální sítě trasy definované uživatelem (UDR). Přesměrování přenosů do místní lokality je vyjádřena jako výchozí trasu pro bránu Azure VPN.

V následující části jsou uvedené aktuální omezení směrovací tabulky a trasy pro služby Azure Virtual Network:

-   Každé podsíti virtuální sítě má integrované, směrovací tabulky systému. Systémovou tabulku směrování má následující tři skupiny tras:

 -  **Místní virtuální sítě trasy:** Přímo do cílového umístění virtuálních počítačů ve stejné virtuální síti

 - **Pro místní trasy:** Ke službě Azure VPN gateway

 -  **Výchozí trasy:** Přímo k Internetu. Pakety směřující na privátní IP adresy není pokrytá předchozí dvě trasy se zahodí.

-   S vydáním trasy definované uživatelem můžete vytvořit směrovací tabulku, která chcete přidat výchozí trasa a přidružte směrovací tabulky pro vaši virtuální síť podsíť povolit vynucené tunelování na těchto podsítí.

-   Budete muset nastavit "výchozí web" mezi místy místní servery připojené k virtuální síti.

-   Vynucené tunelování musí být přidružen virtuální síť, která má bránu dynamického směrování VPN (není statická brána).

- ExpressRoute se vynucené tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho zajišťuje inzeruje výchozí trasu prostřednictvím relací vytvoření partnerského vztahu protokolu BGP ExpressRoute.

> [!Note]
> Další informace najdete v tématu [dokumentace ke službě ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Další informace.

#### <a name="network-security-appliances"></a>Síťová zabezpečovací zařízení
Zatímco skupiny zabezpečení sítě a trasy definované uživatelem můžete zadat míru zabezpečení sítě na síť a přenosové vrstvy [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), že se chystáte se situace, kdy je vhodné nebo nutné povolit zabezpečení na vyšší úrovně síťového zásobníku. V takových situacích doporučujeme, že nasazujete virtuální síťová zabezpečovací zařízení partnery Azure k dispozici.

![Síťová zabezpečovací zařízení](./media/azure-network-security/azure-network-security-fig-10.png)

Síť Azure zabezpečovací zařízení zlepšit zabezpečení virtuální sítě a síťové funkce a jsou k dispozici z mnoha dodavateli prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com). Tyto virtuální zabezpečovací zařízení je možné nasadit poskytnout:

-   Vysoce dostupné brány firewall

-   Narušení ochrany před únikem informací

-   Zjišťování neoprávněných vniknutí

-   Brány firewall webových aplikací (Waf)

-   Optimalizace sítě WAN

-   Směrování

-   Vyrovnávání zatížení

-   Síť VPN

-   Správa certifikátů

-   Active Directory

-   Vícefaktorové ověřování

#### <a name="application-gateway"></a>Application Gateway

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) je vyhrazené virtuální zařízení, která poskytuje kontroler doručování aplikací (ADC) jako službu.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Služba Application Gateway umožňuje optimalizovat webové farmy výkon a dostupnost podle ukončování náročné na CPU SSL ke službě application gateway (snižování zátěže – SSL). Poskytuje také další možnosti přesměrování vrstvy 7 včetně:

-   Kruhové dotazování na distribuci příchozích přenosů

-   Spřažení relace na základě souborů cookie

-   Směrování na základě cest URL

-   Možnost hostování několika webů za jedinou službou Application Gateway


A [firewallu webových aplikací (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) je k dispozici jako součást služby application gateway. To poskytuje ochranu webových aplikací před běžnými webovými ohroženími zabezpečení a zneužitím. Application Gateway lze nakonfigurovat jako internetový brány, interní pouze bránu nebo kombinaci obojího.

Waf služby Application Gateway můžete spustit v režimu detekce a ochrany před únikem informací. Běžným případem použití je pro správce ke spuštění v režimu detekce sledovat provoz pro škodlivé vzorce. Jakmile jsou zjištěna potenciální zneužití, až režim ochrany před únikem informací blokuje podezřelou příchozí provoz.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Kromě toho můžete monitorovat webové aplikace před útoky pomocí WAF protokol v reálném čase, které jsou integrovány s waf služby Application Gateway pomáhá [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) a [Azure Security Center](https://azure.microsoft.com/services/security-center/) ke sledování výstrah WAF a snadnou identifikaci trendů.

Formátovaný protokolu JSON přejde přímo na účet úložiště zákazníka. Mít plnou kontrolu nad tyto protokoly a můžete použít vlastní zásady uchovávání informací.

Tyto protokoly můžete také přijímat do vlastní systém analýz pomocí [Azure Log Integration](https://aka.ms/AzLog). Protokolů WAF je integrovaná taky s [Log Analytics](../log-analytics/log-analytics-overview.md) tak Log Analytics můžete použít k provádění sofistikované podrobných dotazů.

#### <a name="azure-web-application-firewall-waf"></a>Firewall webových aplikací (WAF)

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení, jako jsou Injektáž SQL, útoky skriptování napříč weby a další útoky, které se zobrazují v [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Prevence takové zneužitím v aplikaci vyžaduje pečlivou údržbu, opravy a monitorování několika vrstev topologie aplikace.

 ![Firewall webových aplikací (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Centralizovaná [firewallu webových aplikací (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) chrání před webovými útoky a zjednodušuje správu zabezpečení a aniž by bylo nutné žádné změny aplikací.

Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

#### <a name="network-availability-controls"></a>Řízení dostupnost sítě

Existují různé možnosti distribuce síťového provozu pomocí systému Microsoft Azure. Tyto možnosti fungují různě, obsahují jiné sady funkcí a podporují různé scénáře. Lze je používat jednotlivě nebo je kombinovat.

Ovládací prvky dostupnost sítě jsou následující:

-   Nástroj pro vyrovnávání zatížení Azure

-   Application Gateway

-   Traffic Manager

**Nástroj Azure Load balancer**

Zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Je Vyrovnávání zatížení vrstvy 4 (TCP, UDP), který distribuuje příchozí provoz mezi v pořádku instance služby definované v sadě s vyrovnáváním zatížení.

 ![Nástroj pro vyrovnávání zatížení Azure](media/azure-network-security/azure-network-security-fig-14.png)


Nástroj Azure Load Balancer můžete nakonfigurovat na:

-   Načtěte vyrovnávat příchozí přenosy z Internetu k virtuálním počítačům. Tato konfigurace se označuje jako [přístupem k Internetu pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Vyrovnávání zatížení provozu mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními a virtuálními počítači ve virtuální síti mezi různými místy. Tato konfigurace se označuje jako [interní Vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Předávání externích přenosů konkrétní virtuální počítač.

Všechny prostředky v cloudu musí být dosažitelný z Internetu veřejnou IP adresu. Cloudové infrastruktury v Azure používá nesměrovatelných adres IP pro její prostředky. Azure používá překlad síťových adres (NAT) s veřejnými IP adresami pro komunikaci na Internetu.

 **Služba Application gateway**

 Služba Application Gateway funguje na aplikační vrstvě (vrstva 7 v balíčku referenčních síti modelu OSI). Funguje jako služba reverzních proxy serverů, ukončuje připojení klienta a předává požadavky do back-endových koncových bodů.

 **Traffic Manageru**

Microsoft Azure Traffic Manager umožňuje řídit distribuci uživatelského provozu do koncových bodů služby v různých datových centrech. Koncové body služby, které Traffic Manager podporuje patří virtuální počítače Azure, webové aplikace a cloudové služby. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure.

Traffic Manager používá v systému DNS (Domain Name) přesměrovává klientské žádosti na nejvhodnější koncové body na základě [metody směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) a stavu koncových bodů. Traffic Manager poskytuje řadu metod směrování provozu tak, aby vyhovoval potřebám různé aplikace, koncový bod stavu [monitorování](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)a automatické převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

Azure Traffic Manager umožňuje řídit distribuci provozu mezi koncových bodů vaší aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager poskytuje dva klíčové výhody:

-   Distribuce provozu podle jednoho z několika [metody směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Nepřetržité monitorování koncových bodů](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) a automatické převzetí služeb při selhání, když dojde k selhání koncových bodů.

Když se klient pokouší připojit ke službě, musí nejdřív se přeložit název DNS služby na IP adresu. Klient pak připojí k této IP adresy pro přístup ke službě. Nasměrování klientů na určité služby koncové body na základě pravidel metodu směrování provozu Traffic Manageru pomocí služby DNS. Klienti připojovat přímo k vybraný koncový bod. Traffic Manager se proxy server nebo bránu. Traffic Manager nezná provoz mezi klientem a služby předávání.

### <a name="azure-network-validation"></a>Ověřování sítě Azure

Síť Azure ověření je zajistit, že síť Azure funguje jako je nakonfigurovaná a provést ověření pomocí služby a funkce, které jsou k dispozici pro monitorování sítě. Pomocí služby Azure Network Watcher, dostanete množství protokolování a možnosti diagnostiky, které vám pomohou s přehledy, které vám pomohou pochopit výkon sítě a stavu. Tyto možnosti jsou přístupné přes portál, prostředí PowerShell, rozhraní příkazového řádku, rozhraní Rest API a sady SDK.

Azure Operational Security odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků ve službě Microsoft Azure. Provozní zabezpečení Azure je postavená na systém, který zahrnuje poznatky získané při různé schopnosti, které jsou jedinečné pro Microsoftu, mezi které patří Microsoft Security Development Lifecycle (SDL), programu Microsoft Security odpovědi střediska a hloubkové povědomí o oblasti kybernetických hrozeb zabezpečení.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure resource Manageru

Nejdůležitější funkce zabezpečení platformy jsou možná lidi a procesy, které pracují Microsoft Azure. Tato část popisuje funkce infrastruktury globálních datacenter Microsoftu, které pomáhají vylepšovat a údržbě zabezpečení, kontinuity podnikových procesů a ochranu osobních údajů.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. Azure Resource Manager umožňuje pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Resource Manageru**

Resource Manager poskytuje několik výhod:

-   Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

-   Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

-   Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

-   Můžete definovat závislosti mezi prostředky, takže se nasadí ve správném pořadí.

-   Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

-   Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

-   Můžete zpřehlednit fakturaci vaší organizace zobrazením nákladů na skupinu prostředků, které sdílejí značky.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste využívali dřívější model nasazení a chcete se dozvědět o změnách, přečtěte si článek věnovaný [nasazení Resource Manageru a klasickému nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Síť Azure protokolování a monitorování

Azure nabízí řadu nástrojů pro monitorování, hrozbami, detekci a reakce na události zabezpečení sítě. Některé z nejúčinnějších nástrojů, které jsou k dispozici v této oblasti patří:

-   Network Watcher

-   Úroveň sledování síťových prostředků

-   Log Analytics

### <a name="network-watcher"></a>Sledovací proces sítě

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -založené na scénářích monitorování se poskytuje zároveň s funkcí ve službě Network Watcher. Tato služba zahrnuje zachycení paketu, další směrování, IP tok ověřit, zobrazení skupin zabezpečení, protokoly toků NSG. Scénář úrovně monitorování poskytuje komplexní přehled síťové prostředky, na rozdíl od sledování jednotlivých síťových prostředků.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v aplikaci, do a z Azure. Pro diagnostiku sítě a vizualizačních nástrojů, které jsou k dispozici pomocí služby Network Watcher pomáhají porozumět, diagnostice a získání přehledu o vaší síti v Azure.

Network Watcher je aktuálně má následující možnosti:

#### <a name="topology"></a>Topologie

[Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) vrátí graf síťových prostředků ve virtuální síti. Graf znázorňuje propojení mezi prostředky, které představují komplexní síťové připojení. Na portálu topologie vrátí objekty prostředků podle jednotlivých virtuální sítě. Vztahy jsou znázorněné těmito řádky mezi prostředky mimo oblast, Network Watcher, i v případě, že v prostředku skupiny se nezobrazí. Prostředky vráceny v zobrazení na portálu jsou podmnožinou síťové součásti, které jsou zaneseny do grafu. Pokud chcete zobrazit úplný seznam síťových prostředků, můžete použít [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) nebo [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Protože prostředky jsou vráceny připojení mezi jejich jsou modelovány pod dvě relace.

- **Členství ve skupině** -virtuální síť obsahuje podsítě, která obsahuje síťový adaptér.

- **Související** -A NIC je přidružený k virtuálnímu počítači.

#### <a name="variable-packet-capture"></a>Proměnné zachytávání paketů

Network Watcher [proměnné zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) vám umožní vytvořit relace zachycení paketů sledujte provoz do a z virtuálního počítače. Pomáhá zachytávání paketů pro diagnostiku sítě anomálie reaktivně a proactivity. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí.

Zachytávání paketů se rozšíření virtuálního počítače, která je spuštěna vzdáleně prostřednictvím Network Watcher. Tato schopnost snižuje zátěž ručně spustit zachytávání paketů na požadovaný virtuální počítač, což šetří cenný čas. Zachytávání paketů se dá spouštět na portálu, Powershellu, rozhraní příkazového řádku nebo rozhraní REST API. Jeden příklad, jak se dá spouštět zachytávání paketů se virtuální počítač výstrahy.

#### <a name="ip-flow-verify"></a>Ověření toku protokolu IP

[Ověření IP toků](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) zkontroluje, jestli je paket povolený nebo nebo z virtuálního počítače na základě informací o 5 řazené kolekce členů. Tyto informace se skládá z směr, protokol, místní IP Adrese, vzdálenou IP adresu, místní port a vzdálených portů. Pokud paketu je skupina zabezpečení, je vrácen název pravidla, které paket zamítlo. Při jakékoli zdrojové nebo cílové IP adresy mohou být vybrána, tato funkce pomáhá správcům rychle diagnostikovat problémy s připojením z nebo internet a z nebo v místním prostředí.

Ověření IP toků cílí na síťové rozhraní virtuálního počítače. Tok potom ověření podle nakonfigurovaného nastavení do nebo z rozhraní sítě. Tato možnost je užitečná v potvrzení, pokud se pravidlo skupiny zabezpečení sítě blokuje příchozí a odchozí provoz do nebo z virtuálního počítače.

#### <a name="next-hop"></a>Další směrování

Určuje, [dalšího segmentu směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pro pakety směrovány v prostředcích infrastruktury sítě Azure, umožňuje vám to zjistit všechny správně nakonfigurovaný. trasy definované uživatelem. Přenosy z virtuálního počítače se odesílají do cílového umístění podle efektivních tras, které jsou přidružené k síťovému rozhraní. Další směrování získá typ dalšího segmentu směrování a IP adresa z paketu z konkrétního virtuálního počítače a síťovou kartu To pomáhá určit, jestli je paket směrování do cíle nebo je provoz se černé dírkového.

Další směrování také vrátí hodnotu směrovací tabulky přidružené k další segment směrování. Při dotazování na další segment směrování, pokud trasu je definován jako trasy definované uživatelem, vrátí se danou trasu. Další směrování v opačném případě vrátí "Systémová trasa".

#### <a name="security-group-view"></a>zobrazení skupin zabezpečení

Získá pravidla efektivní a použité zabezpečení, které se použijí na virtuálním počítači. Skupiny zabezpečení sítě jsou spojeny na úrovni podsítě nebo na úrovni síťové karty. Pokud se související na úrovni podsítě, platí pro všechny instance virtuálních počítačů v podsíti. Síť [zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) vrátí nakonfigurované skupiny Nsg a pravidla, které jsou spojeny na úrovni síťové karty NIC a podsítě pro virtuální počítač poskytuje přehled o konfiguraci. Kromě toho jsou vráceny platná pravidla zabezpečení pro jednotlivé síťové adaptéry na virtuálním počítači. Zobrazení pomocí skupiny zabezpečení sítě můžete vyhodnotit virtuálního počítače pro zranitelností sítí, jako je otevření portů. Můžete také ověřit, zda vaše skupiny zabezpečení sítě funguje podle očekávání, na základě [srovnání nakonfigurované a platná pravidla zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Protokolování toku NSG

 Protokoly toků pro skupiny zabezpečení sítě umožňují zachytit protokoly týkající se přenosů, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informace 5 řazené kolekce členů – Zdrojová IP adresa, cílová IP adresa, zdrojový Port, cílový Port a protokol.

[Protokoly toku skupin zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkce služby Network Watcher, který vám umožní zobrazit informace o příchozí a odchozí provoz IP přes skupinu zabezpečení sítě.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Brána virtuální sítě a řešení potíží s připojením

Sledovací proces sítě poskytuje mnoho funkcí, jako má vztah k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. [Řešení potíží s prostředků](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) lze volat rozhraní REST API, Powershellu nebo rozhraní příkazového řádku. Při volání, Network Watcheru kontroluje stav brány virtuální sítě nebo připojení a vrátí jeho zjištění.

Tato část vás provede jiné úlohy, které jsou aktuálně k dispozici pro řešení potíží s prostředků.

-   [Řešení potíží s bránu virtuální sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Řešení potíží s připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limity předplatného sítě

[Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytují podrobnosti o použití každé ze síťových prostředků v předplatném v oblasti pro maximální počet prostředků, které jsou k dispozici.

#### <a name="configuring-diagnostics-log"></a>Konfiguruje se Diagnostika protokolů

Network Watcher dává [diagnostické protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) zobrazení. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťové prostředky, jednoduše a rychle.

### <a name="network-resource-level-monitoring"></a>Úroveň sledování síťových prostředků

Následující funkce jsou k dispozici pro monitorování na úrovni prostředků:

#### <a name="audit-log"></a>Protokol auditu

Operace provedené jako součást konfigurace sítě jsou protokolovány. Tyto protokoly auditu jsou nezbytné k navázání různých dodržování předpisů. Tyto protokoly můžete zobrazit na webu Azure Portal nebo pomocí nástrojů společnosti Microsoft, jako je Power BI nebo nástrojů třetích stran načíst. Protokoly auditu jsou k dispozici prostřednictvím portálu, Powershellu, rozhraní příkazového řádku a rozhraní Rest API.

> [!Note]
> Další informace týkající se protokolů auditu najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Protokoly auditu jsou k dispozici pro operace provést pro všechny síťové prostředky.


#### <a name="metrics"></a>Metriky

Metriky jsou čítače shromažďují průběžně a měření výkonu. Metriky jsou aktuálně k dispozici pro službu Application Gateway. Metriky lze použít k aktivaci výstrahy podle prahovou hodnotu. Ve výchozím nastavení služba Azure Application Gateway monitoruje stav všech prostředků v jeho back endovém fondu a automaticky odstraní všechny prostředky z fondu považoval za poškozený. Služba Application Gateway pokračuje v monitorování instance v nedobrém stavu a přidá je zpět do fondu back-end v pořádku, jakmile budou k dispozici a reagují na sondy stavu. Služba Application gateway odesílá že sondy stavu se stejný port, který je definován v nastavení HTTP back-end. Tato konfigurace zajistí, že test je testování stejný port, který by se zákazníky, kteří využívají pro připojení k back-endu.

> [!Note]
> Zobrazit [diagnostiku brány aplikace](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) Chcete-li zobrazit, jak lze vytvářet upozornění metrik.

#### <a name="diagnostic-logs"></a>Diagnostické protokoly

Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště, odeslané do Log Analytics nebo centra událostí. Tyto protokoly poskytují přehled o stavu prostředku. Tyto protokoly můžete zobrazit v nástrojů, jako je Power BI a Log Analytics. Zjistěte, jak zobrazit diagnostické protokoly, najdete v tématu [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Diagnostické protokoly jsou k dispozici pro [nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy, a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher dává zobrazení protokolů diagnostiky. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto hlediska můžete povolit nebo zakázat síťové prostředky, jednoduše a rychle.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je služba v Azure, která monitoruje cloudové a místní prostředí s cílem zachovat jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

Log Analytics nabízí následující řešení pro monitorování vaší sítě:

-   Network Performance Monitor (NPM)

-   Azure Application Gateway analytics

-   Analýzy skupin zabezpečení sítě Azure

#### <a name="network-performance-monitor-npm"></a>Network performance monitoru (NPM)
[Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) řešení pro správu je řešení, která monitoruje stav, dostupnosti a dosažitelnosti sítí pro monitorování sítě.

Používá se k monitorování připojení mezi:

-   Veřejný cloud a místní

-   Datová centra a umístění uživatele (firemní pobočky)

-   Podsítě poskytující hostování různé úrovně víceúrovňových aplikací.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Analýzy Azure application gateway ve službě log analytics

Tyto protokoly jsou podporovány pro brány Application Gateway:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Pro brány Application Gateway se podporují následující metriky:

-   propustnost 5 minut

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Analýzy skupin zabezpečení sítě Azure v log analytics

Tyto protokoly jsou podporovány pro [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Obsahuje položky, pro které skupina NSG pravidla se použijí u virtuálních počítačů a instancí rolí na základě adresy MAC. Stav pro tato pravidla shromažďovaných každých 60 sekund.

- **NetworkSecurityGroupRuleCounter:** Obsahuje položky pro kolikrát se každé pravidlo NSG se použije pro odepření nebo povolení provozu.

## <a name="next-steps"></a>Další postup
Další informace o zabezpečení najdete některé z našich podrobných zabezpečení témata:

-   [Log Analytics pro skupiny zabezpečení sítě (Nsg)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Inovace v oblasti, které řídí přerušení cloudové sítě](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Sítě přepnout software, který používá globální Cloud od Microsoftu](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Postupy: sestavení jejich rychlé a spolehlivé globální síti Microsoftu](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Osvětlení si inovace sítě](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
