---
title: Limity předplatného Azure a kvóty
description: Obsahuje seznam běžných předplatného Azure a limity, kvóty a omezení. Tento článek obsahuje informace o tom, jak zvýšit omezení spolu s maximální hodnoty.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 88c40021ff2e974ba16b3b3506ee54b45de90fda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427588"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity, kvóty a omezení předplatného a služeb Azure
Tento dokument uvádí některé nejčastějšími omezeními Microsoft Azure, které se taky říká se jim kvóty. Tento dokument nepopisuje aktuálně všech služeb Azure. V průběhu času v seznamu rozbalit a doplněn o popis další služby.

Další informace o cenách Azure najdete v tématu [přehled o cenách Azure](https://azure.microsoft.com/pricing/). Existuje, odhadněte svoje náklady pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/). Můžete také přejít na stránku s cenami podrobnosti pro konkrétní službu, například [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tipy ke správě nákladů najdete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing/billing-getting-started.md).

> [!NOTE]
> Pokud chcete zvýšit limit nebo kvóty nad výchozí omezení [otevřete žádost o podporu online zákazníků bez poplatků](azure-resource-manager/resource-manager-quota-errors.md). Omezení nelze zvýšit nad maximální mezní hodnoty uvedené v následujících tabulkách. Pokud neexistuje žádný sloupec maximálního limitu, nemá zdroj nastavitelná omezení.
>
> [Bezplatné předplatné zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p) se nevztahuje možnost zvýšení limitu nebo kvóty. Pokud máte [bezplatnou zkušební verzi předplatného](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na [s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) předplatného. Další informace najdete v tématu [vaší bezplatné zkušební verze Azure předplatné upgradovat na předplatné s průběžnými platbami](billing/billing-upgrade-azure-subscription.md) a [nejčastější dotazy k bezplatné zkušební předplatné](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Omezení a Azure Resource Manageru
Nyní je možné kombinovat více prostředků Azure na jednu skupinu prostředků Azure. Při použití skupin prostředků, omezení, které byly jednou globální správy na místní úrovni s Azure Resource Manageru. Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md).

V následujícím seznamu omezení nová tabulka odráží případné rozdíly v omezení při použití Azure Resource Manageru. Například neexistuje **limity předplatného** tabulky a **limity předplatného – Azure Resource Manageru** tabulky. Když omezení platí pro oba scénáře, se zobrazí pouze v první tabulce. Pokud není uvedeno jinak, jsou ve všech oblastech globální omezení.

> [!NOTE]
> Kvóty pro prostředky ve skupinách prostředků Azure jsou jednotlivých oblastech přístupné pro vaše předplatné není předplatných, jako jsou kvóty služby správy. Jako příklad použijeme kvóty virtuálních procesorů. Požádat o zvýšení kvóty díky podpoře pro virtuální procesory, musíte rozhodnout, kolik virtuálních procesorů, které chcete použít, ve kterých oblastech. Částky nebo oblasti, které chcete, aby pak provedete konkrétní žádosti o kvóty virtuálních procesorů skupiny prostředků Azure. Pokud potřebujete použít ke spuštění aplikace existuje 30 virtuálních procesorů v oblasti západní Evropa, můžete konkrétně požádat o 30 virtuálních procesorů v oblasti západní Evropa. Kvótu virtuálních procesorů není zvýšit v kterékoli jiné oblasti – pouze pro oblasti západní Evropa má kvótu 30 virtuálních procesorů.
> <!-- -->
> V důsledku toho rozhodněte, co musí být vaše kvóty skupiny prostředků Azure pro vaše úlohy v jakékoli jedné oblasti. Potom žádost o velikosti v každé oblasti, do které chcete nasadit. Nápovědu v tom, jak zjistit aktuální kvóty pro konkrétní oblasti najdete v tématu [řešení problémů s nasazením](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Omezení konkrétní služby
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Brány Firewall na Azure](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Služba Azure Machine Learning](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Službě Azure SignalR](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Síť pro doručování obsahu](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Služba Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Branou služby](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [Služba IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Sítě](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Služba Azure přední dveře](#azure-front-door-service-limits)
  * [Brány Firewall na Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Veřejná IP adresa](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
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
* [StorSimple systému](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Předplatné](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Škálovací sady virtuálních počítačů](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Omezení předplatného
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limity předplatného – Správa služeb Azure (model nasazení classic)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity předplatného – Azure Resource Manageru
Následující omezení platí při použití Azure Resource Manageru a skupin prostředků Azure. Limity, které nebyly změněny pomocí Azure Resource Manageru nejsou uvedené. Najdete v předchozí tabulce tyto limity.

Informace o rozhraní API Resource Manageru čtení a zápis omezení, najdete v článku [požadavky omezení využití sítě Správce prostředků](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Omezení skupiny prostředků
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Omezení virtuálních počítačů
#### <a name="virtual-machines-limits"></a>Omezení virtuálních počítačů
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Omezení virtuálních počítačů – Azure Resource Manageru
Následující omezení platí při použití Azure Resource Manageru a skupin prostředků Azure. Limity, které nebyly změněny pomocí Azure Resource Manageru nejsou uvedené. Najdete v předchozí tabulce tyto limity.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Sdílené omezení Galerie obrázků

Existují omezení na jedno předplatné, pro nasazení prostředků pomocí Galerie sdílené bitové kopie:
- 100 Galerie sdílené bitové kopie na předplatné a oblast
- 1 000 obrázků definice na předplatné a oblast
- 10 000 obrázků verze na předplatné a oblast

### <a name="virtual-machine-scale-sets-limits"></a>Virtual machine scale sets s limity
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity instancí kontejneru
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Omezení registru kontejneru
Následující tabulka obsahuje podrobnosti o funkcích a omezení Basic, Standard a Premium [úrovně služeb](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Omezení služby Azure Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Omezení služby Azure Machine Learning
Nejnovější hodnoty pro Azure Machine Learning Compute kvóty najdete v [kvóty stránku Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Síťová omezení
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limity pro ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Omezuje Application Gateway

V následující tabulce platí pro v1, v2, Standard a SKU WAF, pokud není uvedeno jinak.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Omezuje Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Omezení Traffic Manageru
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Omezení Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Omezení brány Firewall pro Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Omezení služby Azure branou
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Omezení úložiště
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Další informace o omezení účtů úložiště najdete v tématu [škálovatelnost a výkonnostní cíle Azure Storage](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Omezení poskytovatele prostředků úložiště 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Omezení úložiště Azure Blob
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Omezení pro soubory Azure
Další informace o omezení soubory Azure najdete v tématu [škálovatelnost a výkonnostní cíle Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Omezení služby Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Omezení úložiště Azure Queue
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Omezení úložiště Azure Table
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Omezením disku virtuálního počítače
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Další informace najdete v tématu [velikostí virtuálních počítačů](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Spravované virtuální počítače s disky

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nespravované virtuální počítače s disky

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Omezení pro Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Omezení služby App Service
Následující omezení služby App Service zahrnují omezení pro Web Apps, Mobile Apps a API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Omezení funkcí
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Omezení
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Omezení služby batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Omezení služby BizTalk Services
Následující tabulka uvádí omezení pro Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Omezení služby Azure Cosmos DB
Omezení služby Azure Cosmos DB najdete v tématu [omezení ve službě Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL – omezení, najdete v části [omezení ve službě Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for postgresql – omezení, najdete v části [omezení ve službě Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Omezení služby Azure Search
Cenové úrovně určit kapacitu a limity služby search. Vrstvy patří:

* **Bezplatné** víceklientské služby, sdílenou s jinými odběrateli Azure slouží k vyhodnocení a malých vývojových projektů.
* **Základní** zajišťují vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku pomocí až tří replik pro úlohy s vysokou dostupností dotazu.
* **Standardní**, což zahrnuje S1, S2, S3, a S3 High Density, je pro větší úlohy v produkčním prostředí. Více úrovní existují v rámci úrovně Standard, takže můžete použít konfiguraci prostředků, která nejlépe odpovídá profilu zatížení.

**Omezení jednoho předplatného**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Omezení pro službu search**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Další informace o omezeních na podrobnější úrovni, jako je například velikost dokumentu, dotazy za sekundu, klíčů, požadavků a odpovědí, najdete v článku [omezení služby Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Omezení služby Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Omezení sítě Content Delivery Network
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Omezení Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Omezení monitorování
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Omezení Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs omezení
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Omezení služby Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Omezení služby IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Omezení IoT Hub Device Provisioning Service
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Omezení datové továrny
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics omezení
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Omezení data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Omezení služby Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Omezení Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Omezení služby Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Omezení služby Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Omezení služby Azure Maps
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Omezení zásad Azure
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Omezení systému StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Omezuje log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Omezení zálohování
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Omezení služby Azure SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Omezení Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Omezení Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Omezení rozhraní API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Mezipaměť Azure Redis omezení
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Omezení služby Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Omezení ověřování službou Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Omezení služby Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Omezení identity Manageru
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Omezení řízení přístupu na základě role
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limity SQL Database
Omezení SQL Database najdete v tématu [limity prostředků SQL Database pro izolované databáze](sql-database/sql-database-vcore-resource-limits-single-databases.md), [limity pro elastické fondy a databáze ve fondu prostředků SQL Database](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), a [limity prostředků SQL Database pro spravované instance](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse omezení
SQL Data Warehouse omezení najdete v tématu [omezení prostředků SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Další informace najdete v tématech
- [Principy omezení v Azure a zvýšení](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Služby velikosti virtuálního počítače a cloud pro Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Velikosti pro Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
