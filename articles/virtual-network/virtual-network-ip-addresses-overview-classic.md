---
title: Typy IP adres v Azure (Classic)
titlesuffix: Azure Virtual Network
description: Přečtěte si o veřejných a privátních IP adresách (Classic) v Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 7b197e1acf696c2ae6e919ee2eddacfb82ac3802
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646774"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Typy IP adres a metody přidělování (Classic) v Azure
Přiřazením IP adres k prostředkům Azure umožníte komunikaci s ostatními prostředky Azure, místní sítí a internetem. Existují dva typy IP adres, které můžete použít v Azure: Public a Private.

Veřejné IP adresy se používají ke komunikaci s internetem, včetně veřejně přístupných služeb Azure.

Privátní IP adresy se používají ke komunikaci v rámci virtuální sítě Azure (VNet), cloudové služby a místní sítě, když k rozšiřování sítě do Azure používáte bránu VPN nebo okruh ExpressRoute.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby většina nových nasazení používala Správce prostředků. Další informace o IP adresách v Správce prostředků najdete v článku [IP adresy](virtual-network-ip-addresses-overview-arm.md) .

## <a name="public-ip-addresses"></a>Veřejné IP adresy
Veřejné IP adresy umožňují prostředkům Azure komunikovat s internetem a veřejnými službami Azure, jako je [Azure cache pro Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [databáze SQL](../sql-database/sql-database-technical-overview.md)a [Azure Storage](../storage/common/storage-introduction.md).

Veřejná IP adresa je přidružená k následujícím typům prostředků:

* Cloudové služby
* IaaS Virtual Machines (virtuální počítače)
* Instance rolí PaaS
* Brány VPN Gateway
* Application Gateway

### <a name="allocation-method"></a>Metoda přidělování
Pokud musí být veřejná IP adresa přiřazena prostředku Azure, *dynamicky* se přiděluje z fondu dostupné veřejné IP adresy v umístění, ve kterém je prostředek vytvořený. Tato IP adresa se uvolní při zastavení prostředku. V případě cloudové služby k tomu dojde, když se zastaví všechny instance rolí, což se může vyhnout použitím *statické* (REZERVOVANÉ) IP adresy (viz [Cloud Services](#cloud-services)).

> [!NOTE]
> Seznam rozsahů IP adres, ze kterých se k prostředkům Azure přiděluje veřejné IP adresy, se zveřejňuje v [rozsahu IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Překlad názvů hostitelů DNS
Když vytváříte cloudovou službu nebo virtuální počítač s IaaS, musíte zadat název DNS cloudové služby, který je jedinečný pro všechny prostředky v Azure. Tím se vytvoří mapování na serverech DNS spravovaných Azure pro *DnsName*. cloudapp.NET na veřejnou IP adresu prostředku. Když například vytvoříte cloudovou službu s názvem DNS cloudové služby **Contoso**, bude plně kvalifikovaný název domény (FQDN) **contoso.cloudapp.NET** překládat na veřejnou IP adresu (VIP) cloudové služby. Tento plně kvalifikovaný název domény můžete použít k vytvoření vlastního záznamu CNAME domény odkazujícího na veřejnou IP adresu v Azure.

### <a name="cloud-services"></a>Cloudové služby
Cloudová služba má vždy veřejnou IP adresu, která se označuje jako virtuální IP adresa (VIP). V cloudové službě můžete vytvářet koncové body, které přiřadí různé porty v rámci virtuální IP adresy k interním portům virtuálních počítačů a instancí rolí v rámci cloudové služby. 

Cloudová služba může obsahovat několik virtuálních počítačů s IaaS nebo instancí PaaS rolí, které jsou vystavené prostřednictvím stejné virtuální IP adresy cloudové služby. [Cloudové službě](../load-balancer/load-balancer-multivip.md)můžete také přiřadit více virtuálních IP adres, což umožňuje použití scénářů s více VIP, jako je víceklientské prostředí s weby využívajícími SSL.

Můžete zajistit, aby veřejná IP adresa cloudové služby zůstala stejná, i když se všechny instance rolí zastaví, a to pomocí *statické* veřejné IP adresy, která se označuje jako [vyhrazená IP adresa](virtual-networks-reserved-public-ip.md). Můžete vytvořit statický (rezervovaný) prostředek IP adresy v určitém umístění a přiřadit ho ke cloudové službě v tomto umístění. Nemůžete zadat skutečnou IP adresu rezervované IP adresy, která se přidělí z fondu dostupných IP adres v umístění, ve kterém je vytvořená. Tato IP adresa se uvolní, dokud ji explicitně neodstraníte.

Statické (vyhrazené) veřejné IP adresy se obvykle používají v situacích, kdy cloudová služba:

* vyžaduje, aby se pravidla brány firewall nastavila koncovými uživateli.
* závisí na překladu externího názvu DNS a dynamická IP adresa by vyžadovala aktualizaci záznamů.
* využívá externí webové služby, které používají model zabezpečení založený na IP adresách.
* používá certifikáty SSL propojené s IP adresou.

> [!NOTE]
> Když vytvoříte klasický virtuální počítač, vytvoří se v Azure *cloudová služba* kontejneru, která má virtuální IP adresu (VIP). Když se vytváření provádí přes portál, na portálu se nastaví výchozí *koncový bod* RDP nebo SSH, abyste se mohli k virtuálnímu počítači připojit prostřednictvím virtuální IP adresy cloudové služby. Tato virtuální IP adresa cloudové služby se dá rezervovat, což efektivně poskytuje vyhrazenou IP adresu pro připojení k virtuálnímu počítači. Další porty můžete otevřít tak, že nakonfigurujete více koncových bodů.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuální počítače a instance rolí PaaS
Veřejnou IP adresu můžete přiřadit přímo k [virtuálnímu počítači](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) s IaaS nebo k instanci role PaaS v rámci cloudové služby. Tento postup se označuje jako veřejná IP adresa na úrovni instance ([ILPIP](virtual-networks-instance-level-public-ip.md)). Tato veřejná IP adresa může být pouze dynamická.

> [!NOTE]
> To se liší od virtuální IP adresy cloudové služby, což je kontejner pro IaaS virtuální počítače nebo instance role PaaS, protože cloudová služba může obsahovat víc virtuálních počítačů IaaS nebo instancí rolí PaaS, které jsou vystavené prostřednictvím stejné virtuální IP adresy cloudové služby.
> 
> 

### <a name="vpn-gateways"></a>Brány VPN Gateway
[Bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) je možné použít k připojení virtuální sítě Azure k jiným službám Azure virtuální sítě nebo místním sítím. Bráně VPN je *dynamicky*PŘIŘAZENA veřejná IP adresa, která umožňuje komunikaci se vzdálenou sítí.

### <a name="application-gateways"></a>Application Gateway
Služba Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) se dá použít k vyrovnávání zatížení vrstvy 7 ke směrování síťového provozu na základě http. Aplikační bráně je *dynamicky*přiřazená veřejná IP adresa, která slouží jako virtuální IP adresa s vyrovnáváním zatížení.

### <a name="at-a-glance"></a>První pohled
V následující tabulce jsou uvedeny jednotlivé typy prostředků s možnými metodami přidělení (dynamické/statické) a možnost přiřadit více veřejných IP adres.

| Prostředek | Dynamický | Statický | Několik IP adres |
| --- | --- | --- | --- |
| Cloudová služba |Ano |Ano |Ano |
| Instance role virtuálního počítače IaaS nebo PaaS |Ano |Ne |Ne |
| VPN Gateway |Ano |Ne |Ne |
| Application Gateway |Ano |Ne |Ne |

## <a name="private-ip-addresses"></a>Privátní IP adresy
Privátní IP adresy umožňují prostředkům Azure komunikovat s dalšími prostředky v cloudové službě nebo [virtuální síti](virtual-networks-overview.md)nebo v místní síti (přes bránu VPN nebo okruh ExpressRoute) bez použití IP adresy dostupné pro Internet.

V modelu nasazení Azure Classic je možné přiřadit k následujícím prostředkům Azure privátní IP adresu:

* IaaS virtuální počítače a instance rolí PaaS
* Interní nástroj pro vyrovnávání zatížení
* Application Gateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS virtuální počítače a instance rolí PaaS
Virtuální počítače vytvořené pomocí modelu nasazení Classic jsou vždycky umístěné v cloudové službě, podobně jako instance rolí PaaS. Chování privátních IP adres je proto pro tyto prostředky podobné.

Je důležité si uvědomit, že cloudovou službu je možné nasadit dvěma způsoby:

* Jako *samostatná* cloudová služba, ve které se nenachází v rámci virtuální sítě.
* Jako součást virtuální sítě.

#### <a name="allocation-method"></a>Metoda přidělování
V případě *samostatné* cloudové služby se prostředkům získají privátní IP adresa přidělená *dynamicky* z rozsahu privátních IP adres datacentra Azure. Dá se použít jenom pro komunikaci s jinými virtuálními počítači v rámci stejné cloudové služby. Tato IP adresa se může změnit při zastavení a spuštění prostředku.

V případě cloudové služby nasazené ve virtuální síti prostředky získají privátní IP adresy přidělené z rozsahu adres přidružených podsítí (jak je uvedeno v konfiguraci sítě). Tato privátní IP adresa se dá použít ke komunikaci mezi všemi virtuálními počítači v rámci virtuální sítě.

Kromě toho platí, že v případě cloudových služeb v rámci virtuální sítě se privátní IP adresa přiřazuje *dynamicky* (pomocí protokolu DHCP) ve výchozím nastavení. Může se změnit při zastavení a spuštění prostředku. Chcete-li zajistit, aby IP adresa zůstala stejná, je třeba nastavit metodu přidělení na *statickou*a zadat platnou IP adresu v rámci odpovídajícího rozsahu adres.

Statické privátní IP adresy se obvykle používají pro:

* Virtuální počítače, které slouží jako řadiče domény nebo servery DNS.
* Virtuální počítače, které vyžadují pravidla brány firewall pomocí IP adres.
* Virtuální počítače, na kterých běží služby, k nimž přistupovaly jiné aplikace prostřednictvím IP adresy.

#### <a name="internal-dns-hostname-resolution"></a>Interní překlad názvů hostitelů DNS
Všechny virtuální počítače Azure a instance rolí PaaS jsou ve výchozím nastavení nakonfigurované se [servery DNS spravovanými Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) , pokud explicitně nenakonfigurujete vlastní servery DNS. Tyto servery DNS poskytují interní překlad adres IP pro virtuální počítače a instance rolí, které se nacházejí ve stejné virtuální síti nebo cloudové službě.

Když vytvoříte virtuální počítač, do serverů DNS spravovaných Azure se přidá mapování názvu hostitele na jeho IP adresu. S virtuálním počítačem s více síťovými kartami se název hostitele namapuje na privátní IP adresu primárního síťového rozhraní. Tyto informace o mapování jsou však omezeny na prostředky v rámci stejné cloudové služby nebo virtuální sítě.

V případě *samostatné* cloudové služby budete moct přeložit názvy hostitelů všech virtuálních počítačů nebo instancí rolí jenom v rámci stejné cloudové služby. V případě cloudové služby v rámci virtuální sítě budete moct přeložit názvy hostitelů všech instancí virtuálních počítačů nebo rolí v rámci virtuální sítě.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interní nástroje pro vyrovnávání a Application Gateway
Privátní IP adresu můžete přiřadit konfiguraci **front-endu** nástroje [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) nebo služby [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Tato privátní IP adresa slouží jako interní koncový bod, který je přístupný pouze pro prostředky v příslušné virtuální síti (VNet) a ve vzdálených sítích připojených k virtuální síti. Konfiguraci front-endu můžete přiřadit dynamickou nebo statickou privátní IP adresu. Můžete také přiřadit několik privátních IP adres a povolit tak scénáře s více VIP.

### <a name="at-a-glance"></a>První pohled
V následující tabulce jsou uvedeny jednotlivé typy prostředků s možnými metodami přidělení (dynamické/statické) a možnost přiřazení více privátních IP adres.

| Prostředek | Dynamický | Statický | Několik IP adres |
| --- | --- | --- | --- |
| Virtuální počítač (v *samostatné* cloudové službě nebo virtuální síti) |Ano |Ano |Ano |
| Instance role PaaS (v *samostatné* cloudové službě nebo virtuální síti) |Ano |Ne |Ne |
| Front-end interního nástroje pro vyrovnávání zatížení |Ano |Ano |Ano |
| Front-end aplikační brány |Ano |Ano |Ano |

## <a name="limits"></a>Omezení
V následující tabulce jsou uvedena omezení pro IP adresování v Azure na předplatné. Pokud chcete v závislosti na svých obchodních potřebách zvýšit výchozí omezení na povolené maximum, [kontaktujte podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

|  | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy (dynamické) |5 |kontaktovat podporu |
| Vyhrazené veřejné IP adresy |20 |kontaktovat podporu |
| Veřejná VIP na nasazení (cloudová služba) |5 |kontaktovat podporu |
| Privátní VIP (interního nástroje) na nasazení (cloudová služba) |1\. místo |1\. místo |

Ujistěte se, že jste si přečetli kompletní sadu [omezení pro sítě](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) v Azure.

## <a name="pricing"></a>Ceny
Ve většině případů jsou veřejné IP adresy bezplatné. Pro použití dalších nebo statických veřejných IP adres se účtuje nominální poplatek. Ujistěte se, že rozumíte [cenové struktuře pro veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Rozdíly mezi Správce prostředků a klasickými nasazeními
Níže je srovnání funkcí IP adresování v Správce prostředků a modelu nasazení Classic.

|  | Prostředek | Klasické | Správce prostředků |
| --- | --- | --- | --- |
| **Veřejná IP adresa** |***VM*** |Odkazováno jako na ILPIP (pouze dynamické) |Označuje se jako veřejná IP adresa (dynamická nebo statická). |
|  ||Přiřazeno k virtuálnímu počítači s IaaS nebo k instanci role PaaS |Přidruženo k síťovému adaptéru virtuálního počítače |
|  |***Internetový nástroj pro vyrovnávání zatížení*** |Označuje se jako virtuální IP adresa (dynamická) nebo Vyhrazená IP adresa (static). |Označuje se jako veřejná IP adresa (dynamická nebo statická). |
|  ||Přiřazeno ke cloudové službě |Přidruženo k konfiguraci front-endu nástroje pro vyrovnávání zatížení |
|  | | | |
| **Privátní IP adresa** |***VM*** |Označuje se jako DIP. |Označuje se jako soukromá IP adresa. |
|  ||Přiřazeno k virtuálnímu počítači s IaaS nebo k instanci role PaaS |Přiřazeno síťovému adaptéru virtuálního počítače |
|  |***Interní nástroj pro vyrovnávání zatížení (interního nástroje)*** |Přiřazeno k interního nástroje (dynamická nebo statická) |Přiřazeno ke konfiguraci front-endu interního nástroje (dynamická nebo statická) |

## <a name="next-steps"></a>Další kroky
* [Nasaďte virtuální počítač se statickou privátní IP adresou](virtual-networks-static-private-ip-classic-pportal.md) pomocí Azure Portal.

