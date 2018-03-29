---
title: Síťová topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace | Microsoft Docs
description: Další konfigurace zdrojové a cílové služby migrace databáze.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 5904864ffba656dab17e1549ed9832be4258a67f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace
V tomto článku se dozvíte o různé topologie sítě, které služba Azure databáze migrace můžete pracovat a poskytuje prostředí bezproblémovou migraci z místních SQL serverů do Azure SQL Database spravované Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL spravované Instance databáze nakonfigurované pro hybridní úlohy 
Tato topologie použijte, pokud vaše Azure SQL Database spravované Instance je připojen k síti na pracovišti. Tento přístup poskytuje nejvíc zjednodušené směrování sítě a výsledkem propustnost maximální dat během migrace.

![Topologie sítě pro hybridní úlohy](media\resource-network-topologies\hybrid-workloads.png)

**Požadavky**
- V tomto scénáři spravované Instance databáze Azure SQL a instanci Azure databáze migrace služby se vytvoří ve stejné virtuální síti Azure, ale používají jinou podsítí.  
- Virtuální sítě v tomto scénáři používají také připojené k místní síti pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure Instance databáze serveru SQL spravované izolované z místní sítě
Tato topologie sítě použijte, pokud vaše prostředí vyžaduje jeden nebo více z následujících scénářů:
- Spravované Instance databáze Azure SQL je izolovaná od místní připojení, ale instanci služby migrace databáze Azure je připojen k místní síti.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) na místě a je nutné omezit uživatele k přístupu do stejného předplatného, který je hostitelem spravované Instance databáze Azure SQL.
- Virtuální sítě používá pro spravované Instance databáze Azure SQL a službu Azure databáze migrace jsou v různých předplatných.

![Topologie sítě pro spravované Instance izolované z místní sítě](media\resource-network-topologies\mi-isolated-workload.png)

**Požadavky**
- Virtuální síť, který používá služba migrace databáze Azure pro tento scénář musí být taky připojen k místní síti pomocí (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Je nutné nastavit [partnerský vztah sítě VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítě používá pro Azure spravované Instance databáze SQL a službu Azure databáze migrace.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrace cloudu cloudu: sdílené virtuální sítě

Tato topologie použijte, pokud zdroje systému SQL Server je hostovaná ve virtuálním počítači Azure a sdílí stejnou virtuální síť s Azure spravované Instance databáze SQL a službu Azure databáze migrace.

![Topologie sítě pro Cloud-Cloud migrace s sdílené virtuální sítě](media\resource-network-topologies\cloud-to-cloud.png)

**Požadavky**
- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Cloudu pro cloud migrace: izolované virtuální sítě

Tato topologie sítě použijte, pokud vaše prostředí vyžaduje jeden nebo více z následujících scénářů:
- Spravované Instance databáze Azure SQL je zajištěna v izolované virtuální sítě.
- Pokud jsou zásady na základě řízení přístupu Role (RBAC) na místě a je nutné omezit uživatele k přístupu do stejného předplatného, který je hostitelem spravované Instance databáze Azure SQL.
- Virtuální sítě používá pro Azure spravované Instance databáze SQL a službu Azure databáze migrace jsou v různých předplatných.

![Topologie sítě pro Cloud-Cloud migrace s izolované virtuální sítě](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Požadavky**
- Je nutné nastavit [partnerský vztah sítě VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítě používá pro Azure spravované Instance databáze SQL a službu Azure databáze migrace.


## <a name="see-also"></a>Viz také
- [Migrace systému SQL Server na spravované Instance databáze Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled požadavků na používání služby Azure databáze migrace](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další postup
Přehled služby Azure databáze migrace a místní dostupnosti během verzi Public Preview, najdete v článku [co je Azure databáze migrace služby Preview](dms-overview.md). 