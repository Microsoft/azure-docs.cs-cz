---
title: Virtuální síť pro služby Azure | Dokumentace Microsoftu
description: Přečtěte si o výhodách nasazování prostředků do virtuální sítě. Prostředky ve virtuálních sítích komunikovat mezi sebou a místní prostředky, provoz prochází Internetem.
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
ms.openlocfilehash: c92a986d06deb9f7de10f0682fe46804e6ebb6e7
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069872"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služeb Azure ke službě Azure virtual network umožňuje privátní přístup z instancí služby nasazené ve virtuální síti.

Integrace služeb Azure s vaší virtuální síti s těmito možnostmi:
- Nasazení přímo vyhrazenou instancí služby do virtuální sítě. Vyhrazené instance těchto služeb soukromě přístupná v rámci virtuální sítě a z místní sítě.
- Tím, že rozšíří do služby prostřednictvím koncových bodů služby virtuální sítě. Koncové body služby umožňují prostředkům jednotlivé služby být svázat s virtuální sítí.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Nasazení služby Azure do virtuální sítě

Může komunikovat s prostředky většinu služeb Azure přes Internet prostřednictvím veřejné IP adresy. Při nasazení služby v Azure [virtuální sítě](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě, prostřednictvím privátních IP adres.

![Službám nasazeným ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služby v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky v rámci virtuální sítě můžete mezi sebou komunikovat soukromě mezi, prostřednictvím privátních IP adres. Příklad přímo přenosu dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres [VPN typu Site-to-Site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální sítě můžou být [v partnerském vztahu](virtual-network-peering-overview.md) zajistěte, aby prostředky ve virtuálních sítích komunikovat mezi sebou, pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou plně spravované služby Azure, můžete monitorovat stav instancí a zadejte požadované škálování na základě zatížení.
- Instance služby, které jsou nasazené do vyhrazenou podsíť ve virtuální síti. Příchozí a odchozí síťový přístup musí být otevřeny prostřednictvím [skupiny zabezpečení sítě](security-overview.md#network-security-groups) pro podsíť, podle pokynů poskytovaných služeb.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

Každá služba nasadit přímo do virtuální sítě má specifické požadavky na směrování a typy dat, která musí být povoleno do proměnné a z podsítě. Další informace naleznete v tématu: 
 
- Virtual machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Služba Application Gateway (interní)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Modul Azure Container Service](https://github.com/Azure/acs-engine) pomocí virtuální sítě Azure CNI [modulu plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Služba Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Spravovaná Instance Azure SQL Database](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Cloudové služby](https://msdn.microsoft.com/library/azure/jj156091): virtuální síť (klasická) pouze

Můžete nasadit [interní nástroji Azure load balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) načíst vyvážit mnoho prostředků v předchozím seznamu. V některých případech může služba automaticky vytvoří a nasadí nástroj pro vyrovnávání zatížení při vytváření prostředku.

## <a name="service-endpoints-for-azure-services"></a>Koncové body služby pro služby Azure

Některé služby Azure se nedá nasadit virtuální sítě. Pokud zvolíte, tím, že koncový bod služby virtuální sítě můžete omezit přístup k některé z prostředků služby k podsítím pouze konkrétní virtuální sítě. Další informace o [koncové body služeb virtuální sítě](virtual-network-service-endpoints-overview.md)a služby, které je možné povolit koncové body pro.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integrace služby Virtual network napříč několika službami Azure

Nasazení služby Azure do podsítě ve virtuální síti a zabezpečené důležité prostředky služeb k této podsíti. Například můžete nasadit HDInsight do vaší virtuální sítě a zabezpečení účtu úložiště do podsítě HDInsight.





