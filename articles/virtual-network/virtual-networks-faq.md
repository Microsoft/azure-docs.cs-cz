---
title: Azure Virtual Network – nejčastější dotazy | Dokumentace Microsoftu
description: Odpovědi na nejčastější dotazy o Microsoft Azure virtual Network.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: e92c099d9e0dfacff71c13382059acb06037bb1e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999864"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Virtuální síť Azure, nejčastější dotazy (FAQ)

## <a name="virtual-network-basics"></a>Základy virtuální sítě

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co je Azure Virtual Network (VNet)?
Azure Virtual Network (VNet) je reprezentace vaší vlastní sítě v cloudu. Je to logická izolace cloudu Azure vyhrazeného pro vaše předplatné. Můžete použít ke zřízení virtuálních sítí a spravovat v Azure virtuální privátní sítě (VPN) a, Volitelně můžete propojit virtuální sítě s dalším virtuálním sítím v Azure nebo s místní IT infrastrukturu pro vytváření řešení hybridní nebo mezi různými místy. Každá virtuální síť, kterou jste vytvořili má svůj vlastní blok CIDR a mohou být propojeny s jinými virtuálními sítěmi a místními sítěmi, tak dlouho, dokud bloků CIDR nemusí být stejné. Máte také ovládání nastavení serveru DNS pro virtuální sítě a segmentaci virtuální sítě do podsítí.

Pomocí virtuálních sítí s:

* Vytvoření vyhrazené privátní výhradně cloudový virtuální sítě někdy není zapotřebí konfigurace mezi různými místy pro vaše řešení. Když vytvoříte virtuální síť, služeb a virtuálních počítačů v rámci vaší virtuální sítě může komunikovat přímo a bezpečně mezi sebou v cloudu. Stále můžete nakonfigurovat koncový bod připojení pro virtuální počítače a služby, které vyžadují internetovou komunikaci v rámci vašeho řešení.
* Bezpečně Rozšiřte své datové centrum s virtuálními sítěmi, můžete vytvářet tradiční VPN typu site-to-site (S2S) bezpečně škálovat kapacitu datového centra. S2S VPN pomocí protokolu IPSEC a nabízí tak zabezpečené připojení mezi vaší firemní bránou VPN a Azure.
* Povolit hybridní cloudové scénáře virtuální sítě získáte flexibilitu umožňující podporu celou řadu scénářů hybridního cloudu. Můžete bezpečně připojit cloudových aplikací libovolného typu v místním systému, jako je například sálové počítače a systémů Unix.

### <a name="how-do-i-get-started"></a>Jak mám začít?
Přejděte [dokumentace ke službě Virtual network](https://docs.microsoft.com/azure/virtual-network/) začít. Tato část poskytuje přehled a nasazení informace pro všechny funkce sítě VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Můžete použít virtuální sítě bez připojení mezi místními sítěmi?
Ano. Bez připojení k místním můžete použít virtuální síť. Například může spustit řadiče domény Microsoft Windows Server Active Directory a farmy služby SharePoint výhradně ve virtuální síti Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Můžete provádět optimalizace sítě WAN mezi virtuálními sítěmi nebo virtuální sítě a my v místním datovém centru?
Ano. Můžete nasadit [optimalizace sítě WAN síťové virtuální zařízení](https://azure.microsoft.com/marketplace/?term=wan+optimization) od více dodavatelů v Tržišti Azure Marketplace.

## <a name="configuration"></a>Konfigurace

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jaké nástroje použít k vytvoření virtuální sítě?
Tyto nástroje můžete použít k vytvoření nebo konfigurace virtuální sítě:

* portál Azure
* PowerShell
* Azure CLI
* Soubor konfigurace sítě (netcfg - pro jenom klasické virtuální sítě). Zobrazit [konfigurace virtuální sítě pomocí souboru konfigurace sítě](virtual-networks-using-network-configuration-file.md) článku.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jaké rozsahy adres můžete použít ve své virtuální sítě?
Všechny rozsahy IP adres definované v [RFC 1918](http://tools.ietf.org/html/rfc1918). Například 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Může mít veřejné IP adresy ve virtuální sítě?
Ano. Další informace o veřejné rozsahy IP adres najdete v tématu [vytvoření virtuální sítě](manage-virtual-network.md#create-a-virtual-network). Veřejné IP adresy nejsou přímo dostupné z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Existuje nějaké omezení počtu podsítí ve virtuální síti?
Ano. Zobrazit [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) podrobnosti. Adresní prostory podsítě navzájem překrývají.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existují nějaká omezení pomocí IP adresy v rámci těchto podsítí?
Ano. Azure si vyhrazuje některé IP adresy v každé podsíti. První a poslední IP adresy každé podsíti jsou vyhrazené pro udržování souladu s protokoly, spolu s x.x.x.1 x.x.x.3 adres každé podsítě, které se používají pro služby Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Způsob, jakým malé a jak velká může být virtuální sítě a podsítě?
Nejmenší podporované podsítě je minimální velikostí/29 a největší /8 (pomocí definice podsítě CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Můžu převést Moje virtuální místní sítě do Azure pomocí virtuální sítě?
Ne. Překryvy vrstvy 3 jsou virtuální sítě. Azure nepodporuje všechny vrstvy 2 sémantiku.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Můžete zadat vlastní zásady směrování na své virtuální sítě a podsítě
Ano. Můžete vytvořit směrovací tabulku a přidružte ji k podsíti. Další informace o směrování v Azure najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Podporují virtuální sítě vícesměrového vysílání nebo všesměrového vysílání?
Ne. Vícesměrové vysílání a vysílání nejsou podporovány.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jaké protokolů můžete použít v rámci virtuální sítě?
Můžete použít protokoly TCP, UDP a ICMP TCP/IP v rámci virtuální sítě. Jednosměrového vysílání je podporována v rámci virtuální sítě, s výjimkou hostitele konfigurace protokolu DHCP (Dynamic) prostřednictvím jednosměrového vysílání (zdrojový port UDP/68 / cílový port UDP/67). Vícesměrové vysílání, vysílání, IP-in-IP zapouzdřené pakety a zapouzdření protokolu GRE (Generic Routing) pakety jsou blokována v rámci virtuální sítě. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Může příkaz ping mé výchozí směrovačů v rámci virtuální sítě?
Ne.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Můžete použít příkaz tracert Diagnostika připojení?
Ne.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Můžete přidat podsítě, po vytvoření virtuální sítě?
Ano. Podsítě můžete přidat k virtuálním sítím kdykoli za předpokladu, rozsah adres podsítě není součástí jiné podsítě a není k dispozici místa v rozsahu adres virtuální sítě.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Můžete upravit velikost mou podsíť, po můžu vytvořit?
Ano. Můžete přidat, odebrat, rozbalte položku nebo zmenšit podsíť, pokud neexistují žádné virtuální počítače nebo službám nasazeným v rámci něj.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Můžete upravit po jejich vytvoření podsítí?
Ano. Můžete přidat, odebrat a upravit bloků CIDR používaných ve virtuální síti.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Když provozuju Moje služby ve virtuální síti, můžu se připojit k Internetu?
Ano. Všechny služby, které jsou nasazené v rámci virtuální sítě můžete připojit k Internetu. Další informace o odchozích připojení k Internetu v Azure najdete v tématu [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud chcete připojit příchozí prostředku nasazena prostřednictvím Resource Manageru, prostředku musí mít přiřazenou veřejnou IP adresu. Další informace o veřejné IP adresy najdete v tématu [veřejné IP adresy](virtual-network-public-ip-address.md). Každé cloudové službě Azure nasazené v Azure je veřejně adresovatelný virtuální IP adresy přiřazené k němu. Definujte vstupní koncové body pro role PaaS a koncových bodů pro virtuální počítače povolit tyto služby tak, aby přijímal připojení z Internetu.

### <a name="do-vnets-support-ipv6"></a>Podporují virtuální sítě IPv6?
Ne. V tuto chvíli nemůžete použít protokol IPv6 s virtuálními sítěmi. Můžete si ale, přiřadit IPv6 adres nástroje pro vyrovnávání zatížení Azure se načíst vyrovnávání virtuálních počítačů. Podrobnosti najdete v tématu [přehled protokolu IPv6 pro Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Virtuální síť může mít rozsah oblastech?
Ne. Virtuální síť je omezená na jednu oblast. Virtuální síť, ale span zóny dostupnosti. Další informace o zónách dostupnosti najdete v tématu [Přehled zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). S využitím partnerského vztahu virtuálních sítí můžete propojit virtuální sítě v různých oblastech. Podrobnosti najdete v tématu [přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Můžete připojit virtuální síť k jiné virtuální síti v Azure?
Ano. Jednu virtuální síť můžete připojit k jiné virtuální síti pomocí:
- **Partnerský vztah virtuální sítě**: Podrobnosti najdete v tématu [přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md)
- **Služby Azure VPN Gateway**: Podrobnosti najdete v tématu [konfigurace připojení typu VNet-to-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Překlad názvů (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jaké jsou možnosti služby DNS pro virtuální sítě?
Pomocí tabulky rozhodnutí na [překlad názvů pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md) stránka vás provede všechny DNS možnosti k dispozici.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Můžete určit servery DNS pro virtuální síť?
Ano. V nastavení virtuální sítě můžete určit IP adresy serverů DNS. Nastavení bude použito jako výchozí servery DNS pro všechny virtuální počítače ve virtuální síti.

### <a name="how-many-dns-servers-can-i-specify"></a>Počet serverů DNS, které můžete zadat
Referenční dokumentace [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Můžete upravit po vytvoření sítě servery DNS?
Ano. Seznam serverů DNS pro vaši virtuální síť můžete změnit kdykoli. Pokud změníte váš seznam serverů DNS, je potřeba restartování všech virtuálních počítačích ve vaší virtuální síti je ke sbírání na nový server DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co je Azure DNS a funguje s virtuálními sítěmi?
DNS poskytnutých platformou Azure je víceklientská služba DNS nabízené microsoftem. Azure zaregistruje všechny virtuální počítače a instance rolí cloudové služby v této službě. Tato služba poskytuje překlad názvu hostitele pro virtuální počítače a instance rolí, které jsou obsaženy v rámci stejné cloudové službě a plně kvalifikovaný název domény pro virtuální počítače a instance rolí ve stejné virtuální síti. Další informace o DNS, najdete v článku [překlad názvů pro instance rolí virtuálních počítačů a cloudových služeb](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existuje omezení na prvních 100 cloudové služby ve virtuální síti k napříč tenanty překladu DNS poskytnutých platformou Azure. Pokud používáte vlastní server DNS, toto omezení neplatí.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Můžete přepsat nastavení DNS na základě jednotlivé virtuální počítače nebo cloudové služby?
Ano. Můžete nastavit DNS servery na virtuální počítač nebo cloudovou službu, chcete-li přepsat výchozí nastavení sítě. Doporučujeme však, že používáte co nejvíc celé sítě DNS.

### <a name="can-i-bring-my-own-dns-suffix"></a>Můžete použít vlastní přípona DNS?
Ne. Nelze zadat vlastní přípona DNS pro vaše virtuální sítě.

## <a name="connecting-virtual-machines"></a>Připojení virtuálních počítačů

### <a name="can-i-deploy-vms-to-a-vnet"></a>Můžete nasadit virtuální počítače k virtuální síti?
Ano. Všechna síťová rozhraní (NIC) připojených k virtuálnímu počítači nasazené prostřednictvím modelu nasazení Resource Manager musí být připojené k virtuální síti. Virtuální počítače nasazené pomocí modelu nasazení classic jde připojení k virtuální síti.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jaké jsou různé typy IP adres, které můžu můžete přiřadit k virtuálním počítačům?
* **Privátní:** přiřazené k jednotlivým síťovým KARTÁM v rámci každého virtuálního počítače. Adresa se přiřadí používat buď statické nebo dynamické metody. Privátní IP adresy jsou přiřazené, z rozsahu, který jste zadali v nastavení podsítě vaší virtuální sítě. Prostředky nasazené prostřednictvím modelu nasazení classic přiřazených privátních IP adres, i když nejsou připojené k virtuální síti. Chování metody přidělení se liší v závislosti na tom, zda prostředek nasazené pomocí modelu nasazení classic nebo Resource Manager: 

  - **Resource Manager**: přiřazované pomocí metody dynamickou nebo statickou privátní IP adresa zůstane přiřazená k virtuálnímu počítači (Resource Manager) až do odstranění prostředku. Rozdíl je, že vyberete adresu přiřadíte při používání statické a vybere Azure při použití dynamické. 
  - **Klasické**: privátní IP adresy přiřazené dynamické metody mohou změnit, pokud virtuální počítač (klasický) virtuální počítač se restartuje poté, co bylo ve stavu Zastaveno (přidělení zrušeno). Pokud je potřeba zajistit, že nikdy nemění privátní IP adresy pro prostředek nasazené prostřednictvím modelu nasazení classic, přiřadíte privátní IP adresu se statickou metodu.

* **Veřejná:** volitelně přiřazené k síťové adaptéry připojené k virtuálním počítačům, které jsou nasazené prostřednictvím modelu nasazení Azure Resource Manageru. Adresu je možné přiřadit pomocí metody statického nebo dynamického přidělení. Všechny virtuální počítače a Cloud Services instance rolí, které jsou nasazené prostřednictvím modelu nasazení classic existovat v rámci cloudové služby, které je přiřazeno *dynamické*, veřejné virtuální IP adresa (VIP). Veřejnou *statické* IP adresu, volá se [rezervovaná IP adresa](virtual-networks-reserved-public-ip.md), volitelně může být přiřazen jako virtuální IP adresu. Můžete přiřadit veřejné IP adresy pro jednotlivé virtuální počítače nebo cloudové služby instance rolí, které jsou nasazené prostřednictvím modelu nasazení classic. Tyto adresy jsou volány [veřejná IP adresa na úrovni Instance (ILPIP](virtual-networks-instance-level-public-ip.md) adres a můžou být dynamicky přiřazovány.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Lze rezervovat privátní IP adresu pro virtuální počítač, který můžu vytvořit později?
Ne. Nelze rezervovat privátní IP adresu. Pokud se privátní IP adresa je k dispozici, je přiřazen k instanci virtuálního počítače nebo role Server DHCP. Virtuální počítač může nebo nemusí být ten, který má že přiřazenou privátní IP adresu. Privátní IP adresu již vytvořené virtuální počítač, můžete však změnit na libovolné dostupné privátní IP adresy.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Proveďte privátní změna IP adresy pro virtuální počítače ve virtuální síti?
To záleží na okolnostech. Pokud virtuální počítač byl nasazený prostřednictvím Resource Manageru, ne, bez ohledu na to, jestli byla IP adresa přiřadí pomocí metody statického nebo dynamického přidělení. Pokud virtuální počítač byl nasazený prostřednictvím modelu nasazení classic, dynamické IP adresy můžou změnit při spuštění virtuálního počítače poté, co bylo ve stavu Zastaveno (přidělení zrušeno). Adresa se uvolní z virtuálního počítače nasazeného prostřednictvím buď modelu nasazení, když virtuální počítač se odstraní.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Můžu ručně přiřazovat IP adresy pro síťové adaptéry v rámci operačního systému virtuálního počítače?
Ano, ale nedoporučuje, pokud není nezbytné, například při přiřazování více IP adres k virtuálnímu počítači. Podrobnosti najdete v tématu [přidávání více IP adres pro virtuální počítač](virtual-network-multiple-ip-addresses-portal.md#os-config). Pokud IP adresa přiřazená Azure síťové rozhraní připojené k virtuálnímu počítači změny a IP adresu v rámci operačního systému virtuálního počítače se liší, ztratíte připojení k virtuálnímu počítači.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Je-li zastavit slot pro nasazení cloudové služby nebo vypnutí virtuálního počítače z operačního systému, co se stane Moje IP adresy?
Nothing. IP adresy (veřejných virtuálních IP adres, veřejné a privátní) zůstanou zařazené do slot nasazení cloudové služby nebo virtuálního počítače.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Můžu přesunout virtuální počítače z jedné podsítě do jiné podsítě ve virtuální síti bez opětovného nasazení?
Ano. Můžete najít další informace najdete v [postup přesunutí role virtuálního počítače nebo instance k jiné podsíti](virtual-networks-move-vm-role-to-subnet.md) článku.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Můžete nakonfigurovat statickou adresu MAC pro virtuální počítač?
Ne. Adresa MAC není staticky nakonfigurovat.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Adresa MAC zůstane stejný pro můj virtuální počítač po jeho vytvoření?
Ano, adresa MAC zůstává stejná pro virtuální počítače nasazené prostřednictvím Resource Manageru a modely nasazení classic, dokud je neodstraníte. Adresa MAC byla vydána dříve, pokud virtuální počítač bylo zastaveno (přidělení zrušeno), ale teď je adresa MAC zachovány i v případě, že virtuální počítač je ve stavu Uvolněno.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Můžu připojit k Internetu z virtuálního počítače ve virtuální síti?
Ano. Všechny virtuální počítače a Cloud Services instance rolí, které jsou nasazené v rámci virtuální sítě můžete připojit k Internetu.

## <a name="azure-services-that-connect-to-vnets"></a>Služby Azure, které se připojují k virtuálním sítím

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Můžete použít Azure App Service Web Apps pomocí virtuální sítě?
Ano. Můžete nasadit webové aplikace v rámci virtuální sítě pomocí prostředí ASE (App Service Environment). Pokud jste nakonfigurovali pro vaše virtuální síť připojení point-to-site, všechny webové aplikace bezpečně připojit a přístup k prostředkům ve virtuální síti. Další informace najdete v následujících článcích:

* [Vytváření webových aplikací ve službě App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrujte svou aplikaci s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrace virtuální sítě a Hybrid Connections pomocí Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Můžete nasadit cloudové služby pomocí webových a pracovních rolí (PaaS) ve virtuální síti?
Ano. (Volitelně) můžete nasadit instance rolí cloudové služby v rámci virtuální sítě. Uděláte to tak, zadejte název virtuální sítě a mapování podsíť/role v konfiguračním oddílu síťové služby konfigurace. Není potřeba aktualizovat některé binární soubory.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Můžete připojit virtuální počítač Škálovací nastavit (VMSS) k virtuální síti?
Ano. VMSS musí připojit k virtuální síti.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Existuje, že úplný seznam Azure services, je možné nasadit prostředky z do virtuální sítě?
Ano, podrobnosti najdete v tématu [integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Které prostředky Azure PaaS můžete omezit přístup k datům z virtuální sítě?

Prostředky nasazené prostřednictvím některých služeb Azure PaaS (například Azure Storage a Azure SQL Database), můžete omezit přístup k síti pouze na prostředky ve virtuální síti prostřednictvím koncových bodů služby virtuální sítě. Podrobnosti najdete v tématu [přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Můžu přesunout Moje služby do a z virtuální sítě?
Ne. Služby do a z virtuální sítě se nedá přesunout. Pokud chcete přesunout prostředek do jiné virtuální síti, budete muset odstranit a znovu nasaďte prostředek.

## <a name="security"></a>Zabezpečení

### <a name="what-is-the-security-model-for-vnets"></a>Jaký je model zabezpečení pro virtuální sítě?
Virtuální sítě jsou izolované od mezi sebou a další služby hostované v infrastruktuře Azure. Virtuální síť je hranice vztahů důvěryhodnosti.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Můžete omezit příchozí a odchozí přenosy do virtuální sítě připojené zdroje
Ano. Můžete použít [skupiny zabezpečení sítě](security-overview.md) pro jednotlivé podsítě v rámci virtuální sítě, síťové adaptéry připojené k virtuální síti, nebo obojí.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Můžete implementovat brány firewall mezi prostředky virtuální sítě připojený?
Ano. Můžete nasadit [síťové virtuální zařízení brány firewall](https://azure.microsoft.com/marketplace/?term=firewall) od více dodavatelů v Tržišti Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Existuje informace o zabezpečení virtuální sítě k dispozici?
Ano. Podrobnosti najdete v tématu [Přehled zabezpečení sítě Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>Rozhraní API, schémat a nástroje

### <a name="can-i-manage-vnets-from-code"></a>Můžete spravovat virtuální sítě z kódu?
Ano. Rozhraní REST API můžete použít pro virtuální sítě v [Azure Resource Manageru](/rest/api/virtual-network) a [classic (Správa služeb)](http://go.microsoft.com/fwlink/?LinkId=296833) modely nasazení.

### <a name="is-there-tooling-support-for-vnets"></a>Je k dispozici podpora nástrojů pro virtuální sítě?
Ano. Další informace o používání:
- Na webu Azure portal k nasazení virtuálních sítí prostřednictvím [Azure Resource Manageru](manage-virtual-network.md#create-a-virtual-network) a [classic](virtual-networks-create-vnet-classic-pportal.md) modely nasazení.
- Prostředí PowerShell ke správě virtuálních sítích nasazených prostřednictvím [Resource Manageru](/powershell/module/azurerm.network) a [classic](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modely nasazení.
- Rozhraní příkazového řádku Azure (CLI) k nasazení a správě virtuálních sítích nasazených prostřednictvím [Resource Manageru](/cli/azure/network/vnet) a [classic](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) modely nasazení.  

## <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

### <a name="what-is-vnet-peering"></a>Co je partnerský vztah virtuální sítě?
Partnerský vztah virtuální sítě (nebo partnerský vztah virtuální sítě) můžete použít k propojení virtuálních sítí. Připojení partnerského vztahu virtuální sítě mezi virtuálními sítěmi umožňuje směrovat přenosy mezi nimi soukromě prostřednictvím adresy IPv4. Virtuální počítače v partnerských virtuálních sítích komunikovat mezi sebou, jako kdyby byly ve stejné síti. Tyto virtuální sítě může být ve stejné oblasti nebo v různých oblastech (označované také jako globální VNet Peering). Partnerské vztahy virtuálních sítí můžete také vytvořit napříč předplatnými Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Můžete vytvořit připojení s partnerským vztahem k virtuální síti v jiné oblasti?
Ano. Globální VNet peering umožní vytvořit partnerský vztah virtuálních sítí v různých oblastech. Globální VNet peering je k dispozici ve všech veřejných oblastech Azure. Můžete nejde vytvořit partnerský vztah globálně z veřejných oblastech Azure k národním cloudům. Globální partnerský vztah není aktuálně k dispozici v národních cloudech.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Můžete povolit VNet Peering Pokud Můj virtuální sítě patří do předplatných v rámci různých tenantů Azure Active Directory?
Aktuálně není možné navázat partnerský vztah virtuální sítě (ať už místní nebo globální) Pokud vaše předplatná patří do různých tenantů Azure Active Directory.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Připojení s partnerským vztahem je ve virtuální síti *iniciováno* stav, proč se nemůžu připojit?
Pokud vaše připojení s partnerským vztahem je ve stavu iniciováno, znamená to, že které jste vytvořili jenom jedno propojení. Obousměrné odkaz musí být vytvořen, aby bylo možné navázat úspěšné připojení. Například na vytvoření partnerského vztahu virtuálních sítí A virtuální síť B, odkaz musí vytvořit virtuální síť k sítí VNetB a VNetB pro virtuální síť. Vytváří se obě propojení se změní stav, který má *připojeno.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Připojení s partnerským vztahem je ve virtuální síti *odpojeno* stav, proč nelze vytvořit připojení s partnerským vztahem?
Pokud vaše připojení s partnerským vztahem virtuální sítě je v odpojeném stavu, znamená to, že jeden z odkazů, vytvoří se odstranila. Pokud chcete znovu navázat připojení s partnerským vztahem, je potřeba odstranit odkaz a znovu ho vytvořte.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Můžu vytvořit partnerský vztah virtuální síti pomocí virtuální sítě jiného předplatného?
Ano. Můžete vytvořit partnerský vztah virtuální sítě, mezi předplatnými a napříč oblastmi.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Můžu vytvořit partnerský vztah dvou virtuálních sítí s odpovídající nebo překrývající se rozsahy adres?
Ne. Adresní prostory musí není overalap povolit vytvoření partnerského vztahu virtuální sítě.

### <a name="how-much-do-vnet-peering-links-cost"></a>Kolik dělat VNet peering – propojení náklady?
Neplatí žádné poplatky pro vytvoření připojení s partnerským vztahem virtuální sítě. Účtuje se přenos dat pro partnerské vztahy virtuálních sítí. [Tady uvidíte](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Je provoz VNet peering šifrovaná?
Ne. Přenosy mezi prostředky ve virtuálních sítích s navázaným partnerským vztahem je privátní a izolované. Zcela zůstane na Backbone Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Proč je můj připojení s partnerským vztahem v odpojeném stavu?
Virtuální síť partnerské vztahy virtuálních sítí, přejděte do *odpojeno* stav, když se odstraní jeden partnerské propojení virtuálních sítí. Aby bylo možné obnovit úspěšné připojení s partnerským vztahem je nutné odstranit obě propojení.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Je-li vytvořit partnerský vztah virtuální síť k sítí VNetB a vytvořit partnerský vztah VNetB se sítí VNetC, to znamená, že virtuální síť a sítí VNetC vytvoření partnerského vztahu?
Ne. Tranzitivní partnerský vztah se nepodporuje. Virtuální síť a sítí VNetC to provedou musí vytvořit partnerský vztah.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existují nějaká omezení šířky pásma pro partnerské vztahy virtuálních sítí?
Ne. VNet peering, ať už místní nebo globální, nepředstavuje nějaká omezení šířky pásma. Šířka pásma je pouze omezení podle virtuálního počítače nebo výpočetních prostředků.

## <a name="virtual-network-tap"></a>Klepněte na virtuální síť

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Které oblasti Azure jsou k dispozici pro virtuální síť TAP?
Ve verzi preview pro vývojáře možnost je dostupná v oblasti střed USA – západ. Monitorovaných síťových rozhraní, klepněte na prostředek virtuální sítě a řešení kolekcí nebo analytics se musí nasadit ve stejné oblasti.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Naslouchací zařízení virtuální sítě podporuje všechny možnosti filtrování v zrcadlené pakety?
Možnosti filtrování nepodporují klepnutím na virtuální síť, ve verzi Preview. Při konfigurace klepněte na se přidá k síťovému rozhraní hluboká kopie všechny příchozí a výchozí přenos do síťového rozhraní se streamuje do cíle TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Lze přidat více konfigurací klepněte na monitorovaných síťových rozhraní?
Monitorovaných síťových rozhraní může mít pouze jednu konfiguraci TAP. Obraťte se na jednotlivé [partnerská řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) možnosti pro streamování víc kopií provoz klepnutím na analytických nástrojů podle vašeho výběru.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Můžete agregovat stejný prostředek virtuální sítě klepněte na provoz z monitorovaných síťových rozhraní ve více než jednu virtuální síť?
Ano. Stejné virtuální síti TAP prostředků je možné použít k agregaci přenosy se zrcadlením z monitorovaných síťových rozhraní v partnerských virtuálních sítích v rámci stejného předplatného nebo jiném předplatném. Klepněte na prostředek virtuální sítě a cíl nástroj pro vyrovnávání zatížení nebo cílové síťové rozhraní musí být ve stejném předplatném. Všechna předplatná musí být ve stejném tenantovi Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Je-li povolit konfiguraci klepnutím na virtuální sítě v síťovém rozhraní jsou nějaké speciální pokyny jakékoli výkonu na produkční provoz?

Virtuální síť TAP je ve verzi preview pro vývojáře. Ve verzi preview není žádná smlouva o úrovni služeb. Funkce by neměla používat pro produkční úlohy. Obrysů síťovému rozhraní virtuálního počítače s konfigurací klepněte na stejných prostředků na hostiteli azure přidělená k virtuálnímu počítači k posílání síťového provozu produkční slouží k provádění funkci zrcadlení a odeslat pakety pro zrcadlené. Vyberte správné [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikost virtuálního počítače zajistit dostatek prostředků k dispozici pro virtuální počítač pro odesílání provozních přenosů a přenosy se zrcadlením.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Je akcelerované síťové služby pro [Linux](create-vm-accelerated-networking-cli.md) nebo [Windows](create-vm-accelerated-networking-powershell.md) podporované službou virtual network TAP?

Budete moct přidat klepnutím na konfiguraci na síťové rozhraní připojené k virtuálnímu počítači, který je povolen s akcelerovanými síťovými službami. Ale její výkon a latenci na virtuálním počítači bude mít vliv přidáním TAP konfigurace od Azure není aktuálně podporovaná přesměrování zpracování pro zrcadlení provoz akcelerované síťové služby.