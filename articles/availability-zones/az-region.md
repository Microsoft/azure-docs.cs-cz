---
title: Služby Azure, které podporují zóny dostupnosti
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 4c592c2d67df1e792200cc36449a6268807bbb56
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816255"
---
# <a name="azure-services-that-support-availability-zones"></a>Služby Azure, které podporují zóny dostupnosti

Microsoft Azure globální infrastruktura je navržená a vytvořená na všech úrovních k zajištění nejvyšší úrovně redundance a odolnosti vůči zákazníkům. Infrastruktura Azure se skládá z geografických oblastí, oblastí a Zóny dostupnosti, které omezují poloměr vysokého poloměru selhání, a proto omezují potenciální dopad na aplikace a data zákazníků. Zóny dostupnosti Azure konstrukce byla vyvinuta tak, aby poskytovala softwarové a síťové řešení pro ochranu před chybami datového centra a poskytovala zákazníkům větší vysokou dostupnost (HA).

Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každá zóna se skládá z jednoho nebo více datových center s nezávisle napájením, chlazením a sítí. Fyzické oddělení Zóny dostupnosti v rámci oblasti omezuje dopad na aplikace a data před selháním zóny, jako je například zahlcení velkých objemů, velké množství a přenásobení a další události, které by mohly přerušit přístup k webu, bezpečný průchod, rozšířené nástroje v době provozu a dostupnost prostředků. Zóny dostupnosti a jejich přidružená datová centra jsou navržena tak, aby v případě ohrožení jedné zóny byly služby, kapacity a dostupnost podporovány jinými Zóny dostupnosti v oblasti.

Všechny služby správy Azure jsou navržené tak, aby byly odolné proti selháním na úrovni jednotlivých oblastí. V případě selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání v porovnání s selháním celé oblasti. Azure se může zotavit z neúspěšného selhání služeb správy v rámci oblasti. Azure provádí kritickou údržbu jedné zóny v čase v rámci určité oblasti, aby nedocházelo k chybám, které mají vliv na prostředky zákazníka nasazené napříč Zóny dostupnosti v rámci oblasti.


![koncepční zobrazení oblasti Azure se 3 zónami](./media/az-region/azure-region-availability-zones.png)


Služby Azure, které podporují Zóny dostupnosti spadají do tří kategorií: **oblast**, **redundantní zóna** a **neregionální** služby. Úlohy zákazníků je možné kategorizovat tak, aby využívaly některé z těchto scénářů architektury ke splnění výkonu a odolnosti aplikací.

- Hraniční **služby** – prostředek se dá nasadit na konkrétní, samostatně vybranou zónu dostupnosti, abyste dosáhli přísnějších požadavků na latenci nebo výkon.  Odolnost proti chybám je navržená replikací aplikací a dat do jedné nebo více zón v rámci oblasti.  Prostředky je možné připnout ke konkrétní zóně. Například virtuální počítače, spravované disky nebo standardní IP adresy je možné připnout ke konkrétní zóně, což umožňuje zvýšit odolnost díky tomu, že se jedna nebo víc instancí prostředků rozloží v různých zónách.

- **Služba – redundantní služby** – prostředky se replikují nebo distribuují mezi zónami automaticky. Například ZRS replikuje data ve třech zónách, aby selhání zóny neovlivnilo HA dat.  

- **Neregionální služby** – služby jsou vždycky dostupné z geografických oblastí Azure a jsou odolné vůči výpadkům v rámci zóny a také výpadkům v rámci oblastí. 


Pro zajištění komplexní provozní kontinuity v Azure Sestavte architekturu aplikace pomocí kombinace Zóny dostupnosti s páry oblastí Azure. Můžete synchronně replikovat aplikace a data pomocí Zóny dostupnosti v oblasti Azure pro zajištění vysoké dostupnosti a asynchronní replikace napříč oblastmi Azure pro ochranu proti havárii. Pokud se chcete dozvědět víc, přečtěte si téma [vytváření řešení pro zajištění vysoké dostupnosti pomocí zóny dostupnosti](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Služby Azure podporující Zóny dostupnosti

 - Virtuální počítače starší generace nejsou uvedeny. Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).
 - Jak je uvedeno v [oblastech a zóny dostupnosti v Azure](az-overview.md), některé služby jsou jiné než regionální. Tyto služby nemají závislost na konkrétní oblasti Azure, protože jsou odolné vůči výpadkům v rámci zóny a také k výpadkům v rámci oblastí.  Seznam neoblastních služeb najdete v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Oblasti Azure s Zóny dostupnosti
 

| Amerika           | Evropa               | Afrika              | Asie a Tichomoří   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazílie – jih       | Francie – střed       | Jihoafrická Jižní Afrika – sever * | Austrálie – východ |
| Střední Kanada     | Německo – středozápad |                     | Střed Indie * |
| USA – střed         | Severní Evropa         |                     | Japonsko – východ     |
| East US            | Spojené království – jih             |                     | Korea – střed * |
| USA – východ 2          | West Europe          |                     | Southeast Asia |
| Střed USA – jih |                      |                     |                |
| USA (Gov) – Virginia    |                      |                     |                |
| Západní USA 2        |                      |                     |                |
| Západní USA 3 *       |                      |                     |                |

\* Další informace o podpoře Zóny dostupnosti a dostupných služeb v těchto oblastech vám poskytne zástupce Microsoftu pro prodej nebo zákazníky. Informace o nadcházejících oblastech, které budou podporovat Zóny dostupnosti, najdete v tématu geografické oblasti [Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Služby Azure podporující Zóny dostupnosti

- Virtuální počítače starší generace nejsou uvedeny níže. Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).

- Některé služby jsou neregionální, další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md) . Tyto služby nemají závislost na konkrétní oblasti Azure, protože jsou odolné vůči výpadkům v rámci zóny a výpadkům v rámci oblastí.  Seznam neoblastních služeb najdete v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Odolné služby zóny 

: globe_with_meridians: neregionální služby jsou vždycky dostupné z geografických oblastí Azure a jsou odolné vůči výpadkům v rámci zóny a také výpadkům v rámci oblastí.

: large_blue_diamond: odolný vůči výpadkům v rámci zóny 

**Základní služby**

|     Produkty                                                    | Odolnost             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (v2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | : large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | : large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | : large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | : large_blue_diamond:  |
|     [Trasa Azure Express](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | : large_blue_diamond:  |
|     [Veřejná IP adresa Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | : large_blue_diamond:  |
|     Azure SQL Database ([pro obecné účely vrstva](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))                 | : large_blue_diamond:  |
|     Azure SQL Database ([úroveň Premium & pro důležité obchodní informace](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | : large_blue_diamond:  |
|     [Disk Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | : large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | : large_blue_diamond:  |
|     [Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | : large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | : large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | : large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | : large_blue_diamond:  |
|     [Účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | : large_blue_diamond:  |
|     Storage:   [horká a studená BLOB Storage vrstva](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | : large_blue_diamond:  |
|     Úložiště:   [Managed disks](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | : large_blue_diamond:  |
|     [Virtual Machines škálování sad](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | : large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | : large_blue_diamond:  |
|     Virtual Machines: [Av2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Virtual Machines: [BS-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [DSv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Virtual Machines: [DSv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | : large_blue_diamond:  |
|     Virtual Machines: [Dv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Virtual Machines: [Dv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Virtual Machines: [ESv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | : large_blue_diamond:  |
|     Virtual Machines: [Ev3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Virtual Machines: [Řada F-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Virtual Machines: [řada FS](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | : large_blue_diamond:  |
|     [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | : large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | : large_blue_diamond:  |


**Běžné služby**


|     Produkty                                                    | Odolnost             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Prostředí App Service](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | : large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | : large_blue_diamond:  |
|     [Azure API Management](https://docs.microsoft.com/azure/api-management/zone-redundancy)                      | : large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | : large_blue_diamond:  |
|     [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | : large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | : large_blue_diamond:  |
|     Cognitive Services Azure: [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | : large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | : large_blue_diamond:  |
|     Azure Database for MySQL – [flexibilní Server](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | : large_blue_diamond:  |
|     Azure Database for PostgreSQL – [flexibilní Server](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | : large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | : large_blue_diamond:  |
|     [Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | : large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | : large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | : large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | : large_blue_diamond:  |
|     [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | : large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | : large_blue_diamond:  |
|     Azure SQL: [virtuální počítač](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | : large_blue_diamond:  |
|     [Azure Web Application Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | : large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | : large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | : large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | : large_blue_diamond:  |
|     Network Watcher: [Analýza provozu](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | : large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | : large_blue_diamond:  |
|     [Blob Storage úrovně Premium](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | : large_blue_diamond:  |
|     Storage: [soubory Azure Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | : large_blue_diamond:  |
|     Virtual Machines: [vyhrazený hostitel Azure](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | : large_blue_diamond:  |
|     Virtual Machines: [Ddsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Virtual Machines: [Ddv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [Dsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [dv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Virtual Machines: [Edsv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | : large_blue_diamond:  |
|     Virtual Machines: [Edv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [Esv4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [Ev4-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | : large_blue_diamond:  |
|     Virtual Machines: [Fsv2-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | : large_blue_diamond:  |
|     Virtual Machines: [řada M-Series](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | : large_blue_diamond:  |
|     [Virtuální síť WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | : large_blue_diamond:  |
|     Virtuální síť WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | : large_blue_diamond:  |
|     Virtuální síť WAN: připojení [typu Point-to-Site VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | : large_blue_diamond:  |
|     Virtuální síť WAN: [VPN Gateway typu Site-to-site](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | : large_blue_diamond:  |


**Specializované služby**

|     Produkty                                                    | Odolnost             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | : large_blue_diamond:  |
|     Cognitive Services: detektor anomálií                        | : large_blue_diamond:  |
|     Cognitive Services: Nástroj pro rozpoznávání formulářů                         | : large_blue_diamond:  |
|     Úložiště: Ultra disk                                         | : large_blue_diamond:  |


**Bez regionu**

|     Produkty                                                    | Odolnost             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | : globe_with_meridians: |
|     Azure Active Directory                                    | : globe_with_meridians: |
|     Azure Advanced Threat Protection                            | : globe_with_meridians: |
|     Azure Advisor                                               | : globe_with_meridians: |
|     Azure Blueprints                                            | : globe_with_meridians: |
|     Azure Bot Services                                          | : globe_with_meridians: |
|     Azure Front Door                                            | : globe_with_meridians: |
|     Azure Defender pro IoT                                    | : globe_with_meridians: |
|     Azure Front Door                                            | : globe_with_meridians: |
|     Azure Information Protection                              | : globe_with_meridians: |
|     Lighthouse Azure                                          | : globe_with_meridians: |
|     Azure Managed Applications                                | : globe_with_meridians: |
|     Azure Maps                                                  | : globe_with_meridians: |
|     Diagnostika výkonu Azure                               | : globe_with_meridians: |
|     Azure Policy                                                | : globe_with_meridians: |
|     Graf prostředků Azure                                      | : globe_with_meridians: |
|     Azure Sentinel                                              | : globe_with_meridians: |
|     Azure Stack                                                 | : globe_with_meridians: |
|     Azure Stack Edge                                          | : globe_with_meridians: |
|     Cloud Shell                                                 | : globe_with_meridians: |
|     Content Delivery Network                                    | : globe_with_meridians: |
|     Správa nákladů                                             | : globe_with_meridians: |
|     Customer Lockbox pro Microsoft Azure                      | : globe_with_meridians: |
|     Intune                                                      | : globe_with_meridians: |
|     Služba partnerského vztahu Microsoft Azure                           | : globe_with_meridians: |
|     portál Microsoft Azure                                    | : globe_with_meridians: |
|     Microsoft Cloud App Security                                | : globe_with_meridians: |
|     Microsoft Graph                                             | : globe_with_meridians: |
|     Security Center                                           | : globe_with_meridians: |
|     Traffic Manager                                           | : globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Ceny pro virtuální počítače v Zóny dostupnosti

Zóny dostupnosti Azure jsou k dispozici v rámci vašeho předplatného Azure. Další informace najdete na [stránce s cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Začínáme s Zóny dostupnosti

- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování virtuálních počítačů v zóně redundantní](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer se zónou redundantního front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy.md)
- [SQL Database úroveň pro obecné účely](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidat redundantní oblast zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme Azure cache pro Zóny dostupnosti Redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Vytvoření instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti](../aks/availability-zones.md)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Oblasti a zóny dostupnosti v Azure](az-overview.md)
