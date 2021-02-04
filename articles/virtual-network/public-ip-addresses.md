---
title: Veřejné IP adresy v Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o veřejných IP adresách v Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 702ca4f4c3fc92eca70479ba02b1ddc8f6858857
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549656"
---
# <a name="public-ip-addresses"></a>Veřejné IP adresy

Veřejné IP adresy umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy umožňují prostředkům Azure komunikovat s internetem a veřejně přístupnými službami Azure. Adresa je vyhrazená pro prostředek, dokud není Nepřiřazená vámi. Prostředek bez přiřazeného veřejné IP adresy může komunikovat odchozí. Azure dynamicky přiřadí dostupnou IP adresu, která není vyhrazená pro daný prostředek. Další informace o odchozích připojeních v Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[Veřejná IP](virtual-network-public-ip-address.md) adresa v Azure Resource Manageru je prostředek, který má svoje vlastní vlastnosti. K některým prostředkům můžete přidružit prostředek veřejné IP adresy:

* síťová rozhraní virtuálních počítačů,
* Internetové nástroje pro vyrovnávání zatížení
* VPN Gateway
* brány Application Gateway.
* Azure Firewall

## <a name="ip-address-version"></a>Verze IP adresy

Veřejné IP adresy se vytvářejí s IPv4 nebo IPv6 adresou. 

## <a name="sku"></a>SKU

Další informace o upgradu SKU najdete v tématu [upgrade veřejné IP adresy](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Veřejné IP adresy se vytvářejí s jednou z následujících SKU:

>[!IMPORTANT]
> Pro nástroj pro vyrovnávání zatížení a prostředky veřejné IP adresy se vyžadují vyhovující SKU. Není možné kombinovat prostředky z SKU Basic s prostředky z SKU Standard. Samostatné virtuální počítače, virtuální počítače v prostředku skupiny dostupnosti ani prostředky škálovacích sad virtuálních počítačů není možné připojit k oběma SKU zároveň.  Při nových návrzích by se měla zvážit možnost použít prostředky SKU Standard.  Podrobnosti najdete v článku o [nástroji pro vyrovnávání zatížení úrovně Standard](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="standard"></a>Standard

Veřejné IP adresy standardní SKU:

- Vždy použít metodu statického přidělení.
- Musí mít nastavitelný časový limit nečinnosti příchozího výstupního toku 4-30 minut, výchozí hodnota je 4 minuty a pevný časový limit odchozího pocházejícího toku je 4 minuty.
- Zabezpečení je ve výchozím nastavení a uzavřeno pro příchozí provoz. Povoluje výpis příchozích přenosů se [skupinou zabezpečení sítě](./network-security-groups-overview.md#network-security-groups).
- Přiřazeno k síťovým rozhraním, standardním veřejným nástrojům pro vyrovnávání zatížení nebo aplikačním branám. Další informace o službě Load Balancer úrovně Standard najdete v tématu [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Může být redundantní v zóně (v inzerci ze všech 3 zón), oblastí (zaručená v konkrétní předem vybrané zóně dostupnosti) nebo v žádné zóně (nesouvisí s konkrétní předem vybranou zónou dostupnosti). Další informace o zónách dostupnosti najdete v článku s [přehledem zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a v článku o [nástroji pro vyrovnávání zatížení úrovně Standard a zónách dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Redundantní IP adresy zóny se dají vytvářet jenom v [oblastech, kde jsou živé zóny dostupnosti tři](../availability-zones/az-region.md) .** IP adresy vytvořené před živými zónami nebudou zóny redundantní.
- Dá se použít jako IP adresy front-endu pro služby [Vyrovnávání zatížení mezi oblastmi](../load-balancer/cross-region-overview.md) (funkce Preview).
 
> [!NOTE]
> Příchozí komunikace se zdrojem standardní skladové položky selže, dokud nevytvoříte a nepřidružíte [skupinu zabezpečení sítě](./network-security-groups-overview.md#network-security-groups) a výslovně nepovolíte požadovaný příchozí provoz.

> [!NOTE]
> Při použití [služby metadata instance IMDS](../virtual-machines/windows/instance-metadata-service.md)jsou k dispozici pouze veřejné IP adresy se základní SKU. Standardní SKU se nepodporuje.

### <a name="basic"></a>Basic

Všechny veřejné IP adresy vytvořené před zavedením položek SKU jsou základní SKU veřejných IP adres. 

Když zadáváte SKU, určete, která SKU má být veřejná IP adresa. 

Adresy základní SKU:

- Jsou přiřazované pomocí metody statického nebo dynamického přidělení.
- Musí mít nastavitelný časový limit nečinnosti příchozího výstupního toku 4-30 minut, výchozí hodnota je 4 minuty a pevný časový limit odchozího pocházejícího toku je 4 minuty.
- Jsou standardně otevřené.  Tyto skupiny jsou doporučené k omezení příchozího nebo odchozího provozu, ale nejsou povinné.
- Přiřazen k jakémukoli prostředku Azure, ke kterému se dá přiřadit veřejná IP adresa, třeba:
    * Síťová rozhraní
    * Brány VPN Gateway
    * Brány Application Gateway
    * Veřejné nástroje pro vyrovnávání zatížení
- Nepodporují scénáře zón dostupnosti. Pro scénáře zón dostupnosti použijte veřejnou IP adresu standardního SKU. Další informace o zónách dostupnosti najdete v článku s [přehledem zón dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a v článku o [nástroji pro vyrovnávání zatížení úrovně Standard a zónách dostupnosti](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Metoda přidělování

Základní a standardní veřejné IP adresy podporují **statické** přiřazení.  Prostředku je přiřazena IP adresa v době jejího vytvoření. IP adresa se uvolní při odstranění prostředku.

Veřejné IP adresy základní SKU podporují **dynamické** přiřazení. Dynamic je výchozí metoda přiřazení. Tato IP adresa **není** přidělena prostředku v době vytvoření při výběru možnosti Dynamic.

IP adresa se přiřadí při přidružení prostředku veřejné IP adresy k:

* Virtuální počítač 
* První virtuální počítač je přidružený ke fondu back-endu nástroje pro vyrovnávání zatížení.

Když tento prostředek zastavíte (nebo odstraníte), IP adresa se uvolní.  

Například prostředek veřejné IP adresy se uvolní z prostředku s názvem **Resource a**. Pokud se prostředek veřejné IP adresy znovu přiřadí, **vytvoří** se při spuštění jiná IP adresa.

IP adresa se uvolní, když se změní metoda přidělení ze **statické** na **dynamickou**. Pokud chcete zajistit, aby IP adresa přidruženého prostředku zůstala stejná, nastavte metodu alokace explicitně na **statickou**. Statická IP adresa se přiřadí okamžitě.

> [!NOTE]
> Ani při nastavení **statické** metody přidělování není možné určit vlastní IP adresu přiřazenou k prostředku s veřejnou IP adresou. Azure přiřazuje IP adresu z fondu dostupných IP adres v umístění Azure, ve kterém je prostředek vytvořený.
>

Statické veřejné IP adresy se obvykle používají v následujících scénářích:

* Když potřebujete aktualizovat pravidla brány firewall pro komunikaci s prostředky Azure.
* Překlad názvů DNS, kde by změna IP adresy vyžadovala aktualizace záznamů A.
* Vaše prostředky Azure komunikují s ostatními aplikacemi nebo službami, které využívají model zabezpečení založený na IP adresách.
* Používáte certifikáty TLS/SSL propojené s IP adresou.

> [!NOTE]
> Azure přiřazuje veřejné IP adresy z rozsahu, který je pro každou oblast v každém cloudu Azure jedinečný. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-hostname-resolution"></a>Překlad názvů hostitelů DNS

Tuto možnost vyberte, pokud chcete zadat popisek názvu domény DNS pro prostředek veřejné IP adresy. 

Tento výběr vytvoří mapování pro **domainnamelabel**. **umístění**. cloudapp.Azure.com na veřejnou IP adresu v DNS spravovaném Azure. 

Například vytvoření veřejné IP adresy pomocí:

* **Contoso** jako **domainnamelabel**
* **Západní USA** **Umístění** Azure

Plně kvalifikovaný název domény (FQDN) **contoso.westus.cloudapp.Azure.com** se přeloží na veřejnou IP adresu prostředku.

> [!IMPORTANT]
> Každý vytvořený popisek názvu domény musí být v rámci příslušného umístění Azure jedinečný.  
>

## <a name="dns-recommendations"></a>Doporučení DNS

Pokud je potřeba přesunout oblast, nemůžete migrovat plně kvalifikovaný název domény vaší veřejné IP adresy. Pomocí plně kvalifikovaného názvu domény vytvořte vlastní záznam CNAME odkazující na veřejnou IP adresu. 

Pokud se vyžaduje přesun na jinou veřejnou IP adresu, aktualizujte záznam CNAME místo aktualizace plně kvalifikovaného názvu domény.

Pro záznam DNS můžete použít [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) nebo externího poskytovatele DNS. 

## <a name="virtual-machines"></a>Virtuální počítače

Veřejnou IP adresu můžete k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přidružit tak, že ji přiřadíte jeho **síťovému rozhraní**. 

Pro veřejnou IP adresu vyberte **Dynamická** nebo **statická** . Další informace o [přiřazování IP adres k síťovým rozhraním](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Internetové nástroje pro vyrovnávání zatížení

Můžete přidružit veřejnou IP adresu libovolné [SKU](#sku) s [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a to tak, že ji přiřadíte ke konfiguraci **front-endu** nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako IP s vyrovnáváním zatížení. 

Front-endu nástroje pro vyrovnávání zatížení můžete přiřadit dynamickou nebo statickou veřejnou IP adresu. Front-endu nástroje pro vyrovnávání zatížení můžete přiřadit několik veřejných IP adres. Tato konfigurace povoluje scénáře s [více vipmi](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , jako je víceklientské prostředí s weby založenými na TLS. 

Další informace o skladových položkách nástroje pro vyrovnávání zatížení Azure najdete v tématu věnovaném [standardní SKU nástroje pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>VPN Gateway

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) připojuje virtuální síť Azure k:

* Virtuální sítě Azure
* Místní sítě (y). 

K VPN Gateway je přiřazena veřejná IP adresa, která umožňuje komunikaci se vzdálenou sítí. Službě VPN Gateway můžete přiřadit pouze *dynamickou* veřejnou IP adresu úrovně Basic.

## <a name="application-gateways"></a>brány Application Gateway.

Veřejnou IP adresu můžete přiřadit službě [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tak, že ji přiřadíte konfiguraci **front-endu** této brány. 

* Přiřaďte konfiguraci front-endu služby Application Gateway v1 **dynamické** základní veřejné IP adresy. 
* Přiřaďte **statickou** adresu Standard SKU pro konfiguraci front-endu v2.

## <a name="azure-firewall"></a>Azure Firewall

[Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) umožňuje vytvářet, vysazovat a protokolovat zásady pro připojení aplikací a sítí napříč předplatnými a virtuálními sítěmi.

Pomocí brány firewall můžete přidružit pouze **statické** standardní veřejné IP adresy. To umožňuje, aby mimo brány firewall identifikovaly provoz pocházející z vaší virtuální sítě. 


## <a name="at-a-glance"></a>Přehledně

V následující tabulce je uvedena vlastnost, jejímž prostřednictvím je možné veřejnou IP adresu přiřadit prostředku nejvyšší úrovně a možné metody přidělení.

| Prostředek nejvyšší úrovně | Přidružení IP adresy | Dynamická | Static |
| --- | --- | --- | --- |
| Virtuální počítač |Síťové rozhraní |Yes |Yes |
| Internetový nástroj pro vyrovnávání zatížení |Konfigurace front-endu |Yes |Yes |
| VPN Gateway |Konfigurace protokolu IP brány |Yes |No |
| Application Gateway |Konfigurace front-endu |Ano (jenom V1) |Ano (jenom v2) |
| Azure Firewall | Konfigurace front-endu | No | Yes|

## <a name="limits"></a>Omezení

Omezení IP adresování jsou uvedená v kompletní sadě [omezení pro sítě](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) v Azure. 

Omezení platí pro jednotlivé oblasti a jednotlivá předplatná. Pokud chcete zvýšit výchozí omezení na základě vašich obchodních potřeb, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="pricing"></a>Ceny

Za veřejné IP adresy se může účtovat nominální poplatek. Další informace o cenách IP adres v Azure najdete na stránce [Ceny IP adres](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Další kroky
* Informace o [privátních IP adresách v Azure](private-ip-addresses.md)
* [Nasazení virtuálního počítače se statickou veřejnou IP adresou pomocí webu Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
