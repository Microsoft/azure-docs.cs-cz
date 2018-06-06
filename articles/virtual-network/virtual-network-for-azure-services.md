---
title: Virtuální sítě pro služby Azure | Microsoft Docs
description: Informace o výhodách nasazení prostředků do virtuální sítě. Prostředky ve virtuálních sítích můžete vzájemně komunikovat a místních prostředků, bez provoz procházející Internetu.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: eeb5fd765a6cad10be5706162566605feaa8af4c
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802557"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služby Azure pro virtuální síť Azure umožňuje privátní přístup z instancí služby nasazený ve virtuální síti.

Azure services lze integrovat s vaší virtuální sítě pomocí následujících možností:
- Přímo nasazení vyhrazenou instancí služby do virtuální sítě. Vyhrazenou instancí těchto služeb soukromě přístupná v rámci virtuální sítě a z místní sítě.
- Tím, že rozšíří do virtuální sítě služby, pomocí koncových bodů služby. Koncové body služby povolit prostředky jednotlivé služby zabezpečený do virtuální sítě.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Nasazení služby Azure do virtuální sítě

Může komunikovat s prostředky nejvíce Azure přes Internet prostřednictvím veřejné IP adresy. Při nasazení služby Azure v [virtuální sítě](virtual-networks-overview.md), vám může komunikovat s prostředky služby soukromě, prostřednictvím privátních IP adres.

![Službám nasazeným ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služeb v rámci virtuální sítě poskytují následující možnosti:

- Prostředky v rámci virtuální sítě může komunikovat s navzájem soukromě, prostřednictvím privátních IP adres. Příklad přímo přenosu dat mezi HDInsight a SQL Server běžící na virtuálním počítači, ve virtuální síti.
- Místní prostředky přístup k prostředkům ve virtuální síti pomocí privátních IP adres [VPN Site-to-Site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální sítě můžou být [peered](virtual-network-peering-overview.md) , aby prostředky ve virtuálních sítích ke komunikaci mezi sebou, použití privátních IP adres.
- Instance služby ve virtuální síti je plně spravovaná pomocí služby Azure a monitorovat stav instancí, zadejte požadované škálování, podle zatížení.
- Instance služby, které jsou nasazeny do vyhrazené podsíť ve virtuální síti. Příchozí a odchozí síťový přístup musí být otevřeno prostřednictvím [skupin zabezpečení sítě](security-overview.md#network-security-groups) pro podsíť, podle pokynů poskytovaných služeb.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které lze nasadit do virtuální sítě

Každá služba přímo nasazený do virtuální sítě má specifické požadavky na směrování a typů dat do a z podsítě, musí být povoleno. Další informace naleznete v tématu: 
 
- Virtuální počítače: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Aplikační brána (interní)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Služba Azure Kubernetes (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Stroj služby Azure kontejneru](https://github.com/Azure/acs-engine) s CNI aplikace pro virtuální sítě Azure [modulu plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): virtuální sítě (klasické) pouze
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Cloudové služby](https://msdn.microsoft.com/library/azure/jj156091): virtuální sítě (klasické) pouze

Můžete nasadit [pro vyrovnávání zatížení pro vnitřní Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) načíst vyvážit mnoho prostředků v předchozím seznamu. V některých případech službu automaticky vytvoří a nasadí nástroj pro vyrovnávání zatížení při vytváření prostředku.

## <a name="service-endpoints-for-azure-services"></a>Koncové body služby pro služby Azure

Některé Azure služby nelze nasadit ve virtuálních sítích. Pokud si zvolíte, tím, že umožňuje koncového bodu služby virtuální sítě můžete omezit přístup k některým prostředkům služby k podsítím pouze konkrétní virtuální sítě. Další informace o [koncové body služby virtuální sítě](virtual-network-service-endpoints-overview.md), služby a služby, které je možné povolit koncové body pro.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integrace virtuální sítě napříč více službami Azure

Služba Azure můžete nasadit do podsítě virtuální sítě a prostředky zabezpečené kritické služby k této podsíti. Například můžete nasadit HDInsight do vaší virtuální sítě a zabezpečení účtu úložiště do podsítě HDInsight.





