---
title: Oblasti, které podporují Zóny dostupnosti v Azure
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/07/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 888365e04566f18e5a73454fea4c96c321f5ce52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858383"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Oblasti, které podporují Zóny dostupnosti v Azure

Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Další informace o Zóny dostupnosti najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md).

V této části jsou uvedené služby a oblasti Azure, které podporují Zóny dostupnosti.

Služby, které jsou dostupné v jednotlivých oblastech, společně s nadcházejícím plánem dostupnosti, najdete v [dostupných produktech](https://azure.microsoft.com/global-infrastructure/services/)v jednotlivých oblastech.


## <a name="americas"></a>Amerika

| **Produkty** | **USA – střed** | **East US** | **USA – východ 2** | **Západní USA 2** | **Střední Kanada** |
|--|--|--|--|--|--|
| **Výpočetní služby** |  |  |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |  |  |
| [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Zabezpečení** |  |  |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Evropa

| **Produkty** | **Francie – střed** | **Severní Evropa** | **Spojené království – jih** | **West Europe** |
|--|--|--|--|--|
| **Výpočetní služby** |  |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |  |
| [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Zabezpečení** |  |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asie a Tichomoří



| **Produkty** | **Japan East** | **Southeast Asia** | **Austrálie – východ** |
|--|--|--|--|
| **Výpočetní služby** |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |
| [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |
| [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |
| [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |
| [Event Grid](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Zabezpečení** |  |  |  |
| [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: |  |





## <a name="other"></a>Ostatní

Azure také nabízí podporu Zóny dostupnosti v následujících oblastech: • US Gov – Virginie • Jižní Afrika sever • Střed USA – jih

Pokud chcete získat další informace o podpoře Zóny dostupnosti v těchto třech oblastech, obraťte se na prodejce od Microsoftu nebo zákazníka.


## <a name="next-steps"></a>Další kroky

- [Oblasti a zóny dostupnosti v Azure](az-overview.md)
