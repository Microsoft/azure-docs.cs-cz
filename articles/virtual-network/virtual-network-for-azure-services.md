---
title: Virtuální síť pro služby Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o výhodách nasazování prostředků do virtuální sítě. Prostředky ve virtuálních sítích komunikovat mezi sebou a místní prostředky, provoz prochází Internetem.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: 357122abba483a96dd9f2d602a793ee06e80b245
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785554"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služeb Azure ke službě Azure virtual network umožňuje soukromý přístup ke službě z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.
Integrace služeb Azure ve vaší virtuální síti s následujícími možnostmi:
- Nasazení vyhrazených instancích služby do virtuální sítě. Služby lze poté soukromě přistupovat v rámci virtuální sítě a z místní sítě.
- Rozšíření virtuální sítě do služby prostřednictvím koncových bodů služby. Koncové body služby umožňují prostředkům jednotlivé služby být svázat s virtuální sítí.

Integrovat několik služeb Azure ke službě virtual network, může kombinovat nejméně jeden z výše uvedených vzory. Například můžete nasadit HDInsight do vaší virtuální sítě a zabezpečení účtu úložiště do podsítě HDInsight prostřednictvím koncových bodů služby.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Nasazení služby Azure do virtuální sítě

Při nasazení ve vyhrazené služby Azure [virtuální sítě](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě, prostřednictvím privátních IP adres.

![Službám nasazeným ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služby v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky v rámci virtuální sítě můžete mezi sebou komunikovat soukromě mezi, prostřednictvím privátních IP adres. Příklad přímo přenosu dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres [VPN typu Site-to-Site (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální sítě můžou být [v partnerském vztahu](virtual-network-peering-overview.md) zajistěte, aby prostředky ve virtuálních sítích komunikovat mezi sebou, pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou obvykle plně spravované služby Azure. To zahrnuje monitorování stavu prostředků a škálování u zatížení.
- Instance služby, které se nasazují do podsítě ve virtuální síti. Příchozí a odchozí síťový přístup pro podsíť musí být otevřeny prostřednictvím [skupiny zabezpečení sítě](security-overview.md#network-security-groups), podle pokynů poskytovaných službou.
- Některé služby také omezení na podsíť, ve které se nasadí, omezení uplatnění zásad, trasy nebo kombinování virtuálních počítačů a prostředky služeb ve stejné podsíti. Obraťte se na každou službu na konkrétní omezení, jak můžou časem změnit. Příklady těchto služeb se soubory Azure NetApp, vyhrazené HSM, Azure Container Instances, App Service. 
- Volitelně může vyžadovat služby [delegovaný podsítě](virtual-network-manage-subnet.md#add-a-subnet) jako explicitní identifikátor, že podsíť může hostovat konkrétní službu. Delegování, služby mít explicitní oprávnění k vytváření prostředků specifické pro služby v podsíti delegovaný.
- Viz příklad odpověď rozhraní REST API na [virtuální síť s podsítí delegované](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Úplný seznam služeb, které používají model delegované podsítě můžete získat přes [dostupné delegace](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) rozhraní API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

|Kategorie|Služba| Dedicated¹ podsítě
|-|-|-|
| Compute | Virtuální počítače: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloudová služba](https://msdn.microsoft.com/library/azure/jj156091): Virtuální síť (klasická) pouze<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Ne <br/> Ne <br/> Ne <br/> No²
| Síť | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Brány Firewall na Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Síťová virtuální zařízení](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ano <br/> Ano <br/> Ano <br/> Ne
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Spravovaná Instance Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ano <br/> Ano <br/> 
|Analýzy | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identita | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ne <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Modul Azure Container Service](https://github.com/Azure/acs-engine) s Azure Virtual Network CNI [modulu plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Ano <br/><br/> Ne
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/> Ano
| Hostované | [Azure vyhrazených HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Soubory Azure NetApp](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/>
| | |

¹ "Dedicated" znamená, že pouze konkrétní prostředky služby je možné nasadit v této podsíti a nelze kombinovat s zákazníka VM/VMSSs <br/> ² doporučené, ale ne závazný požadavek uložené službou.


## <a name="service-endpoints-for-azure-services"></a>Koncové body služby pro služby Azure

Některé služby Azure se nedá nasadit virtuální sítě. Pokud zvolíte, tím, že koncový bod služby virtuální sítě můžete omezit přístup k některé z prostředků služby k podsítím pouze konkrétní virtuální sítě.  Další informace o [koncové body služeb virtuální sítě](virtual-network-service-endpoints-overview.md)a služby, které je možné povolit koncové body pro.
