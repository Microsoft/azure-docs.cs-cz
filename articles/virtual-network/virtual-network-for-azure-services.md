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
ms.openlocfilehash: 163a8f318608ba980d98209277371fb4d148c4ff
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902769"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služeb Azure ke službě Azure virtual network umožňuje soukromý přístup ke službě z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.
Můžete integrovat služby Azure ve vaší virtuální síti s následujícími možnostmi: přímém nasazování vyhrazenou instancí služby do virtuální sítě. Služby lze poté soukromě přistupovat v rámci virtuální sítě a z místní sítě.
Tím, že rozšíří do služby prostřednictvím koncových bodů služby virtuální sítě. Koncové body služby umožňují prostředkům jednotlivé služby být svázat s virtuální sítí.

Integrovat několik služeb Azure ke službě virtual network, může kombinovat nejméně jeden z výše uvedených vzory. Například můžete nasadit HDInsight do vaší virtuální sítě a zabezpečení účtu úložiště do podsítě HDInsight prostřednictvím koncových bodů služby.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Nasazení služby Azure do virtuální sítě

Při nasazení ve vyhrazené služby Azure [virtuální sítě](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě, prostřednictvím privátních IP adres.

![Službám nasazeným ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služby v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky v rámci virtuální sítě můžete mezi sebou komunikovat soukromě mezi, prostřednictvím privátních IP adres. Příklad přímo přenosu dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres [VPN typu Site-to-Site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální sítě můžou být [v partnerském vztahu](virtual-network-peering-overview.md) zajistěte, aby prostředky ve virtuálních sítích komunikovat mezi sebou, pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou plně spravované služby Azure, můžete monitorovat stav instancí a zadejte požadované škálování na základě zatížení.
- Instance služby, které se nasazují do podsítě ve virtuální síti. Příchozí a odchozí síťový přístup musí být otevřeny prostřednictvím [skupiny zabezpečení sítě](security-overview.md#network-security-groups) pro podsíť, podle pokynů poskytovaných služeb.
- Volitelně může vyžadovat služby [delegovaný podsítě](virtual-network-manage-subnet.md#add-a-subnet) jako explicitní identifikátor, že podsíť může hostovat konkrétní službu. Podsíť delegování dává explicitní oprávnění ke službě vytvářet prostředky specifické pro služby v podsíti.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

|Kategorie|Služba|
|-|-|
| Compute | Virtual machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloudová služba](https://msdn.microsoft.com/library/azure/jj156091): virtuální síť (klasická) pouze<br/> [Služba Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| Síť | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Brány Firewall na Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Spotřebiče virtuální sítě](/windowsserverdocs/WindowsServerDocs/networking/sdn/manage/Use-Network-Virtual-Appliances-on-a-VN.md) 
|Data|[RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Spravovaná Instance Azure SQL Database](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Analýzy | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Identita | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Containers | [Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instance kontejneru Azure (ACI)](http://www.aka.ms/acivnet)<br/>[Modul Azure Container Service](https://github.com/Azure/acs-engine) s Azure Virtual Network CNI [modulu plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)||
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>
<br/>


## <a name="service-endpoints-for-azure-services"></a>Koncové body služby pro služby Azure

Některé služby Azure se nedá nasadit virtuální sítě. Pokud zvolíte, tím, že koncový bod služby virtuální sítě můžete omezit přístup k některé z prostředků služby k podsítím pouze konkrétní virtuální sítě.  Další informace o [koncové body služeb virtuální sítě](virtual-network-service-endpoints-overview.md)a služby, které je možné povolit koncové body pro.
