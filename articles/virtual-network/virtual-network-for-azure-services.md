---
title: Virtuální síť pro služby Azure
titlesuffix: Azure Virtual Network
description: Naučte se nasazovat vyhrazené služby Azure do virtuální sítě a získat informace o možnostech, které tato nasazení poskytují.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 79b6fa1043b1bb8add6b6beb5fd38312ee12ea75
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210270"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Nasazení vyhrazených služeb Azure do virtuálních sítí

Když nasadíte vyhrazené služby Azure ve [virtuální síti](virtual-networks-overview.md), můžete komunikovat s prostředky služby soukromě prostřednictvím privátních IP adres.

![Služby nasazené ve virtuální síti](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení služeb v rámci virtuální sítě poskytuje následující možnosti:

- Prostředky ve virtuální síti můžou vzájemně komunikovat soukromě prostřednictvím privátních IP adres. Například přímý přenos dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres přes [síť VPN typu Site-to-Site (VPN Gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) nebo [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- U virtuálních sítí je možné navázat [partnerský vztah](virtual-network-peering-overview.md) , aby bylo možné mezi sebou vzájemně komunikovat prostředky pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou většinou plně spravovány službou Azure. To zahrnuje monitorování stavu prostředků a škálování s využitím zátěže.
- Instance služby jsou nasazeny do podsítě ve virtuální síti. Příchozí a odchozí síťový přístup pro podsíť musí být otevřený prostřednictvím [skupin zabezpečení sítě](./network-security-groups-overview.md#network-security-groups)podle pokynů poskytovaných službou.
- Některé služby také ukládají omezení pro podsíť, ve které jsou nasazená, a omezují použití zásad, trasy nebo kombinování virtuálních počítačů a prostředků služeb ve stejné podsíti. U každé služby se můžete podívat na konkrétní omezení, která se v průběhu času můžou měnit. Příklady takových služeb jsou Azure NetApp Files, vyhrazené HSM, Azure Container Instances App Service. 
- V případě potřeby můžou služby vyžadovat [delegovanou podsíť](virtual-network-manage-subnet.md#add-a-subnet) jako explicitní identifikátor, který podsíť může hostovat určitou službu. Delegováním služeb získají explicitní oprávnění k vytváření prostředků specifických pro službu v delegované podsíti.
- Podívejte se na příklad odpovědi REST API ve [virtuální síti s delegovanou podsítí](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). K dispozici je úplný seznam služeb, které používají delegovaný model podsítě, [k dispozici](/rest/api/virtualnetwork/availabledelegations/list) prostřednictvím rozhraní API pro delegování.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Služby, které je možné nasadit do virtuální sítě

|Kategorie|Služba| Vyhrazená podsíť<sup>1</sup>
|-|-|-|
| Výpočetní prostředky | Virtuální počítače: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloudová služba](/previous-versions/azure/reference/jj156091(v=azure.100)): jenom virtuální síť (Classic)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Ne <br/> Ne <br/> Ne <br/> Ne<sup>2</sup>
| Síť | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Síťová virtuální zařízení](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| Ano <br/> Ano <br/> Ano <br/> Ano <br/> Ne
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Spravovaná instance Azure SQL](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ano <br/> Ano <br/> 
|Analýzy | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ne<sup>2</sup> <br/> Ne<sup>2</sup> <br/> 
| Identita | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Ne <br/>
| Kontejnery | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container instance (ACI)](https://www.aka.ms/acivnet)<br/>Modul [Azure Container Service](https://github.com/Azure/acs-engine) s [modulem plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) Azure Virtual Network CNI<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |Ne<sup>2</sup><br/> Ano <br/> Ne <br/> Ano
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/> Ano <br/> Ano
| Hostováno | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ano <br/> Ano <br/>
| Azure Spring Cloud | [Nasazení ve službě Azure Virtual Network (vkládání virtuální sítě)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Ano <br/>
| | |

<sup>1</sup> "vyhrazené" znamená, že v této podsíti můžou být nasazené jenom prostředky specifické pro službu a nejde je kombinovat s virtuálním počítačem/VMSSs zákazníka. <br/> 
<sup>2</sup> doporučuje se mít tyto služby ve vyhrazené podsíti, ale nejedná se o povinný požadavek, který služba ukládá.
