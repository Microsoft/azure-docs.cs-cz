---
title: Dostupnost privátního propojení Azure
description: V tomto článku se dozvíte, které služby Azure podporují privátní odkaz.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555428"
---
# <a name="azure-private-link-availability"></a>Dostupnost privátního propojení Azure

Privátní odkaz Azure vám umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám, které hostuje zákaznická/Partnerská služba Azure, prostřednictvím [privátního koncového bodu](private-endpoint-overview.md) ve vaší virtuální síti. 

> [!IMPORTANT]
> Privátní propojení Azure je teď všeobecně dostupné. K dispozici jsou všeobecně dostupné služby privátního i privátního koncového bodu (služba za službu Load Balancer úrovně Standard). Různé služby Azure PaaS se budou připojovat k privátnímu propojení Azure v různých plánech. Známá omezení najdete v tématu [privátní koncový bod](private-endpoint-overview.md#limitations) a [Služba privátního propojení](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Dostupnost služby

V následujících tabulkách jsou uvedeny služby privátních odkazů a oblasti, kde jsou k dispozici. 

### <a name="ai--machine-learning"></a>AI a strojové učení

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Všechny veřejné oblasti    |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analýzy

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Všechny veřejné oblasti <br/> Všechny oblasti státní správy |  Podporováno pro [zásady připojení](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Naučte se vytvářet privátní koncový bod pro Azure synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Všechny veřejné oblasti<br/>Všechny oblasti státní správy      |   | GA   <br/> [Naučte se vytvářet privátní koncový bod pro centrum událostí Azure.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Všechny veřejné oblasti      |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti    | Přihlašovací údaje musí být uložené v trezoru klíčů Azure.| GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Všechny veřejné oblasti      |  | Preview  </br> [Zjistěte, jak vytvořit privátní koncový bod pro konfiguraci aplikací Azure.](../azure-app-configuration/concept-private-endpoint.md) |
|Disky spravované Azure | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti    | [Vybrat pro známá omezení](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Kontejnery

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Všechny veřejné oblasti<br/> Všechny oblasti státní správy    | Podporováno s úrovní Premium registru kontejneru. [Vybrat pro vrstvy](../container-registry/container-registry-skus.md)| GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service – rozhraní API pro Kubernetes | Všechny veřejné oblasti      |  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro službu Azure Kubernetes.](../aks/private-clusters.md)   |

### <a name="databases"></a>Databáze

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Všechny veřejné oblasti <br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti      |  Podporováno pro [zásady připojení](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Zjistěte, jak vytvořit privátní koncový bod pro Azure SQL.](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Všechny veřejné oblasti<br/> Všechny oblasti státní správy</br> Všechny čínské oblasti | |GA <br/> [Naučte se vytvořit privátní koncový bod pro Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – jeden server         | Všechny veřejné oblasti <br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti     | Podporováno pro Pro obecné účely a paměťově optimalizované cenové úrovně | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti      |  | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti     |  | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integrace

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Všechny veřejné oblasti<br/>Všechny oblasti státní správy  | Podporováno s úrovní Premium Azure Service Bus. [Vybrat pro vrstvy](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet věcí (IoT)

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Všechny veřejné oblasti    |  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Všechny veřejné oblasti podporované pomocí digitálních vláken Azure     |  | Preview <br/> [Naučte se vytvářet privátní koncový bod pro digitální vlákna Azure.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Správa a zásady správného řízení

| Podporované služby | Dostupné oblasti | Další důležité informace | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Všechny veřejné oblasti<br/> Všechny oblasti státní správy |  | Preview </br> [Naučte se vytvořit privátní koncový bod pro Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Všechny veřejné oblasti<br/> Všechny oblasti státní správy   |  | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Zabezpečení

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Všechny veřejné oblasti<br/> Všechny oblasti státní správy      |  | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Úložiště objektů BLOB v Azure (včetně Data Lake Storage Gen2)       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště objektů BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Všechny veřejné oblasti<br/> Všechny oblasti státní správy      | |   GA <br/> [Naučte se vytvářet koncové body sítě služby soubory Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Synchronizace souborů Azure | Všechny veřejné oblasti      | |   GA <br/> [Naučte se vytvářet koncové body sítě služby soubory Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště Queue.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Všechny veřejné oblasti<br/> Všechny oblasti státní správy       |  Podporováno na druhu účtu Pro obecné účely v2 | GA <br/> [Naučte se vytvořit privátní koncový bod pro úložiště tabulek.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Všechny veřejné oblasti kromě: Německo – střed, Německo – SEVEROVÝCHOD <br/> Všechny oblasti státní správy  | | GA <br/> [Naučte se vytvořit privátní koncový bod pro Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | VÝCHODNÍ USA, STŘED USA – JIH,<br/>ZÁPADNÍ USA 2, všechny čínské oblasti      |  | Preview   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro službu Azure Signal.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Všechny veřejné oblasti<br/> Čína – sever 2 & východní 2    | Podporováno s plánem PremiumV2, PremiumV3 nebo funkcí Premium  | GA   <br/> [Přečtěte si, jak vytvořit privátní koncový bod pro Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Všechny veřejné oblasti <br/> Všechny oblasti státní správy | Podporováno u služby v privátním režimu | GA   <br/> [Naučte se vytvořit privátní koncový bod pro Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Všechny veřejné oblasti      |  | Preview <br/> [Naučte se vytvořit privátní koncový bod pro Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Služba privátního propojení se standardním nástrojem pro vyrovnávání zatížení

|Podporované služby  |Dostupné oblasti | Další důležité informace | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Služby privátního propojení za standardním Azure Load Balancer | Všechny veřejné oblasti<br/> Všechny oblasti státní správy<br/>Všechny čínské oblasti  | Podporováno v Standard Load Balancer | GA <br/> [Naučte se vytvořit službu privátního propojení.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Private Link:
- [Co je privátní propojení Azure?](private-link-overview.md)
- [Vytvoření privátního koncového bodu pomocí webu Azure Portal](create-private-endpoint-portal.md)
