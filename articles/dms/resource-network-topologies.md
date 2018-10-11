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
ms.date: 10/10/2018
ms.openlocfilehash: 39bcea36f3599530413aa9fc4dbb308ee2fb1681
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49066849"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Síťové topologie pro migrace Azure SQL DB Managed Instance pomocí Azure Database Migration Service
Tento článek popisuje různé topologie sítě, které Azure Database Migration Service můžete pracovat na poskytovat komplexní migrace z místních SQL serverů do Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Managed Instance nakonfigurované pro hybridní úlohy 
Tuto topologii použijte, pokud Azure SQL Database Managed Instance je připojený k vaší místní síti. Tento přístup poskytuje nejvíce zjednodušené směrování sítě a vrací data maximální propustnost během migrace.

![Topologie sítě pro hybridní úlohy](media\resource-network-topologies\hybrid-workloads.png)

**Požadavky**
- V tomto scénáři Azure SQL Database Managed Instance a instance služby Azure Database Migration Service se vytvoří ve stejné virtuální síti Azure, ale použít různé podsítě.  
- Virtuální sítě v tomto scénáři používají také připojené k místní síti pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Managed Instance izolovaně od místní sítě
Tato topologie sítě použijte, pokud vaše prostředí vyžaduje nejméně jeden z následujících scénářů:
- Azure SQL Database Managed Instance je izolovaná od místního připojení, ale vaše instance služby Azure Database Migration Service je připojený k místní síti.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) v místě a je potřeba omezit uživatele při přístupu ke stejnému předplatnému, který je hostitelem Azure SQL Database Managed Instance.
- Virtuální sítě používá pro Azure SQL Database Managed Instance a Azure Database Migration Service jsou v různých předplatných.

![Topologie sítě pro Managed Instance, izolovaná z místní sítě](media\resource-network-topologies\mi-isolated-workload.png)

**Požadavky**
- Virtuální síť, která používá Azure Database Migration Service pro tento scénář musí být také připojený k místní síti pomocí (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Nastavit [sítě VNET peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro Azure SQL Database Managed Instance a Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrace do cloudu cloudu: sdílený virtuální sítě

Tuto topologii použijte, pokud zdrojového systému SQL Server je hostovaný na Virtuálním počítači Azure a sdílí stejné virtuální síti s Azure SQL Database Managed Instance a Azure Database Migration Service.

![Síťové topologie pro migrace do cloudu do cloudu pomocí sdílené virtuální sítě](media\resource-network-topologies\cloud-to-cloud.png)

**Požadavky**
- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrace do cloudu v cloudu: izolované virtuální sítě

Tato topologie sítě použijte, pokud vaše prostředí vyžaduje nejméně jeden z následujících scénářů:
- Azure SQL Database Managed Instance je zřizována jako izolované virtuální sítě.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) v místě a je potřeba omezit uživatele při přístupu ke stejnému předplatnému, který je hostitelem Azure SQL Database Managed Instance.
- Virtuální sítě pro Azure SQL Database Managed Instance a Azure Database Migration Service jsou v různých předplatných.

![Síťové topologie pro migrace do cloudu do cloudu pomocí izolované virtuální sítě](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Požadavky**
- Nastavit [sítě VNET peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro Azure SQL Database Managed Instance a Azure Database Migration Service.


## <a name="see-also"></a>Viz také
- [Migrace SQL serveru do spravované Instance Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled požadavků na používání Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a regionální dostupnost ve verzi Public Preview, najdete v článku [co je služba Azure Database Migration ve verzi Preview](dms-overview.md). 