---
title: Nejčastější dotazy k Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Odpovědi na nejčastější dotazy týkající se Microsoft Azurech virtuálních sítí.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2020
ms.author: kumud
ms.openlocfilehash: 2cf28565818f6de4d52b57040a80c21d0e03a76c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218514"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Nejčastější dotazy ke službě Azure Virtual Network

## <a name="virtual-network-basics"></a>Základy Virtual Network

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co je Azure Virtual Network (VNet)?
Azure Virtual Network (VNet) je reprezentace vaší vlastní sítě v cloudu. Je to logická izolace cloudu Azure vyhrazeného pro vaše předplatné. Virtuální sítě můžete použít ke zřízení a správě virtuálních privátních sítí (VPN) v Azure a volitelně k propojení virtuální sítě s ostatními virtuální sítě v Azure nebo s místní infrastrukturou IT pro vytváření hybridních nebo mezimístěných řešení. Každá virtuální síť, kterou vytvoříte, má svůj vlastní blok CIDR a může být propojena s jinými virtuální sítě a místními sítěmi, pokud se bloky CIDR nepřekrývají. Také máte kontrolu nad nastavením serveru DNS pro virtuální sítě a segmentace virtuální sítě do podsítí.

Použít virtuální sítě k:

* Vytvořte vyhrazenou virtuální síť jen pro privátní cloud. Někdy nepotřebujete pro vaše řešení konfiguraci mezi různými místy. Při vytváření sítě VNet můžou vaše služby a virtuální počítače v rámci virtuální sítě komunikovat přímo a bezpečně mezi sebou v cloudu. I nadále můžete nakonfigurovat připojení koncových bodů pro virtuální počítače a služby, které vyžadují internetovou komunikaci, jako součást řešení.

* Zabezpečeně šíří vaše datové centrum. Pomocí virtuální sítě můžete vytvářet tradiční sítě VPN S2S (site-to-site) k bezpečnému škálování kapacity datacentra. S2S VPN používají protokol IPSEC k zajištění zabezpečeného připojení mezi vaší firemní bránou VPN a Azure.

* Povolte hybridní cloudové scénáře. Virtuální sítě vám nabízí flexibilitu při podpoře řady hybridních cloudových scénářů. Cloudové aplikace můžete bezpečně propojit s jakýmkoli typem místního systému, jako jsou sálové počítače a systémy UNIX.

### <a name="how-do-i-get-started"></a>Jak mám začít?
Začněte tím, že přejdete do dokumentace ke službě [Virtual Network](./index.yml) . Tento obsah poskytuje informace o přehledu a nasazení všech funkcí virtuální sítě.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Můžu používat virtuální sítě bez připojení mezi místními místy?
Ano. Virtuální síť můžete použít bez připojení k vašemu pracovišti. Můžete například spustit řadiče domény služby Active Directory systému Microsoft Windows Server a farmy služby SharePoint výhradně ve virtuální síti Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Můžu provést optimalizaci sítě WAN mezi virtuální sítě nebo virtuální sítí a místním datovým centrem?
Ano. [Virtuální zařízení pro optimalizaci sítě WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) můžete nasadit od několika dodavatelů prostřednictvím Azure Marketplace.

## <a name="configuration"></a>Konfigurace

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jaké nástroje slouží k vytvoření virtuální sítě?
Virtuální síť můžete vytvořit nebo nakonfigurovat pomocí následujících nástrojů:

* portál Azure
* PowerShell
* Azure CLI
* Konfigurační soubor sítě (jenom netcfg – jenom pro klasický virtuální sítě). Informace najdete v článku [Konfigurace sítě VNet pomocí konfiguračního souboru sítě](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) .

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jaké rozsahy adres můžu ve svém virtuální sítě použít?
Doporučujeme, abyste používali rozsahy adres uvedené v [dokumentu RFC 1918](https://tools.ietf.org/html/rfc1918), které byly vyjmenovány sdružením IETF pro soukromé Nesměrovatelné adresní prostory:
* 10.0.0.0 – 10.255.255.255 (předpona 10/8)
* 172.16.0.0-172.31.255.255 (předpona 172.16/12)
* 192.168.0.0-192.168.255.255 (předpona 192.168/16)

Další adresní prostory mohou fungovat, ale mohou mít nežádoucí vedlejší účinky.

Kromě toho nemůžete přidat následující rozsahy adres:
* 224.0.0.0/4 (vícesměrové vysílání)
* 255.255.255.255/32 (všesměrové vysílání)
* 127.0.0.0/8 (zpětná smyčka)
* 169.254.0.0/16 (místní propojení)
* 168.63.129.16/32 (interní DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Můžu mít v virtuální sítě veřejné IP adresy?
Ano. Další informace o rozsahech veřejných IP adres najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network). Veřejné IP adresy nejsou přímo přístupné z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existuje omezení počtu podsítí ve virtuální síti?
Ano. Podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) . Adresní prostory podsítě nemůžou překrývat sebe.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existují nějaká omezení používání IP adres v těchto podsítích?
Ano. Azure si v každé podsíti vyhrazuje 5 IP adres. Jedná se o x. x. x. 0-x. x. x. 3 a poslední adresu podsítě. x. x. x. 1-x. x. x. 3 je v každé podsíti pro služby Azure rezervované.   
- x. x. x. 0: Síťová adresa
- x. x. x. 1: vyhrazené pro Azure pro výchozí bránu
- x. x. x. 2, x. x. x. 3: vyhrazené pro Azure pro mapování Azure DNSch IP adres na prostor virtuální sítě
- x. x. x. 255: adresa všesměrového vysílání sítě

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak malý a jak velké můžou virtuální sítě a podsítě?
Nejnižší podporovaná podsíť IPv4 je/29 a největší je/8 (pomocí definice podsítě CIDR).  Podsítě IPv6 musí mít velikost přesně/64.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Můžu své sítě VLAN přenést do Azure pomocí virtuální sítě?
No. Virtuální sítě jsou překryvy vrstvy 3. Azure nepodporuje žádné sémantiky vrstvy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Můžu v virtuální sítě a podsítích zadat vlastní zásady směrování?
Ano. Můžete vytvořit směrovací tabulku a přidružit ji k podsíti. Další informace o směrování v Azure najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Podporuje virtuální sítě vícesměrové vysílání nebo všesměrové vysílání?
No. Vícesměrové vysílání a všesměrové vysílání nejsou podporovány.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jaké protokoly můžu v virtuální sítě použít?
V rámci virtuálních sítí můžete využít protokoly TCP, UDP a ICMP TCP/IP. V rámci virtuálních sítí se podporuje jednosměrové vysílání s výjimkou protokolu DHCP (Dynamic Host Configuration Protocol) přes jednosměrové vysílání (zdrojový port UDP /68, cílový port UDP /67) a zdrojový port UDP 65330, který je vyhrazený pro hostitele. Vícesměrové vysílání, všesměrové vysílání, zapouzdřené pakety IP-in-IP a pakety GRE (Generic Routing Encapsulation) se v rámci virtuálních sítí blokují. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Můžu v rámci virtuální sítě testovat svůj výchozí směrovač?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Můžu pomocí příkazu tracert diagnostikovat připojení?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Můžu po vytvoření virtuální sítě přidat podsítě?
Ano. Podsítě lze kdykoli přidat do virtuální sítě, pokud rozsah adres podsítě není součástí jiné podsítě a v rozsahu adres virtuální sítě je dostupné místo.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Můžu po vytvoření této podsítě změnit její velikost?
Ano. Podsíť můžete přidat, odebrat, rozšířit nebo zmenšit, pokud v ní nejsou nasazené virtuální počítače ani služby.

### <a name="can-i-modify-vnet-after-i-created-them"></a>Můžu po vytvoření virtuální sítě upravit?
Ano. Můžete přidávat, odebírat a upravovat bloky CIDR používané virtuální sítí.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Když mám služby ve virtuální síti, můžu se připojit k Internetu?
Ano. Všechny služby nasazené ve virtuální síti můžou připojit odchozí připojení k Internetu. Další informace o odchozích připojeních k Internetu v Azure najdete v tématu [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete příchozí připojení k prostředku nasazenému prostřednictvím Správce prostředků, musí mít prostředek přiřazenou veřejnou IP adresu. Další informace o veřejných IP adresách najdete v tématu [veřejné IP adresy](virtual-network-public-ip-address.md). Každá cloudová služba Azure nasazená v Azure má přiřazenou veřejně adresovatelnou VIP adresu. Definujete vstupní koncové body pro role PaaS a koncové body pro virtuální počítače, aby tyto služby mohly přijímat připojení z Internetu.

### <a name="do-vnets-support-ipv6"></a>Podporuje virtuální sítě protokol IPv6?
Ano, virtuální sítě může být jenom IPv4 nebo duální zásobník (IPv4 + IPv6).  Podrobnosti najdete v tématu [Přehled protokolu IPv6 pro virtuální sítě Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Můžou být virtuální sítě oblastí rozsahů?
No. Virtuální síť je omezená jenom na jednu oblast. Virtuální síť ale zahrnuje zóny dostupnosti. Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Virtuální sítě můžete propojit v různých oblastech s využitím partnerského vztahu virtuálních sítí. Podrobnosti najdete v tématu [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) .

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Můžu připojit virtuální síť k jiné virtuální síti v Azure?
Ano. Jednu virtuální síť můžete připojit k jiné virtuální síti pomocí těchto akcí:
- **Partnerský vztah virtuální sítě**: Podrobnosti najdete v tématu [Přehled partnerských vztahů](virtual-network-peering-overview.md) virtuálních sítí.
- **Azure VPN Gateway**: Podrobnosti najdete v tématu [Konfigurace připojení typu VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Překlad názvů (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jaké jsou možnosti služby DNS pro virtuální sítě?
Na stránce [pro překlad IP adres pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md) použijte tabulku rozhodnutí, která vás provede všemi dostupnými možnostmi DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Můžu zadat servery DNS pro virtuální síť?
Ano. IP adresy serveru DNS můžete zadat v nastavení virtuální sítě. Toto nastavení se použije jako výchozí server DNS pro všechny virtuální počítače ve virtuální síti.

### <a name="how-many-dns-servers-can-i-specify"></a>Kolik serverů DNS lze zadat?
Odkazy na [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Můžu po vytvoření sítě změnit svoje servery DNS?
Ano. V každém okamžiku můžete seznam serverů DNS pro virtuální síť kdykoli změnit. Pokud změníte seznam serverů DNS, musíte provést obnovení zapůjčení DHCP u všech ovlivněných virtuálních počítačů ve virtuální síti, aby se nové nastavení DNS projevilo. Pro virtuální počítače s operačním systémem Windows to můžete provést tak, že `ipconfig /renew` na virtuálním počítači zadáte přímo. Další typy operačních systémů najdete v dokumentaci k obnovení zapůjčení DHCP pro konkrétní typ operačního systému. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co je služba DNS poskytovaná Azure a funguje s virtuální sítě?
DNS poskytovaná Azure je víceklientské služba DNS nabízená Microsoftem. Azure zaregistruje všechny vaše virtuální počítače a instance rolí cloudové služby v této službě. Tato služba poskytuje překlad názvů podle názvu hostitele pro virtuální počítače a instance rolí obsažené v rámci stejné cloudové služby a podle plně kvalifikovaného názvu domény pro virtuální počítače a instance rolí ve stejné virtuální síti. Další informace o DNS najdete v tématu [překlad názvů pro virtuální počítače a Cloud Services instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md).

K dispozici jsou omezení prvních 100 cloudových služeb ve virtuální síti pro překlad IP adres mezi klienty pomocí služby DNS poskytované službou Azure. Pokud používáte vlastní server DNS, toto omezení se nevztahuje.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Můžu přepsat nastavení DNS v závislosti na virtuálním počítači nebo cloudové službě?
Ano. Můžete nastavit servery DNS na virtuální počítač nebo cloudovou službu a přepsat tak výchozí nastavení sítě. Doporučuje se ale co nejvíc používat službu DNS v rámci sítě.

### <a name="can-i-bring-my-own-dns-suffix"></a>Můžu si přenést vlastní příponu DNS?
No. Pro virtuální sítě nejde zadat vlastní příponu DNS.

## <a name="connecting-virtual-machines"></a>Připojení virtuálních počítačů

### <a name="can-i-deploy-vms-to-a-vnet"></a>Můžu nasazovat virtuální počítače do virtuální sítě?
Ano. Všechna síťová rozhraní (NIC) připojená k virtuálnímu počítači nasazenému prostřednictvím modelu nasazení Správce prostředků musí být připojená k virtuální síti. Virtuální počítače nasazené prostřednictvím modelu nasazení Classic se můžou volitelně připojit k virtuální síti.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jaké jsou různé typy IP adres, které můžu přiřadit k virtuálním počítačům?
* **Privátní:** Přiřazené ke každému síťovému rozhraní v rámci každého virtuálního počítače. Adresa je přiřazena buď pomocí statické, nebo dynamické metody. Privátní IP adresy se přiřazují z rozsahu, který jste zadali v nastavení podsítě vaší virtuální sítě. Prostředky nasazené prostřednictvím modelu nasazení Classic jsou přiřazeny privátní IP adresy, i když nejsou připojené k virtuální síti. Chování metody přidělování se liší v závislosti na tom, jestli byl prostředek nasazený pomocí modelu nasazení Správce prostředků nebo Classic: 

  - **Správce prostředků**: privátní IP adresa přiřazená s dynamickou nebo statickou metodou zůstane přiřazená k virtuálnímu počítači (Správce prostředků) až do odstranění prostředku. Rozdílem je, že vyberete adresu, která se má přiřadit při použití Static, a Azure při použití dynamického výběru zvolí. 
  - **Classic**: privátní IP adresa přiřazená k dynamické metodě se může změnit, když se virtuální počítač (klasický) restartuje po restartování virtuálního počítače v zastaveném (uvolněném) stavu. Pokud potřebujete zajistit, aby se privátní IP adresa pro prostředek nasazená prostřednictvím modelu nasazení Classic nikdy nezměnila, přiřaďte privátní IP adresu statickou metodou.

* **Veřejné:** Volitelně se přiřazují síťové adaptéry připojené k virtuálním počítačům nasazeným prostřednictvím modelu nasazení Azure Resource Manager. Adresa může být přiřazena se statickou nebo dynamickou metodou přidělování. Všechny virtuální počítače a Cloud Services instance rolí nasazené prostřednictvím modelu nasazení Classic existují v rámci cloudové služby, která je přiřazená k *dynamické* veřejné virtuální IP adrese (VIP). Veřejnou *statickou* IP adresu, která se nazývá [vyhrazená IP adresa adresa](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip), se dá volitelně přiřadit jako virtuální IP adresa. Veřejné IP adresy můžete přiřadit jednotlivým virtuálním počítačům nebo Cloud Services instancím rolí nasazeným prostřednictvím modelu nasazení Classic. Tyto adresy se nazývají [veřejné IP adresy na úrovni instance (adresy ILPIP](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) a dají se dynamicky přiřazovat.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Můžu si vyhradit soukromou IP adresu pro virtuální počítač, který vytvořím později?
No. Nemůžete rezervovat privátní IP adresu. Pokud je k dispozici privátní IP adresa, je server DHCP přiřazen k virtuálnímu počítači nebo instanci role. Virtuální počítač může nebo nemusí být ten, ke kterému chcete přiřadit privátní IP adresu. Můžete však změnit soukromou IP adresu již vytvořeného virtuálního počítače na libovolnou dostupnou privátní IP adresu.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Mění se privátní IP adresy pro virtuální počítače ve virtuální síti?
To závisí na okolnostech. Pokud byl virtuální počítač nasazen prostřednictvím Správce prostředků, ne bez ohledu na to, zda byla IP adresa přiřazena se statickou nebo dynamickou metodou přidělení. Pokud byl virtuální počítač nasazen prostřednictvím modelu nasazení Classic, dynamické IP adresy se můžou změnit, když se virtuální počítač spustí po uplynutí stavu Zastaveno (přidělení zrušeno). Adresa se uvolní z virtuálního počítače nasazeného pomocí modelu nasazení v případě odstranění virtuálního počítače.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Můžu ručně přiřadit IP adresy k síťovým kartám v operačním systému virtuálního počítače?
Ano, ale nedoporučuje se, pokud je to nutné, například při přiřazování více IP adres k virtuálnímu počítači. Podrobnosti najdete v tématu [Přidání více IP adres k virtuálnímu počítači](virtual-network-multiple-ip-addresses-portal.md#os-config). Pokud se změní IP adresa přiřazená k síťové kartě Azure připojené k virtuálnímu počítači a IP adresa v operačním systému virtuálního počítače je odlišná, ztratíte připojení k virtuálnímu počítači.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Když zastavím slot nasazení cloudové služby nebo vypnete virtuální počítač z operačního systému, co se stane s IP adresami?
Ničeho. IP adresy (veřejné virtuální IP adresy, veřejné a privátní) zůstanou přiřazené k slotu nasazení cloudové služby nebo k virtuálnímu počítači.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Můžu přesunout virtuální počítače z jedné podsítě do jiné podsítě ve virtuální síti bez nutnosti opětovného nasazení?
Ano. Další informace najdete v tématu [Postup přesunutí instance virtuálního počítače nebo role do jiné podsítě](/previous-versions/azure/virtual-network/virtual-networks-move-vm-role-to-subnet) .

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Můžu pro svůj virtuální počítač nakonfigurovat statickou adresu MAC?
No. Adresu MAC nelze staticky konfigurovat.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Zůstane adresa MAC pro svůj virtuální počítač stejná, až se vytvoří?
Ano, adresa MAC zůstane stejná pro virtuální počítač nasazený prostřednictvím modelu nasazení Správce prostředků i klasický, dokud ho neodstraníte. Dříve byla adresa MAC uvolněna v případě, že byl virtuální počítač zastaven (přidělení zrušeno), ale nyní je adresa MAC uchována i v případě, že je virtuální počítač ve stavu zrušeno přidělení. Adresa MAC zůstane přiřazená síťovému rozhraní, dokud se neodstraní síťové rozhraní nebo se nezmění privátní IP adresa přiřazená k primární konfiguraci IP rozhraní primárního síťového rozhraní. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Můžu se připojit k Internetu z virtuálního počítače ve virtuální síti?
Ano. Všechny virtuální počítače a Cloud Services instance rolí nasazené v rámci virtuální sítě se můžou připojit k Internetu.

## <a name="azure-services-that-connect-to-vnets"></a>Služby Azure, které se připojují k virtuální sítě

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Můžu použít Azure App Service Web Apps s virtuální sítí?
Ano. Můžete nasadit Web Apps v rámci virtuální sítě pomocí pomocného mechanismu řízení (App Service Environment), připojit back-end vašich aplikací k virtuální sítě s integrací virtuální sítě a uzamknout příchozí provoz do vaší aplikace pomocí koncových bodů služby. Další informace najdete v následujících článcích:

* [Funkce App Service sítě](../app-service/networking-features.md)
* [Vytváření Web Apps v App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrace aplikace s Virtual Network Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Omezení přístupu App Service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Můžu ve virtuální síti nasazovat Cloud Services s webovými a pracovními rolemi (PaaS)?
Ano. Můžete (volitelně) nasadit instance rolí Cloud Services v rámci virtuální sítě. Uděláte to tak, že v části Konfigurace sítě v konfiguraci služby zadáte název virtuální sítě a mapování rolí a podsítí. Nemusíte aktualizovat žádné z vašich binárních souborů.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Můžu připojit sadu škálování virtuálního počítače k virtuální síti?
Ano. Musíte připojit sadu škálování virtuálního počítače k virtuální síti.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existuje úplný seznam služeb Azure, které můžu nasadit prostředky z do virtuální sítě?
Ano, podrobnosti najdete v tématu [Integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Jak můžu omezit přístup k prostředkům Azure PaaS z virtuální sítě?

Prostředky nasazené prostřednictvím některých služeb Azure PaaS (například Azure Storage a Azure SQL Database) můžou omezit síťový přístup k virtuální síti prostřednictvím použití koncových bodů služby virtuální sítě nebo privátního propojení Azure. Podrobnosti najdete v tématu [Přehled koncových bodů služby virtuální sítě](virtual-network-service-endpoints-overview.md), [Přehled privátních odkazů Azure](../private-link/private-link-overview.md) .

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Můžu přesunout služby z virtuální sítě a z něj?
No. Nemůžete přesouvat služby z virtuální sítě a z ní. Pokud chcete přesunout prostředek do jiné virtuální sítě, musíte prostředek odstranit a znovu nasadit.

## <a name="security"></a>Zabezpečení

### <a name="what-is-the-security-model-for-vnets"></a>Jaký je model zabezpečení pro virtuální sítě?
Virtuální sítě jsou izolované od sebe a další služby, které jsou hostovány v infrastruktuře Azure. Virtuální síť je hranice vztahu důvěryhodnosti.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Můžu na prostředky připojené k virtuální síti omezit tok příchozích nebo odchozích přenosů?
Ano. [Skupiny zabezpečení sítě](./network-security-groups-overview.md) můžete použít pro jednotlivé podsítě v rámci virtuální sítě, síťových adaptérů připojených k virtuální síti nebo obou.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Můžu mezi prostředky připojenými k virtuální síti implementovat bránu firewall?
Ano. Můžete nasadit [síťové virtuální zařízení brány firewall](https://azure.microsoft.com/marketplace/?term=firewall) od několika dodavatelů prostřednictvím Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Jsou k dispozici informace o zabezpečení virtuální sítě?
Ano. Podrobnosti najdete v tématu [Přehled zabezpečení sítě Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="do-virtual-networks-store-customer-data"></a>Ukládají se do virtuálních sítí zákaznická data?
No. Virtuální sítě neukládají žádná zákaznická data. 

## <a name="apis-schemas-and-tools"></a>Rozhraní API, schémata a nástroje

### <a name="can-i-manage-vnets-from-code"></a>Můžu spravovat virtuální sítě z kódu?
Ano. Rozhraní REST API pro virtuální sítě můžete použít v modelech nasazení [Azure Resource Manager](/rest/api/virtual-network) a [Classic](/previous-versions/azure/ee460799(v=azure.100)) .

### <a name="is-there-tooling-support-for-vnets"></a>Je k dispozici podpora nástrojů pro virtuální sítě?
Ano. Další informace o používání:
- Azure Portal k nasazení virtuální sítě prostřednictvím modelu nasazení [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) a [Classic](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) .
- PowerShell pro správu virtuální sítě nasazených prostřednictvím modelů nasazení [Správce prostředků](/powershell/module/az.network) a [Classic](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0) .
- Rozhraní příkazového řádku Azure (CLI) pro nasazení a správu virtuální sítě nasazených prostřednictvím modelů nasazení [Správce prostředků](/cli/azure/network/vnet) a [Classic](/previous-versions/azure/virtual-machines/azure-cli-arm-commands?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) .  

## <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

### <a name="what-is-vnet-peering"></a>Co je partnerský vztah VNet?
Partnerský vztah virtuálních sítí (nebo partnerský vztah virtuálních sítí) umožňuje propojit virtuální sítě. Připojení peer-to-VNet mezi virtuálními sítěmi umožňuje směrovat provoz mezi nimi soukromě prostřednictvím adres IPv4. Virtuální počítače v partnerských virtuální sítě můžou vzájemně komunikovat, jako kdyby byly ve stejné síti. Tyto virtuální sítě mohou být ve stejné oblasti nebo v různých oblastech (označuje se také jako globální partnerské vztahy virtuálních sítí). Připojení partnerských vztahů virtuální sítě je také možné vytvořit v rámci předplatných Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Můžu vytvořit připojení partnerského vztahu k virtuální síti v jiné oblasti?
Ano. Globální VNet peering umožňuje peer virtuální sítě v různých oblastech. Globální síť VNet peering je dostupná ve všech veřejných oblastech Azure, Číně v oblasti cloudu a oblastech cloudu pro státní správu. Z veřejných oblastí Azure se nemůžete globálně peere navázat na národní oblasti cloudu.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Jaká jsou omezení týkající se globálních partnerských vztahů virtuální sítě a nástrojů pro vyrovnávání zatížení?
Pokud jsou dvě virtuální sítě ve dvou různých oblastech partnerského vztahu přes globální partnerský vztah VNet, nemůžete se připojit k prostředkům, které jsou za základní Load Balancer prostřednictvím IP adresy front-endu Load Balancer. Toto omezení pro Standard Load Balancer neexistuje.
Následující prostředky můžou používat základní nástroje pro vyrovnávání zatížení, což znamená, že se k nim nemůžete dostat prostřednictvím IP adresy front-endu Load Balancer přes globální partnerský vztah virtuálních sítí. Můžete ale použít globální partnerský vztah virtuálních sítí k dosažení prostředků přímo prostřednictvím svých privátních IP adres, pokud je to povoleno. 
- Virtuální počítače za základními nástroji pro vyrovnávání zatížení
- Virtual Machine Scale Sets se základními nástroji pro vyrovnávání zatížení 
- Redis Cache 
- SKU Application Gateway (V1)
- Service Fabric
- API Management
- Služba Doména služby Active Directory (přidává)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service Environment

K těmto prostředkům se můžete připojit prostřednictvím ExpressRoute nebo VNet-to-VNet prostřednictvím bran virtuální sítě.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Můžu povolit partnerský vztah virtuálních sítí, pokud moje virtuální sítě patří k předplatným v rámci různých klientů Azure Active Directory?
Ano. Je možné vytvořit partnerský vztah virtuální sítě (místní nebo globální), pokud vaše předplatná patří do různých klientů Azure Active Directory. Můžete to provést prostřednictvím portálu, PowerShellu nebo rozhraní příkazového řádku.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Moje připojení partnerského vztahu virtuální sítě je v *inicializovaném* stavu, proč se nemůžu připojit?
Pokud je připojení partnerského vztahu v *inicializovaném* stavu, znamená to, že jste vytvořili pouze jeden odkaz. Aby bylo možné vytvořit úspěšné připojení, je nutné vytvořit obousměrný odkaz. Například pro partnerský virtuální síť A k virtuální síti B se musí vytvořit odkaz z partnerském na VNetB a od VNetB do partnerském. Vytvořením obou propojení dojde ke změně stavu na *připojeno*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Moje připojení partnerského vztahu virtuálních sítí je v *odpojeném* stavu, proč nemůžu vytvořit připojení partnerského vztahu?
Pokud je připojení partnerského vztahu virtuálních sítí v *odpojeném* stavu, znamená to, že se odstranila jedna z vytvořených odkazů. Aby bylo možné znovu vytvořit partnerské připojení, bude nutné odstranit odkaz a znovu ho vytvořit.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Můžu na virtuální síť navázat partnerský virtuální síť v jiném předplatném?
Ano. Můžete peer virtuální sítě napříč předplatnými a různými oblastmi.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Můžu mít dva virtuální sítěy rovnocennosti se stejnými nebo překrývajícími se rozsahy adres?
No. Adresní prostory se nesmí překrývat, aby bylo možné povolit partnerský vztah virtuální sítě.

### <a name="can-i-peer-a-vnet-to-two-different-vnets-with-the-the-use-remote-gateway-option-enabled-on-both-the-peerings"></a>Můžu vytvořit síť VNet se dvěma různými virtuální sítěy s povolenou možností použít vzdálenou bránu u partnerských vztahů?
No. Možnost použít vzdálenou bránu můžete povolit jenom pro jeden partnerský vztah k jednomu z virtuální sítě.

### <a name="how-much-do-vnet-peering-links-cost"></a>Kolik stojí propojení partnerských vztahů mezi virtuálními sítěmi?
Za vytvoření připojení partnerského vztahu virtuálních sítí se neúčtují žádné poplatky. Přenos dat mezi připojeními partnerských vztahů se účtuje. [Podívejte se sem](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Je provoz partnerských vztahů virtuálních sítí zašifrovaný?
Při přesunu provozu Azure mezi datacentry (mimo fyzických hranic, které neřídí společnost Microsoft nebo jménem Microsoftu), se [šifrování vrstvy MACsec (Data-Link](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) ) využívá na základním síťovém hardwaru.  To platí pro provoz partnerských vztahů virtuálních sítí.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Proč je moje připojení partnerského vztahu v *odpojeném* stavu?
Připojení partnerských vztahů virtuálních sítí se při odstranění jednoho partnerského vztahu mezi virtuálními sítěmi přejdou do *odpojeného* stavu. Aby bylo možné znovu vytvořit úspěšné připojení partnerského vztahu, je nutné odstranit oba odkazy.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Pokud se mi partnerském peer-to-VNetB and I peer VNetB to sítí vnetc, znamená to, že se jedná o partnerský vztah partnerském a sítí vnetc?
No. Přenosných partnerských vztahů se nepodporuje. Aby bylo možné provést tuto službu, je nutné, aby byly partnerské partnerském a sítí vnetc.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existují nějaká omezení šířky pásma pro připojení partnerských vztahů?
No. Síť VNet peering, ať už místní nebo globální, neomezuje žádná omezení šířky pásma. Šířka pásma je omezená jenom virtuálním počítačem nebo výpočetním prostředkem.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Jak mohu řešit problémy s partnerským vztahem virtuální sítě?
Tady je [Průvodce odstraňováním potíží](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) , který můžete vyzkoušet.

## <a name="virtual-network-tap"></a>Naslouchací zařízení virtuální sítě

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Které oblasti Azure jsou dostupné pro virtuální síť klepněte na?
V oblasti služby Virtual Network klepněte na možnost Náhled je dostupná ve všech oblastech Azure. Monitorovaná síťová rozhraní, virtuální síť klepněte na prostředek a řešení sběrače nebo analýzy musí být nasazeno ve stejné oblasti.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtual Network klepněte podporovat možnosti filtrování zrcadlených paketů?
Možnosti filtrování nejsou v této virtuální síti v rámci verze Preview podporované. Když se v síťovém rozhraní přidá konfigurace klepnutím do síťového rozhraní se vytvoří hluboká kopie všech přenosů dat příchozího a odchozího přenosu v síťovém rozhraní do datového proudu klepnutím na cíl.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Je možné do monitorovaného síťového rozhraní přidat několik konfigurací klepnutí?
Monitorované síťové rozhraní může mít jenom jednu konfiguraci klepnutím. Podívejte se na jednotlivá [Partnerská řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) , abyste mohli zasílat streamování několika kopií přenosů klepnutím do analytických nástrojů podle vašeho výběru.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Může stejná virtuální síť KLEPNOUT na agregovaný provoz prostředků z monitorovaných síťových rozhraní ve více než jedné virtuální síti?
Ano. K agregaci zrcadleného provozu z monitorovaných síťových rozhraní v partnerských virtuálních sítích ve stejném předplatném nebo v jiném předplatném se dá použít stejná virtuální síť klepněte na prostředek. Virtuální síť klepněte na prostředek a cílový Nástroj pro vyrovnávání zatížení nebo cílové síťové rozhraní musí být ve stejném předplatném. Všechna předplatná musí být ve stejném Azure Active Directory tenantovi.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Existují nějaké požadavky na výkon v produkčním provozu, pokud povolíte virtuální síti v síťovém rozhraní KLEPNEte na konfigurace?

Klepnutí na virtuální síť je ve verzi Preview. Během období Preview není k dispozici žádná smlouva o úrovni služeb. Možnost by se neměla používat pro produkční úlohy. Když je v případě, že je síťové rozhraní virtuálního počítače povolené v případě konfigurace klepnutím, k provedení funkce zrcadlení a k odesílání zrcadlených paketů slouží stejné prostředky hostitele Azure, které jsou přiděleny k virtuálnímu počítači pro odeslání produkčního provozu. Vyberte správnou velikost virtuálního počítače se [systémem Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby bylo zajištěno, že pro virtuální počítač jsou k dispozici dostatečné prostředky pro odeslání provozního provozu a zrcadleného provozu.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Podporuje síť pro [Linux](create-vm-accelerated-networking-cli.md) nebo [Windows](create-vm-accelerated-networking-powershell.md) urychlené používání virtuální sítě?

Do síťového rozhraní připojeného k virtuálnímu počítači, který je povolený pomocí akcelerovaných sítí, budete moct přidat konfiguraci klepnutím. Ale výkon a latence na virtuálním počítači se projeví tak, že se přidá konfigurace klepnutím, protože snižování zátěže pro přenos zrcadlení v současné době nepodporují urychlené síťové služby Azure.

## <a name="virtual-network-service-endpoints"></a>Koncové body služby pro virtuální síť

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Jaké jsou správné posloupnosti operací nastavení koncových bodů služby na službu Azure?
Existují dva kroky pro zabezpečení prostředku služby Azure prostřednictvím koncových bodů služby:
1. Zapněte koncové body služby pro službu Azure.
2. Nastavení seznamů ACL pro virtuální sítě ve službě Azure

Prvním krokem je operace na straně sítě a druhý krok je operace na straně prostředku služby. Oba kroky může provést stejný správce nebo různí správci na základě oprávnění Azure RBAC udělených roli správce. Před nastavením seznamů ACL virtuální sítě na straně služby Azure doporučujeme, abyste nejdřív zapnuli koncové body služby pro virtuální síť. Proto je třeba provést kroky v pořadí uvedeném výše a nastavit koncové body služby virtuální sítě.

>[!NOTE]
> Aby bylo možné omezit přístup služby Azure na povolenou virtuální síť a podsíť, musí být obě výše popsané operace dokončené. Pouze zapnutí koncových bodů služby pro službu Azure na straně sítě vám neposkytuje omezený přístup. Kromě toho musíte také na straně služby Azure nastavit seznamy ACL pro virtuální sítě.

Některé služby (například SQL a CosmosDB) umožňují výjimky z výše uvedené sekvence pomocí příznaku **IgnoreMissingVnetServiceEndpoint** . Po nastavení příznaku na **hodnotu true** můžete na straně služby Azure nastavit seznamy ACL pro virtuální sítě před nastavením koncových bodů služby na straně sítě. Pomocí tohoto příznaku služby Azure můžou zákazníkům pomáhat v případech, kdy jsou konkrétní brány firewall IP v rámci služeb Azure nakonfigurované a zapnutí koncových bodů služby na straně sítě může způsobit odpojení od změny zdrojové IP adresy z veřejné IPv4 adresy na soukromou adresu. Nastavení seznamů ACL pro virtuální síť na straně služby před nastavením koncových bodů služby na straně sítě může zabránit vyřazení připojení.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Budou se všechny služby Azure nacházet ve virtuální síti Azure poskytované zákazníkem? Jak funguje koncový bod služby VNet se službami Azure?

Ne, ne všechny služby Azure se nacházejí ve virtuální síti zákazníka. Většina datových služeb Azure, jako jsou Azure Storage, Azure SQL a Azure Cosmos DB, je víceklientské služby, ke kterým je možné přistupovat přes veřejné IP adresy. Další informace o integraci služby Virtual Network pro Azure najdete [tady](virtual-network-for-azure-services.md). 

Když použijete funkci koncové body služby virtuální sítě (zapnete koncový bod služby virtuální sítě na straně sítě a nastavíte příslušné seznamy ACL pro virtuální síť na straně služby Azure), přístup ke službě Azure se omezí z povolené virtuální sítě a podsítě.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Jak koncový bod služby virtuální sítě poskytuje zabezpečení?

Funkce koncového bodu služby virtuální sítě (zapnutí koncového bodu služby virtuální sítě na straně sítě a nastavení odpovídajících seznamů ACL pro virtuální síť na straně služby Azure) omezuje přístup ke službě Azure na povolenou virtuální síť a podsíť, takže zajišťuje zabezpečení na úrovni sítě a izolaci provozu služeb Azure. Veškerý provoz využívající koncové body služby virtuální sítě natéká přes páteřní síť Microsoftu a zajišťuje další vrstvu izolace z veřejného Internetu. Zákazníci si navíc můžou vybrat možnost plně odebrat veřejný internetový přístup k prostředkům služby Azure a pomocí kombinace seznamů ACL protokolu IP a virtuální sítě, aby se povolily přenosy jenom z jejich virtuální sítě, a tak chránit prostředky služeb Azure před neoprávněným přístupem.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Co jsou prostředky ochrany koncových bodů služby virtuální sítě nebo služba Azure?
Koncové body služby virtuální sítě vám pomůžou chránit prostředky služeb Azure. Prostředky virtuální sítě jsou chráněné pomocí skupin zabezpečení sítě (skupin zabezpečení sítě).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Platí se za používání koncových bodů služby virtuální sítě nějaké náklady?

Ne, za použití koncových bodů služby virtuální sítě se neúčtují žádné další náklady.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Můžu zapnout koncové body služby virtuální sítě a nastavit seznamy řízení přístupu k virtuálním sítím, pokud virtuální síť a prostředky služeb Azure patří do různých předplatných?

Ano, je to možné. Virtuální sítě a prostředky služeb Azure můžou být ve stejném nebo různých předplatných. Jediným požadavkem je, že virtuální síť i prostředky služby Azure musí být ve stejném tenantovi Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Můžu zapnout koncové body služby virtuální sítě a nastavit seznamy ACL pro virtuální sítě, pokud virtuální síť a prostředky služeb Azure patří do různých tenantů služby AD?
Ano, je možné použít koncové body služby pro Azure Storage a Azure Key Vault. V případě služeb REST nejsou v klientech služby AD podporovány koncové body služby virtuální sítě a seznamy ACL virtuální sítě.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Může IP adresa místního zařízení připojená prostřednictvím služby Azure Virtual Network Gateway (VPN) nebo brány ExpressRoute přistupovat ke službě Azure PaaS prostřednictvím koncových bodů služby VNet?
Prostředky služeb Azure svázané s virtuálními sítěmi ve výchozím nastavení nejsou přístupné z místních sítí. Pokud chcete povolený provoz z místního prostředí, musíte taky z místního nebo ExpressRoute povolených IP adres veřejných (obvykle NAT). Tyto IP adresy je možné přidat prostřednictvím konfigurace brány firewall protokolu IP pro prostředky služeb Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Můžu použít funkci koncového bodu služby VNet k zabezpečení služby Azure na více podsítí v rámci virtuální sítě nebo napříč více virtuálními sítěmi?
Chcete-li zabezpečit služby Azure na více podsítí v rámci virtuální sítě nebo napříč více virtuálními sítěmi, povolte koncové body služby na straně sítě v každé z těchto podsítí nezávisle a poté Zabezpečte prostředky služby Azure do všech podsítí nastavením příslušných seznamů ACL pro virtuální síť na straně služby Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Jak mohu filtrovat odchozí provoz z virtuální sítě do služeb Azure a pořád používat koncové body služby?
Pokud chcete prozkoumat nebo filtrovat provoz směřující do služby Azure z virtuální sítě, můžete v rámci virtuální sítě nasadit síťové virtuální zařízení. Pak můžete použít koncové body služby pro podsíť, ve které je virtuální síťové zařízení nasazené, a zabezpečit prostředky služby Azure jenom pro tuto podsíť prostřednictvím seznamů ACL pro virtuální sítě. Tento scénář může být užitečný i v případě, že chcete omezit přístup služby Azure z vaší virtuální sítě jenom na konkrétní prostředky Azure pomocí filtrování síťového virtuálního zařízení. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Co se stane, když přistupujete k účtu služby Azure, který má povolený seznam řízení přístupu (ACL) k virtuální síti, mimo virtuální síť?
Vrátí se chyba HTTP 403 nebo HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Mají podsítě virtuální sítě vytvořené v různých oblastech povolený přístup k účtu služby Azure v jiné oblasti? 
Ano, pro většinu služeb Azure mají virtuální sítě vytvořené v různých oblastech přístup ke službám Azure v jiné oblasti prostřednictvím koncových bodů služby virtuální sítě. Pokud je například účet Azure Cosmos DB v Západní USA nebo Východní USA a virtuální sítě jsou ve více oblastech, může k Azure Cosmos DB přistupovat tato virtuální síť. Úložiště a SQL jsou výjimky a jsou v podstatě regiony a virtuální síť i služba Azure musí být ve stejné oblasti.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Může služba Azure mít jak seznam ACL virtuální sítě, tak bránu firewall protokolu IP?
Ano, seznam ACL virtuální sítě a brána firewall protokolu IP můžou existovat souběžně. Obě funkce se vzájemně doplňují, aby se zajistila izolace a zabezpečení.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Co se stane, když odstraníte virtuální síť nebo podsíť s povoleným koncovým bodem služby pro službu Azure?
Odstranění virtuální sítě a podsítí jsou nezávislé operace a podporují se i v případě, že jsou pro služby Azure zapnuté koncové body služby. V případech, kdy jsou u služeb Azure nastavené seznamy ACL virtuální sítě pro tyto virtuální sítě a podsítě, jsou informace seznamu ACL virtuální sítě přidružené k této službě Azure zakázané, když se odstraní virtuální síť nebo podsíť, která má zapnutý koncový bod služby virtuální sítě.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Co se stane, když se odstraní účet služby Azure s povoleným koncovým bodem služby virtuální sítě?
Odstranění účtu služby Azure je nezávislá operace a podporuje se i v případě, že je koncový bod služby povolen na straně sítě a seznamy ACL pro virtuální síť jsou nastavené na straně služby Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Co se stane se zdrojovou IP adresou prostředku (jako je třeba virtuální počítač v podsíti) s povoleným koncovým bodem služby VNet?
Když jsou povoleny koncové body služby virtuální sítě, zdrojové IP adresy prostředků v podsíti virtuální sítě přecházejí z použití veřejných IPV4 adres na privátní IP adresy virtuální sítě Azure pro provoz do služby Azure. Všimněte si, že to může způsobit, že specifické brány firewall protokolu IP, které se v předchozích verzích služby Azure nastavily na veřejnou IPV4 adresu, nejsou úspěšné. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Má vždy přednost směrování koncového bodu služby?
Koncové body služby přidávají systémovou trasu, která má přednost před trasami protokolu BGP a poskytuje optimální směrování provozu koncového bodu služby. Koncové body služby vždy přebírají provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Další informace o tom, jak Azure vybírá trasu, najdete v tématu [směrování provozu virtuální sítě Azure](virtual-networks-udr-overview.md).

### <a name="do-service-endpoints-work-with-icmp"></a>Fungují koncové body služby s protokolem ICMP?
Ne. přenosy protokolu ICMP, které se nacházely z podsítě s povolenými koncovými body služby, převezmou cestu k tunelu služby na požadovaný koncový bod. Koncové body služby budou zpracovávat jenom přenosy TCP. To znamená, že pokud chcete otestovat latenci nebo připojení ke koncovému bodu prostřednictvím koncových bodů služby, nástroje, jako je třeba příkazy příkazového testu a příkazu tracert, nebudou zobrazovat skutečnou cestu, kterou budou prostředky v podsíti provádět.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Jak funguje NSG v podsíti s koncovými body služby?
Aby se dosáhlo služby Azure, skupin zabezpečení sítě musí umožňovat odchozí připojení. Pokud se vaše skupin zabezpečení sítě otevřou na veškerý internetový odchozí provoz, měl by provoz koncového bodu služby fungovat. Odchozí provoz na IP adresy služeb taky můžete omezit jenom pomocí značek služeb.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Jaká oprávnění potřebuji k nastavení koncových bodů služby?
Koncové body služby je možné nakonfigurovat ve virtuální síti nezávisle na uživateli s oprávněním k zápisu do virtuální sítě. Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel oprávnění **Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action** pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení součástí předdefinované role správce služeb a dá se upravit vytvořením vlastních rolí. Další informace o předdefinovaných rolích a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Můžu filtrovat provoz virtuální sítě do služeb Azure a povolit jenom konkrétní prostředky služeb Azure, přes koncové body služby VNet? 

Zásady koncového bodu služby Virtual Network (VNet) umožňují filtrovat provoz virtuální sítě do služeb Azure a povolit jenom určité prostředky služby Azure nad koncovými body služby. Zásady koncového bodu poskytují podrobné řízení přístupu z provozu virtuální sítě do služeb Azure. Další informace o zásadách koncového bodu služby najdete [tady](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Podporuje Azure Active Directory (Azure AD) koncové body služby virtuální sítě?

Azure Active Directory (Azure AD) nepodporují nativní koncové body služby. Úplný seznam služeb Azure, které podporují koncové body služby virtuální sítě, najdete [tady](./virtual-network-service-endpoints-overview.md). Všimněte si, že značka "Microsoft. Azureactivedirectory selhala" uvedená v části služby podporující koncové body služby se používá pro podporu koncových bodů služby ADLS pro obecné 1. V případě ADLS 1 pro obecné služby Virtual Network Integration Service pro Azure Data Lake Storage Gen1 využívá zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a Azure Active Directory (Azure AD) ke generování dalších deklarací zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. Další informace o [integraci virtuální sítě Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Existují nějaká omezení počtu koncových bodů služby virtuální sítě, které můžu nastavit z mé virtuální sítě?
Celkový počet koncových bodů služby virtuální sítě ve virtuální síti není nijak omezený. Pro prostředek služby Azure (například účet Azure Storage) můžou služby vymáhat omezení počtu podsítí používaných k zabezpečení prostředku. Následující tabulka uvádí některé příklady omezení: 

|Služba Azure| Omezení pravidel virtuální sítě|
|---|---|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure Synapse Analytics|   128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store v1|  100|
 
>[!NOTE]
> Tato omezení se vztahují na změny na uvážení služby Azure. Podrobnosti o službách najdete v příslušné dokumentaci ke službě. 




