---
title: Limity a kvóty předplatného Azure
description: Obsahuje seznam běžných omezení předplatného a služeb Azure, kvót a omezení. Tento článek obsahuje informace o tom, jak zvýšit limity spolu s maximální hodnoty.
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 00bd85febe34a7bd9b4e993d5697fa810347711b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768769"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Omezení předplatného a služeb Azure, kvóty a omezení

Tento dokument obsahuje seznam některých nejběžnějších limitů Microsoft Azure, které se také někdy nazývají kvóty.

Další informace o cenách Azure najdete v [tématu Přehled cen Azure](https://azure.microsoft.com/pricing/). Zde můžete odhadnout své náklady pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/). Můžete také přejít na stránku s podrobnostmi o cenách pro konkrétní službu, například [virtuální chodwindowsů windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tipy, které vám pomůžou spravovat náklady, najdete v [tématu Prevence neočekávaných nákladů pomocí fakturace azure a správy nákladů](../../billing/billing-getting-started.md).

## <a name="managing-limits"></a>Správa limitů

> [!NOTE]
> Některé služby mají nastavitelné limity.
>
> Pokud služba nemá nastavitelná omezení, následující tabulky používají záhlaví **Limit**. V těchto případech jsou výchozí a maximální limity stejné.
>
> Když lze limit upravit, tabulky obsahují **výchozí limit** a **maximální limit** záhlaví. Limit lze zvýšit nad výchozí limit, ale ne nad maximální limit.
>
> Chcete-li zvýšit limit nebo kvótu nad výchozí limit, [otevřete online žádost o zákaznickou podporu zdarma](../templates/error-resource-quota.md).

[Bezplatná zkušební předplatná](https://azure.microsoft.com/offers/ms-azr-0044p) nemají nárok na omezení ani zvýšení kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné [s průběžným platbou.](https://azure.microsoft.com/offers/ms-azr-0003p/) Další informace najdete [v tématu Upgrade bezplatnézkušební verze předplatného Azure na předplatné s průběžným platbou](../../billing/billing-upgrade-azure-subscription.md) a [nejčastější dotazy k bezplatné zkušební verzi](https://azure.microsoft.com/free/free-account-faq).

Některé limity jsou spravovány na regionální úrovni.

Jako příklad použijeme kvóty virtuálních procesorů. Chcete-li požádat o zvýšení kvóty s podporou virtuálních procesorů, musíte se rozhodnout, kolik virtuálních procesorů chcete použít ve kterých oblastech. Potom provést konkrétní požadavek na kvóty virtuálních procesorů skupiny prostředků Azure pro částky a oblasti, které chcete. Pokud potřebujete použít 30 virtuálních procesorů v západní Evropě ke spuštění aplikace tam, můžete konkrétně požádat o 30 virtuálních procesorů v západní Evropě. Kvóta virtuálního procesoru se nezvyšuje v žádné jiné oblasti – pouze západní Evropa má kvótu 30 virtuálních procesorů.

V důsledku toho rozhodněte, jaké kvóty skupiny prostředků Azure musí být pro vaše úlohy v jedné oblasti. Pak požádejte o tuto částku v každé oblasti, do které chcete nasadit. Nápovědu k určení aktuálních kvót pro určité oblasti naleznete v tématu [Řešení chyb kvót prostředků](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Obecné limity

Omezení názvů prostředků najdete v [tématu Pravidla pro pojmenování a omezení pro prostředky Azure](resource-name-rules.md).

Informace o omezeních čtení a zápisu rozhraní API správce prostředků naleznete v [tématu Omezení požadavků Správce prostředků](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limity skupiny pro správu

Následující omezení platí pro [skupiny pro správu](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Omezení předplatného

Následující omezení platí při použití Azure Resource Manager a skupiny prostředků Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Omezení skupiny prostředků

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Omezení služby Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limity správy rozhraní API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Omezení služby App Service

Následující omezení služby App Service zahrnují limity pro webové aplikace, mobilní aplikace a aplikace rozhraní API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limity automatizace

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Limity Azure Cache for Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limity cloudových služeb Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limity azure kognitivního vyhledávání

Cenové úrovně určují kapacitu a limity vyhledávací služby. Úrovně zahrnují:

* **Bezplatná** služba pro více klientů sdílená s ostatními předplatiteli Azure je určena pro vyhodnocování a malé vývojové projekty.
* **Basic** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, s až třemi replikami pro úlohy vysoce dostupných dotazů.
* **Standard**, který zahrnuje S1, S2, S3 a S3 s vysokou hustotou, je určen pro větší produkční úlohy. V rámci úrovně Standard existuje více úrovní, takže můžete zvolit konfiguraci prostředků, která nejlépe odpovídá profilu pracovního vytížení.

**Limity na předplatné**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Omezení na vyhledávací službu**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Další informace o omezeních na podrobnější úrovni, jako je velikost dokumentu, dotazy za sekundu, klíče, požadavky a odpovědi, najdete [v tématu Omezení služeb v Azure Cognitive Search](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limity azure kognitivních služeb

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limity Azure Cosmos DB

Omezení Služby Azure Cosmos DB najdete v tématu [Limity v Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limity Azure Data Exploreru

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Omezení Azure Database for MySQL najdete [v tématu Omezení v Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Limity Azure Database for PostgreSQL najdete [v tématu Omezení v Azure Database for PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limity funkcí Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Limity služby Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limity Azure Machine Learning

Nejnovější hodnoty pro výpočetní kvóty Azure Machine Learning najdete na [stránce kvót Azure Machine Learning.](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limity Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limity Azure Monitoru

### <a name="alerts"></a>Výstrahy

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Skupiny akcí

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>Protokolovat dotazy a jazyk

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Pracovní prostory služby Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limity zásad Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Omezení služby Azure SignalR

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limity zálohování

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limity dávek

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Omezení klasického modelu nasazení

Pokud místo modelu nasazení Azure Resource Manageru použijete klasický model nasazení, platí následující omezení.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Omezení instancí kontejneru

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Omezení registru kontejnerů

V následující tabulce jsou uvedeny funkce a omezení [úrovní služeb](../../container-registry/container-registry-skus.md)Basic, Standard a Premium .

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Omezení sítě pro doručování obsahu

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limity datové továrny

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limity Analýzy datových jezer

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Limity úložiště datových jezer

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limity sdílení dat

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limity služby migrace databáze

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>Omezení mřížky událostí

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Omezení centra událostí

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>Omezení správce identit

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>Limity IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limity ioT hubu

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limity služby Zřizování zařízení služby IoT Hub

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limity trezoru klíčů

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Omezení mediálních služeb

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (starší verze)

Omezení specifická pro media services v2 (starší verze) viz [Media Services v2 (starší verze)](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations)

## <a name="mobile-services-limits"></a>Limity pro mobilní služby

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Omezení vícefaktorového ověřování

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Síťová omezení

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Omezení ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>Limity virtuální sítě WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Omezení aplikační brány

Následující tabulka se vztahuje na v1, v2, Standard a WAF SKU, pokud není uvedeno jinak.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Omezení sledování sítě

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limity služby Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Limity Traffic Manageru

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limity Azure Bastion

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limity Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Omezení azure brány Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limity služby Azure Front Door

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Omezení centra oznámení

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>Limity řízení přístupu na základě rolí

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Limity sběrnice

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Omezení Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limity databáze SQL

Omezení databáze SQL naleznete v [tématu limity prostředků databáze SQL pro jednotlivé databáze](../../sql-database/sql-database-vcore-resource-limits-single-databases.md), omezení prostředků databáze SQL pro [elastické fondy a sdružené databáze](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)a limity prostředků databáze SQL pro spravované [instance](../../sql-database/sql-database-managed-instance-resource-limits.md).

## <a name="sql-data-warehouse-limits"></a>Limity datového skladu SQL

Omezení datového skladu SQL naleznete v tématu [limity prostředků datového skladu SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limity úložiště

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Další informace o limitech pro účty standardního úložiště najdete v tématu [Škálovatelnost cíle pro účty standardníúložiště](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Omezení zprostředkovatele prostředků úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limity úložiště objektů blob Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Omezení souborů Azure

Další informace o omezeních souborů Azure najdete v [tématu Škálovatelnost souborů Azure a cíle výkonu](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limity synchronizace souborů Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limity úložiště fronty Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limity úložiště Azure Table

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Omezení disku virtuálního počítače

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Další informace naleznete v tématu [Velikosti virtuálních strojů](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="managed-virtual-machine-disks"></a>Spravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Nespravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limity systému StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limity analýzy streamů

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Omezení virtuálních počítačů

### <a name="virtual-machines-limits"></a>Omezení virtuálních počítačů

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Omezení virtuálních počítačů – Správce prostředků Azure

Následující omezení platí při použití Azure Resource Manager a skupiny prostředků Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limity sdílené galerie obrázků

Existují omezení pro nasazení prostředků pomocí sdílených galerií obrázků na jedno předplatné:

- 100 sdílených galerií obrázků na jedno předplatné a na region
- 1 000 definic obrázků na jedno předplatné a na oblast
- 10 000 verzí obrázků na jedno předplatné a na oblast

## <a name="virtual-machine-scale-sets-limits"></a>Limity škálovacích sad virtuálních strojů

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Viz také

* [Pochopení omezení a zvýšení Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Velikosti virtuálních strojů a cloudových služeb pro Azure](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Velikosti cloudových služeb Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Omezení a pravidla pojmenování prostředků Azure](resource-name-rules.md)
