---
title: Azure Virtual Network
description: Přečtěte si o konceptech a funkcích Azure Virtual Network, včetně adresního prostoru, podsítí, oblastí a předplatných.
services: virtual-network
documentationcenter: na
author: KumudD
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 11651b7a76ea0e61f78f3b2f305e39ed621df391
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109313"
---
# <a name="what-is-azure-virtual-network"></a>Co je Azure Virtual Network?

Azure Virtual Network (VNet) je základní stavební blok vaší privátní sítě v Azure. Virtuální síť umožňuje mnoha typům prostředků Azure, jako je Azure Virtual Machines (VM), bezpečně komunikovat mezi sebou, internetem a místními sítěmi. Virtuální síť je podobná tradiční síti, kterou provozujete ve svém vlastním datovém centru, ale přináší další výhody infrastruktury Azure, jako je například škálování, dostupnost a izolace.

## <a name="why-use-an-azure-virtual-network"></a>Proč používat službu Azure Virtual Network?
Azure Virtual Network umožňuje prostředkům Azure zabezpečeně komunikovat mezi sebou, internetem a místními sítěmi. Mezi klíčové scénáře, které můžete provést mezi virtuálními sítěmi, patří komunikace prostředků Azure s internetem, komunikace mezi prostředky Azure, komunikace s místními prostředky, filtrování síťového provozu, směrování síťového provozu a integrace se službami Azure.

### <a name="communicate-with-the-internet"></a>Komunikace s internetem

Všechny prostředky ve virtuální síti můžou ve výchozím nastavení komunikovat odchozí připojení k Internetu. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Veřejnou IP adresu nebo veřejný Load Balancer můžete použít také ke správě odchozích připojení.  Další informace o odchozích připojeních v Azure najdete v tématech [Odchozí připojení](../load-balancer/load-balancer-outbound-connections.md), [Veřejné IP adresy](virtual-network-public-ip-address.md) a [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Pokud používáte pouze interní [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md), odchozí připojení nebudou dostupná, dokud neurčíte, jak mají [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) pracovat s veřejnou IP adresou nebo veřejným Load Balancerem na úrovni instance.

### <a name="communicate-between-azure-resources"></a>Komunikace mezi prostředky Azure

Prostředky Azure mezi sebou zabezpečeně komunikují jedním z následujících způsobů:

- **Prostřednictvím virtuální sítě:** Do virtuální sítě můžete nasadit virtuální počítače a několik dalších typů prostředků Azure, jako jsou služby Azure App Service Environment, Azure Kubernetes Service (AKS) a Azure Virtual Machine Scale Sets. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](virtual-network-for-azure-services.md).
- **Prostřednictvím koncového bodu služby virtuální sítě**: svůj privátní adresní prostor virtuální sítě a identitu vaší virtuální sítě do prostředků služby Azure, jako jsou Azure Storage účty a Azure SQL Database, přes přímé připojení. Koncové body služeb umožňují svázat vaše důležité prostředky služeb Azure pouze s virtuální sítí. Další informace najdete v tématu [Přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md).
- **Prostřednictvím partnerského vztahu** virtuálních sítí: můžete vzájemně propojit virtuální sítě a povolit komunikaci prostředků v obou virtuálních sítích pomocí partnerského vztahu virtuálních sítí. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace najdete v tématu [Partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Komunikace s místními prostředky

Své místní počítače a sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

- **Virtuální privátní síť (VPN) typu Point-to-Site:** Vytváří se mezi virtuální sítí a jedním počítačem ve vaší síti. Každý počítač, který chcete navázat připojení k virtuální síti, musí toto připojení nakonfigurovat. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Komunikace mezi počítačem a virtuální sítí se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Point-to-Site](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **Síť VPN typu Site-to-Site:** Vytváří se mezi místním zařízením VPN a službou Azure VPN Gateway nasazenou do virtuální sítě. Tento typ připojení povoluje přístup k virtuální síti všem místním prostředkům, které autorizujete. Komunikace mezi místním zařízením VPN a službou Azure VPN Gateway se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Site-to-Site](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Vytváří se mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Provoz se nepřenáší přes internet. Další informace najdete v tématu popisujícím [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Filtrování provozu sítě

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupiny zabezpečení sítě:** Skupiny zabezpečení sítě a skupiny zabezpečení aplikací můžou obsahovat několik příchozích a odchozích pravidel zabezpečení, která umožňují filtrovat provoz do a z prostředků podle zdrojové a cílové IP adresy, portu a protokolu. Další informace najdete v tématu [skupiny zabezpečení sítě](security-overview.md#network-security-groups) nebo [skupiny zabezpečení aplikací](security-overview.md#application-security-groups).
- **Síťová virtuální zařízení:** Síťové virtuální zařízení je virtuální počítač, který provádí určitou síťovou funkci, jako je například brána firewall, optimalizace sítě WAN nebo jiná síťová funkce. Úplný seznam dostupných síťových virtuálních zařízení, která můžete nasadit do virtuální sítě, najdete na webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Směrování provozu sítě

Azure ve výchozím nastavení směruje provoz mezi podsítěmi, propojenými virtuálními sítěmi, místními sítěmi a internetem. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Směrovací tabulky:** Pro jednotlivé podsítě můžete vytvářet vlastní směrovací tabulky s trasami, které řídí cíl směrování provozu. Další informace o [směrovacích tabulkách](virtual-networks-udr-overview.md#user-defined).
- **Trasy protokolu Border Gateway Protocol (BGP):** Pokud připojíte virtuální síť k místní síti pomocí připojení Azure VPN Gateway nebo ExpressRoute, můžete do svých virtuálních sítí rozšířit místní trasy BGP. Další informace o použití BGP se službou [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služeb Azure do služby Azure Virtual Network umožňuje privátní přístup ke službě z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.
Služby Azure ve vaší virtuální síti můžete integrovat pomocí následujících možností:
- Nasazení [vyhrazených instancí služby](virtual-network-for-azure-services.md) do virtuální sítě. Služby pak mohou být soukromě přistupované v rámci virtuální sítě a z místních sítí.
- Použití [privátního odkazu](../private-link/private-link-overview.md) pro přístup do soukromé konkrétní instance služby z vaší virtuální sítě a z místních sítí.
- Ke službě můžete také přistupovat pomocí veřejných koncových bodů rozšířením virtuální sítě na službu prostřednictvím [koncových bodů služby](virtual-network-service-endpoints-overview.md). Koncové body služby umožňují zabezpečit prostředky služby ve virtuální síti.
 

## <a name="azure-vnet-limits"></a>Omezení virtuální sítě Azure

Existují určitá omezení počtu prostředků Azure, které můžete nasadit. Většina omezení sítě Azure je na maximálních hodnotách. Můžete ale [zvýšit určitá omezení sítě](../azure-portal/supportability/networking-quota-requests.md) , jak je uvedeno na [stránce omezení virtuální](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)sítě. 

## <a name="pricing"></a>Ceny

Za použití virtuální sítě Azure se neúčtují žádné poplatky. to je zdarma. Pro prostředky, jako jsou Virtual Machines (virtuální počítače) a další produkty, platí standardní poplatky. Další informace najdete v tématu [ceny virtuální](https://azure.microsoft.com/pricing/details/virtual-network/) sítě a [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)Azure.

## <a name="next-steps"></a>Další kroky
 - Přečtěte si o [konceptech a osvědčených postupech pro Azure Virtual Network](concepts-and-best-practices.md) .
 - Pokud chcete začít používat virtuální síť, nějakou vytvořte, nasaďte do ní několik virtuálních počítačů a navažte komunikaci mezi těmito virtuálními počítači. Informace o postupu najdete v rychlém startu [Vytvoření virtuální sítě](quick-create-portal.md).
