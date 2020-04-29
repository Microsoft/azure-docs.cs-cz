---
title: Síťové topologie pro migrace spravované instance SQL
titleSuffix: Azure Database Migration Service
description: Seznamte se se zdrojovými a cílovými konfiguracemi pro Azure SQL Database migrace spravované instance pomocí Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78254961"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Síťové topologie pro migrace spravované instance služby Azure SQL DB pomocí Azure Database Migration Service

Tento článek pojednává o různých topologiích sítě, se kterými Azure Database Migration Service může pracovat, a poskytuje tak komplexní prostředí pro migraci z místních SQL serverů pro Azure SQL Database spravovanou instanci.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database spravovaná instance nakonfigurovaná pro hybridní úlohy 

Tuto topologii použijte, pokud je vaše Azure SQL Database spravovaná instance připojená k vaší místní síti. Tento přístup poskytuje nejjednodušší síťové směrování a poskytuje maximální propustnost dat během migrace.

![Síťová topologie pro hybridní úlohy](media/resource-network-topologies/hybrid-workloads.png)

**Požadavky**

- V tomto scénáři se Azure SQL Database spravovaná instance a instance Azure Database Migration Service vytvoří ve stejné Microsoft Azure Virtual Network, ale používají jiné podsítě.  
- Virtuální síť použitá v tomto scénáři je taky připojená k místní síti pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database spravovaná instance izolovaná od místní sítě

Tuto topologii sítě použijte v případě, že vaše prostředí vyžaduje jeden nebo více následujících scénářů:

- Azure SQL Database spravovaná instance je izolovaná od místního připojení, ale vaše instance Azure Database Migration Service je připojená k místní síti.
- Pokud jsou zásady Access Control na základě role (RBAC) na místě a potřebujete omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem Azure SQL Database spravované instance.
- Virtuální sítě používané pro Azure SQL Database Managed instance a Azure Database Migration Service jsou v různých předplatných.

![Síťová topologie pro spravovanou instanci izolovaná od místní sítě](media/resource-network-topologies/mi-isolated-workload.png)

**Požadavky**

- Virtuální síť, kterou Azure Database Migration Service používá pro tento scénář, musí být také připojená k místní síti pomocí (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)).
- Nastavte [partnerský vztah sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuální sítě mezi virtuální sítí použitou pro Azure SQL Database spravovanou instanci a Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrace z cloudu do cloudu: sdílená virtuální síť

Tuto topologii použijte, pokud je zdrojový SQL Server hostovaný na virtuálním počítači Azure a sdílí stejnou virtuální síť s Azure SQL Database spravované instance a Azure Database Migration Service.

![Síťová topologie pro migrace z cloudu do cloudu se sdílenou virtuální sítí](media/resource-network-topologies/cloud-to-cloud.png)

**Požadavky**

- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrace z cloudu do cloudu: izolovaná virtuální síť

Tuto topologii sítě použijte v případě, že vaše prostředí vyžaduje jeden nebo více následujících scénářů:

- Azure SQL Database Managed instance se zřídí v izolované virtuální síti.
- Pokud jsou zásady Access Control na základě role (RBAC) na místě a potřebujete omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem Azure SQL Database spravované instance.
- Virtuální sítě používané pro Azure SQL Database spravované instance a Azure Database Migration Service jsou v různých předplatných.

![Síťová topologie pro migrace z cloudu do cloudu s izolovanou virtuální sítí](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Požadavky**

- Nastavte [partnerský vztah sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuální sítě mezi virtuální sítí použitou pro Azure SQL Database spravovanou instanci a Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| **NÁZEV**   | **PŘÍSTAVNÍ** | **PROTOKOL** | **Zdrojová** | **TABULKA** | **KROKY** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Všechny      | Všechny          | PODSÍŤ DMS | Všechny             | Povolit      |

## <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| **NÁZEV**                  | **PŘÍSTAVNÍ**                                              | **PROTOKOL** | **Zdrojová** | **TABULKA**           | **KROKY** | **Důvod pravidla**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| správa                | 443, 9354                                              | TCP          | Všechny        | Všechny                       | Povolit      | Komunikace roviny správy prostřednictvím Service Bus a úložiště objektů BLOB v Azure. <br/>(Pokud je povolen partnerský vztah Microsoftu, možná toto pravidlo nebudete potřebovat.)                                                             |
| Diagnostika               | 12000                                                 | TCP          | Všechny        | Všechny                       | Povolit      | DMS pomocí tohoto pravidla shromažďuje diagnostické informace pro účely řešení potíží.                                                                                                                      |
| Zdrojový server SQL         | 1433 (nebo port TCP IP, na který SQL Server naslouchá) | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Připojení ke zdroji SQL Server z DMS <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.)                                                                                       |
| SQL Server pojmenované instance | 1434                                                  | UDP          | Všechny        | Adresní prostor místního prostředí | Povolit      | SQL Server připojení ke zdroji pojmenované instance z DMS <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.)                                                                        |
| Sdílená složka SMB                 | 445                                                   | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Síťová sdílená složka SMB pro DMS pro ukládání záložních souborů databáze pro migrace do Azure SQL Database MI a SQL serverů na virtuálním počítači Azure <br/>(Pokud máte připojení Site-to-site, možná toto pravidlo nebudete potřebovat.) |
| DMS_subnet                | Všechny                                                   | Všechny          | Všechny        | DMS_Subnet                | Povolit      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Viz také

- [Migrace SQL Server do Azure SQL Database spravované instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled požadavků pro použití Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další kroky

- Přehled Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](dms-overview.md).
- Aktuální informace o místní dostupnosti Azure Database Migration Service najdete na stránce [Dostupné produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .
