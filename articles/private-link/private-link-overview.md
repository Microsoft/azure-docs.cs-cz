---
title: Co je privátní propojení Azure?
description: Přehled funkcí, architektury a implementace privátních odkazů Azure Přečtěte si, jak fungují privátní koncové body Azure a služba privátního propojení Azure a jak je používat.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: adc08e978be699ea6ea3dd00beae1762d48644c0
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781058"
---
# <a name="what-is-azure-private-link"></a>Co je privátní propojení Azure? 
Privátní odkaz Azure vám umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám, které hostuje zákaznická/Partnerská služba Azure, prostřednictvím [privátního koncového bodu](private-endpoint-overview.md) ve vaší virtuální síti.

Provoz mezi vaší virtuální sítí a službou přenáší páteřní síť Microsoftu. Vystavení vaší služby pro veřejný Internet již není nutné. Ve virtuální síti můžete vytvořit vlastní [službu privátního propojení](private-link-service-overview.md) a předat ji zákazníkům. Nastavení a spotřeba pomocí privátního propojení Azure jsou konzistentní v rámci služeb Azure PaaS, Customer a Shared partnered.

> [!IMPORTANT]
> Privátní propojení Azure je teď všeobecně dostupné. K dispozici jsou všeobecně dostupné služby privátního i privátního koncového bodu (služba za službu Load Balancer úrovně Standard). Různé služby Azure PaaS se budou připojovat k privátnímu propojení Azure v různých plánech. V tomto článku najdete v části [dostupnost dostupnosti](#availability) přesný stav služby Azure PaaS na privátním odkazu. Známá omezení najdete v tématu [privátní koncový bod](private-endpoint-overview.md#limitations) a [Služba privátního propojení](private-link-service-overview.md#limitations). 

## <a name="key-benefits"></a>Klíčové výhody
Privátní propojení Azure přináší následující výhody:  
- **Služby soukromého přístupu na platformě Azure**: Připojte svoji virtuální síť ke službám v Azure bez veřejné IP adresy ve zdroji nebo cíli. Poskytovatelé služeb mohou vykreslovat své služby ve své vlastní virtuální síti a příjemci mají přístup k těmto službám ve své místní virtuální síti. Platforma privátního propojení bude zpracovávat připojení mezi příjemcem a službami přes páteřní síť Azure. 
 
- **Místní a partnerské sítě**: přístup ke službám, které běží v Azure, prostřednictvím privátního partnerského vztahu ExpressRoute, tunelových propojení VPN a partnerských virtuálních sítí s použitím privátních koncových bodů. Pro přístup k této službě není nutné konfigurovat ExpressRoute partnerský vztah Microsoftu ani procházet internetem. Privátní odkaz poskytuje zabezpečený způsob migrace úloh do Azure.
 
- **Ochrana před únikem dat**: privátní koncový bod je namapován na instanci prostředku PaaS namísto celé služby. Příjemci se můžou připojit jenom ke konkrétnímu prostředku. Přístup k jakémukoli jinému prostředku ve službě je blokovaný. Tento mechanismus zajišťuje ochranu před riziky úniku dat. 
 
- **Globální dosah**: Připojte soukromě ke službám běžícím v jiných oblastech. Virtuální síť příjemce může být v oblasti A a může se připojit ke službám za soukromým odkazem v oblasti B.  
 
- **Rozšiřování na vlastní služby**: umožňuje stejné prostředí a funkce pro vlastní vygenerování služby pro uživatele v Azure. Umístěním služby za standardní Azure Load Balancer můžete povolit pro privátní propojení. Příjemce se pak může připojit přímo k vaší službě pomocí privátního koncového bodu ve své vlastní virtuální síti. Žádosti o připojení můžete spravovat pomocí toku volání schválení. Privátní propojení Azure funguje pro zákazníky a služby patřící různým klientům Azure Active Directory. 

## <a name="availability"></a>Dostupnost 
 V následující tabulce jsou uvedeny služby privátních odkazů a oblasti, kde jsou k dispozici. 

|Podporované služby  |Dostupné oblasti | Další aspekty | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Služby privátního propojení za standardním Azure Load Balancer | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti  | Podporováno v Standard Load Balancer | GA <br/> [Naučte se vytvořit službu privátního propojení.](create-private-link-service-portal.md) |
| Úložiště objektů BLOB v Azure (včetně Data Lake Storage Gen2)       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště objektů BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| Soubory Azure | Všechny veřejné oblasti<br/> Všechny oblasti státní správy      | |   GA <br/> [Naučte se vytvářet koncové body sítě služby soubory Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Synchronizace souborů Azure | Všechny veřejné oblasti      | |   GA <br/> [Naučte se vytvářet koncové body sítě služby soubory Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště Queue.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště tabulek.](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL Database         | Všechny veřejné oblasti <br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti      |  Podporováno pro [zásady připojení](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Zjistěte, jak vytvořit privátní koncový bod pro Azure SQL.](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Všechny veřejné oblasti <br/> Všechny oblasti státní správy |  Podporováno pro [zásady připojení](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Naučte se vytvářet privátní koncový bod pro Azure synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Všechny veřejné oblasti<br/> Všechny oblasti státní správy</br> Všechny čínské oblasti | |GA <br/> [Naučte se vytvořit privátní koncový bod pro Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – jeden server         | Všechny veřejné oblasti <br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti     | Podporováno pro Pro obecné účely a paměťově optimalizované cenové úrovně | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti      |  | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti     |  | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Key Vault         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy      |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Key Vault.](../key-vault/general/private-link-service.md)   |
|Azure Kubernetes Service – rozhraní API pro Kubernetes | Všechny veřejné oblasti      |  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro službu Azure Kubernetes.](../aks/private-clusters.md)   |
|Azure Search | Všechny veřejné oblasti <br/> Všechny oblasti státní správy | Podporováno u služby v privátním režimu | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Container Registry | Všechny veřejné oblasti<br/> Všechny oblasti státní správy    | Podporováno s úrovní Premium registru kontejneru. [Vybrat pro vrstvy](../container-registry/container-registry-skus.md)| GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure App Configuration | Všechny veřejné oblasti      |  | Preview  </br> [Zjistěte, jak vytvořit privátní koncový bod pro konfiguraci aplikací Azure.](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | Všechny veřejné oblasti<br/> Všechny oblasti státní správy   |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Backup.](../backup/private-endpoints.md)   |
|Azure Event Hub | Všechny veřejné oblasti<br/>Všechny oblasti státní správy      |   | GA   <br/> [Naučte se vytvářet privátní koncový bod pro centrum událostí Azure.](../event-hubs/private-link-service.md)  |
|Azure Service Bus | Všechny veřejné oblasti<br/>Všechny oblasti státní správy  | Podporováno s úrovní Premium Azure Service Bus. [Vybrat pro vrstvy](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |
|Azure Relay | Všechny veřejné oblasti      |  | Preview <br/> [Naučte se vytvořit privátní koncový bod pro Azure Relay.](../azure-relay/private-link-service.md)  |
|Azure Event Grid| Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Event Grid.](../event-grid/network-security.md) |
|Azure Web Apps | Všechny veřejné oblasti      | Podporováno s plánem PremiumV2, PremiumV3 nebo funkcí Premium  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Všechny veřejné oblasti    |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |
| Azure Automation  | Všechny veřejné oblasti |  | Preview </br> [Naučte se vytvořit privátní koncový bod pro Azure Automation.](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | Všechny veřejné oblasti    |  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | VÝCHODNÍ USA, STŘED USA – JIH,<br/>ZÁPADNÍ USA 2, všechny čínské oblasti      |  | Preview   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro službu Azure Signal.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Všechny veřejné oblasti      |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Monitor.](../azure-monitor/platform/private-link-security.md)   | 
| Azure Batch | Všechny veřejné oblasti kromě: Německo – střed, Německo – SEVEROVÝCHOD <br/> Všechny oblasti státní správy  | | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Batch.](../batch/private-connectivity.md) |
|Azure Data Factory | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti    | Přihlašovací údaje musí být uložené v trezoru klíčů Azure.| GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Data Factory.](../data-factory/data-factory-private-link.md)   |
|Spravované disky Azure | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti    | [Kliknutím sem zobrazíte známá omezení.](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal#limitations) | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure Managed Disks.](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal)   |



Nejaktuálnější oznámení najdete na [stránce s aktualizacemi privátních odkazů Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Privátní propojení Azure má integraci s Azure Monitor. Tato kombinace umožňuje:

 - Archivace protokolů do účtu úložiště.
 - Streamování událostí do centra událostí.
 - Protokolování Azure Monitor

Na Azure Monitor můžete získat přístup k následujícím informacím: 
- **Privátní koncový bod**: 
    - Data zpracovaná privátním koncovým bodem (v/v)
 
- **Služba privátního propojení**:
    - Data zpracovaná službou privátního propojení (v/v)
    - Dostupnost portu NAT  
 
## <a name="pricing"></a>Ceny   
Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Nejčastější dotazy  
Nejčastější dotazy najdete v tématu [Nejčastější dotazy k privátním linkám Azure](private-link-faq.md).
 
## <a name="limits"></a>Omezení  
Omezení najdete v tématu [omezení privátních propojení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>smlouva SLA
Pro smlouvu SLA si přečtěte téma věnované [smlouvě SLA pro privátní propojení Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure Portal](create-private-endpoint-portal.md)
- [Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal](create-private-link-service-portal.md)


