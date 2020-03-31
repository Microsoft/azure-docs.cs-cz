---
title: Nejčastější dotazy k virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Odpovědi na nejčastější dotazy týkající se virtuálních sítí Microsoft Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244807"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Nejčastější dotazy ke službě Azure Virtual Network

## <a name="virtual-network-basics"></a>Základy virtuální sítě

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co je virtuální síť Azure??
Virtuální síť Azure (VNet) je reprezentace vlastní sítě v cloudu. Je to logická izolace cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě můžete použít k zřizování a správě virtuálních privátních sítí (VIRTUÁLNÍsítě) v Azure a volitelně propojit virtuální sítě s jinými virtuálními sítěmi v Azure nebo s místní IT infrastrukturou k vytváření hybridních nebo mezimístních řešení. Každá virtuální síť, kterou vytvoříte, má svůj vlastní blok CIDR a může být propojena s jinými virtuálními sítěmi a místními sítěmi, pokud se bloky CIDR nepřekrývají. Máte také kontrolu nastavení serveru DNS pro virtuální sítě a segmentaci virtuální sítě do podsítí.

Virtuální sítě slouží k:

* Vytvořte vyhrazenou virtuální síť určenou pouze pro privátní cloud. Někdy nepotřebujete pro vaše řešení konfiguraci mezi prostory. Když vytvoříte virtuální síť, vaše služby a virtuální počítače v rámci vaší virtuální sítě můžou přímo a bezpečně komunikovat mezi sebou v cloudu. Stále můžete nakonfigurovat připojení koncových bodů pro virtuální počítače a služby, které vyžadují internetovou komunikaci, jako součást vašeho řešení.

* Bezpečně rozšiřte své datové centrum. Pomocí virtuálních sítí můžete vytvářet tradiční sítě VIRTUÁLNÍ SÍTĚ (S2S) od sítě site-to-site, které bezpečně škálují kapacitu datového centra. Vpn S2S používají ipsec k zajištění zabezpečeného připojení mezi vaší firemní bránou VPN a Azure.

* Povolte scénáře hybridního cloudu. Virtuální sítě poskytují flexibilitu pro podporu řady scénářů hybridního cloudu. Cloudové aplikace můžete bezpečně propojit s libovolným typem místního systému, jako jsou sálové počítače a unixové systémy.

### <a name="how-do-i-get-started"></a>Jak mám začít?
Chcete-li začít, navštivte [dokumentaci k virtuální síti.](https://docs.microsoft.com/azure/virtual-network/) Tento obsah poskytuje přehled a informace o nasazení pro všechny funkce virtuální sítě.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Můžu používat virtuální sítě bez připojení mezi místními?
Ano. Virtuální síť můžete použít bez připojení k vašim prostorům. Můžete například spustit řadiče domény Microsoft Windows Server Active Directory a farmy SharePoint výhradně ve virtuální síti Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Můžu provádět optimalizaci sítě WAN mezi virtuálními sítěmi nebo virtuální sítí a místním datovým centrem?
Ano. Můžete nasadit [virtuální zařízení sítě optimalizace WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) od několika dodavatelů prostřednictvím Azure Marketplace.

## <a name="configuration"></a>Konfigurace

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jaké nástroje mám použít k vytvoření virtuální sítě?
K vytvoření nebo konfiguraci virtuální sítě můžete použít následující nástroje:

* portál Azure
* PowerShell
* Azure CLI
* Konfigurační soubor sítě (netcfg - pouze pro klasické virtuální sítě). Podívejte se [na článek Konfigurace virtuální sítě pomocí konfiguračního souboru sítě.](virtual-networks-using-network-configuration-file.md)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jaké rozsahy adres lze použít ve svých virtuálních sítích?
Libovolný rozsah IP adres definovaný v [rfc 1918](https://tools.ietf.org/html/rfc1918). Například 10.0.0.0/16. Nelze přidat následující rozsahy adres:
* 224.0.0.0/4 (Vícesměrové vysílání)
* 255.255.255.255/32 (Vysílání)
* 127.0.0.0/8 (Zpětná smyčka)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (Interní DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Můžu mít ve virtuálních sítích veřejné IP adresy?
Ano. Další informace o rozsahu veřejných IP adres naleznete [v tématu Vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network). Veřejné IP adresy nejsou přímo přístupné z internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existuje omezení počtu podsítí v mé virtuální síti?
Ano. Podrobnosti najdete v [tématu Limity Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Adresní prostory podsítě se nemohou překrývat.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existují nějaká omezení pro používání IP adres v těchto podsítích?
Ano. Azure si v každé podsíti vyhrazuje 5 IP adres. Jedná se o x.x.x.0-x.x.x.3 a poslední adresu podsítě. x.x.x.1-x.x.x.3 je vyhrazena v každé podsíti pro služby Azure.   
- x.x.x.0: Síťová adresa
- x.x.x.1: Vyhrazeno Pro Azure pro výchozí bránu
- x.x.x.2, x.x.x.3: Vyhrazeno pro Azure pro mapování IP adresy Azure DNS na místo virtuální sítě
- x.x.x.255: Adresa síťového vysílání

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak malé a jak velké mohou být virtuální sítě a podsítě?
Nejmenší podporovaná podsíť IPv4 je /29 a největší je /8 (pomocí definic podsítě CIDR).  Podsítě IPv6 musí mít velikost přesně /64.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Můžu do Azure přenést svoje sítě VLAN pomocí virtuálních sítí?
Ne. Virtuální sítě jsou překrytí vrstvy 3. Azure nepodporuje žádnou sémantiku vrstvy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Můžu ve virtuálních sítích a podsítích určit vlastní zásady směrování?
Ano. Můžete vytvořit tabulku postupu a přidružit ji k podsíti. Další informace o směrování v Azure najdete v [tématu Přehled směrování](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Podporují virtuální sítě vícesměrové vysílání nebo vysílání?
Ne. Vícesměrové vysílání a vysílání nejsou podporovány.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jaké protokoly lze použít v rámci virtuálních sítí?
V rámci virtuálních sítí můžete používat protokoly TCP, UDP a ICMP TCP/IP. Jednosměrové vysílání je podporováno v rámci virtuálních sítí, s výjimkou protokolu DHCP (DHCP) prostřednictvím jednosměrového vysílání (zdrojový port UDP/68 / cílový port UDP/67). Vícesměrové vysílání, všesměrové vysílání, pakety zapouzdřené protokolem IP v IP a pakety obecné zapouzdření směrování (GRE) jsou blokovány v rámci virtuálních sítí. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Můžu pingovat výchozí směrovače v rámci virtuální sítě?
Ne.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Mohu tracert použít k diagnostice připojení?
Ne.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Můžu po vytvoření virtuální sítě přidat podsítě?
Ano. Podsítě lze přidat do virtuálních sítí kdykoli, pokud rozsah adres podsítě není součástí jiné podsítě a v rozsahu adres virtuální sítě je volné místo.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Mohu po vytvoření podsítě upravit velikost podsítě?
Ano. Podsíť můžete přidat, odebrat, rozšířit nebo zmenšit, pokud v ní nejsou nasazené virtuální počítače ani služby.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Mohu podsítě po vytvoření upravit?
Ano. Můžete přidat, odebrat a upravit bloky CIDR používané virtuální sítí.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Pokud používám své služby ve virtuální síti, můžu se připojit k internetu?
Ano. Všechny služby nasazené v rámci virtuální sítě se můžou připojit k Internetu. Další informace o odchozích připojeních k internetu v Azure najdete v tématu [Odchozí připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud se chcete připojit k příchozí prostředek nasazené prostřednictvím Správce prostředků, musí mít prostředek mít veřejnou IP adresu přiřazenou k němu. Další informace o veřejných IP adresách najdete [v tématu Veřejné IP adresy](virtual-network-public-ip-address.md). Ke každé cloudové službě Azure nasazené v Azure je přiřazena veřejně adresovatelná virtuální ip adresa. Definujete vstupní koncové body pro role PaaS a koncové body pro virtuální počítače, aby tyto služby mohly přijímat připojení z Internetu.

### <a name="do-vnets-support-ipv6"></a>Podporují virtuální sítě IPv6?
Ano, virtuální sítě může být pouze IPv4 nebo duální zásobník (IPv4 + IPv6).  Podrobnosti najdete [v tématu Přehled IPv6 pro virtuální sítě Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Může virtuální síť span oblastí?
Ne. Virtuální síť je omezená na jednu oblast. Virtuální síť však zahrnuje zóny dostupnosti. Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Virtuální sítě v různých oblastech můžete připojit pomocí partnerského vztahu virtuální sítě. Podrobnosti najdete [v tématu Přehled partnerského vztahu virtuální sítě.](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Můžu virtuální síť připojit k jiné virtuální síti v Azure?
Ano. Jednu virtuální síť můžete připojit k jiné virtuální síti buď takto:
- **Partnerský vztah virtuální sítě**: Podrobnosti najdete v [tématu Přehled partnerského vztahu virtuální sítě](virtual-network-peering-overview.md)
- **Brána Azure VPN**: Podrobnosti najdete [v tématu Konfigurace připojení virtuální sítě k virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Překlad názvů (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jaké jsou moje možnosti DNS pro virtuální sítě?
Pomocí tabulky rozhodnutí na stránce [Překlad názvů pro virtuální počítača a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md) vás provedete všemi dostupnými možnostmi DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Můžu pro virtuální síť zadat servery DNS?
Ano. V nastavení virtuální sítě můžete zadat adresy IP serveru DNS. Toto nastavení se použije jako výchozí server (servery) DNS pro všechny virtuální servery ve virtuální síti.

### <a name="how-many-dns-servers-can-i-specify"></a>Kolik serverů DNS mohu zadat?
Referenční [limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Mohu po vytvoření sítě upravit servery DNS?
Ano. Seznam serverů DNS pro virtuální síť můžete kdykoli změnit. Pokud změníte seznam serverů DNS, budete muset provést obnovení zapůjčení DHCP na všech ovlivněných virtuálních počítačích ve virtuální síti, aby se nové nastavení DNS projevilo. Pro virtuální servery se systémem Windows OS `ipconfig /renew` to můžete provést zadáním přímo na virtuálním počítači. Další typy operačních systému naleznete v dokumentaci k obnovení zapůjčení DHCP pro konkrétní typ operačního systému. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co je DNS poskytovaná Azure a funguje s virtuálními sítěmi?
Azure-poskytované DNS je víceklientská služba DNS nabízená společností Microsoft. Azure registruje všechny vaše virtuální počítače a instance rolí cloudové služby v této službě. Tato služba poskytuje překlad názvů podle názvu hostitele pro virtuální chody a instance rolí obsažené ve stejné cloudové službě a podle fQDN pro virtuální chody a instance rolí ve stejné virtuální síti. Další informace o SLUŽBĚ DNS najdete v [tématu Překlad názvů pro instance rolí virtuálních vod a cloudových služeb](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existuje omezení prvních 100 cloudových služeb ve virtuální síti pro překlad názvů mezi klienty pomocí DNS poskytovaného Azure. Pokud používáte vlastní server DNS, toto omezení neplatí.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Můžu přepsat nastavení DNS na základě služby pro jeden virtuální počítač nebo cloudovou službu?
Ano. Servery DNS můžete nastavit na virtuální hod nebo cloudovou službu tak, aby přepsaly výchozí nastavení sítě. Doporučujeme však co nejvíce používat službu DNS v celé síti.

### <a name="can-i-bring-my-own-dns-suffix"></a>Mohu si přinést vlastní příponu DNS?
Ne. Pro virtuální sítě nelze zadat vlastní příponu DNS.

## <a name="connecting-virtual-machines"></a>Připojení virtuálních počítačů

### <a name="can-i-deploy-vms-to-a-vnet"></a>Můžu nasadit virtuální ms do virtuální sítě?
Ano. Všechna síťová rozhraní (NIC) připojená k virtuálnímu virtuálnímu počítače nasazeného prostřednictvím modelu nasazení Správce prostředků musí být připojena k virtuální síti. Virtuální počítače nasazené prostřednictvím klasického modelu nasazení lze volitelně připojit k virtuální síti.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jaké jsou různé typy IP adres, které můžu přiřadit virtuálním počítačům?
* **Soukromé:** Přiřazeno každé nic v rámci každého virtuálního virtuálního mísy. Adresa je přiřazena pomocí statické nebo dynamické metody. Privátní IP adresy se přiřazují z rozsahu, který jste zadali v nastavení podsítě vaší virtuální sítě. Prostředky nasazené prostřednictvím klasického modelu nasazení jsou přiřazeny privátní IP adresy, i když nejsou připojené k virtuální síti. Chování metody přidělení se liší v závislosti na tom, zda byl prostředek nasazen pomocí Správce prostředků nebo klasického modelu nasazení: 

  - **Správce prostředků**: Privátní IP adresa přiřazená dynamickou nebo statickou metodou zůstane přiřazena virtuálnímu počítači (Resource Manager), dokud není prostředek odstraněn. Rozdíl je v tom, že vyberete adresu přiřadit při použití statické a Azure zvolí při použití dynamické. 
  - **Classic**: Privátní IP adresa přiřazená dynamickou metodou se může změnit, když se virtuální počítač (klasický) virtuální počítač restartuje poté, co byl ve stavu zastavené (vyrovnané). Pokud potřebujete zajistit, aby se privátní IP adresa prostředku nasazeného prostřednictvím klasického modelu nasazení nikdy neměnila, přiřaďte privátní IP adresu statickou metodou.

* **Veřejné:** Volitelně přiřazené k síťové karty připojené k virtuálním počítačům nasazeným prostřednictvím modelu nasazení Azure Resource Manageru. Adresu lze přiřadit pomocí statické nebo dynamické metody přidělení. Všechny instance rolí virtuálních počítačů a cloudových služeb nasazené prostřednictvím klasického modelu nasazení existují v rámci cloudové služby, které je přiřazena *dynamická*, veřejná virtuální IP adresa (VIP). Veřejnou *statickou* IP adresu, nazývanou [vyhrazená IP adresa](virtual-networks-reserved-public-ip.md), lze volitelně přiřadit jako virtuální ip adresu. Veřejné IP adresy můžete přiřadit jednotlivým virtuálním počítačům nebo instantům rolí cloudových služeb nasazeným prostřednictvím klasického modelu nasazení. Tyto adresy se nazývají [veřejná IP adresa na úrovni instance (adresy ILPIP](virtual-networks-instance-level-public-ip.md) a lze je přiřadit dynamicky.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Můžu si rezervovat privátní IP adresu pro virtuální hod, který vytvořím později?
Ne. Nelze rezervovat privátní IP adresu. Pokud je k dispozici privátní IP adresa, server DHCP ji přiřadí k virtuálnímu virtuálnímu montovi nebo instanci role. Virtuální ho virtuálního počítačů může nebo nemusí být ten, který chcete privátní IP adresu přiřazenou. Můžete však změnit privátní IP adresu již vytvořeného virtuálního počítačů na libovolnou dostupnou privátní IP adresu.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Mění se privátní IP adresy pro virtuální sítě ve virtuální síti?
To závisí na okolnostech. Pokud byl virtuální virtuální ms nasazen prostřednictvím Správce prostředků, ne, bez ohledu na to, zda byla adresa IP přiřazena statickou nebo dynamickou alokační metodou. Pokud byl virtuální virtuální počítače nasazen prostřednictvím klasického modelu nasazení, dynamické IP adresy se můžou změnit při spuštění virtuálního počítače poté, co byl ve stavu zastavené (vyrovnané). Adresa se uvolní z virtuálního počítače nasazeného prostřednictvím obou modelů nasazení při odstranění virtuálního počítače.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Můžu ručně přiřadit IP adresy nic nic v rámci operačního systému virtuálního počítače?
Ano, ale nedoporučuje se, pokud to není nutné, například při přiřazování více IP adres virtuálnímu počítači. Podrobnosti najdete [v tématu Přidání více IP adres do virtuálního počítače](virtual-network-multiple-ip-addresses-portal.md#os-config). Pokud se změní IP adresa přiřazená k věnečnici Azure připojené k virtuálnímu počítači a IP adresa v operačním systému virtuálního počítače se liší, ztratíte připojení k virtuálnímu počítači.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Co se stane s mými IP adresami, když zastavím slot pro nasazení cloudové služby nebo odpnu virtuální hospo, co se stane s mými IP adresami?
Nic. IP adresy (veřejné VIRTUÁLNÍ IP, veřejné a soukromé) zůstávají přiřazené k slotu pro nasazení cloudové služby nebo virtuálnímu virtuálnímu počítače.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Můžu přesunout virtuální uživatele z jedné podsítě do jiné podsítě ve virtuální síti, aniž bych to bylo znovu nasazeno?
Ano. Další informace najdete v článku [Jak přesunout virtuální ho virtuálního uživatele nebo instance role do jiného článku podsítě.](virtual-networks-move-vm-role-to-subnet.md)

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Můžu pro svůj virtuální počítač nakonfigurovat statickou MAC adresu?
Ne. Adresu MAC nelze staticky konfigurovat.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Zůstane mac adresa pro můj virtuální počítač po jeho vytvoření stejná?
Ano, adresa MAC zůstává stejná pro virtuální počítač nasazený prostřednictvím Správce prostředků i klasické modely nasazení, dokud se neodstraní. Dříve byla uvolněna adresa MAC, pokud byl virtuální počítač zastaven (uvolněno), ale teď je adresa MAC zachována i v případě, že je virtuální počítač ve stavu s adlocated. Adresa MAC zůstává přiřazena k síťovému rozhraní, dokud není síťové rozhraní odstraněno nebo dokud se nezmění privátní adresa IP přiřazená primární konfiguraci IP primárního síťového rozhraní. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Můžu se připojit k internetu z virtuálního virtuálního virtuálního zařízení?
Ano. Všechny instance rolí virtuálních virtuálních zařízení a cloudových služeb nasazené v rámci virtuální sítě se můžou připojit k Internetu.

## <a name="azure-services-that-connect-to-vnets"></a>Služby Azure, které se připojují k virtuálním sítím

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Můžu s virtuální sítí používat Webové aplikace Služby aplikací Azure?
Ano. Webové aplikace můžete nasadit ve virtuální síti pomocí služby ASE (Prostředí služby App Service), připojit back-end vašich aplikací k vašim virtuálním sítím pomocí integrace virtuální sítě a uzamknout příchozí provoz do vaší aplikace pomocí koncových bodů služby. Další informace najdete v těchto článcích:

* [Síťové funkce služby App Service](../app-service/networking-features.md)
* [Vytváření webových aplikací v prostředí služby App Service](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrace aplikace s virtuální sítí Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Omezení přístupu ke službě App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Můžu nasadit cloudové služby s webovými a pracovními rolemi (PaaS) ve virtuální síti?
Ano. Můžete (volitelně) nasadit instance role cloudových služeb v rámci virtuálních sítí. Chcete-li tak učinit, zadejte název virtuální sítě a mapování rolí/podsítí v části konfigurace sítě konfigurace služby. Není nutné aktualizovat žádné binární soubory.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Můžu k virtuální síti připojit škálovací sadu virtuálních strojů?
Ano. Musíte připojit škálovací sadu virtuálních počítačů k virtuální síti.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existuje úplný seznam služeb Azure, ze kterých můžu nasadit prostředky do virtuální sítě?
Ano, podrobnosti najdete v tématu [Integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Jak můžu omezit přístup k prostředkům Azure PaaS z virtuální sítě?

Prostředky nasazené prostřednictvím některých služeb Azure PaaS (jako je Azure Storage a Azure SQL Database) můžou omezit přístup k síti k virtuální síti pomocí koncových bodů služby virtuální sítě nebo privátního propojení Azure. Podrobnosti najdete v tématu [Přehled koncových bodů služby Virtuální sítě](virtual-network-service-endpoints-overview.md), přehled [privátního spojení Azure.](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Můžu přesunout své služby do a z virtuálních sítí?
Ne. Služby nelze přesunout do a z virtuálních sítí. Chcete-li přesunout prostředek do jiné virtuální sítě, musíte prostředek odstranit a znovu nasadit.

## <a name="security"></a>Zabezpečení

### <a name="what-is-the-security-model-for-vnets"></a>Jaký je model zabezpečení pro virtuální sítě?
Virtuální sítě jsou izolované od sebe navzájem a další služby hostované v infrastruktuře Azure. Virtuální síť je hranice důvěryhodnosti.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Můžu omezit tok příchozího nebo odchozího provozu na prostředky připojené k virtuální síti?
Ano. [Skupiny zabezpečení sítě](security-overview.md) můžete použít na jednotlivé podsítě v rámci virtuální sítě, síťové karty připojené k virtuální síti nebo obojí.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Můžu implementovat bránu firewall mezi prostředky připojenými k virtuální síti?
Ano. Virtuální zařízení [sítě firewall můžete](https://azure.microsoft.com/marketplace/?term=firewall) nasadit od několika dodavatelů prostřednictvím Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Jsou k dispozici informace o zabezpečení virtuálních sítí?
Ano. Podrobnosti najdete v [tématu Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API, schémata a nástroje

### <a name="can-i-manage-vnets-from-code"></a>Můžu spravovat virtuální sítě z kódu?
Ano. Rozhraní REST API pro virtuální sítě můžete použít ve [Správci prostředků Azure](/rest/api/virtual-network) a [v klasických](https://go.microsoft.com/fwlink/?LinkId=296833) modelech nasazení.

### <a name="is-there-tooling-support-for-vnets"></a>Existuje podpora nástrojů pro virtuální sítě?
Ano. Další informace o používání:
- Portál Azure k nasazení virtuálních sítí prostřednictvím [Azure Resource Managera](manage-virtual-network.md#create-a-virtual-network) a [klasických](virtual-networks-create-vnet-classic-pportal.md) modelů nasazení.
- Prostředí PowerShell pro správu virtuálních sítí nasazených prostřednictvím [Správce prostředků](/powershell/module/az.network) a [klasických](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modelů nasazení.
- Rozhraní příkazového řádku Azure (CLI) k nasazení a správě virtuálních sítí nasazených prostřednictvím [Správce prostředků](/cli/azure/network/vnet) a [klasických](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) modelů nasazení.  

## <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

### <a name="what-is-vnet-peering"></a>Co je partnerský vztah virtuální sítě?
Partnerský vztah virtuální sítě (nebo partnerský vztah virtuální sítě) umožňuje připojení virtuálních sítí. Připojení partnerského vztahu virtuální sítě mezi virtuálními sítěmi umožňuje směrovat přenosy mezi nimi soukromě prostřednictvím adres IPv4. Virtuální počítače v partnerských virtuálních sítích spolu můžou komunikovat, jako by byly ve stejné síti. Tyto virtuální sítě může být ve stejné oblasti nebo v různých oblastech (označované také jako globální partnerský vztah virtuální sítě). Připojení partnerského vztahu virtuální sítě se můžou taky vytvářet napříč předplatnými Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Můžu vytvořit připojení partnerského vztahu k virtuální síti v jiné oblasti?
Ano. Globální partnerský vztah virtuální sítě umožňuje partnerských virtuálních sítí v různých oblastech. Globální partnerský vztah virtuální sítě je dostupný ve všech veřejných oblastech Azure, v oblastech cloudu v Číně a v cloudových oblastech pro správu. Globálně nelze globálně peer z veřejných oblastí Azure do národních cloudových oblastí.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Jaká omezení souvisí s globálním partnerským vztahem virtuální sítě a vykladači zatížení?
Pokud jsou dvě virtuální sítě ve dvou různých oblastech propojeny prostřednictvím globálního partnerského vztahu virtuální sítě, nemůžete se připojit k prostředkům, které jsou za základním nástrojem pro vyrovnávání zatížení prostřednictvím ip adresy front-endnástroje pro vyrovnávání zatížení. Toto omezení neexistuje pro standardní nástroj pro vyrovnávání zatížení.
Následující prostředky můžou používat základní vykladače zatížení, což znamená, že se k nim nemůžete dostat přes front-endovou IP adresu vykladače zatížení přes globální partnerský vztah virtuální sítě. Globální partnerský vztah virtuální sítě však můžete použít k dosažení prostředků přímo prostřednictvím jejich privátní IP adresy virtuální sítě, pokud je to povoleno. 
- Virtuální virtuální hráči za základními vykladači zatížení
- Škálovací sady virtuálních strojů se základními nástroji pro vyrovnávání zatížení 
- Redis Cache 
- Aplikační brána (v1) Skladová položka
- Service Fabric
- SQL MI
- API Management
- Služba Active Directory Domain Service (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service Environment

K těmto prostředkům se můžete připojit prostřednictvím ExpressRoute nebo virtuální sítě k virtuální síti prostřednictvím bran virtuální sítě.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Můžu povolit partnerský vztah virtuální sítě, pokud moje virtuální sítě patří do předplatných v rámci různých klientů Služby Azure Active Directory?
Ano. Je možné vytvořit partnerský vztah virtuální sítě (místní nebo globální), pokud vaše předplatná patří do různých klientů služby Azure Active Directory. Můžete to udělat prostřednictvím prostředí PowerShell nebo CLI. Portál ještě není podporován.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Připojení partnerského vztahu virtuální sítě je ve stavu *Zahájeno,* proč se nemůžu připojit?
Pokud je připojení partnerského vztahu ve stavu *Zahájeno,* znamená to, že jste vytvořili pouze jeden odkaz. Chcete-li navázat úspěšné připojení, musí být vytvořeno obousměrné propojení. Například pro druhou síť Virtuální sítě A na virtuální síť B, musí být vytvořen odkaz z VNetA na Virtuální síť a z Virtuální ho do Virtuální sítě. Vytvořením obou propojení se změní stav na *Připojeno*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Připojení partnerského vztahu virtuální sítě je ve stavu *Odpojeno,* proč nemůžu vytvořit připojení partnerského vztahu?
Pokud je vaše připojení partnerského vztahu virtuální sítě ve stavu *Odpojeno,* znamená to, že jeden z vytvořených odkazů byl odstraněn. Chcete-li obnovit připojení partnerského vztahu, budete muset propojení odstranit a znovu vytvořit.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Můžu svou virtuální síť s virtuální sítí s jiným předplatným sue?
Ano. Virtuální sítě můžete předešlový vztah napříč předplatnými a napříč oblastmi.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Můžu mít dvě virtuální sítě s odpovídajícími nebo překrývajícími se rozsahy adres?
Ne. Adresní prostory se nesmí překrývat, aby bylo možné povolit partnerský vztah virtuální sítě.

### <a name="how-much-do-vnet-peering-links-cost"></a>Kolik stojí propojení partnerského vztahu virtuální sítě?
Za vytvoření připojení partnerského vztahu virtuální sítě se neplatí žádné poplatky. Přenos dat mezi partnerskými sítěmi připojení se účtuje. [Viz zde](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Je přenos y partnerského vztahu virtuální sítě šifrovaný?
Ne. Přenos mezi prostředky v partnerských virtuálních sítích je soukromý a izolovaný. Zůstává zcela na páteřní straně Společnosti Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Proč je připojení partnerského vztahu v *odpojeném* stavu?
Připojení partnerského vztahu virtuální sítě přejdou do *odpojeného* stavu, když se odstraní jedno propojení partnerského vztahu virtuální sítě. Chcete-li obnovit úspěšné připojení partnerského vztahu, je nutné odstranit obě propojení.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Pokud jsem peer VNetA na Virtuální síť a já peer VNetB na VNetC, znamená to, že VNetA a VNetC jsou peered?
Ne. Přenosité partnerský vztah není podporován. Musíte peer VNetA a VNetC pro to to provést.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existují nějaká omezení šířky pásma pro připojení partnerského vztahu?
Ne. Partnerský vztah virtuální sítě, místní nebo globální, neukládá žádná omezení šířky pásma. Šířka pásma je omezena jenom virtuálním počítačem nebo výpočetním prostředkem.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Jak můžu řešit problémy partnerského vztahu virtuální sítě?
Zde je [průvodce poradcem při potížích,](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) který můžete vyzkoušet.

## <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Které oblasti Azure jsou dostupné pro virtuální síť TAP?
Virtuální síť TAP preview je k dispozici ve všech oblastech Azure. Monitorovaná síťová rozhraní, prostředek TAP virtuální sítě a kolektor nebo analytické řešení musí být nasazeny ve stejné oblasti.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Podporuje rozhraní TAP virtuální sítě možnosti filtrování u zrcadlených paketů?
Možnosti filtrování nejsou podporovány s náhledem zklepal virtuální sítě. Při přidání konfigurace tap do síťového rozhraní je do cíle TAP přenášena do cílového umístění hluboká kopie všech příchozích a odchozích přenosů v síťovém rozhraní.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Lze do monitorovaného síťového rozhraní přidat více konfigurací TAP?
Monitorované síťové rozhraní může mít pouze jednu konfiguraci TAP. Ověřte si u jednotlivých [partnerských řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) možnost streamovat více kopií provozu TAP do analytických nástrojů, které si vyberete.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Může stejný prostředek technologie TAP ve stejné virtuální síti agregovat provoz ze sledovaných síťových rozhraní ve více než jedné virtuální síti?
Ano. Stejný prostředek rozhraní TAP ve virtuální síti lze použít k agregaci zrcadleného provozu ze sledovaných síťových rozhraní v partnerských virtuálních sítích ve stejném předplatném nebo v jiném předplatném. Prostředek tap virtuální sítě a cílové vyrovnávání zatížení nebo cílové síťové rozhraní musí být ve stejném předplatném. Všechna předplatná musí být pod stejným klientem služby Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Existují nějaké aspekty výkonu produkčního provozu, pokud povolím konfiguraci protokolu TAP ve virtuální síti v síťovém rozhraní?

Virtuální síť TAP je ve verzi preview. Během náhledu neexistuje žádná smlouva o úrovni služeb. Funkce by neměla být použita pro produkční úlohy. Pokud je síťové rozhraní virtuálního počítače povoleno s konfigurací TAP, stejné prostředky na hostiteli Azure přidělené virtuálnímu počítači k odeslání produkčního provozu se používají k provedení funkce zrcadlení a odeslání zrcadlených paketů. Vyberte správnou velikost virtuálního počítače [linuxnebo](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Windows,](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) abyste zajistili, že pro virtuální počítač bude k dispozici dostatek prostředků k odeslání produkčního provozu a zrcadleného provozu.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Je zrychlená síť pro [Linux](create-vm-accelerated-networking-cli.md) nebo [Windows](create-vm-accelerated-networking-powershell.md) podporována pomocí virtuální sítě TAP?

Budete moci přidat konfiguraci TAP na síťové rozhraní připojené k virtuálnímu počítači, který je povolen s akcelerací sítě. Ale výkon a latence na virtuálním počítači bude ovlivněna přidáním konfigurace TAP, protože snížení zatížení pro zrcadlení provozu není aktuálně podporováno azure akcelerované sítě.

## <a name="virtual-network-service-endpoints"></a>Koncové body služby pro virtuální síť

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Jaká je správná posloupnost operací pro nastavení koncových bodů služby pro službu Azure?
Existují dva kroky k zabezpečení prostředku služby Azure prostřednictvím koncových bodů služby:
1. Zapněte koncové body služby pro službu Azure.
2. Nastavte počet aklů virtuální sítě ve službě Azure.

Prvním krokem je operace na straně sítě a druhým krokem je operace na straně prostředků služby. Oba kroky mohou provádět stejný správce nebo různí správci na základě oprávnění RBAC udělených roli správce. Doporučujeme, abyste před nastavením seznamů ACL virtuální sítě na straně služby Azure nejprve zapnuli koncové body služby pro vaši virtuální síť. Proto kroky musí být provedeny v pořadí uvedené výše nastavit koncové body služby virtuální sítě.

>[!NOTE]
> Obě výše popsané operace musí být dokončeny, než můžete omezit přístup ke službě Azure na povolenou virtuální síť a podsíť. Jenom zapnutí koncových bodů služby pro službu Azure na straně sítě neposkytuje omezený přístup. Kromě toho je nutné také nastavit akly virtuální sítě na straně služby Azure.

Některé služby (například SQL a CosmosDB) umožňují výjimky z výše uvedené sekvence prostřednictvím příznaku **IgnoreMissingVnetServiceEndpoint.** Jakmile je příznak nastaven na **hodnotu True**, mohou být záznamy ACL virtuální sítě nastaveny na straně služby Azure před nastavením koncových bodů služby na straně sítě. Služby Azure poskytují tento příznak, který zákazníkům pomáhá v případech, kdy jsou konkrétní brány firewall IP nakonfigurované ve službách Azure a zapnutí koncových bodů služby na straně sítě může vést k poklesu připojení, protože zdrojová IP adresa se změní z veřejné adresy IPv4 na soukromou adresu. Nastavení aklů virtuální sítě na straně služby Azure před nastavením koncových bodů služby na straně sítě může pomoci vyhnout se přetažení připojení.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Jsou všechny služby Azure umístěny ve virtuální síti Azure poskytované zákazníkem? Jak koncový bod služby virtuální sítě funguje se službami Azure?

Ne, ne všechny služby Azure jsou umístěny ve virtuální síti zákazníka. Většina datových služeb Azure, jako je Azure Storage, Azure SQL a Azure Cosmos DB, jsou víceklientské služby, ke kterým se dá přistupovat přes veřejné IP adresy. Další informace o integraci virtuálních sítí pro služby Azure [najdete tady](virtual-network-for-azure-services.md). 

Když použijete funkci koncových bodů služby virtuální sítě (zapnutí koncového bodu služby virtuální sítě na straně sítě a nastavení příslušných přístupových bodů Virtuální sítě na straně služby Azure), přístup ke službě Azure je omezen z povolené virtuální sítě a podsítě.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Jak koncový bod služby virtuální sítě poskytuje zabezpečení?

Funkce koncového bodu služby virtuální sítě (zapnutí koncového bodu služby virtuální sítě na straně sítě a nastavení příslušných přístupových bodů AC l virtuální sítě na straně služby Azure) omezuje přístup ke službě Azure na povolenou virtuální síť a podsíť, čímž poskytuje zabezpečení na úrovni sítě a izolace provozu služby Azure. Veškerý provoz pomocí koncových bodů služby virtuální sítě toky přes páteřní síť Microsoftu, čímž poskytuje další vrstvu izolace z veřejného internetu. Kromě toho se zákazníci mohou rozhodnout, že plně odeberou veřejný přístup k Internetu k prostředkům služeb Azure a povolí provoz pouze ze své virtuální sítě prostřednictvím kombinace ip firewallu a seznamů ACL virtuální sítě, čímž chrání prostředky služeb Azure před neoprávněnými povoleními. Přístup.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Co koncový bod služby virtuální sítě chrání – prostředky virtuální sítě nebo služba Azure?
Koncové body služby Virtuální sítě pomáhají chránit prostředky služeb Azure. Prostředky virtuální sítě jsou chráněné prostřednictvím skupin zabezpečení sítě (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Existují nějaké náklady na používání koncových bodů služby virtuální sítě?

Ne, neexistuje žádné další náklady na použití koncových bodů služby virtuální sítě.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Můžu zapnout koncové body služby Virtuální sítě a nastavit seznamy ACL virtuální sítě, pokud virtuální síť a prostředky služby Azure patří do různých předplatných?

Ano, je to možné. Virtuální sítě a prostředky služeb Azure můžou být ve stejném nebo jiném předplaceném předplatném. Jediným požadavkem je, že prostředky virtuální sítě i služby Azure musí být pod stejným klientem služby Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Můžu zapnout koncové body služby Virtuální sítě a nastavit seznamy ACL virtuální sítě, pokud virtuální síť a prostředky služby Azure patří do různých klientů služby AD?
Ne, koncové body služby virtuální sítě a počet klientů virtuální sítě nejsou podporovány mezi klienty služby AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Může ip adresa místního zařízení, která je připojená přes bránu Azure Virtual Network gateway (VPN) nebo bránu ExpressRoute, přistupovat ke službě Azure PaaS service prostřednictvím koncových bodů služby Virtuální sítě?
Prostředky služeb Azure svázané s virtuálními sítěmi ve výchozím nastavení nejsou přístupné z místních sítí. Pokud chcete povolit provoz z místního prostředí, musíte také povolit veřejné (obvykle NAT) IP adresy z vašeho místního prostředí nebo ExpressRoute. Tyto IP adresy lze přidat prostřednictvím konfigurace brány firewall IP pro prostředky služby Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Můžu použít funkci Koncového bodu služby Virtuální sítě k zabezpečení služby Azure do více podsítí v rámci virtuální sítě nebo ve více virtuálních sítích?
Chcete-li zabezpečit služby Azure do více podsítí v rámci virtuální sítě nebo ve více virtuálních sítích, povolte koncové body služby na straně sítě v každé podsíti nezávisle a pak zabezpečte prostředky služeb Azure pro všechny podsítě nastavením příslušné akly virtuální sítě na straně služby Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Jak můžu odfiltrovat odchozí provoz z virtuální sítě do služeb Azure a pořád používat koncové body služby?
Pokud chcete zkontrolovat nebo filtrovat provoz určený pro službu Azure z virtuální sítě, můžete nasadit síťové virtuální zařízení v rámci virtuální sítě. Koncové body služby pak můžete použít pro podsíť, kde se nasadí síťové virtuální zařízení a zabezpečené prostředky služby Azure pouze pro tuto podsíť prostřednictvím seznamů ACL virtuální sítě. Tento scénář může být také užitečné, pokud chcete omezit přístup ke službě Azure z vaší virtuální sítě pouze na konkrétní prostředky Azure pomocí filtrování virtuálních zařízení sítě. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Co se stane, když přistupujete k účtu služby Azure, který má povolený seznam řízení přístupu k virtuální síti (ACL) mimo virtuální síť?
Je vrácena chyba HTTP 403 nebo HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Jsou podsítě virtuální sítě vytvořené v různých oblastech povolen přístup k účtu služby Azure v jiné oblasti? 
Ano, pro většinu služeb Azure virtuální sítě vytvořené v různých oblastech přístup ke službám Azure v jiné oblasti prostřednictvím koncových bodů služby virtuální sítě. Například pokud účet Azure Cosmos DB je v usa – západ nebo východ usa a virtuální sítě jsou ve více oblastech, virtuální síť přístup k Azure Cosmos DB. Úložiště a SQL jsou výjimky a jsou místní povahy a virtuální síť i služba Azure musí být ve stejné oblasti.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Může mít služba Azure seznam ACL virtuální sítě i bránu firewall IP?
Ano, seznam ACL virtuální sítě a brána firewall IP mohou existovat společně. Obě funkce se vzájemně doplňují, aby byla zajištěna izolace a bezpečnost.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Co se stane, když odstraníte virtuální síť nebo podsíť, která má pro službu Azure zapnutý koncový bod služby?
Odstranění virtuálních sítí a podsítí jsou nezávislé operace a jsou podporované i v případě, že jsou pro služby Azure zapnuté koncové body služby. V případech, kdy mají služby Azure nastavené seznamy ACL virtuální sítě, pro tyto virtuální sítě a podsítě se informace o acl virtuální sítě přidružené k této službě Azure deaktivují, když se odstraní virtuální síť nebo podsíť, která má zapnutý koncový bod služby virtuální sítě.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Co se stane, když se odstraní účet služby Azure, který má povolený koncový bod služby Virtuální sítě?
Odstranění účtu služby Azure je nezávislá operace a je podporovaná i v případě, že koncový bod služby je povolenna na straně sítě a a klientské sítě ACL s nastavené na straně služby Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Co se stane se zdrojovou IP adresou prostředku (například virtuálního virtuálního počítačů v podsíti), který má povolený koncový bod služby virtuální sítě?
Pokud jsou povoleny koncové body virtuální síťové služby, zdrojové IP adresy prostředků v podsíti vaší virtuální sítě se přepínají z používání veřejných ipv4 adres na privátní IP adresy virtuální sítě Azure pro přenos do služby Azure. Všimněte si, že to může způsobit selhání konkrétních ip firewallů, které jsou nastaveny na veřejnou adresu IPV4 dříve ve službách Azure. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Má trasa koncového bodu služby vždy přednost?
Koncové body služby přidávají systémovou trasu, která má přednost před trasami Protokolu BGP a poskytuje optimální směrování pro provoz koncových bodů služby. Koncové body služby vždy přebírají provoz služeb přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Další informace o tom, jak Azure vybírá trasu, najdete v [tématu Směrování provozu virtuální sítě Azure](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Jak skupiny síťových služeb v podsíti fungují s koncovými body služby?
Chcete-li dosáhnout služby Azure, nsgy musí povolit odchozí připojení. Pokud jsou vaše sítě zabezpečení sítě otevřeny pro všechny odchozí přenosy v Internetu, měl by provoz koncového bodu služby fungovat. Odchozí přenos můžete také omezit na ip adresy služby pouze pomocí značek Service.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Jaká oprávnění potřebuji k nastavení koncových bodů služby?
Koncové body služby lze nakonfigurovat ve virtuální síti nezávisle uživatelem s přístupem pro zápis do virtuální sítě. Chcete-li zabezpečit prostředky služby Azure do virtuální sítě, musí mít uživatel oprávnění **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení zahrnuto do předdefinované role správce služby a lze je upravit vytvořením vlastních rolí. Další informace o předdefinovaných rolích a přiřazení konkrétních oprávnění k [vlastním rolím](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Můžu filtrovat provoz virtuální sítě do služeb Azure, což umožňuje jenom konkrétní prostředky služby Azure, přes koncové body služby Virtuální sítě? 

Zásady koncového bodu služby virtuální sítě (Virtuální síť) umožňují filtrovat provoz virtuálních sítí do služeb Azure, což umožňuje jenom konkrétní prostředky služby Azure přes koncové body služby. Zásady koncového bodu poskytují podrobné řízení přístupu z provozu virtuální sítě ke službám Azure. Další informace o zásadách koncového bodu služby naleznete [zde](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Podporuje Azure Active Directory (Azure AD) koncové body služby Virtuální sítě?

Azure Active Directory (Azure AD) nepodporuje koncové body služby nativně. Kompletní seznam služeb Azure podporujícíkoncové body služby Virtuální sítě najdete [tady](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Všimněte si, že značka "Microsoft.AzureActiveDirectory" uvedená v části služby podporující koncové body služby se používá pro podporu koncových bodů služby na ADLS Gen 1. Pro ADLS Gen 1 integrace virtuální sítě pro Azure Data Lake Storage Gen1 využívá zabezpečení koncového bodu virtuální síťové služby mezi vaší virtuální sítí a Azure Active Directory (Azure AD) ke generování dalších deklarací zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. Další informace o [integraci virtuálnísítě Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Existují nějaká omezení na to, kolik koncových bodů služby virtuální sítě můžu nastavit z mé virtuální sítě?
Celkový počet koncových bodů služby virtuální sítě ve virtuální síti není nijak omezen. Pro prostředek služby Azure (například účet Azure Storage) mohou služby vynutit omezení počtu podsítí používaných k zabezpečení prostředku. V následující tabulce jsou uvedeny některé příklady omezení: 

|||
|---|---|
|Služba Azure| Omezení pravidel virtuální sítě|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Úložiště datových jezer Azure V1|  100|
 
>[!NOTE]
> Omezení podléhají změnám podle uvážení služby Azure. Podrobnosti o službách naleznete v příslušné servisní dokumentaci. 




  



