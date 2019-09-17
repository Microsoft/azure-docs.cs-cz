---
title: Omezení a kvóty předplatného Azure
description: Poskytuje seznam běžných limitů, kvót a omezení předplatného a služeb Azure. Tento článek obsahuje informace o tom, jak zvýšit limity spolu s maximálními hodnotami.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 0f036462eb9b37b4ec783bcf8957d307cc55f7ac
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018517"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity, kvóty a omezení předplatného a služeb Azure
Tento dokument obsahuje seznam některých nejběžnějších omezení Microsoft Azure, která se také někdy označují jako kvóty. Tento dokument v tuto chvíli nepokrývá všechny služby Azure. V průběhu času se seznam rozbalí a aktualizuje, aby pokryl víc služeb.

Další informace o cenách Azure najdete v tématu [Přehled cen Azure](https://azure.microsoft.com/pricing/). V takovém případě můžete náklady odhadnout pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/). Můžete také přejít na stránku s podrobnostmi o cenách pro konkrétní službu, například na [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tipy, které vám pomůžou se správou nákladů, najdete v tématu [Ochrana před neočekávanými náklady pomocí fakturace a správy nákladů Azure](billing/billing-getting-started.md).

> [!NOTE]
> Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](azure-resource-manager/resource-manager-quota-errors.md)zdarma. Mezní hodnoty se nedají vyhodnotit nad maximální hodnotou limitu uvedenou v následujících tabulkách. Pokud není sloupec maximální limit, prostředek nemá nastavitelný limit.
>
> [Bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p) neplatí pro zvýšení limitu nebo kvóty. Pokud máte [bezplatnou zkušební verzi předplatného](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) předplatného. Další informace najdete v tématu [upgrade předplatného bezplatné zkušební verze Azure na předplatné](billing/billing-upgrade-azure-subscription.md) s průběžnými platbami a na [Nejčastější dotazy k bezplatné zkušební verzi předplatného](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Omezení a Azure Resource Manager
Je teď možné zkombinovat několik prostředků Azure do jedné skupiny prostředků Azure. Když použijete skupiny prostředků, omezení, které byly globální, se spravují na regionální úrovni s Azure Resource Manager. Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

V následujícím seznamu omezení zobrazuje nová tabulka rozdíly v omezeních při použití Azure Resource Manager. Například tabulka **omezení předplatného** a **omezení předplatného – Azure Resource Manager** tabulka. Pokud se limit vztahuje na oba scénáře, je zobrazen pouze v první tabulce. Pokud není uvedeno jinak, omezení jsou globální v rámci všech oblastí.

> [!NOTE]
> Kvóty pro prostředky ve skupinách prostředků Azure jsou pro jednotlivé oblasti dostupné pro vaše předplatné, nikoli za předplatné, jako jsou kvóty pro správu služeb. Použijte jako příklad kvóty vCPU. Pokud chcete požádat o zvýšení kvóty s podporou pro vCPU, musíte se rozhodnout, kolik vCPU chcete použít pro tyto oblasti. Pak vytvoříte konkrétní požadavek na vCPU kvóty skupiny prostředků Azure pro příslušné množství a oblasti. Pokud potřebujete použít 30 vCPU v Západní Evropa ke spuštění vaší aplikace, konkrétně si vyžádáte 30 vCPU v Západní Evropa. Vaše kvóta vCPU se nezvyšuje v žádné jiné oblasti – pouze Západní Evropa má kvótu 30 vCPU.
> <!-- -->
> V důsledku toho se rozhodněte, jaké kvóty skupin prostředků Azure musí být pro vaše úlohy v libovolné oblasti. Pak vyžádejte tuto částku v každé oblasti, do které chcete nasadit. Nápovědu k určení vašich současných kvót pro konkrétní oblasti najdete v tématu [řešení potíží s nasazením](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Omezení specifická pro službu
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Služba Azure Machine Learning](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Služba signalizace Azure](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Služba front-dveří](#azure-front-door-service-limits)
* [Správce identit](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [Služba IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Sítě](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Network Watcher](#network-watcher-limits)
  * [Veřejná IP adresa](#publicip-address)
  * [Privátní odkaz](#private-link-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Skupina prostředků](#resource-group-limits)
* [Řízení přístupu na základě rolí](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple systém](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Předplatné](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Škálovací sady virtuálních počítačů](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Omezení předplatného
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Omezení předplatného – Azure Service Management (model nasazení Classic)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Omezení předplatného – Azure Resource Manager
Následující omezení platí při použití Azure Resource Manager a skupin prostředků Azure. Omezení, která nebyla změněna pomocí Azure Resource Manager nejsou uvedena. Tato omezení najdete v předchozí tabulce.

Informace o Správce prostředků omezení čtení a zápisu rozhraní API najdete v tématu [omezování správce prostředků požadavků](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Omezení skupiny prostředků
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Omezení Virtual Machines
#### <a name="virtual-machines-limits"></a>Omezení Virtual Machines
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity Virtual Machines – Azure Resource Manager
Následující omezení platí při použití Azure Resource Manager a skupin prostředků Azure. Omezení, která nebyla změněna pomocí Azure Resource Manager nejsou uvedena. Tato omezení najdete v předchozí tabulce.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Omezení Galerie sdílených imagí

Pro nasazení prostředků pomocí galerií sdílených imagí existují omezení pro každý odběr:
- Galerie sdílených imagí 100 na jedno předplatné, podle jednotlivých oblastí
- 1 000 definice imagí pro každé předplatné v jednotlivých oblastech
- verze image 10 000 na jedno předplatné v jednotlivých oblastech

### <a name="virtual-machine-scale-sets-limits"></a>Omezení Virtual Machine Scale Sets
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Omezení Container Instances
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Omezení Container Registry
Následující tabulka obsahuje informace o funkcích a omezeních [úrovní služeb](./container-registry/container-registry-skus.md)Basic, Standard a Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Omezení služby Azure Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Omezení služby Azure Machine Learning
Nejnovější hodnoty pro Azure Machine Learning výpočetních kvót najdete na [stránce kvóty Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Síťová omezení
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Omezení ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Omezení Application Gateway

Následující tabulka se vztahuje na SKU V1, v2, Standard a WAF, pokud není uvedeno jinak.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Omezení Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Omezení privátních odkazů
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Omezení Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Omezení Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Omezení Azure Firewall
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limity služby front-dveří pro Azure
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Omezení úložiště
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Další informace o limitech účtu úložiště najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Omezení poskytovatele prostředků úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Omezení úložiště objektů BLOB v Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Omezení souborů Azure
Další informace o omezeních souborů Azure najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Omezení Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Omezení Azure Queue Storage
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Omezení úložiště tabulek v Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Omezení disku virtuálního počítače
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Další informace najdete v tématu [velikosti virtuálních počítačů](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Spravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nespravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Omezení Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Omezení Azure Cognitive Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Omezení App Service
Následující omezení App Service zahrnují omezení pro Web Apps, Mobile Apps a API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Omezení funkcí
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Omezení plánovače
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Omezení dávky
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Omezení BizTalk Services
Následující tabulka uvádí omezení pro Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Omezení Azure Cosmos DB
Omezení Azure Cosmos DB najdete v části [omezení v Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Omezení Azure Database for MySQL najdete v tématu [omezení v Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Omezení Azure Database for PostgreSQL najdete v tématu [omezení v Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Omezení Azure Search
Cenové úrovně určují kapacitu a omezení služby vyhledávání. Mezi vrstvy patří:

* **Bezplatná** víceklientská služba, která je sdílená s ostatními předplatiteli Azure, je určená pro vyhodnocení a malé vývojové projekty.
* **Basic** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, a to až tři repliky pro úlohy dotazů s vysokou dostupností.
* Pro větší produkční úlohy je **Standard**, který zahrnuje S1, S2, S3 a S3 vysokou hustotu. Na úrovni Standard existuje více úrovní, takže můžete zvolit konfiguraci prostředků, která nejlépe odpovídá vašemu profilu zatížení.

**Omezení na předplatné**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Omezení podle vyhledávací služby**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Další informace o omezeních na podrobnější úrovni, jako je velikost dokumentu, dotazy za sekundu, klíče, žádosti a odpovědi, najdete v tématu [omezení služby v Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Omezení Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Omezení Content Delivery Network
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Omezení Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Omezení Azure Monitor

#### <a name="alerts"></a>Upozornění

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Skupiny akcí

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Dotazy a jazyky protokolu

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Pracovní prostory Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Omezení Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Omezení Event Hubs
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Omezení Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Omezení IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Omezení IoT Hub Device Provisioning Service
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Omezení Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Omezení Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Omezení Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Omezení Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Omezení Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Omezení služby Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Omezení Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Omezení Azure Maps
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Omezení Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Omezení StorSimple systému
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Omezení zálohování
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Omezení služby signalizace Azure
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Omezení Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Omezení API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Mezipaměť Azure pro limity Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Omezení Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Omezení Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Omezení automatizace
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Omezení identity Manageru
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Omezení řízení přístupu na základě rolí
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Omezení SQL Database
Omezení SQL Database najdete v tématu omezení [prostředků SQL Database pro izolované databáze](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database omezení prostředků pro elastické fondy a databáze ve fondu](sql-database/sql-database-vcore-resource-limits-elastic-pools.md)a [SQL Database omezení prostředků pro spravované instance](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Omezení SQL Data Warehouse
Omezení SQL Data Warehouse najdete v tématu [omezení prostředků SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Viz také:
- [Pochopení omezení a zvýšení počtu Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Velikosti virtuálních počítačů a cloudových služeb pro Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Velikosti pro Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
