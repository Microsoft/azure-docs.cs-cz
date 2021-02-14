---
title: Omezení a kvóty předplatného Azure
description: Poskytuje seznam běžných limitů, kvót a omezení předplatného a služeb Azure. Tento článek obsahuje informace o tom, jak zvýšit limity spolu s maximálními hodnotami.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: ac35f09e1a7274019a8ca504fc697ca81de000f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370091"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity, kvóty a omezení předplatného a služeb Azure

Tento dokument obsahuje seznam některých nejběžnějších omezení Microsoft Azure, která se také někdy označují jako kvóty.

Další informace o cenách Azure najdete v tématu [Přehled cen Azure](https://azure.microsoft.com/pricing/). V takovém případě můžete náklady odhadnout pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/). Můžete také přejít na stránku s podrobnostmi o cenách pro konkrétní službu, například na [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tipy, které vám pomůžou se správou nákladů, najdete v tématu [Ochrana před neočekávanými náklady pomocí fakturace a správy nákladů Azure](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Správa omezení

> [!NOTE]
> Některé služby mají nastavitelná omezení.
>
> Pokud služba nemá nastavitelná omezení, používají následující tabulky **limit** hlaviček. V těchto případech jsou výchozí a maximální limity stejné.
>
> V případě, že je možné upravit limit, tabulky obsahují **výchozí limit** a **maximální počet** hlaviček. Limit je možné vyvýšený nad výchozí limit, ale ne nad maximálním limitem.
>
> Pokud chcete limit nebo kvótu nad výchozím limitem zvýšit, [otevřete online žádost o zákaznickou podporu](../templates/error-resource-quota.md)zdarma.
>
> Podmínky *podmíněného omezení* a *pevného omezení* často slouží k popisu aktuálního, upravitelného limitu (měkkého limitu) a maximálního limitu (pevně stanoveného limitu). Pokud je limit nepřizpůsobitelný, nebude se jednat o částečný limit, ale jenom na pevný limit.
>

[Bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p) neplatí pro zvýšení limitu nebo kvóty. Pokud máte [bezplatné zkušební předplatné](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat na předplatné s průběžnými [platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) . Další informace najdete v tématu [upgrade předplatného bezplatné zkušební verze Azure na předplatné](../../cost-management-billing/manage/upgrade-azure-subscription.md) s průběžnými platbami a na [Nejčastější dotazy k bezplatné zkušební verzi předplatného](https://azure.microsoft.com/free/free-account-faq).

Některá omezení se spravují na regionální úrovni.

Použijte jako příklad kvóty vCPU. Pokud chcete požádat o zvýšení kvóty s podporou pro vCPU, musíte se rozhodnout, kolik vCPU chcete použít pro tyto oblasti. Pak vytvoříte konkrétní požadavek na vCPU kvóty skupiny prostředků Azure pro příslušné množství a oblasti. Pokud potřebujete použít 30 vCPU v Západní Evropa ke spuštění vaší aplikace, konkrétně si vyžádáte 30 vCPU v Západní Evropa. Vaše kvóta vCPU se nezvyšuje v žádné jiné oblasti – pouze Západní Evropa má kvótu 30 vCPU.

V důsledku toho se rozhodněte, jaké kvóty skupin prostředků Azure musí být pro vaše úlohy v libovolné oblasti. Pak vyžádejte tuto částku v každé oblasti, do které chcete nasadit. Nápovědu k určení vašich současných kvót pro konkrétní oblasti najdete v tématu [řešení chyb pro kvóty prostředků](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Obecná omezení

Omezení názvů prostředků najdete v tématu [pravidla a omezení pojmenování pro prostředky Azure](resource-name-rules.md).

Informace o Správce prostředků omezení čtení a zápisu rozhraní API najdete v tématu [omezování správce prostředků požadavků](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Omezení skupiny pro správu

Následující omezení platí pro [skupiny pro správu](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Omezení předplatného

Následující omezení platí při použití Azure Resource Manager a skupin prostředků Azure.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Omezení skupiny prostředků

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Omezení služby Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Omezení API Management

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Omezení App Service

Následující omezení App Service zahrnují omezení pro Web Apps, Mobile Apps a API Apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Omezení automatizace

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Mezipaměť Azure pro limity Redis

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Omezení Azure Cloud Services

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Omezení Azure Kognitivní hledání

Cenové úrovně určují kapacitu a omezení služby vyhledávání. Mezi vrstvy patří:

* **Bezplatná** víceklientská služba, která je sdílená s ostatními předplatiteli Azure, je určená pro vyhodnocení a malé vývojové projekty.
* **Basic** poskytuje vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku, a to až tři repliky pro úlohy dotazů s vysokou dostupností.
* Pro větší produkční úlohy je **Standard**, který zahrnuje S1, S2, S3 a S3 vysokou hustotu. Na úrovni Standard existuje více úrovní, takže můžete zvolit konfiguraci prostředků, která nejlépe odpovídá vašemu profilu zatížení.

**Omezení na předplatné**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Omezení podle vyhledávací služby**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Další informace o omezeních na podrobnější úrovni, jako je velikost dokumentu, dotazy za sekundu, klíče, žádosti a odpovědi, najdete v tématu [omezení služby v Azure kognitivní hledání](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Omezení Azure Cognitive Services

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Omezení Azure Cosmos DB

Omezení Azure Cosmos DB najdete v části [omezení v Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Omezení Azure Průzkumník dat

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Omezení Azure Database for MySQL najdete v tématu [omezení v Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Omezení Azure Database for PostgreSQL najdete v tématu [omezení v Azure Database for PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Omezení Azure Functions

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Další informace najdete v tématu [funkce pro plánování hostování funkcí](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Omezení služby Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Omezení Azure Machine Learning

Nejnovější hodnoty pro Azure Machine Learning výpočetních kvót najdete na [stránce kvóty Azure Machine Learning](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Omezení Azure Maps

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Omezení Azure Monitor

### <a name="alerts"></a>Výstrahy

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Skupiny akcí

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Automatické škálování

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Dotazy a jazyky protokolu

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Pracovní prostory služby Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Omezení Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Omezení pro Azure za sebou

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Omezení řízení přístupu na základě role v Azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Omezení služby signalizace Azure

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Omezení zálohování

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Omezení dávky

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Omezení modelu nasazení Classic

Použijete-li model nasazení Classic místo modelu nasazení Azure Resource Manager, platí následující omezení.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Omezení Container Instances

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Omezení Container Registry

Následující tabulka obsahuje informace o funkcích a omezeních [úrovní služeb](../../container-registry/container-registry-skus.md)Basic, Standard a Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Omezení Content Delivery Network

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Omezení Data Factory

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Omezení Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Omezení Data Lake Storage

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Omezení sdílení dat

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Omezení Database Migration Service

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Omezení digitálních vláken

> [!NOTE]
> Některé oblasti této služby mají nastavitelná omezení a jiné ne. To je znázorněno v tabulkách níže a *upravitelný sloupec?* . Pokud je možné limit upravit, je hodnota *seřiditelná?* *Ano*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Omezení Event Grid

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Omezení Event Hubs

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>Omezení IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Omezení IoT Hub

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Omezení IoT Hub Device Provisioning Service

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Omezení Key Vault

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Omezení spravovaných identit

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Omezení Media Services

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (starší verze)

Omezení specifická pro Media Services V2 (starší verze) najdete v tématu [Media Services V2 (starší verze)](../../media-services/previous/media-services-quotas-and-limitations.md) .

## <a name="mobile-services-limits"></a>Omezení Mobile Services

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Omezení Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Síťová omezení

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Omezení ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Omezení Virtual Network brány

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Omezení brány NAT

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Omezení virtuální sítě WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Omezení Application Gateway

Následující tabulka se vztahuje na SKU V1, v2, Standard a WAF, pokud není uvedeno jinak.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Omezení Network Watcher

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limity služby Private Link

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Omezení dosah

Nejnovější hodnoty pro kvóty Azure dosah najdete na [stránce věnované kvótám Azure dosah](../../purview/how-to-manage-quotas.md) .

### <a name="traffic-manager-limits"></a>Omezení Traffic Manager

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Omezení Azure bastionu

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Omezení Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Omezení Azure Firewall

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limity služby front-dveří pro Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Omezení Notification Hubs

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Omezení Service Bus

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Omezení Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Omezení SQL Database

Omezení SQL Database najdete v tématu omezení [prostředků SQL Database pro izolované databáze](../../azure-sql/database/resource-limits-vcore-single-databases.md), [SQL Database omezení prostředků pro elastické fondy a databáze ve fondu](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)a [SQL Database omezení prostředků pro spravovanou instanci SQL](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Omezení Azure synapse Analytics

Omezení Azure synapse Analytics najdete v tématu [omezení prostředků Azure synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="azure-files-and-azure-file-sync"></a>Soubory a Synchronizace souborů Azure Azure
Další informace o omezeních pro soubory a Synchronizace souborů Azure najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../../storage/files/storage-files-scale-targets.md).

## <a name="storage-limits"></a>Omezení úložiště

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Další informace o omezeních pro účty úložiště úrovně Standard najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Omezení poskytovatele prostředků úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Omezení úložiště objektů BLOB v Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Omezení Azure Queue Storage

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Omezení úložiště tabulek v Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Omezení disku virtuálního počítače

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Další informace najdete v tématu [velikosti virtuálních počítačů](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Sady šifrování disků

U každého předplatného je omezení 1000 sad Disk Encryption pro jednotlivé oblasti. Další informace najdete v dokumentaci k šifrování pro virtuální počítače se systémem [Linux](../../virtual-machines/disk-encryption.md#restrictions) nebo [Windows](../../virtual-machines/disk-encryption.md#restrictions) . Pokud potřebujete tuto kvótu zvýšit, obraťte se na podporu Azure.

### <a name="managed-virtual-machine-disks"></a>Spravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Nespravované disky virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Omezení StorSimple systému

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Omezení Stream Analytics

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Omezení Virtual Machines

### <a name="virtual-machines-limits"></a>Omezení Virtual Machines

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limity Virtual Machines – Azure Resource Manager

Následující omezení platí při použití Azure Resource Manager a skupin prostředků Azure.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Omezení Galerie sdílených imagí

Pro nasazení prostředků pomocí galerií sdílených imagí existují omezení pro každý odběr:

- Galerie sdílených imagí 100 na jedno předplatné, podle jednotlivých oblastí
- 1 000 definice imagí pro každé předplatné v jednotlivých oblastech
- verze image 10 000 na jedno předplatné v jednotlivých oblastech

## <a name="virtual-machine-scale-sets-limits"></a>Omezení Virtual Machine Scale Sets

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Viz také

* [Pochopení omezení a zvýšení počtu Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Velikosti virtuálních počítačů a cloudových služeb pro Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Velikosti pro Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Omezení a pravidla pojmenování prostředků Azure](resource-name-rules.md)
