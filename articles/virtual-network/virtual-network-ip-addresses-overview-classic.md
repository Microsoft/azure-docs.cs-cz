---
title: Typy adres IP v Azure (klasický) | Microsoft Docs
description: Další informace o veřejné a privátní IP adresy (klasické) v Azure.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: f9e36abd44c2012d96a6ead8659197dcf66c6032
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP adresa typy a metody přidělování (klasické) v Azure
Přiřazením IP adres k prostředkům Azure umožníte komunikaci s ostatními prostředky Azure, místní sítí a internetem. Existují dva typy IP adres můžete použít v Azure: veřejné a soukromé.

Veřejné IP adresy se používají ke komunikaci s Internetem, včetně Azure veřejně přístupných služeb.

Privátní IP adresy se používají ke komunikaci v rámci virtuální sítě Azure (VNet), cloudové služby a v místní síti, při použití brány sítě VPN nebo okruhem ExpressRoute pro rozšíření vaší sítě do Azure.

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md).  Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala Resource Manager. Další informace o IP adres ve službě Správce prostředků načtením [IP adresy](virtual-network-ip-addresses-overview-arm.md) článku.

## <a name="public-ip-addresses"></a>Veřejné IP adresy
Veřejné IP adresy umožňují prostředkům Azure komunikovat s internetem a veřejnými službami Azure, jako jsou například [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Databases](../sql-database/sql-database-technical-overview.md) a [Azure Storage](../storage/common/storage-introduction.md).

Veřejná IP adresa je přidružen následující typy prostředků:

* Cloud Services
* Virtuální počítače IaaS (VM)
* Instance rolí PaaS
* VPN Gateway
* Application Gateway

### <a name="allocation-method"></a>Metoda přidělování
Veřejná IP adresa je třeba přiřadit k prostředku Azure, je *dynamicky* přidělit z fondu k dispozici veřejnou IP adresu v rámci umístění byl vytvořený. Tato IP adresa je vydala, pokud prostředek je zastavená. V případě, že cloudové služby, se to stane, když jsou zastaveny všechny instance rolí, které se vyhnout pomocí *statické* (vyhrazené) IP adresu (najdete v části [cloudové služby](#Cloud-services)).

> [!NOTE]
> Seznam rozsahů IP adres, ze kterých se veřejné IP adresy přidělené prostředky Azure je publikován v [rozsahy IP Datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Překlad názvů hostitelů DNS
Při vytváření virtuálního počítače IaaS nebo cloudové služby, je třeba zadat název DNS cloudové služby, které je jedinečné v rámci všechny prostředky v Azure. Tím se vytvoří mapování na serverech spravovaných Azure DNS pro *dnsname*. cloudapp.net na veřejnou IP adresu prostředku. Například při vytváření cloudové služby s názvem služby DNS služby cloud **contoso**, plně kvalifikovaný název domény (FQDN) **contoso.cloudapp.net** bude odkazující na veřejnou IP adresu (VIP) z cloudové služby. Tento plně kvalifikovaný název domény můžete použít k vytvoření vlastního záznamu CNAME domény odkazujícího na veřejnou IP adresu v Azure.

### <a name="cloud-services"></a>Cloud Services
Cloudové služby má vždy veřejnou IP adresu označuje jako virtuální adresy IP (VIP). Koncové body můžete vytvořit v cloudové službě přidružit jiné porty v VIP na interní porty virtuální počítače a instance rolí v rámci cloudové služby. 

Cloudové služby může obsahovat více virtuálních počítačů IaaS nebo instancí rolí PaaS, všechny zveřejněné prostřednictvím virtuálních IP adres stejné cloudové služby. Je také možné přiřadit [několika virtuálními IP adresami v cloudové službě](../load-balancer/load-balancer-multivip.md), která umožňuje scénáře více virtuálních IP adres jako víceklientské prostředí s weby založené na protokolu SSL.

Můžete zajistit veřejnou IP adresu cloudové služby zůstává stejná, i když jsou zastaveny všechny instance role pomocí *statické* veřejnou IP adresu, označuje jako [vyhrazenou IP adresu](virtual-networks-reserved-public-ip.md). Můžete vytvořit prostředek statické IP (vyhrazené) v určitém umístění a přiřaďte ho pro všechny cloudové služby v tomto umístění. Nelze zadat IP adresu skutečného pro vyhrazenou IP adresu, se přidělí z fondu dostupné IP adresy v umístění, je vytvořena. Tato IP adresa se neuvolní, dokud neodstraníte explicitně.

Statické (vyhrazené) veřejné IP adresy se obvykle používají ve scénářích, kde je Cloudová služba:

* vyžaduje pravidla brány firewall nastavit koncových uživatelů.
* závisí na externí překlad názvů DNS, a dynamických IP by vyžadovaly aktualizace záznamů.
* využívá externí webové služby, které používají model zabezpečení na základě IP.
* používá certifikáty SSL propojený na IP adresu.

> [!NOTE]
> Při vytváření klasické virtuální počítač, kontejner *Cloudová služba* se vytvoří v Azure, který má virtuální adresy IP (VIP). Při vytváření se provádí prostřednictvím portálu, výchozí protokolu RDP nebo SSH *koncový bod* se nakonfiguruje prostřednictvím portálu, aby se mohli připojit k virtuálnímu počítači prostřednictvím VIP cloudové služby. Nelze vyhradit VIP této cloudové služby, které efektivně poskytuje vyhrazenou IP adresu pro připojení k virtuálnímu počítači. Další porty můžete otevřít tak, že nakonfigurujete další koncové body.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuální počítače IaaS a instancí rolí PaaS
Veřejnou IP adresu můžete přiřadit přímo k IaaS [virtuálních počítačů](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo instance role PaaS v rámci cloudové služby. Tento proces se označuje jako úrovni instance veřejnou IP adresu ([splnění](virtual-networks-instance-level-public-ip.md)). Tato veřejná IP adresa může být pouze dynamické.

> [!NOTE]
> To se liší od VIP cloudové služby, který slouží jako kontejner pro virtuální počítače IaaS a PaaS instance rolí, protože cloudové služby může obsahovat více virtuálních počítačů IaaS nebo instancí rolí PaaS, všechny vystavenou přes virtuálních IP adres stejné cloudové služby.
> 
> 

### <a name="vpn-gateways"></a>VPN Gateway
A [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lze použít pro připojení k jiné sítě Azure Vnet nebo místní sítě o virtuální síť Azure. Brána sítě VPN je přiřazen veřejnou IP adresu *dynamicky*, který umožňuje komunikaci se vzdáleným sítím.

### <a name="application-gateways"></a>Application Gateway
Azure [Aplikační brána](../application-gateway/application-gateway-introduction.md) lze použít pro Layer7 Vyrovnávání zatížení ke směrování síťového provozu založené na protokolu HTTP. Aplikační brána je přiřazen veřejnou IP adresu *dynamicky*, který slouží jako virtuální IP adresy Vyrovnávání zatížení sítě.

### <a name="at-a-glance"></a>První pohled
Následující tabulka uvádí každý typ prostředku s možné přidělení metody (dynamický nebo statický) a schopnost přiřadit víc veřejných IP adres.

| Prostředek | Dynamická | Statická | Několik IP adres |
| --- | --- | --- | --- |
| Cloudová služba |Ano |Ano |Ano |
| Instance role virtuálních počítačů IaaS nebo PaaS |Ano |Ne |Ne |
| VPN Gateway |Ano |Ne |Ne |
| Application Gateway |Ano |Ne |Ne |

## <a name="private-ip-addresses"></a>Privátní IP adresy
Privátní IP adresy umožňují prostředkům Azure komunikovat s jiným prostředkům v cloudové službě nebo [virtuální sítě](virtual-networks-overview.md)(VNet), nebo k místní síti (prostřednictvím brány sítě VPN nebo okruh ExpressRoute), bez použití Internet dostupnou IP adresu.

V modelu nasazení Azure classic je možné přiřadit privátní IP adresu pro následující prostředky Azure:

* Virtuální počítače IaaS a instancí rolí PaaS
* Interní nástroj pro vyrovnávání zatížení
* Application Gateway

### <a name="iaas-vms-and-paas-role-instances"></a>Virtuální počítače IaaS a instancí rolí PaaS
Virtuální počítače (VM) vytvořené pomocí modelu nasazení classic jsou vždy umístěny v cloudové službě, podobně jako instance rolí PaaS. Chování soukromé IP adresy jsou proto podobné pro tyto prostředky.

Je důležité si uvědomit, že cloudové služby lze nasadit dvěma způsoby:

* Jako *samostatné* Cloudová služba, pokud to není v rámci virtuální sítě.
* V rámci virtuální sítě.

#### <a name="allocation-method"></a>Metoda přidělování
Pro *samostatné* Cloudová služba, prostředky get privátní IP adresy přidělené *dynamicky* z privátní IP adresy datové centrum Azure rozsahu adres. Slouží pouze pro komunikaci s ostatních virtuálních počítačů v rámci stejné cloudové služby. Tuto IP adresu lze změnit, pokud prostředek je zastavit a spustit.

V případě cloudové služby nasadit v rámci virtuální sítě získat prostředky privátní IP adresy přidělené z rozsahu adres přidružených podsítí (jako je zadaný v konfiguraci sítě). Tento privátní IP adresy lze použít pro komunikaci mezi všechny virtuální počítače v rámci virtuální sítě.

Kromě toho v případě cloudové služby v rámci virtuální sítě, je přidělená privátní IP adresy *dynamicky* (pomocí protokolu DHCP) ve výchozím nastavení. Ho lze změnit, pokud prostředek je zastavit a spustit. Aby se zajistilo, IP adresa zůstane stejný, budete muset nastavena metoda přidělení na *statické*a zadejte platnou IP adresu odpovídající rozsahu adres.

Statické privátní IP adresy se obvykle používají pro:

* Virtuální počítače, které slouží jako řadiče domény nebo servery DNS.
* Virtuální počítače, které jsou potřebné pravidla brány firewall pomocí IP adresy.
* Virtuální počítače spuštěné služby přistupují jiné aplikace pomocí adresy IP.

#### <a name="internal-dns-hostname-resolution"></a>Interní překlad DNS názvu hostitele
Všechny instance rolí PaaS a virtuální počítače Azure jsou nakonfigurovány s [servery spravovat Azure DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ve výchozím nastavení, pokud explicitně nakonfigurovat vlastní servery DNS. Tyto servery DNS zadejte interní překlad adres pro virtuální počítače a instance rolí, které se nacházejí v rámci stejné virtuální síť nebo cloudové služby.

Když vytvoříte virtuální počítač, do serverů DNS spravovaných Azure se přidá mapování názvu hostitele na jeho IP adresu. V případě více síťovými Kartami virtuálního počítače název hostitele je namapována na privátní IP adresu na primární síťový adaptér. Tyto informace o mapování je však s omezeným přístupem k prostředkům v rámci stejné cloudové služby nebo virtuální sítě.

Pro *samostatné* Cloudová služba, bude možné přeložit názvy hostitelů všech instancí virtuálních počítačů nebo rolí v rámci pouze stejné cloudové služby. V případě cloudové služby v rámci virtuální sítě bude moct přeložit názvy hostitelů všech instancí virtuálních počítačů nebo rolí v rámci virtuální sítě.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interní nástroje pro vyrovnávání a Application Gateway
Privátní IP adresu můžete přiřadit konfiguraci **front-endu** nástroje [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) nebo služby [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Tato privátní IP adresa slouží jako interní koncový bod, který je přístupný pouze pro prostředky v příslušné virtuální síti (VNet) a ve vzdálených sítích připojených k virtuální síti. Konfiguraci front-endu můžete přiřadit dynamickou nebo statickou privátní IP adresu. Můžete také přiřadit více privátních IP adres k povolení scénářů více virtuálních IP adres.

### <a name="at-a-glance"></a>První pohled
Následující tabulka uvádí každý typ prostředku s možné přidělení metody (dynamický nebo statický) a schopnost přiřadit více privátních IP adres.

| Prostředek | Dynamická | Statická | Několik IP adres |
| --- | --- | --- | --- |
| Virtuální počítač (v *samostatné* Cloudová služba nebo virtuální síť) |Ano |Ano |Ano |
| PaaS role instance (v *samostatné* Cloudová služba nebo virtuální síť) |Ano |Ne |Ne |
| Nástroje pro vyrovnávání zatížení interní front-end |Ano |Ano |Ano |
| Front-endu aplikace brány |Ano |Ano |Ano |

## <a name="limits"></a>Omezení
Následující tabulka uvádí omezení, která na IP adresách v Azure za předplatné. Pokud chcete v závislosti na svých obchodních potřebách zvýšit výchozí omezení na povolené maximum, [kontaktujte podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

|  | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy (dynamické) |5 |kontaktovat podporu |
| Vyhrazené veřejné IP adresy |20 |kontaktovat podporu |
| Veřejné VIP jedno nasazení (Cloudová služba) |5 |kontaktovat podporu |
| Privátní virtuální adresy IP (ILB) na jedno nasazení (Cloudová služba) |1 |1 |

Ujistěte se, abyste si přečetli kompletní [omezení pro sítě](../azure-subscription-service-limits.md#networking-limits) v Azure.

## <a name="pricing"></a>Ceny
Ve většině případů jsou volné veřejné IP adresy. Je nominální poplatků používat další nebo statické veřejné IP adresy. Musíte rozumět [struktura cenách pro veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Rozdíly mezi Resource Manager a nasazení classic
Níže je uvedeno porovnání IP adresování funkcí ve Správci prostředků a modelu nasazení classic.

|  | Prostředek | Classic | Resource Manager |
| --- | --- | --- | --- |
| **Veřejná IP adresa** |***VIRTUÁLNÍ POČÍTAČ*** |Označuje jako splnění (jenom dynamické) |Označuje jako veřejnou IP adresu (dynamický nebo statický) |
|  ||Přiřazené virtuálních počítačů IaaS nebo PaaS role instance |Přidružené k síťový adaptér Virtuálního počítače | |
|  |***Internet přístupných nástroj pro vyrovnávání zatížení*** |Označuje jako VIP (dynamické) nebo vyhrazené IP adresy (statické) |Označuje jako veřejnou IP adresu (dynamický nebo statický) | |
|  ||Přiřazené cloudové služby |Přidružený ke konfiguraci front-end služby Vyrovnávání zatížení | |
|  | | | |
| **Privátní IP adresa** |***VIRTUÁLNÍ POČÍTAČ*** |Označuje jako vyhrazené IP adresy |Označuje jako privátní IP adresy |
|  ||Přiřazené virtuálních počítačů IaaS nebo PaaS role instance |Přiřadit síťový adaptér Virtuálního počítače | |
|  |***Interní vyrovnávání zátěže (ILB)*** |Přiřazené ILB (dynamický nebo statický) |Přiřazené ke konfiguraci front-end ILB (dynamická nebo statická) | |

## <a name="next-steps"></a>Další postup
* [Nasadit virtuální počítač se statickou privátní IP adresou](virtual-networks-static-private-ip-classic-pportal.md) pomocí portálu Azure.

