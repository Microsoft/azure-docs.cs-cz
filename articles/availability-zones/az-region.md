---
title: Služby Azure, které podporují Zóny dostupnosti
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723030"
---
# <a name="azure-services-that-support-availability-zones"></a>Služby Azure, které podporují Zóny dostupnosti

Zóny dostupnosti je vysoce dostupná nabídka, která chrání vaše aplikace a data před selháními datových center. Seznam stávajících a nadcházejících oblastí, které podporují Zóny dostupnosti, najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md).  

V této části jsou uvedené služby Azure, které podporují Zóny dostupnosti. 

Služby, které jsou dostupné v jednotlivých oblastech, společně s nadcházejícím plánem dostupnosti, najdete v [produktech dostupných](https://azure.microsoft.com/global-infrastructure/services/)v jednotlivých oblastech.

Všechny služby správy Azure jsou navržené tak, aby byly odolné proti selháním na úrovni jednotlivých oblastí. V případě selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání v porovnání s selháním celé oblasti. Azure se může zotavit z neúspěšného selhání služeb správy v rámci oblasti. Azure provádí kritickou údržbu jedné zóny v čase v rámci určité oblasti, aby nedocházelo k chybám, které mají vliv na prostředky zákazníka nasazené napříč Zóny dostupnosti v rámci oblasti.


![koncepční zobrazení oblasti Azure se 3 zónami](./media/az-region/azure-region-availability-zones.png)


Služby Azure, které podporují Zóny dostupnosti spadají do tří kategorií: **oblast**, **redundantní zóna** a **neregionální** služby. Úlohy zákazníků je možné kategorizovat tak, aby využívaly některé z těchto scénářů architektury ke splnění výkonu a odolnosti aplikací.

- Hraniční **služby** – prostředek se dá nasadit na konkrétní, samostatně vybranou zónu dostupnosti, abyste dosáhli přísnějších požadavků na latenci nebo výkon.  Odolnost proti chybám je navržená replikací aplikací a dat do jedné nebo více zón v rámci oblasti.  Prostředky je možné připnout ke konkrétní zóně. Například virtuální počítače, spravované disky nebo standardní IP adresy je možné připnout ke konkrétní zóně, což umožňuje zvýšit odolnost díky tomu, že se jedna nebo víc instancí prostředků rozloží v různých zónách.

- **Redundantní služby v zóně** – platforma Azure replikuje prostředky a data napříč zónami.  Microsoft spravuje doručování vysoké dostupnosti, protože Azure automaticky replikuje a distribuuje instance v rámci dané oblasti.  ZRS například replikuje data ve třech zónách tak, že selhání zóny nemá vliv na HA dat. 

- **Neregionální služby** – služby, které nemají závislost na konkrétní oblasti Azure, jsou odolné proti výpadkům v rámci zóny a také výpadkům v rámci oblastí.


Pro zajištění komplexní provozní kontinuity v Azure Sestavte architekturu aplikace pomocí kombinace Zóny dostupnosti s páry oblastí Azure. Můžete synchronně replikovat aplikace a data pomocí Zóny dostupnosti v oblasti Azure pro zajištění vysoké dostupnosti a asynchronní replikace napříč oblastmi Azure pro ochranu proti havárii. Pokud se chcete dozvědět víc, přečtěte si téma [vytváření řešení pro zajištění vysoké dostupnosti pomocí zóny dostupnosti](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Služby Azure podporující Zóny dostupnosti

- Virtuální počítače starší generace nejsou uvedeny níže. Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).

- Některé služby jsou neregionální, další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md) . Tyto služby nemají závislost na konkrétní oblasti Azure, protože jsou odolné vůči výpadkům v rámci zóny a výpadkům v rámci oblastí.  Seznam neoblastních služeb najdete v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Amerika

| **Produkty** | **USA – střed** | **East US** | **USA – východ 2** | **Západní USA 2** | **Střední Kanada** |
|--|--|--|--|--|--|
| **Výpočetní služby** |  |  |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Containers** |  |  |  |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Container Registry](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – flexibilní Server](../mysql/flexible-server/concepts-high-availability.md) | znak | znak | :heavy_check_mark: | :heavy_check_mark: | znak |
| [Azure Database for PostgreSQL – flexibilní Server](../postgresql/flexible-server/overview.md) | znak | znak | :heavy_check_mark: | :heavy_check_mark: | znak |
| [Azure SQL Database (Pro obecné účely vrstva)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | znak | : heavy_check_mark: (Preview) | : heavy_check_mark: (Preview) | : heavy_check_mark: (Preview) | znak |
| [Azure SQL Database (úrovně Premium & Pro důležité obchodní informace](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Správa a zásady správného řízení** |  |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | znak | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | znak |
| **Zabezpečení** |  |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Evropa

| **Produkty** | **Francie – střed** | **Severní Evropa** | **Spojené království – jih** | **West Europe** |
|--|--|--|--|--|
| **Výpočetní služby** |  |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – flexibilní Server](../mysql/flexible-server/concepts-high-availability.md) | znak | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL – flexibilní Server](../postgresql/flexible-server/overview.md) | znak | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Pro obecné účely vrstva)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | znak | : heavy_check_mark: (Preview) | znak | : heavy_check_mark: (Preview) |
| [Azure SQL Database (úrovně Premium & Pro důležité obchodní informace](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Správa a zásady správného řízení** |  |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | znak | :heavy_check_mark: |
| **Zabezpečení** |  |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Asie a Tichomoří



| **Produkty** | **Japan East** | **Southeast Asia** | **Austrálie – východ** |
|--|--|--|--|
| **Výpočetní služby** |  |  |  |
| [Prostředí App Service (interního nástroje)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes Service (AKS)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines škálování sad](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Úložiště Premium Files](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob Storage](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Spravované disky](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sítě** |  |  |  |
| [Application Gateway v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Trasa Azure Express](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Firewall](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Standardní IP adresa](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual Network NAT](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Virtual WAN](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN Gateway](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Databáze** |  |  |  |
| [Azure Cache for Redis](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for MySQL – flexibilní Server](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Database for PostgreSQL – flexibilní Server](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL Database (Pro obecné účely vrstva)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | znak | : heavy_check_mark: (Preview) | : heavy_check_mark: (Preview) |
| [Azure SQL Database (úrovně Premium & Pro důležité obchodní informace](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analýzy** |  |  |  |
| [Event Hubs](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integrace** |  |  |  |
| [Event Grid](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Bus](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Správa a zásady správného řízení** |  |  |  |
| [Network Watcher](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | znak | znak |
| **Zabezpečení** |  |  |  |
| [Azure Active Directory Domain Services](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Nadcházející Zóny dostupnosti 

Azure nabízí podporu Zóny dostupnosti v následujících oblastech:
- USA (Gov) – Virginia
- Jižní Afrika – sever
- Středojižní USA
- Německo – středozápad

Seznam stávajících a nadcházejících oblastí, které podporují Zóny dostupnosti, najdete [tady](https://azure.microsoft.com/global-infrastructure/geographies/).    

Pokud se chcete dozvědět víc o podpoře Zóny dostupnosti v těchto oblastech, obraťte se na prodejce Microsoftu nebo zákazníka.


## <a name="pricing-for-vms-in-availability-zones"></a>Ceny pro virtuální počítače v Zóny dostupnosti

Pro virtuální počítače nasazené v zóně dostupnosti se neúčtují žádné další náklady. Další informace najdete na stránce s [cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Začínáme s Zóny dostupnosti

- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování virtuálních počítačů v zóně redundantní](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer se zónou redundantního front-endu](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy.md)
- [SQL Database úroveň pro obecné účely](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidat redundantní oblast zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme Azure cache pro Zóny dostupnosti Redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Vytvoření instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti](../aks/availability-zones.md)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Oblasti a zóny dostupnosti v Azure](az-overview.md)
