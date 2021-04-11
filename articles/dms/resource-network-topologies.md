---
title: Síťové topologie pro migrace spravované instance SQL
titleSuffix: Azure Database Migration Service
description: Seznamte se se zdrojovými a cílovými konfiguracemi pro migrace spravované instance Azure SQL pomocí Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093356"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Síťové topologie pro migrace spravované instance SQL Azure pomocí Azure Database Migration Service

Tento článek pojednává o různých topologiích sítě, se kterými Azure Database Migration Service může pracovat, a poskytuje tak komplexní prostředí pro migraci z SQL serverů do spravované instance Azure SQL.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Spravovaná instance Azure SQL nakonfigurovaná pro hybridní úlohy 

Tuto topologii použijte, pokud je vaše spravovaná instance SQL Azure připojená k vaší místní síti. Tento přístup poskytuje nejjednodušší síťové směrování a poskytuje maximální propustnost dat během migrace.

![Síťová topologie pro hybridní úlohy](media/resource-network-topologies/hybrid-workloads.png)

**Požadavky**

- V tomto scénáři jsou spravované instance SQL a instance Azure Database Migration Service vytvořeny ve stejném Microsoft Azure Virtual Network, ale používají jiné podsítě.  
- Virtuální síť použitá v tomto scénáři je taky připojená k místní síti pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Spravovaná instance SQL izolovaná od místní sítě

Tuto topologii sítě použijte v případě, že vaše prostředí vyžaduje jeden nebo více následujících scénářů:

- Spravovaná instance SQL je izolovaná od místního připojení, ale vaše instance Azure Database Migration Service je připojená k místní síti.
- Pokud jsou nastavené zásady řízení přístupu na základě role Azure (Azure RBAC) a potřebujete omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem spravované instance SQL.
- Virtuální sítě používané pro spravovanou instanci SQL a Azure Database Migration Service jsou v různých předplatných.

![Síťová topologie pro spravovanou instanci izolovaná od místní sítě](media/resource-network-topologies/mi-isolated-workload.png)

**Požadavky**

- Virtuální síť, kterou Azure Database Migration Service používá pro tento scénář, musí být také připojená k místní síti pomocí ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)).
- Nastavte [partnerský vztah sítě](../virtual-network/virtual-network-peering-overview.md) virtuální sítě mezi virtuální sítí použitou pro SPRAVOVANOU instanci SQL a Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrace z cloudu do cloudu: sdílená virtuální síť

Tuto topologii použijte, pokud je zdrojový SQL Server hostovaný na virtuálním počítači Azure a sdílí stejnou virtuální síť se spravovanými instancemi SQL a Azure Database Migration Service.

![Síťová topologie pro migrace z cloudu do cloudu se sdílenou virtuální sítí](media/resource-network-topologies/cloud-to-cloud.png)

**Požadavky**

- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrace z cloudu do cloudu: izolovaná virtuální síť

Tuto topologii sítě použijte v případě, že vaše prostředí vyžaduje jeden nebo více následujících scénářů:

- Spravovaná instance SQL se zřídí v izolované virtuální síti.
- Pokud jsou nastavené zásady řízení přístupu na základě role Azure (Azure RBAC) a potřebujete omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem spravované instance SQL.
- Virtuální sítě používané pro spravovanou instanci SQL a Azure Database Migration Service jsou v různých předplatných.

![Síťová topologie pro migrace z cloudu do cloudu s izolovanou virtuální sítí](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Požadavky**

- Nastavte [partnerský vztah sítě](../virtual-network/virtual-network-peering-overview.md) virtuální sítě mezi virtuální sítí použitou pro SPRAVOVANOU instanci SQL a Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| **NÁZEV**   | **PŘÍSTAVNÍ** | **PROTOKOL** | **Zdrojová** | **TABULKA** | **KROKY** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Všechny      | Všechny          | PODSÍŤ DMS | Všechny             | Povolit      |

## <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| **NÁZEV**                  | **PŘÍSTAVNÍ**                                              | **PROTOKOL** | **Zdrojová** | **TABULKA**           | **KROKY** | **Důvod pravidla**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Všechny        | Všechny                       | Povolit      | Komunikace roviny správy prostřednictvím Service Bus. <br/>(Pokud je povolen partnerský vztah Microsoftu, možná toto pravidlo nebudete potřebovat.)                                                             |
| Storage                   | 443, ServiceTag: Storage                              | TCP          | Všechny        | Všechny                       | Povolit      | Rovina správy s využitím úložiště objektů BLOB v Azure. <br/>(Pokud je povolen partnerský vztah Microsoftu, možná toto pravidlo nebudete potřebovat.)                                                             |
| Diagnostika               | 443, ServiceTag: AzureMonitor                         | TCP          | Všechny        | Všechny                       | Povolit      | DMS pomocí tohoto pravidla shromažďuje diagnostické informace pro účely řešení potíží. <br/>(Pokud je povolen partnerský vztah Microsoftu, možná toto pravidlo nebudete potřebovat.)                                                  |
| Zdrojový server SQL         | 1433 (nebo port TCP IP, na který SQL Server naslouchá) | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Připojení ke zdroji SQL Server z DMS <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.)                                                                                       |
| SQL Server pojmenované instance | 1434                                                  | UDP          | Všechny        | Adresní prostor místního prostředí | Povolit      | SQL Server připojení ke zdroji pojmenované instance z DMS <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.)                                                                        |
| Sdílená složka SMB                 | 445 (Pokud se neeeds scénář)                             | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Síťová sdílená složka SMB pro DMS pro ukládání záložních souborů databáze pro migrace do Azure SQL Database MI a SQL serverů na virtuálním počítači Azure <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.) |
| DMS_subnet                | Všechny                                                   | Všechny          | Všechny        | DMS_Subnet                | Povolit      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Viz také

- [Migrace SQL Server do spravované instance SQL](./tutorial-sql-server-to-managed-instance.md)
- [Přehled požadavků pro použití Azure Database Migration Service](./pre-reqs.md)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Další kroky

- Přehled Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](dms-overview.md).
- Aktuální informace o místní dostupnosti Azure Database Migration Service najdete na stránce [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .