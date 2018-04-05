---
title: Limity předplatného Azure a kvóty | Microsoft Docs
description: Poskytuje seznam běžných předplatného Azure a omezení služby, kvóty a omezení. To zahrnuje informace o tom, jak zvýšit omezení spolu s maximální hodnoty.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: byvinyal
ms.openlocfilehash: f8b97aeb4c37578b884c924bd1919a59fa0f1cf8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Limity, kvóty a omezení předplatného a služeb Azure
Tento dokument uvádí některé z nejběžnějších omezení Microsoft Azure, což se taky někdy označují jako kvóty. Tento dokument nepokrývá aktuálně všech služeb Azure. V čase v seznamu rozbalit a aktualizovat tak, aby pokrývalo více platformou.

Navštivte [ceny přehled Azure](https://azure.microsoft.com/pricing/) Další informace o cenách služby Azure. Zde můžete odhadnout náklady na používání [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) nebo když přejdete na stránce s cenami podrobnosti pro službu (například [virtuálních počítačů Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Tipy ke správě náklady na najdete v tématu [zabránit neočekávané náklady s Azure fakturace a náklady na správu](billing/billing-getting-started.md).

> [!NOTE]
> Pokud chcete zvýšit limit nebo kvóty výše **výchozí Limit**, [otevřete žádosti o podporu online zákazníka zdarma](azure-supportability/resource-manager-core-quotas-request.md). Není možné zvýšit limity výše **maximální Limit** hodnota použitá v následujících tabulkách. Pokud není žádná **maximální Limit** sloupec a potom prostředek nemá nastavitelná omezení.
>
> [Uvolněte zkušební verze předplatného](https://azure.microsoft.com/offers/ms-azr-0044p) nejsou způsobilé pro zvýšení limitu nebo kvóty. Pokud máte [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p), můžete upgradovat [průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) předplatné. Další informace najdete v tématu [Upgrade bezplatné zkušební verze Azure na průběžné platby](billing/billing-upgrade-azure-subscription.md) a [bezplatnou zkušební verzi – nejčastější dotazy](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Omezení a Azure Resource Manager
Nyní je možné kombinovat více prostředků Azure v do jedné skupiny prostředků Azure. Při použití skupin prostředků, omezení, které byly jednou globální spravovanou na místní úrovni s Azure Resource Manager. Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md).

V níže uvedené limity se přidal nové tabulky, aby odrážela případné rozdíly v omezení při použití Azure Resource Manager. Například, že je **limity předplatného** tabulky a **limity předplatného – Azure Resource Manager** tabulky. Když omezení platí pro oba scénáře, se zobrazí pouze v první tabulce. Pokud není uvedeno jinak, jsou omezení globální přes všechny oblasti.

> [!NOTE]
> Je nutné zdůraznit, že jsou na oblast přístupný pro vaše předplatné kvóty pro prostředky ve skupinách prostředků Azure a nejsou za předplatné, jako jsou kvóty správy služby. Jako příklad použijeme kvóty virtuální procesory. Pokud potřebujete požádat o zvýšení kvóty s podporou pro Vcpu, musíte se rozhodnout, kolik Vcpu, kterou chcete použít v oblasti, které a pak proveďte konkrétního požadavku pro skupiny prostředků Azure virtuální procesor kvóty pro částky a oblastí, které chcete. Proto pokud budete muset použít 30 Vcpu v oblasti západní Evropa spusťte aplikaci existuje, měli byste požádat o konkrétně 30 Vcpu v oblasti západní Evropa. Ale nebudete mít virtuální procesory kvótu zvýšit v jiné oblasti – pouze západní Evropa budou mít kvótu 30-virtuální procesory.
> <!-- -->
> V důsledku toho může být vhodné vzít v úvahu při rozhodování o tom, co kvóty vaší skupiny prostředků Azure musí být pro úlohy v libovolné oblasti jeden a požadovat tato částka v každé oblasti, do kterého uvažujete o nasazení. V tématu [potíží s nasazením](resource-manager-common-deployment-errors.md) další pomoc zjišťování vaše aktuální kvóty pro konkrétní oblasti.
>
>

## <a name="service-specific-limits"></a>Omezení specifickou pro službu
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure událostí mřížky](#azure-event-grid-limits)
* [Azure Redis Cache](#azure-redis-cache-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Službu kontejneru (AKS)](#container-service-aks-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Služba migrace databáze](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Event Hubs](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Služba zřizování zařízení IoT Hub](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobilní služby](#mobile-services-limits)
* [Monitorování](#monitor-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Sítě](#networking-limits)
* [Sledovací proces sítě](#network-watcher-limits)
* [Služby centra oznámení](#notification-hub-service-limits)
* [Skupina prostředků](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [Search](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Úložiště](#storage-limits)
* [Systém StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Předplatné](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Virtual Machines](#virtual-machines-limits)
* [Škálovací sady virtuálních počítačů](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limity předplatného
#### <a name="subscription-limits"></a>Limity předplatného
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limity předplatného – Azure Resource Manager
Následující omezení platí při použití skupiny prostředků Azure a Azure Resource Manager. Limity, které nebyly změněny s Azure Resource Manager nejsou uvedené níže. Naleznete v předchozí tabulce těchto omezení.

Informace o zpracování omezení pro správce prostředků požadavky najdete v tématu [omezení Resource Manager požadavky](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Omezení skupiny prostředků
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limity virtuální počítače
#### <a name="virtual-machine-limits"></a>Limity virtuální počítač
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtuální počítače omezení - Azure Resource Manager
Následující omezení platí při použití skupiny prostředků Azure a Azure Resource Manager. Limity, které nebyly změněny s Azure Resource Manager nejsou uvedené níže. Naleznete v předchozí tabulce těchto omezení.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Omezení sady škálování virtuálního počítače
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limity instancí kontejneru
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Omezení registru kontejneru
V následující tabulce jsou funkcí a omezení Basic, Standard a Premium [úrovních služeb](./container-registry/container-registry-skus.md).

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="container-service-aks-limits"></a>Omezuje služby kontejneru (AKS)
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Síťová omezení
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Síťová omezení
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Omezuje aplikační brány
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Sledovací proces sítě omezuje
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Omezení Traffic Manageru
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Omezení DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limity úložiště
Další informace o limity účtu úložiště najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](storage/common/storage-scalability-targets.md).

<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure limity úložiště objektů Blob
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Omezení Azure soubory
Další informace o omezení Azure soubory v [Azure Files škálovatelnosti a cílech výkonnosti](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Synchronizace služby Azure souboru omezení
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limity úložiště Azure fronty
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limity úložiště Azure Table
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Omezení disku virtuálního počítače
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

V tématu [velikostí virtuálních počítačů](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) další podrobnosti.

#### <a name="managed-virtual-machine-disks"></a>Disky spravovaných virtuálních počítačů

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Disky nespravované virtuálního počítače

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Omezení cloudové služby
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Omezení služby App Service
Následující omezení služby App Service zahrnují omezení pro webové aplikace, mobilní aplikace, aplikace API a Logic Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Omezení
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Omezení dávky
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Omezení služby BizTalk Services
Následující tabulka uvádí omezení služby Azure Biztalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Omezení Azure Cosmos DB
Azure Cosmos DB je globálním měřítku databáze, ve které je možné rozšířit propustnost a úložiště pro zpracování, ať vaše aplikace vyžaduje. Pokud máte nějaké otázky o rozsahu poskytuje Azure Cosmos DB, pošlete e-mail na askcosmosdb@microsoft.com.

### <a name="mobile-engagement-limits"></a>Omezení Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Omezení vyhledávání
Cenové úrovně určete kapacitu a omezení služby search. Úrovně zahrnují:

* *Volné* víceklientské služby, sdílené s další předplatitelé služby Azure, určený pro malé vývoj a testování projektů.
* *Základní* poskytuje vyhrazený výpočetní prostředky pro úlohy v produkčním prostředí v menším měřítku, s až tři repliky pro vysokou dostupnost dotazu úlohy.
* *Standard (S1, S2, S3, S3 s vysokou hustotou)* je pro větší úlohy v produkčním prostředí. Více úrovní existovat v rámci úroveň standard, aby mohli vybrat konfiguraci prostředků, který nejlépe odpovídá váš profil zatížení.

**Omezení podle předplatného**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Omezení jednu službu vyhledávání**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Další informace o omezeních na podrobnější úrovni, jako je například velikost dokumentu, dotazů za sekundu, klíčů, požadavků a odpovědí, najdete v části [omezení ve službě Azure Search služby](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Omezení služby Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Omezení CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Omezení Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Omezení monitorování
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Omezení služby centra oznámení
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Omezení centra událostí
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Omezení služby Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Omezení služby IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Omezení IoT Hub zařízení zřizování služby
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Omezení pro vytváření dat
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Omezuje data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Omezení data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Omezení služby migrace databáze
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Omezuje služby Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Omezuje služby Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Azure omezení událostí mřížky
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="storsimple-system-limits"></a>Omezení systému StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Omezuje analýzy protokolů
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Zálohování omezení
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Omezení Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Omezení Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Omezení API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Omezení Azure Redis Cache
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Omezení Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Omezení automatizace
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Omezení databáze SQL
Omezení SQL Database, najdete v části [limitů prostředků databáze SQL](sql-database/sql-database-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Omezení SQL Data Warehouse
Omezení SQL Data Warehouse, najdete v části [SQL Data Warehouse prostředků omezení](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Další informace najdete v tématech
[Seznámení s Azure omezení a zvyšuje](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Virtuální počítač velikost služeb a Cloud pro Azure.](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Velikosti pro cloudové služby](cloud-services/cloud-services-sizes-specs.md)
