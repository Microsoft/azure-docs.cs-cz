---
title: Typy IP adres v Azure (klasické)
titlesuffix: Azure Virtual Network
description: Další informace o veřejné a privátní IP adresy (klasické) v Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: f96ac14d68d98937cf230b04b45503e21c5e0187
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024565"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Typy IP adres a metody přidělení (classic) v Azure
Přiřazením IP adres k prostředkům Azure umožníte komunikaci s ostatními prostředky Azure, místní sítí a internetem. Existují dva typy IP adres můžete použít v Azure: veřejné a soukromé.

Veřejné IP adresy se používají ke komunikaci s Internetem, včetně veřejně přístupných služeb Azure.

Privátní IP adresy se používají ke komunikaci v rámci virtuální sítě Azure (VNet), cloudové služby a v místní síti, pokud použijete VPN gateway nebo okruh ExpressRoute můžete svoji síť rozšířit do Azure.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky:  [Resource Manager a classic](../resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala Resource Manageru. Další informace o IP adresách v Resource Manageru najdete [IP adresy](virtual-network-ip-addresses-overview-arm.md) článku.

## <a name="public-ip-addresses"></a>Veřejné IP adresy
Veřejné IP adresy umožňují prostředkům Azure komunikovat s Internetem a Azure veřejně přístupných služeb, jako [mezipaměti Azure Redis](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [databází SQL](../sql-database/sql-database-technical-overview.md), a [služby Azure storage](../storage/common/storage-introduction.md).

Veřejná IP adresa je přidružen následující typy prostředků:

* Cloud Services
* Virtuální počítače IaaS (virtuální počítače)
* Instance rolí PaaS
* VPN Gateway
* Application Gateway

### <a name="allocation-method"></a>Metoda přidělování
Veřejná IP adresa je třeba přiřadit k prostředku Azure, je *dynamicky* přiděluje z fondu k dispozici veřejnou IP adresu v rámci umístění je prostředek vytvořený. Tato IP adresa se uvolní při zastavení prostředku. S cloudovou službou, to se stane, když všechny instance rolí jsou zastaveny, které se můžete vyhnout použitím *statické* (vyhrazená) IP adresa (naleznete v tématu [Cloud Services](#Cloud-services)).

> [!NOTE]
> Seznam rozsahů IP adres, ze kterých jsou přidělené veřejné IP adresy k prostředkům Azure je zveřejněný na webu [rozsahy IP adres Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Překlad názvů hostitelů DNS
Při vytváření cloudové služby nebo Virtuálním počítači IaaS, budete muset poskytnout název cloudové služby DNS, který je jedinečný ve všech prostředků v Azure. Tím se vytvoří mapování na serverech DNS spravovaných Azure pro *dnsname*. cloudapp.net na veřejnou IP adresu prostředku. Například při vytváření cloudové služby s názvem služby DNS cloud z **contoso**, plně kvalifikovaný název domény (FQDN) **contoso.cloudapp.net** se přeloží veřejnou IP adresu (VIP) v cloudu Služba. Tento plně kvalifikovaný název domény můžete použít k vytvoření vlastního záznamu CNAME domény odkazujícího na veřejnou IP adresu v Azure.

### <a name="cloud-services"></a>Cloud Services
Cloudové služby má vždy veřejné IP adresy uvedené jako virtuální IP adresa (VIP). Vytvoření koncových bodů v cloudové službě přidružit jiné porty v virtuální IP adresy na vnitřních portech virtuálních počítačů a instancí rolí v rámci cloudové služby. 

Cloudové služby může obsahovat více virtuálních počítačů IaaS nebo instancí rolí PaaS, všechny vystavené prostřednictvím virtuálních IP adres stejné cloudové služby. Můžete také přiřadit [více virtuálních IP adres v cloudové službě](../load-balancer/load-balancer-multivip.md), která umožňuje scénáře s více virtuálních IP adres, jako je víceklientské prostředí s weby využívajícími SSL.

Můžete zajistit veřejnou IP adresu z cloudové služby zůstane stejný, i když se zastaví všechny instance rolí, pomocí *statické* veřejnou IP adresu, označuje jako [vyhrazenou IP adresu](virtual-networks-reserved-public-ip.md). Můžete vytvořit statická (vyhrazená) IP prostředku v konkrétním umístění a přiřaďte ho k jakékoli cloudové služby v daném umístění. Nelze určit vlastní IP adresu pro vyhrazenou IP adresu, se přiděluje z fondu dostupných IP adres v umístění, na které se vytvoří. Tuto IP adresu není všeobecně dostupné, dokud neodstraníte explicitně.

Statická (vyhrazená) veřejné IP adresy se obvykle používají ve scénářích, kde je Cloudová služba:

* vyžaduje pravidla brány firewall pro nastavit tak, že koncoví uživatelé.
* závisí na externí překlad názvů DNS, a zjišťování dynamických IP by vyžadovala aktualizace záznamů.
* využívá externí webové služby, které používají model zabezpečení na základě IP adresy.
* používá certifikáty SSL propojené k IP adrese.

> [!NOTE]
> Když vytvoříte klasického virtuálního počítače, kontejneru *Cloudová služba* se vytvoří v Azure, která má virtuální IP adresa (VIP). Po vytvoření se provádí prostřednictvím portálu, výchozí protokolu RDP nebo SSH *koncový bod* je nakonfigurován pomocí portálu, abyste se mohli připojit k virtuálnímu počítači přes virtuální IP adresa cloudové služby. Je možné vyhradit virtuální IP ADRESE této cloudové služby, která efektivně poskytuje vyhrazenou IP adresu pro připojení k virtuálnímu počítači. Otevřít další porty tím, že nakonfigurujete další koncové body.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuální počítače IaaS a instance rolí PaaS
Veřejnou IP adresu můžete přiřadit přímo k IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo instance rolí PaaS v rámci cloudové služby. To se označuje jako veřejnou IP adresu na úrovni instance ([ILPIP](virtual-networks-instance-level-public-ip.md)). Tato veřejná IP adresa může být pouze dynamické.

> [!NOTE]
> Tím se liší od virtuálních IP adres cloudové služby, což je kontejner pro virtuální počítače IaaS nebo PaaS instance rolí, protože cloudové služby může obsahovat několik virtuálních počítačů IaaS nebo instancí rolí PaaS, všechny prostřednictvím virtuálních IP adres stejné cloudové služby.
> 
> 

### <a name="vpn-gateways"></a>VPN Gateway
A [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) je možné se připojit virtuální síti Azure k jiným virtuálním sítím Azure nebo místním sítím. Brána VPN je přiřazena veřejná IP adresa *dynamicky*, který umožňuje komunikaci se vzdálenou sítí.

### <a name="application-gateways"></a>Application Gateway
Azure [Application gateway](../application-gateway/application-gateway-introduction.md) lze použít pro vrstvy 7 Vyrovnávání zatížení směrovat síťový provoz založený na protokolu HTTP. Služba Application gateway přiřazena veřejná IP adresa *dynamicky*, který slouží jako virtuální IP adresy s vyrovnáváním zatížení.

### <a name="at-a-glance"></a>První pohled
Následující tabulka ukazuje, každý typ prostředku pomocí metody možné přidělení (dynamické/statické) a schopnost přiřadit několik veřejných IP adres.

| Prostředek | Dynamická | Statická | Několik IP adres |
| --- | --- | --- | --- |
| Cloudová služba |Ano |Ano |Ano |
| Instance role virtuálních počítačů IaaS nebo PaaS |Ano |Ne |Ne |
| VPN Gateway |Ano |Ne |Ne |
| Application Gateway |Ano |Ne |Ne |

## <a name="private-ip-addresses"></a>Privátní IP adresy
Privátní IP adresy umožňují prostředkům Azure komunikovat s ostatními prostředky v cloudové službě nebo [virtuální sítě](virtual-networks-overview.md)(VNet), nebo k místní síti (pomocí VPN gateway nebo okruh ExpressRoute), bez použití Internet dostupnou IP adresu.

V modelu nasazení Azure classic můžete přiřadit privátní IP adresu pro následující prostředky Azure:

* Virtuální počítače IaaS a instance rolí PaaS
* Interní nástroj pro vyrovnávání zatížení
* Application Gateway

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuální počítače IaaS a instance rolí PaaS
Virtuální počítače (VM) vytvořené pomocí modelu nasazení classic jsou vždy umístěny v cloudové službě, podobně jako instance rolí PaaS. Chování privátních IP adres je tedy podobně jako pro tyto prostředky.

Je důležité si uvědomit, že cloudové služby je možné nasadit dvěma způsoby:

* Jako *samostatné* Cloudová služba, kde není v rámci virtuální sítě.
* V rámci virtuální sítě.

#### <a name="allocation-method"></a>Metoda přidělování
V případě klíčových *samostatné* Cloudová služba, prostředky získání privátní IP adresa přiděluje *dynamicky* rozsah adres z privátní IP adresy datacentra Azure. Je možné pouze pro komunikaci s jiným virtuálním počítačům v rámci stejné cloudové službě. Tuto IP adresu můžete změnit, když zdroj je zastavit a spustit.

V případě cloudovou službu nasazenou v rámci virtuální sítě získání zdrojů privátní IP adresa přiděluje z rozsahu adres přidružené podsítě (jak je uvedeno v konfiguraci sítě). Tato privátní IP adresy lze použít pro komunikaci mezi všechny virtuální počítače ve virtuální síti.

Kromě toho v případě cloudových služeb v rámci virtuální sítě, které je přiděleno privátní IP adresu *dynamicky* (pomocí protokolu DHCP) ve výchozím nastavení. Můžete změnit, když zdroj je zastavit a spustit. Aby IP adresa zůstávala stejná, je nutné nastavit metodu přidělování *statické*a zadejte platnou IP adresu odpovídající rozsahu adres.

Statické privátní IP adresy se obvykle používají pro:

* Virtuální počítače, které slouží jako řadiče domény nebo servery DNS.
* Virtuální počítače, které vyžadují pravidla brány firewall pomocí IP adresy.
* Virtuální počítače, které přistupují jiné aplikace přes určitou IP adresu služby.

#### <a name="internal-dns-hostname-resolution"></a>Interní překlad názvů hostitelů DNS
Všechny virtuální počítače Azure a instance rolí PaaS jsou nakonfigurovány s [servery DNS spravovanými Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ve výchozím nastavení, pokud explicitně nenakonfigurujete vlastní servery DNS. Tyto servery DNS poskytují interní překlad adres pro virtuální počítače a instance rolí, které se nacházejí ve stejné virtuální sítě nebo cloudové služby.

Když vytvoříte virtuální počítač, do serverů DNS spravovaných Azure se přidá mapování názvu hostitele na jeho IP adresu. K virtuálnímu počítači s více síťovými Kartami název hostitele se mapuje na privátní IP adresu primární síťové rozhraní Tyto informace o mapování však omezen na prostředky v rámci stejné cloudové službě nebo virtuální sítě.

V případě klíčových *samostatné* cloudové služby, bude moct přeložit názvy hostitelů všech instancí virtuálních počítačů nebo rolí v rámci stejné cloudové službě pouze. V případě cloudové služby v rámci virtuální sítě budou moct přeložit názvy hostitelů všech instancí virtuálních počítačů nebo rolí v rámci virtuální sítě.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interní nástroje pro vyrovnávání a Application Gateway
Privátní IP adresu můžete přiřadit konfiguraci **front-endu** nástroje [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) nebo služby [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Tato privátní IP adresa slouží jako interní koncový bod, který je přístupný pouze pro prostředky v příslušné virtuální síti (VNet) a ve vzdálených sítích připojených k virtuální síti. Konfiguraci front-endu můžete přiřadit dynamickou nebo statickou privátní IP adresu. Můžete také přiřadit několik privátních IP adres umožňuje scénáře s více virtuálních IP adres.

### <a name="at-a-glance"></a>První pohled
Následující tabulka ukazuje, každý typ prostředku se metody možné přidělení (dynamické/statické) a možnost přiřadit k více privátních IP adres.

| Prostředek | Dynamická | Statická | Několik IP adres |
| --- | --- | --- | --- |
| Virtuální počítač (v *samostatné* cloudovou službu nebo virtuální sítě) |Ano |Ano |Ano |
| PaaS role instance (v *samostatné* cloudovou službu nebo virtuální sítě) |Ano |Ne |Ne |
| Interní služby load balancer front-endu |Ano |Ano |Ano |
| Front-endu aplikace brány |Ano |Ano |Ano |

## <a name="limits"></a>Omezení
Následující tabulka uvádí omezení IP adresování v Azure na jedno předplatné. Pokud chcete v závislosti na svých obchodních potřebách zvýšit výchozí omezení na povolené maximum, [kontaktujte podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

|  | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy (dynamické) |5 |kontaktovat podporu |
| Vyhrazené veřejné IP adresy |20 |kontaktovat podporu |
| Veřejné virtuální IP adresy na nasazení (Cloudová služba) |5 |kontaktovat podporu |
| Privátní virtuální IP adresy (ILB) na nasazení (Cloudová služba) |1 |1 |

Nezapomeňte si přečíst úplnou sadu [omezení sítě](../azure-subscription-service-limits.md#networking-limits) v Azure.

## <a name="pricing"></a>Ceny
Ve většině případů veřejné IP adresy jsou zdarma. Zde se účtuje nominální poplatek používat další a/nebo statické veřejné IP adresy. Ujistěte se, že rozumíte [Cenová struktura pro veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Rozdíly mezi Resource Managerem a klasickými nasazeními
Níže je uvedeno porovnání IP adresování funkcí v modelu nasazení classic a Resource Manageru.

|  | Prostředek | Classic | Resource Manager |
| --- | --- | --- | --- |
| **Veřejná IP adresa** |***VIRTUÁLNÍ POČÍTAČ*** |Označuje jako ILPIP (pouze dynamické) |Označuje jako veřejné IP adresy (dynamické nebo statické) |
|  ||Přiřazené k Virtuálním počítači IaaS nebo instancí rolí PaaS |Přidružené k síťové karty Virtuálního počítače | |
|  |***Internet internetový nástroj pro vyrovnávání zatížení*** |Označuje jako virtuální IP adresy (dynamické) nebo rezervovaná IP adresa (staticky) |Označuje jako veřejné IP adresy (dynamické nebo statické) | |
|  ||Přiřadit cloudové službě |Přidružené ke konfiguraci front-endu nástroje pro vyrovnávání zatížení | |
|  | | | |
| **Privátní IP adresa** |***VIRTUÁLNÍ POČÍTAČ*** |Uvedený jako vyhrazené IP adresy |Označuje jako privátní IP adresy |
|  ||Přiřazené k Virtuálním počítači IaaS nebo instancí rolí PaaS |Přiřazené síťové rozhraní Virtuálního počítače | |
|  |***Interní služby load balancer (ILB)*** |Přiřazená ILB (dynamické nebo statické) |Přiřazené konfigurace front-endu ILB (dynamické nebo statické) | |

## <a name="next-steps"></a>Další postup
* [Nasazení virtuálního počítače se statickou privátní IP adresou](virtual-networks-static-private-ip-classic-pportal.md) pomocí webu Azure portal.

