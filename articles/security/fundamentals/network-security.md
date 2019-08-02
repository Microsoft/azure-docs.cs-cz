---
title: Zabezpečení sítě Azure | Microsoft Docs
description: Přečtěte si o cloudových výpočetních službách, které zahrnují nejrůznější výpočetní instance & služby, které se můžou automaticky škálovat a snížit tak, aby vyhovovaly potřebám vaší aplikace nebo podniku.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: db3f5aca0240c19f67d5d0775148d5eec76daa03
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726988"
---
# <a name="azure-network-security"></a>Zabezpečení sítě Azure

Víme, že zabezpečení je úloha v cloudu a jak důležité je, že najdete přesné a včasné informace o zabezpečení Azure. Jedním z nejlepších důvodů, jak používat Azure pro vaše aplikace a služby, je využít výhod nejrůznějších nástrojů a funkcí zabezpečení Azure. Tyto nástroje a možnosti usnadňují vytváření zabezpečených řešení na platformě Azure.

Microsoft Azure poskytuje důvěrnost, integritu a dostupnost zákaznických dat a zároveň umožňuje transparentní zodpovědnost. Abychom vám pomohli lépe pochopit shromažďování kontrolních mechanismů zabezpečení sítě implementovaných v rámci Microsoft Azure z perspektivy zákazníka, je tento článek "Azure Network Security" napsán tak, aby poskytoval komplexní pohled na ovládací prvky zabezpečení sítě. k dispozici v Microsoft Azure.

Tento dokument je určený k informování o široké škále síťových ovládacích prvků, které můžete nakonfigurovat, abyste vylepšili zabezpečení řešení, které nasazujete v Azure. Pokud vás zajímá, co Microsoft zajišťuje zabezpečení síťové infrastruktury samotné platformy Azure, přečtěte si část zabezpečení Azure na [webu Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Platforma Azure

Azure je platforma veřejné cloudové služby, která podporuje širokou škálu operačních systémů, programovacích jazyků, architektur, nástrojů, databází a zařízení.  Může spouštět kontejnery Linux s integrací Docker. Vytvářejte aplikace pomocí jazyků JavaScript, Python, .NET, PHP, Java a Node. js; Vytvořte back-endy pro zařízení s iOS, Androidem a Windows. Cloudové služby Azure podporují stejné technologie jako miliony vývojářů a odborníků na IT, kteří už využívají a důvěřují jim.

Při sestavování nebo migraci IT prostředků do, poskytovatele veřejné cloudové služby, se spoléháte na možnosti organizace, které chrání vaše aplikace a data se službami a ovládacími prvky, které poskytují ke správě zabezpečení cloudových prostředků.

Infrastruktura Azure je navržená od zařízení až po aplikace pro hostování milionů zákazníků současně a poskytuje důvěryhodný základ, na kterém podniky můžou splňovat požadavky na zabezpečení. Kromě toho Azure poskytuje rozsáhlou kolekci konfigurovatelných možností zabezpečení a možnost jejich řízení, abyste mohli přizpůsobit zabezpečení, aby splňovala jedinečné požadavky nasazení vaší organizace.

## <a name="abstract"></a>Abstraktní

Veřejné cloudové služby Microsoftu poskytují služby a infrastrukturu v rámci technologie Hyper-Scale a spoustu možností pro hybridní konektivitu. Přístup k těmto službám můžete vybrat buď prostřednictvím Internetu, nebo pomocí Azure ExpressRoute, která poskytuje připojení k privátní síti. Microsoft Azure platforma vám umožní bezproblémově roztáhnout infrastrukturu do cloudu a vytvářet vícevrstvé architektury. Navíc můžou třetí strany povolit rozšířené funkce tím, že nabízejí služby zabezpečení a virtuální zařízení.

Síťové služby Azure maximalizují flexibilitu, dostupnost, odolnost, zabezpečení a integritu podle návrhu. Tento dokument white paper obsahuje podrobné informace o funkcích sítě Azure a informace o tom, jak zákazníci můžou pomocí nativních funkcí zabezpečení Azure lépe chránit své informační prostředky.

Mezi zamýšlené cílové skupiny pro tento dokument White Paper patří:

- Techničtí manažeři, správci sítě a vývojáři, kteří hledají řešení zabezpečení dostupná a podporovaná v Azure.

-   Malé a střední podniky nebo vedoucí obchodních procesů, kteří chtějí získat podrobný přehled o síťových technologiích a službách Azure, které jsou relevantní v diskuzích o zabezpečení sítě ve veřejném cloudu Azure.

## <a name="azure-networking-big-picture"></a>Velký obrázek o sítích Azure
Microsoft Azure obsahuje robustní síťovou infrastrukturu pro podporu požadavků vaší aplikace a připojení služby. Mezi prostředky umístěnými v Azure, mezi místními a hostovanými prostředky Azure a a z Internetu a Azure se může připojit k síti.

![Velký obrázek o sítích Azure](./media/network-security/azure-network-security-fig-1.png)

[Síťová infrastruktura Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umožňuje zabezpečeně propojit prostředky Azure s virtuálními sítěmi (virtuální sítě). Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť je logická izolace cloudové sítě Azure vyhrazené pro vaše předplatné. Virtuální sítě můžete propojit s vašimi místními sítěmi.

Azure podporuje vyhrazené připojení WAN Link k vaší místní síti a Virtual Network Azure s [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Propojení mezi Azure a vaší lokalitou používá vyhrazené připojení, které nepřekračuje veřejný Internet. Pokud je vaše aplikace Azure spuštěná v několika datových centrech, můžete použít [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) k směrování požadavků od uživatelů, které jsou v jednotlivých instancích aplikace inteligentně. Provoz můžete směrovat i do služeb, které neběží v Azure, pokud jsou dostupné z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Podnikové zobrazení síťových součástí Azure
Azure má spoustu síťových součástí, které jsou relevantní pro diskuze v souvislosti se zabezpečením sítě. Tyto síťové komponenty popisujeme a Zaměřujeme se na problémy zabezpečení, které s nimi souvisejí.

> [!Note]
> Ne všechny aspekty sítí Azure jsou popsané – máme jenom ty, které se považují za při plánování a navrhování zabezpečené síťové infrastruktury kolem vašich služeb a aplikací, které nasazujete v Azure.

V tomto dokumentu se budou pokrývat tyto možnosti sítě Azure v síti:

-   Základní připojení k síti

-   Hybridní připojení

-   Ovládací prvky zabezpečení

-   Ověření sítě

### <a name="basic-network-connectivity"></a>Základní připojení k síti

Služba [azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) umožňuje zabezpečeně propojit prostředky Azure s virtuálními sítěmi (VNET) mezi sebou. Virtuální síť je reprezentace vaší vlastní sítě v cloudu. Virtuální síť je logická izolace síťové infrastruktury Azure, která je vyhrazená pro vaše předplatné. Můžete také vzájemně propojit virtuální sítě a s místními sítěmi pomocí sítě VPN typu Site-to-site a vyhrazených [propojení WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Základní připojení k síti](./media/network-security/azure-network-security-fig-2.png)

Při porozumění použití virtuálních počítačů k hostování serverů v Azure je otázka tím, jak se tyto virtuální počítače připojují k síti. Odpověď je v tom, že se virtuální počítače připojují k [Virtual Network Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Virtuální sítě Azure jsou jako virtuální sítě, které v místním prostředí používáte s vlastními řešeními virtualizačních platforem, jako je Microsoft Hyper-V nebo VMware.

#### <a name="intra-vnet-connectivity"></a>Připojení uvnitř virtuální sítě

Můžete vzájemně propojit virtuální sítě a umožnit tak komunikaci prostředků připojených k libovolné virtuální síti mezi sebou napříč virtuální sítě. K vzájemnému propojení virtuální sítě můžete použít jednu nebo obě následující možnosti:

- **Partnerský vztah** Umožňuje vzájemnou komunikaci prostředků připojených k různým virtuální sítě Azure v rámci stejného umístění Azure. Šířka pásma a latence v rámci virtuální sítě jsou stejné, jako kdyby byly prostředky připojené ke stejné virtuální síti. Pokud se chcete dozvědět víc o partnerském vztahu, přečtěte si téma [Virtual Network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Partnerský vztah](./media/network-security/azure-network-security-fig-3.png)

- **Připojení VNet-to-VNet:** Povoluje prostředky připojené k jiné virtuální síti Azure v rámci stejných nebo různých umístění Azure. Na rozdíl od partnerských vztahů je šířka pásma omezená mezi virtuální sítě, protože provoz musí procházet prostřednictvím VPN Gateway Azure.

![Připojení VNet-to-VNet](./media/network-security/azure-network-security-fig-4.png)


Další informace o připojení virtuální sítě k připojení typu VNet-to-VNet najdete v [článku Konfigurace připojení typu VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Možnosti služby Azure Virtual Network:

Jak vidíte, Virtual Network Azure umožňuje virtuálním počítačům připojit se k síti, aby se mohli zabezpečeně připojit k dalším síťovým prostředkům. Základní konektivita je ale jenom začátek. Následující funkce služby Azure Virtual Network zveřejňují bezpečnostní charakteristiky Virtual Network Azure:

-   Izolace

-   Připojení k internetu

-   Připojení prostředků Azure

-   Připojení virtuální sítě

-   Místní připojení

-   Filtrování provozu

-   Směrování

**Oddělení**

Virtuální sítě jsou [izolované](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) od sebe. Můžete vytvořit samostatné virtuální sítě pro vývoj, testování a produkční prostředí, která používají stejné bloky adres [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Naopak můžete vytvořit více virtuální sítě, které používají různé bloky adres CIDR a propojit sítě dohromady. Virtuální síť můžete rozdělit do několika podsítí.

Azure poskytuje interní překlad adres IP pro virtuální počítače a [Cloud Services](https://azure.microsoft.com/services/cloud-services/) instance rolí, které se připojují k virtuální síti. Volitelně můžete nakonfigurovat virtuální síť tak, aby používala vlastní servery DNS místo použití překladu interního překladu Azure.

V rámci každého předplatného Azure a [](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) [oblasti](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure můžete implementovat víc virtuální sítě. Každá virtuální síť je izolovaná od jiných virtuálních sítí. Pro každou virtuální síť můžete:

-   Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Azure přiřadí prostředky připojené k virtuální síti privátní IP adresu z adresního prostoru, který přiřadíte.

-   Segmentujte virtuální síť do jedné nebo víc podsítí a přidělte část adresního prostoru virtuální sítě ke každé podsíti.

-   Použijte překlad IP adres poskytovaný službou Azure nebo zadejte vlastní server DNS, který budou používat prostředky připojené k virtuální síti. Pokud se chcete dozvědět víc o překladu názvů v virtuální sítě, přečtěte si téma [překlad IP adres pro virtuální počítače a Cloud Services](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Připojení k Internetu**

Všechny instance rolí [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) a Cloud Services připojené k virtuální síti mají ve výchozím nastavení přístup k Internetu. V případě potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům. (Virtuální počítač) a Cloud Services instance rolí připojené k virtuální síti mají ve výchozím nastavení přístup k Internetu. V případě potřeby můžete také povolit příchozí přístup ke konkrétním prostředkům.

Všechny prostředky připojené k virtuální síti mají ve výchozím nastavení odchozí připojení k Internetu. Privátní IP adresa prostředku je zdrojová síťová adresa přeložená (SNAT) k veřejné IP adrese infrastruktury Azure. Výchozí připojení můžete změnit implementací vlastního směrování a filtrování provozu. Pokud se chcete dozvědět víc o odchozím připojení k Internetu, přečtěte si téma [Principy odchozích připojení v Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Aby bylo možné příchozí připojení k prostředkům Azure komunikovat z Internetu nebo komunikovat odchozí na Internet bez SNAT, musí být prostředku přiřazena veřejná IP adresa. Pokud se chcete dozvědět víc o veřejných IP adresách, přečtěte si [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Připojení prostředků Azure**

[Prostředky Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) , například Cloud Services a virtuální počítače, je možné připojit ke stejné virtuální síti. Prostředky se můžou vzájemně propojit pomocí privátních IP adres, a to i v případě, že jsou v různých podsítích. Azure poskytuje výchozí směrování mezi podsítěmi, virtuální sítě a místními sítěmi, takže nemusíte konfigurovat a spravovat trasy.

K virtuální síti můžete připojit několik prostředků Azure, jako je Virtual Machines (VM), Cloud Services, App Service prostředí a Virtual Machine Scale Sets. Virtuální počítače se připojují k podsíti v rámci virtuální sítě prostřednictvím síťového rozhraní (NIC). Pokud se chcete dozvědět víc o síťových rozhraních, přečtěte si [Síťová rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Připojení virtuální sítě**

[Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) může být vzájemně propojena, což umožňuje komunikaci prostředků připojených k libovolné virtuální síti ke komunikaci s jakýmkoli prostředkem v jakékoli jiné virtuální síti.

Můžete vzájemně propojit virtuální sítě a umožnit tak komunikaci prostředků připojených k libovolné virtuální síti mezi sebou napříč virtuální sítě. K vzájemnému propojení virtuální sítě můžete použít jednu nebo obě následující možnosti:

- **Partnerský vztah** Umožňuje vzájemnou komunikaci prostředků připojených k různým virtuální sítě Azure v rámci stejného umístění Azure. Šířka pásma a latence v rámci virtuální sítě je stejná, jako kdyby byly prostředky připojené ke stejné VNet.To. Další informace o partnerském vztahu najdete v tématu [virtuální síť](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)s partnerským vztahem.

- **Připojení VNet-to-VNet:** Povoluje prostředky připojené k jiné virtuální síti Azure v rámci stejných nebo různých umístění Azure. Na rozdíl od partnerských vztahů je šířka pásma omezená mezi virtuální sítě, protože provoz musí procházet prostřednictvím VPN Gateway Azure. Další informace o připojení virtuální sítě s připojením typu VNet-to-VNet. Pokud se chcete dozvědět víc, přečtěte si téma [Konfigurace připojení typu VNet-to-VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Místní připojení**

Virtuální sítě je možné připojit k [místním](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sítím prostřednictvím privátních síťových připojení mezi vaší sítí a Azure nebo prostřednictvím připojení VPN typu Site-to-site přes Internet.

Místní síť můžete připojit k virtuální síti pomocí libovolné kombinace následujících možností:

- **Virtuální privátní síť (VPN) typu Point-to-site:** Navázáno mezi jedním počítačem připojeným k vaší síti a virtuální sítí. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Připojení používá protokol SSTP k poskytování šifrované komunikace přes Internet mezi počítačem a virtuální sítí. Latence pro síť VPN typu Point-to-site je nepředvídatelné, protože přenosy procházejí internetem.

- **Síť VPN typu Site-to-site:** Navázáno mezi zařízením VPN a službou Azure VPN Gateway. Tento typ připojení umožňuje přístup k virtuální síti pomocí všech místních prostředků, které autorizujete. Připojení je síť VPN s protokolem IPsec/IKE, která poskytuje šifrovanou komunikaci přes Internet mezi vaším místním zařízením a bránou Azure VPN. Latence připojení typu Site-to-site je nepředvídatelné, protože přenosy procházejí internetem.

- **ExpressRoute Azure:** Navázáno mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Přenos neprojde internetem. Latence připojení ExpressRoute je předvídatelná, protože provoz neprojde internetem. Pokud se chcete dozvědět víc o všech předchozích možnostech připojení, přečtěte si [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrování přenosu**

[Síťové přenosy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) instancí rolí virtuálních počítačů a Cloud Services lze filtrovat příchozí a odchozí pomocí zdrojové IP adresy a portu, cílové IP adresy a portu a protokolu.

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupiny zabezpečení sítě (NSG):** Každý NSG může obsahovat několik příchozích a odchozích pravidel zabezpečení, která umožňují filtrovat provoz podle zdrojové a cílové IP adresy, portu a protokolu. NSG můžete použít pro každé síťové rozhraní ve virtuálním počítači. Můžete také použít NSG k podsíti, ke které je připojená síťová karta nebo k jinému prostředku Azure. Další informace o skupin zabezpečení sítě najdete v článku [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Virtual Network zařízení:** Virtuální síťové zařízení je virtuální počítač, na kterém je spuštěný software, který provádí síťovou funkci, jako je třeba brána firewall. Zobrazit seznam dostupných síťová virtuální zařízení v Azure Marketplace. K dispozici jsou také síťová virtuální zařízení, které poskytují funkce pro optimalizaci sítě WAN a další síťové přenosy. Síťová virtuální zařízení se obvykle používají pro trasy definované uživatelem nebo BGP. K filtrování provozu mezi virtuální sítě můžete použít taky síťové virtuální zařízení.

**Směrování**

Volitelně můžete přepsat výchozí směrování Azure tím, že nakonfigurujete vlastní trasy nebo použijete trasy protokolu BGP přes bránu sítě.

Azure vytvoří směrovací tabulky, které umožňují vzájemnou komunikaci prostředků připojených k libovolné podsíti v jakékoli virtuální síti, a to ve výchozím nastavení. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Trasy definované uživatelem:** Můžete vytvářet vlastní směrovací tabulky s trasami, které určují, kam se má provoz směrovat pro každou podsíť. Další informace o trasách definovaných uživatelem najdete v článku [trasy definované uživatelem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Trasy protokolu BGP:** Pokud připojíte virtuální síť k místní síti pomocí připojení Azure VPN Gateway nebo ExpressRoute, můžete trasy protokolu BGP rozšířit na své virtuální sítě.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybridní připojení k Internetu: Připojení k místní síti
Místní síť můžete připojit k virtuální síti pomocí libovolné kombinace následujících možností:

-   Připojení k internetu

-   VPN typu Point-to-Site (P2S VPN)

-   Síť VPN typu Site-to-Site (S2S VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Připojení k Internetu

Vzhledem k tomu, že je jeho název navržený, připojení k Internetu zpřístupňuje vaše úlohy z internetu tím, že vystavuje různé veřejné koncové body pro úlohy, které žijí v rámci virtuální sítě. Tyto úlohy mohou být vystaveny pomocí [internetového Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) nebo pouhým přiřazením veřejné IP adresy k virtuálnímu počítači. Díky tomu je možné, že cokoli na Internetu bude možné dosáhnout tohoto virtuálního počítače, za předpokladu, že hostitelská brána firewall, [skupiny zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)a [uživatelsky definované trasy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) umožňují, aby k nim mohlo dojít.

V tomto scénáři můžete vystavit aplikaci, která musí být veřejná na internetu a může se k ní připojit odkudkoli nebo z konkrétních míst v závislosti na konfiguraci vašich úloh.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Síť VPN typu Point-to-site nebo site-to-Site VPN
Tyto dvě spadají do stejné kategorie. Oba potřebují, aby vaše virtuální síť měly VPN Gateway a Vy se k ní můžete připojit pomocí klienta VPN pro vaši pracovní stanici jako součást [Konfigurace Point-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) , nebo můžete nakonfigurovat místní [zařízení VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) tak, aby bylo možné ukončit síť Site-to-site. S2S. Tímto způsobem se můžou místní zařízení připojit k prostředkům v rámci virtuální sítě.

Konfigurace Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení jednotlivých klientských počítačů k virtuální síti. P2S je připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol).

![Point-to-Site VPN](./media/network-security/azure-network-security-fig-5.png)

Připojení typu Point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například z domova nebo z centra konference, nebo pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení typu P2S nevyžadují zařízení VPN ani veřejnou IP adresu. Připojení VPN se navazuje ze strany klientského počítače. Proto se P2S nedoporučuje způsob, jak se připojit k Azure pro případ, že budete potřebovat trvalé připojení z mnoha místních zařízení a počítačů k síti Azure.

![Site-to-Site VPN](./media/network-security/azure-network-security-fig-6.png)

> [!Note]
> Další informace o připojeních Point-to-site najdete v tématu [Point-to-site FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2).

Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Toto připojení probíhá přes Internet a umožňuje propojení informací mezi vaší sítí a Azure v rámci šifrovaného odkazu. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. Tunelové šifrování se provádí pomocí [režimu tunelového propojení IPSec](https://technet.microsoft.com/library/cc786385.aspx).

I když je síť VPN typu Site-to-site důvěryhodná, spolehlivá a zavedená technologie, provoz v rámci tunelu prochází internetem. Šířka pásma je navíc relativně omezená na maximum přibližně 200 MB/s.

Pokud pro připojení mezi různými místy potřebujete mimořádnou úroveň zabezpečení nebo výkonu, doporučujeme pro připojení mezi různými místy použít Azure ExpressRoute. ExpressRoute je vyhrazené propojení WAN mezi vaším místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že se jedná o připojení výpovědi, vaše data necestují přes Internet, a proto se nezveřejňují potenciální rizika vyplývající z internetové komunikace.

> [!Note]
> Další informace o branách VPN najdete v tématu informace o službě [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Vyhrazené propojení WAN
Microsoft Azure ExpressRoute umožňuje rozšiřování místních sítí do Azure prostřednictvím vyhrazeného privátního připojení, které usnadňuje poskytovatel připojení.

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. To dovoluje připojením ExpressRoute poskytovat větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.

![ Vyhrazené propojení WAN](./media/network-security/azure-network-security-fig-7.png)

> [!Note]
> Informace o tom, jak připojit síť k Microsoftu pomocí ExpressRoute, najdete v tématu [modely připojení ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) a [ExpressRoute Technical Overview](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Stejně jako u možností sítě VPN typu Site-to-site ExpressRoute umožňuje také připojit se k prostředkům, které nejsou nutně v jedné virtuální síti. Ve skutečnosti v závislosti na SKU se můžete připojit k 10 virtuální sítě. Pokud máte [doplněk Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), můžou být v závislosti na šířce pásma dostupná připojení až 100 virtuální sítě. Další informace o tom, jaké typy připojení vypadají, najdete v tématu [diagramy topologie připojení](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json)pro čtení.

### <a name="security-controls"></a>Ovládací prvky zabezpečení
Azure Virtual Network poskytuje zabezpečenou logickou síť, která je izolovaná od jiných virtuálních sítí, a podporuje řadu bezpečnostních mechanismů, které používáte v místních sítích. Zákazníci vytvářejí vlastní strukturu pomocí: podsítí – používají svůj vlastní privátní rozsah IP adres, konfigurují tabulky směrování, skupiny zabezpečení sítě, seznamy řízení přístupu (ACL), brány a virtuální zařízení pro spouštění svých úloh v cloudu.

Níže jsou uvedené ovládací prvky zabezpečení, které můžete použít ve virtuálních sítích Azure:

-   Řízení přístupu k síti

-   Trasy definované uživatelem

-   Zařízení zabezpečení sítě

-   Application Gateway

-   Firewall webových aplikací Azure

-   Řízení dostupnosti sítě

#### <a name="network-access-controls"></a>Řízení přístupu k síti
I když je Azure Virtual Network (VNet) základem síťového modelu Azure a poskytuje izolaci a ochranu, jsou [skupiny zabezpečení sítě (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) hlavním nástrojem, který používáte k vymáhání a řízení pravidel síťového provozu na úrovni sítě.

![ Řízení přístupu k síti](./media/network-security/azure-network-security-fig-8.png)


Přístup můžete řídit povolením nebo zakázáním komunikace mezi úlohami v rámci virtuální sítě, ze systémů v sítích zákazníka prostřednictvím připojení mezi různými místy, nebo přímou internetovou komunikací.

V diagramu se virtuální sítě i skupin zabezpečení sítě nacházejí v určité vrstvě v celkovém zásobníku zabezpečení Azure, kde se skupin zabezpečení sítě, UDR a síťová virtuální zařízení dají použít k vytváření hranic zabezpečení pro ochranu nasazení aplikací v chráněné síti.

Skupin zabezpečení sítě použít k vyhodnocení provozu 5-Tuple (a používají se v pravidlech, která nakonfigurujete pro NSG):

-   [Zdrojová a cílová IP adresa](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Zdrojový a cílový port](https://technet.microsoft.com/library/dd197515)

-   Protokol: Protokol [TCP (Transmission Control Protocol)](https://technet.microsoft.com/library/cc940037.aspx) nebo [UDP (User Datagram Protocol)](https://technet.microsoft.com/library/cc940034.aspx)

To znamená, že můžete řídit přístup mezi jedním virtuálním počítačem a skupinou virtuálních počítačů nebo jedním virtuálním počítačem na jiný virtuální počítač nebo mezi celými podsítěmi. Znovu mějte na paměti, že se jedná o jednoduché filtrování stavových paketů, nikoli úplnou kontrolu paketů. Ve skupině zabezpečení sítě není k dispozici žádná ověření protokolu nebo identifikátory na úrovni sítě nebo IP adresy.

NSG obsahuje některá předdefinovaná pravidla, o kterých byste měli vědět. Toto jsou:

-   **Povolení veškerého provozu v konkrétní virtuální síti:** Všechny virtuální počítače ve stejné službě Azure Virtual Network můžou vzájemně komunikovat.

-   **Povolit příchozí vyrovnávání zatížení Azure:**  toto pravidlo umožňuje provoz ze všech zdrojových adres na jakoukoli cílovou adresu pro Azure Load Balancer.

-   **Odepřít všechna příchozí:**  toto pravidlo blokuje veškerý provoz z Internetu, který jste výslovně povolili.

-   **Povolení odchozího provozu do Internetu:** Toto pravidlo umožňuje virtuálním počítačům iniciovat připojení k Internetu. Pokud nechcete iniciovat tato připojení, musíte vytvořit pravidlo pro blokování těchto připojení nebo vynucení vynuceného tunelování.

#### <a name="system-routes-and-user-defined-routes"></a>Systémové trasy a trasy definované uživatelem

Když přidáte virtuální počítače do virtuální sítě (VNet) v Azure, zjistíte, že virtuální počítače můžou vzájemně komunikovat přes síť, a to automaticky. Není nutné určit bránu, ani když jsou virtuální počítače v různých podsítích.

Totéž platí pro komunikaci z virtuálních počítačů do veřejného internetu, a dokonce i do vaší místní sítě, pokud je dostupné hybridní připojení z Azure do vlastního datacentra.

![Systémové trasy](./media/network-security/azure-network-security-fig-9.png)

Tento tok komunikace je možný díky tomu, že Azure pomocí řady systémových tras definuje toky provozu IP. Systémové trasy řídí tok komunikace v těchto scénářích:

-   Ze stejné podsítě.

-   Z jedné podsítě do jiné v rámci jedné virtuální sítě.

-   Z virtuálních počítačů do internetu.

-   Z jedné virtuální sítě do jiné prostřednictvím brány sítě VPN.

-   Z virtuální sítě do jiné virtuální sítě prostřednictvím partnerského vztahu virtuálních sítí ([řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Z virtuální sítě do místní sítě prostřednictvím brány sítě VPN.

Řada podniků má přísné požadavky na zabezpečení a dodržování předpisů, které vyžadují místní kontrolu všech síťových paketů, aby bylo možné uplatnit konkrétní zásady. Azure poskytuje mechanizmus nazvaný [vynucené tunelování](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) , které směruje provoz z virtuálních počítačů do místního prostředí tím, že vytvoří vlastní trasu nebo inzerci [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) prostřednictvím ExpressRoute nebo VPN.

Vynucené tunelování v Azure se konfiguruje prostřednictvím virtuální sítě trasy definované uživatelem (UDR). Přesměrování přenosů do místní lokality je vyjádřena jako výchozí trasu pro bránu Azure VPN.

V následující části jsou uvedené aktuální omezení směrovací tabulky a trasy pro služby Azure Virtual Network:

- Každé podsíti virtuální sítě má integrované, směrovací tabulky systému. Systémovou tabulku směrování má následující tři skupiny tras:

  -  **Trasy místní virtuální sítě:** Přímo k cílovým virtuálním počítačům ve stejné virtuální síti

  - **Místní trasy:** K bráně Azure VPN Gateway

  -  **Výchozí trasa:** Přímo na Internet. Pakety určené k privátním IP adresám, které nejsou pokryté předchozími dvěma trasami, jsou vyřazeny.

- S vydáním uživatelem definovaných tras můžete vytvořit směrovací tabulku, která přidá výchozí trasu, a potom přidružit směrovací tabulku k podsíti virtuální sítě a povolit vynucené tunelování v těchto podsítích.

- Budete muset nastavit "výchozí web" mezi místy místní servery připojené k virtuální síti.

- Vynucené tunelování musí být přidružen virtuální síť, která má bránu dynamického směrování VPN (není statická brána).

- ExpressRoute se vynucené tunelování přes tento mechanismus není nakonfigurovaná, ale místo toho zajišťuje inzeruje výchozí trasu prostřednictvím relací vytvoření partnerského vztahu protokolu BGP ExpressRoute.

> [!Note]
> Další informace najdete v dokumentaci k [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) , kde najdete další informace.

#### <a name="network-security-appliances"></a>Zařízení zabezpečení sítě
I když skupiny zabezpečení sítě a uživatelsky definované trasy poskytují určitou míru zabezpečení sítě v síťové a transportní vrstvě [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), existují situace, kdy chcete nebo potřebujete povolit zabezpečení na vyšších úrovních zásobník sítě. V takových situacích doporučujeme nasadit zařízení zabezpečení virtuální sítě poskytovaná partnery Azure.

![Zařízení zabezpečení sítě](./media/network-security/azure-network-security-fig-10.png)

Zařízení zabezpečení sítě Azure zlepšují zabezpečení virtuální sítě a síťové funkce a jsou k dispozici od mnoha dodavatelů prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com). Tato virtuální bezpečnostní zařízení je možné nasadit, aby poskytovala:

-   Vysoce dostupné brány firewall

-   Prevence vniknutí

-   Zjišťování neoprávněných vniknutí

-   Firewall webových aplikací (WAF)

-   Optimalizace sítě WAN

-   Směrování

-   Vyrovnávání zatížení

-   Síť VPN

-   Správa certifikátů

-   Active Directory

-   Vícefaktorové ověřování

#### <a name="application-gateway"></a>Aplikační brána

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) je vyhrazené virtuální zařízení, které jako službu poskytuje řadič pro doručování aplikací (ADC).

 ![Application Gateway](./media/network-security/azure-network-security-fig-11.png)

Application Gateway vám umožní optimalizovat výkon a dostupnost webové farmy přesměrováním ukončení protokolu SSL náročných na výkon procesoru na aplikační bránu (SSL-snižování zátěže). Nabízí také další možnosti směrování vrstvy 7, včetně:

-   Distribuce příchozích přenosů s kruhovým dotazováním

-   Spřažení relace na základě souborů cookie

-   Směrování na základě cesty URL

-   Možnost hostování více webů za jednou Application Gateway


[Firewall webových aplikací (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) je také k dispozici jako součást služby Application Gateway. Tím se zajistí Ochrana webových aplikací před běžnými webovými ohroženími zabezpečení a zneužití. Application Gateway je možné nakonfigurovat jako internetovou bránu, pouze interní bránu nebo kombinaci obou.

Application Gateway WAF lze spustit v režimu detekce nebo prevence. Běžný případ použití je, aby správci mohli spustit v režimu detekce a sledovat provoz škodlivých vzorů. Po zjištění potenciálního zneužití zablokuje režim prevence podezřelý příchozí provoz.

 ![Application Gateway](./media/network-security/azure-network-security-fig-12.png)

Kromě toho Application Gateway WAF pomáhá monitorovat webové aplikace proti útokům pomocí protokolu WAF v reálném čase, který je integrovaný do [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) a [Azure Security Center](https://azure.microsoft.com/services/security-center/) ke sledování výstrah WAF a ke snadnému sledování trendů.

Protokol JSON ve formátu JSON přejde přímo k účtu úložiště zákazníka. U těchto protokolů máte plnou kontrolu a můžete použít vlastní zásady uchovávání informací.

Tyto protokoly můžete také ingestovat do vlastního analytického systému pomocí [Azure log Integration](https://aka.ms/AzLog). Protokoly WAF jsou také integrované do [protokolů Azure monitor](/azure/log-analytics/log-analytics-overview) , takže můžete používat protokoly Azure monitor k provádění sofistikovaných podrobných dotazů.

#### <a name="azure-web-application-firewall-waf"></a>Firewall webových aplikací Azure (WAF)

Webové aplikace jsou stále větším cílem škodlivých útoků, které využívají běžné známé chyby zabezpečení, jako jsou například injektáže SQL, útoky prostřednictvím skriptování mezi weby a další útoky, které se zobrazují v [horní části OWASP 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Prevence takového zneužití v aplikaci vyžaduje přísnou údržbu, opravy a monitorování ve více vrstvách topologie aplikace.

 ![Firewall webových aplikací Azure (WAF)](./media/network-security/azure-network-security-fig-13.png)

Centralizované [brány firewall webových aplikací (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) se můžou chránit před webovými útoky a zjednodušují správu zabezpečení, aniž by museli provádět žádné změny aplikací.

Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

#### <a name="network-availability-controls"></a>Ovládací prvky dostupnosti sítě

Existují různé možnosti distribuce síťového provozu pomocí systému Microsoft Azure. Tyto možnosti fungují různě, obsahují jiné sady funkcí a podporují různé scénáře. Lze je používat jednotlivě nebo je kombinovat.

Řízení dostupnosti sítě je následující:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Nástroj pro vyrovnávání zatížení Azure**

Zajišťuje vysokou dostupnost a výkon sítě pro vaše aplikace. Jedná se o nástroj pro vyrovnávání zatížení vrstvy 4 (TCP, UDP), který distribuuje příchozí provoz mezi zdravými instancemi služeb definovaných v sadě s vyrovnáváním zatížení.

 ![Azure Load Balancer](./media/network-security/azure-network-security-fig-14.png)


Azure Load Balancer lze nakonfigurovat na:

-   Vyrovnávání zatížení příchozího internetového provozu do virtuálních počítačů. Tato konfigurace se označuje jako [internetové vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Vyrovnávání zatížení mezi virtuálními počítači ve virtuální síti, mezi virtuálními počítači v cloudových službách nebo mezi místními počítači a virtuálními počítači ve virtuální síti mezi pracovišti. Tato konfigurace se označuje jako [interní vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Předejte externí provoz na konkrétní virtuální počítač.

Všechny prostředky v cloudu potřebují veřejnou IP adresu, aby bylo možné získat přístup z Internetu. Cloudová infrastruktura v Azure používá IP adresy Nesměrovatelné pro své prostředky. Azure ke komunikaci s internetem používá překlad síťových adres (NAT) s veřejnými IP adresami.

 **Aplikační brána**

 Application Gateway funguje v aplikační vrstvě (vrstva 7 v zásobníku referenčních informací sítě OSI). Funguje jako služba reverzních proxy serverů, ukončuje připojení klienta a předává požadavky do back-endových koncových bodů.

 **Traffic Manager**

Microsoft Azure Traffic Manager vám umožní řídit distribuci provozu uživatelů pro koncové body služby v různých datových centrech. K koncovým bodům služby podporovaným Traffic Manager patří virtuální počítače Azure, Web Apps a cloudové služby. Službu Traffic Manager můžete používat také s externími koncovými body mimo Azure.

Traffic Manager používá službu DNS (Domain Name System) k přímému směrování požadavků klientů na nejvhodnější koncový bod na základě [metody směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) a stavu koncových bodů. Traffic Manager poskytuje řadu metod směrování provozu, které vyhovují různým potřebám aplikací, [monitorování](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)stavu koncových bodů a automatickému převzetí služeb při selhání. Služba Traffic Manager je odolná vůči selhání, a to i selhání celé oblasti Azure.

Azure Traffic Manager umožňuje řídit distribuci provozu napříč koncovými body vaší aplikace. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure.

Traffic Manager poskytuje dvě klíčové výhody:

-   Distribuce provozu podle jedné z několika [metod směrování provozu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Průběžné monitorování stavu koncového bodu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) a automatického převzetí služeb při selhání koncových bodů.

Když se klient pokusí připojit ke službě, musí nejdřív vyhodnotit název DNS služby na IP adresu. Klient se pak připojí k této IP adrese a bude mít přístup ke službě. Traffic Manager používá DNS k přímému směrování klientů na konkrétní koncové body služby na základě pravidel metody směrování provozu. Klienti se připojují k vybranému koncovému bodu přímo. Traffic Manager není proxy server nebo brána. Traffic Manager se nezobrazuje přenos procházející mezi klientem a službou.

### <a name="azure-network-validation"></a>Ověření sítě Azure

Ověření sítě Azure znamená, že síť Azure pracuje tak, jak je nakonfigurovaná, a ověření se dá provádět pomocí služeb a funkcí, které jsou k dispozici pro monitorování sítě. Pomocí Azure Network Watcher můžete získat přístup k spoustuům a diagnostickým funkcím, které vám poskytnou přehledy o výkonu a stavu vaší sítě. Tyto možnosti jsou dostupné prostřednictvím portálu, Power Shell, CLI, REST API a sady SDK.

Provozní zabezpečení Azure odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure. Provozní zabezpečení Azure je postavené na platformě, která zahrnuje znalostní báze získané různými funkcemi, které jsou jedinečné pro společnost Microsoft, včetně Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center. a podrobné povědomí o počítačové bezpečnostní hrozbě na šířku.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Sledovací proces sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Analýzy Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Lidé a procesy, které provozují Microsoft Azure, jsou možná nejdůležitější funkcí zabezpečení platformy. Tato část popisuje funkce globální infrastruktury Datacenter od Microsoftu, které vám pomůžou zdokonalit a udržovat zabezpečení, kontinuitu a ochranu osobních údajů.

Infrastrukturu aplikace obvykle tvoří celá řada komponent, může to být třeba virtuální počítač, účet úložiště a virtuální síť nebo webová aplikace, databáze, databázový server a služby jiných výrobců. Tyto komponenty nevidíte jako samostatné entity, ale jako související a vzájemně provázané části jedné entity. Chcete je nasadit, spravovat a monitorovat jako skupinu. Azure Resource Manager umožňuje pracovat s prostředky ve vašem řešení jako se skupinou.

Všechny prostředky pro vaše řešení můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje funkce zabezpečení, auditování a označování, které vám po nasazení pomohou prostředky spravovat.

**Výhody použití Správce prostředků**

Resource Manager poskytuje několik výhod:

-   Můžete všechny prostředky pro vaše řešení nasadit, spravovat a monitorovat jako skupinu a nemusíte je zpracovávat jednotlivě.

-   Můžete svoje řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně.

-   Infrastrukturu můžete spravovat pomocí deklarativních šablon místo skriptů.

-   Můžete definovat závislosti mezi prostředky, aby byly nasazeny ve správném pořadí.

-   Můžete využít řízení přístupu pro všechny služby ve vaší skupině prostředků, protože do platformy pro správu je nativně integrováno řízení přístupu na základě role (RBAC).

-   Můžete označit prostředky pomocí značek a logicky tak uspořádat všechny prostředky ve svém předplatném.

-   Můžete vyjasnit fakturaci vaší organizace zobrazením nákladů na skupinu pro sdílení prostředků.

> [!Note]
> Resource Manager poskytuje nový způsob nasazení a správy vašich řešení. Pokud jste využívali dřívější model nasazení a chcete se dozvědět o změnách, přečtěte si článek věnovaný [nasazení Resource Manageru a klasickému nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Protokolování a sledování sítě Azure

Azure nabízí řadu nástrojů, které můžete využít k monitorování, prevenci, detekci a reakci na události zabezpečení sítě. K dispozici jsou některé z nejúčinnějších nástrojů, které jsou v této oblasti dostupné:

-   Network Watcher

-   Monitorování úrovně síťových prostředků

-   Protokoly služby Azure Monitor

### <a name="network-watcher"></a>Sledovací proces sítě

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – monitorování založené na scénářích se poskytuje s funkcemi v Network Watcher. Tato služba zahrnuje zachytávání paketů, další směrování, ověření toku protokolu IP, zobrazení skupiny zabezpečení a protokoly NSG Flow. Monitorování úrovně scénáře nabízí kompletní pohled na síťové prostředky na rozdíl od monitorování jednotlivých síťových prostředků.

 ![Network Watcher](./media/network-security/azure-network-security-fig-15.png)

Network Watcher je místní služba, která umožňuje monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure. Nástroje pro diagnostiku a diagnostiku sítě, které jsou dostupné v Network Watcher, vám pomůžou pochopit, diagnostikovat a získávat přehledy vaší sítě v Azure.

Network Watcher v současné době má následující možnosti:

#### <a name="topology"></a>Topologie

[Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) vrátí graf síťových prostředků ve virtuální síti. Graf znázorňuje vzájemné propojení mezi prostředky, které představuje koncové síťové připojení. Topologie v portálu vrací objekty prostředků podle jednotlivých virtuálních sítí. Relace jsou znázorněny na řádcích mezi prostředky mimo Network Watcher oblast, i když se ve skupině prostředků nezobrazí. Prostředky vrácené v zobrazení portálu jsou podmnožinou síťových komponent, které jsou v grafu. Pokud chcete zobrazit úplný seznam síťových prostředků, můžete použít [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) nebo [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Vzhledem k tomu, že se prostředky vrátí, propojení mezi nimi se modeluje v rámci dvou vztahů.

- **Zahrnutí** -Virtual Network obsahuje podsíť, která obsahuje síťovou kartu.

- **Přidruženo** – k virtuálnímu počítači je přidružená síťová karta.

#### <a name="variable-packet-capture"></a>Proměnné zachytávání paketů

Network Watcher [variabilní zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) umožňuje vytvářet relace zachytávání paketů pro sledování provozu do a z virtuálního počítače. Zachytávání paketů pomáhá diagnostikovat anomálie sítě jak po reaktivně, tak i i na jejich aktivitě. Mezi další použití patří shromažďování statistik sítě a získání informací o neoprávněných vniknutích k síti, k ladění komunikace mezi klientem a serverem a mnohem více.

Zachytávání paketů je rozšíření virtuálního počítače, které se vzdáleně spouští prostřednictvím Network Watcher. Tato schopnost usnadňuje zatížení při ručním zachytávání paketů na požadovaném virtuálním počítači, který šetří cenné časy. Zachytávání paketů se dá aktivovat prostřednictvím portálu, PowerShellu, CLI nebo REST API. Jedním z příkladů, jak se dá zachytávání paketů aktivovat, jsou výstrahy virtuálního počítače.

#### <a name="ip-flow-verify"></a>Ověření toku protokolu IP

[Toky protokolu IP ověřují](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) , jestli je paket povolený nebo zakázaný z virtuálního počítače na základě pěti informací v řazené kolekci členů. Tyto informace se skládají ze směru, protokolu, místní IP adresy, vzdáleného protokolu IP, místního portu a vzdáleného portu. Pokud je paket zakázán skupinou zabezpečení, je vrácen název pravidla, které paket zamítlo. I když se dá zvolit kterákoli zdrojová nebo cílová IP adresa, tato funkce pomůže správcům rychle diagnostikovat problémy s připojením z Internetu a z místního prostředí nebo do něj.

Toky IP ověřují cíle síťového rozhraní virtuálního počítače. Tok přenosů se pak ověří na základě nakonfigurovaného nastavení do nebo z tohoto síťového rozhraní. Tato možnost je užitečná v případě, že je potřeba potvrdit, jestli pravidlo ve skupině zabezpečení sítě blokuje příchozí nebo odchozí provoz do nebo z virtuálního počítače.

#### <a name="next-hop"></a>Další směrování

Určuje [Další segment směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pro pakety směrované do síťových prostředků Azure, které vám umožní diagnostikovat všechny nesprávně nakonfigurované trasy definované uživatelem. Provoz z virtuálního počítače se pošle do cíle na základě efektivních tras přidružených k síťovému rozhraní. Další segment směrování Získá typ dalšího segmentu směrování a IP adresu paketu z konkrétního virtuálního počítače a síťové karty. Pomůže vám to určit, jestli se má paket směrovat do cílového umístění, nebo že se jedná o černých děr v provozu.

Další segment směrování také vrátí směrovací tabulku přidruženou k dalšímu segmentu směrování. Při dotazování dalšího směrování, pokud je trasa definovaná jako uživatelem definovaná trasa, se tato trasa vrátí. V opačném případě další směrování vrátí "systémovou trasu".

#### <a name="security-group-view"></a>Zobrazení skupiny zabezpečení

Získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači. Skupiny zabezpečení sítě jsou přidružené na úrovni podsítě nebo na úrovni síťové karty. V případě, že se přidruží na úrovni podsítě, platí pro všechny instance virtuálních počítačů v podsíti. [Zobrazení skupina zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) sítě vrátí všechna nakonfigurovaná skupin zabezpečení sítě a pravidla, která jsou přidružená na síťové kartě a na úrovni podsítě pro virtuální počítač, který poskytuje přehled o konfiguraci. Kromě toho jsou pro každou ze síťových adaptérů ve virtuálním počítači vracena platná pravidla zabezpečení. Pomocí zobrazení skupiny zabezpečení sítě můžete vyhodnotit virtuální počítač pro ohrožení zabezpečení sítě, například otevřít porty. Můžete také ověřit, jestli vaše skupina zabezpečení sítě funguje podle očekávání, na základě [porovnání mezi nakonfigurovanými a platnými pravidly zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Protokolování toku NSG

 Protokoly toku pro skupiny zabezpečení sítě umožňují zachytit protokoly související s přenosy, které jsou povolené nebo zakázané pravidly zabezpečení ve skupině. Tok je definován pomocí informací o pěti seřazených kolekcích členů – zdrojová IP adresa, cílová IP adresa, zdrojový port, cílový port a protokol.

[Protokoly toku skupin zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) jsou funkcí Network Watcher, která vám umožní zobrazit informace o příchozím a odchozím provozu IP přes skupinu zabezpečení sítě.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Řešení potíží s bránou virtuální sítě a připojením

Network Watcher poskytuje řadu možností, které se týkají porozumění síťovým prostředkům v Azure. Jednou z těchto funkcí je řešení potíží s prostředky. [Řešení potíží s prostředky](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) může být voláno pomocí PowerShellu, rozhraní příkazového řádku nebo REST API. Při volání Network Watcher zkontroluje stav Virtual Network brány nebo připojení a vrátí své závěry.

Tato část vás provede různými úlohami správy, které jsou aktuálně k dispozici pro řešení potíží s prostředky.

-   [Řešení potíží s bránou Virtual Network](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Řešení potíží s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Omezení pro předplatné sítě

[Omezení pro síťové předplatné](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytují podrobné informace o využití všech síťových prostředků v rámci předplatného v oblasti s maximálním počtem dostupných prostředků.

#### <a name="configuring-diagnostics-log"></a>Konfigurace diagnostického protokolu

Network Watcher poskytuje zobrazení [diagnostických protokolů](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) . Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto zobrazení můžete snadno a rychle povolit a zakázat síťové prostředky.

### <a name="network-resource-level-monitoring"></a>Monitorování úrovně síťových prostředků

K dispozici jsou následující funkce pro monitorování úrovně prostředků:

#### <a name="audit-log"></a>Protokol auditu

Operace prováděné jako součást konfigurace sítí jsou protokolovány. Tyto protokoly auditu jsou nezbytné k navázání různých dodržování předpisů. Tyto protokoly můžete zobrazit v Azure Portal nebo načíst pomocí nástrojů Microsoftu, jako jsou Power BI nebo nástroje třetích stran. Protokoly auditu jsou k dispozici prostřednictvím portálu, PowerShellu, CLI a rozhraní REST API.

> [!Note]
> Další informace o protokolech auditu najdete v tématu [operace auditu s správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Protokoly auditu jsou k dispozici pro operace provedené na všech síťových prostředcích.


#### <a name="metrics"></a>Metriky

Metriky představují měření výkonu a čítače shromážděné za určité období. Metriky jsou aktuálně k dispozici pro Application Gateway. Metriky lze použít k aktivaci výstrah na základě prahové hodnoty. Služba Azure Application Gateway ve výchozím nastavení monitoruje stav všech prostředků v rámci fondu back-endu a automaticky odebere z fondu prostředky považované za chybné. Application Gateway nadále monitorovat poškozené instance a přidává je zpátky do správného fondu back-end, jakmile budou k dispozici a reagují na sondy stavu. Application Gateway odesílá sondy stavu se stejným portem, který je definovaný v nastavení back-endu HTTP. Tato konfigurace zajišťuje, že sonda testuje stejný port, který zákazníci používají pro připojení k back-endu.

> [!Note]
> V tématu [diagnostika Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) můžete zobrazit, jak se dají metriky použít k vytváření výstrah.

#### <a name="diagnostic-logs"></a>Diagnostické protokoly

Pravidelné a spontánní události vytváří síťové prostředky a přihlásily se do centra událostí nebo Azure Monitor protokoly. Tyto protokoly poskytují přehled o stavu prostředku. Tyto protokoly můžete zobrazit v nástrojích, jako jsou Power BI a protokoly Azure Monitor. Informace o tom, jak zobrazit diagnostické protokoly, [](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)najdete v Azure Monitorch protokolech.

Diagnostické protokoly jsou k dispozici pro [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher poskytuje zobrazení diagnostických protokolů. Toto zobrazení obsahuje všechny síťové prostředky, které podporují protokolování diagnostiky. Z tohoto zobrazení můžete snadno a rychle povolit a zakázat síťové prostředky.

### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) jsou služby v Azure, které monitorují vaše cloudové a místní prostředí a udržují jejich dostupnost a výkon. Shromažďuje data generovaná prostředky ve vašem cloudovém a místním prostředí a také data z dalších nástrojů pro monitorování a poskytuje analýzy napříč zdroji.

Protokoly Azure Monitor nabízí následující řešení pro monitorování sítí:

-   Network Performance Monitor (NPM)

-   Analýza Azure Application Gateway

-   Analýzy skupin zabezpečení sítě Azure

#### <a name="network-performance-monitor-npm"></a>Sledování výkonu sítě (NPM)
Řešení pro správu [Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) je řešení pro monitorování sítě, které monitoruje stav, dostupnost a dostupnost sítí.

Slouží k monitorování připojení mezi:

-   veřejný cloud a místní

-   datová centra a umístění uživatelů (pobočky)

-   podsítě hostující různé úrovně vícevrstvé aplikace.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Azure Application Gateway Analytics v protokolech Azure Monitor

Pro aplikační brány se podporují tyto protokoly:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Pro aplikační brány se podporují následující metriky:

-   propustnost 5 minut

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Analýzy skupin zabezpečení sítě Azure v protokolech Azure Monitor

Pro [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)jsou podporovány následující protokoly:

- **NetworkSecurityGroupEvent:** Obsahuje položky, pro které se pravidla NSG aplikují na virtuální počítače a role instancí založené na adrese MAC. Stav těchto pravidel se shromáždí každých 60 sekund.

- **NetworkSecurityGroupRuleCounter:** Obsahuje položky pro počet, kolikrát se každé pravidlo NSG použije pro odepření nebo povolení provozu.

## <a name="next-steps"></a>Další postup
Další informace o zabezpečení najdete v našich podrobných tématech o zabezpečení:

-   [Protokoly Azure Monitor pro skupiny zabezpečení sítě (skupin zabezpečení sítě)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Inovace sítě, které řídí narušení cloudu](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [Softwaru Software síťového přepínače, který využívá globální Cloud Microsoftu](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Jak Microsoft vytváří svoji rychlou a spolehlivou globální síť](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Inovace sítě při rozsvícení](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
