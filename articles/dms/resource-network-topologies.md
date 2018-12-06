---
title: Síťové topologie pro migrace Azure SQL Database Managed Instance pomocí Azure Database Migration Service | Dokumentace Microsoftu
description: Přečtěte si požadavky na zdrojovou a cílovou konfiguraci pro služby Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/8/2018
ms.openlocfilehash: cd62b60718a35aed9129db61413086266bb9f2c7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52971075"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Síťové topologie pro migrace Azure SQL DB Managed Instance pomocí Azure Database Migration Service
Tento článek popisuje různé topologie sítě, které Azure Database Migration Service můžete pracovat na poskytovat komplexní migrace z místních SQL serverů do Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Managed Instance nakonfigurované pro hybridní úlohy 
Tuto topologii použijte, pokud Azure SQL Database Managed Instance je připojený k vaší místní síti. Tento přístup poskytuje nejvíce zjednodušené směrování sítě a vrací data maximální propustnost během migrace.

![Topologie sítě pro hybridní úlohy](media/resource-network-topologies/hybrid-workloads.png)

**Požadavky**
- V tomto scénáři Azure SQL Database Managed Instance a instance služby Azure Database Migration Service se vytvoří ve stejné virtuální síti Azure, ale použít různé podsítě.  
- Virtuální sítě v tomto scénáři používají také připojené k místní síti pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Managed Instance izolovaně od místní sítě
Tato topologie sítě použijte, pokud vaše prostředí vyžaduje nejméně jeden z následujících scénářů:
- Azure SQL Database Managed Instance je izolovaná od místního připojení, ale vaše instance služby Azure Database Migration Service je připojený k místní síti.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) v místě a je potřeba omezit uživatele při přístupu ke stejnému předplatnému, který je hostitelem Azure SQL Database Managed Instance.
- Virtuální sítě používá pro Azure SQL Database Managed Instance a Azure Database Migration Service jsou v různých předplatných.

![Topologie sítě pro Managed Instance, izolovaná z místní sítě](media/resource-network-topologies/mi-isolated-workload.png)

**Požadavky**
- Virtuální síť, která používá Azure Database Migration Service pro tento scénář musí být také připojený k místní síti pomocí (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Nastavit [sítě VNET peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro Azure SQL Database Managed Instance a Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrace do cloudu cloudu: sdílený virtuální sítě

Tuto topologii použijte, pokud zdrojového systému SQL Server je hostovaný na Virtuálním počítači Azure a sdílí stejné virtuální síti s Azure SQL Database Managed Instance a Azure Database Migration Service.

![Síťové topologie pro migrace do cloudu do cloudu pomocí sdílené virtuální sítě](media/resource-network-topologies/cloud-to-cloud.png)

**Požadavky**
- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrace do cloudu v cloudu: izolované virtuální sítě

Tato topologie sítě použijte, pokud vaše prostředí vyžaduje nejméně jeden z následujících scénářů:
- Azure SQL Database Managed Instance je zřizována jako izolované virtuální sítě.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) v místě a je potřeba omezit uživatele při přístupu ke stejnému předplatnému, který je hostitelem Azure SQL Database Managed Instance.
- Virtuální sítě pro Azure SQL Database Managed Instance a Azure Database Migration Service jsou v různých předplatných.

![Síťové topologie pro migrace do cloudu do cloudu pomocí izolované virtuální sítě](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Požadavky**
- Nastavit [sítě VNET peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro Azure SQL Database Managed Instance a Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| **JMÉNO**   | **PORT** | **PROTOKOL** | **ZDROJ** | **CÍL** | **AKCE** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Všechny      | Všechny          | DMS PODSÍTĚ | Všechny             | Povolit      |

## <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| **JMÉNO**                  | **PORT**                                              | **PROTOKOL** | **ZDROJ** | **CÍL**           | **AKCE** | **Důvod pro pravidlo**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Správa                | 443,9354                                              | TCP          | Všechny        | Všechny                       | Povolit      | Komunikace roviny správy prostřednictvím služby Service bus a Azure blob storage. <br/>(Pokud je zapnutá partnerský vztah Microsoftu, možná nebudete potřebovat toto pravidlo.)                                                             |
| Diagnostika               | 12000                                                 | TCP          | Všechny        | Všechny                       | Povolit      | DMS toto pravidlo používá ke shromažďování diagnostických informací pro účely odstraňování potíží.                                                                                                                      |
| Zdrojového SQL serveru         | 1433 (nebo port TCP/IP, který SQL Server naslouchá na) | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Zdroj připojení serveru SQL Server z DMS <br/>(Pokud máte připojení site-to-site, možná nebudete potřebovat toto pravidlo.)                                                                                       |
| Pojmenovanou instanci systému SQL Server | 1434                                                  | UDP          | Všechny        | Adresní prostor místního prostředí | Povolit      | SQL Server s názvem instance zdroje připojení z DMS <br/>(Pokud máte připojení site-to-site, možná nebudete potřebovat toto pravidlo.)                                                                        |
| Sdílené složky SMB                 | 445                                                   | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | SMB síťové sdílené složky pro DMS, aby mohl uložit databázi záložních souborů pro migrace na SQL servery a Azure SQL Database, MI na virtuálním počítači Azure <br/>(Pokud máte připojení site-to-site, možná nebudete potřebovat tohoto pravidla). |
| DMS_subnet                | Všechny                                                   | Všechny          | Všechny        | DMS_Subnet                | Povolit      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Viz také
- [Migrace SQL serveru do spravované Instance Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled požadavků pro používání služby Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a regionální dostupnost ve verzi Public Preview, najdete v článku [co je služba Azure Database Migration ve verzi Preview](dms-overview.md). 