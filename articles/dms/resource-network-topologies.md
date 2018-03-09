---
title: "Síťová topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace | Microsoft Docs"
description: "Další konfigurace zdrojové a cílové služby migrace databáze."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Síťové topologie pro Azure SQL DB spravované Instance migrace pomocí služby Azure databáze migrace
V tomto článku se dozvíte o různé topologie sítě, aby služba migrace databáze Azure můžete pracovat a poskytuje prostředí bezproblémovou migraci na Azure SQL Database spravované Instance z místní servery SQL.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL spravované Instance databáze nakonfigurované pro hybridní úlohy 
Tato topologie použijte, pokud vaše Azure SQL Database spravované Instance je připojen k síti na pracovišti. Tento přístup poskytuje nejvíc zjednodušené směrování sítě a výsledkem propustnost maximální dat během migrace.

![Topologie sítě pro hybridní úlohy](media\resource-network-topologies\hybrid-workloads.png)

**Požadavky**
- V tomto scénáři spravované Instance databáze Azure SQL a instanci Azure databáze migrace služby se vytvoří ve stejné virtuální síti Azure, ale používají jinou podsítí.  
- Virtuální síť používá v tomto scénáři je také připojené k místní síti pomocí ExpressRoute nebo VPN.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure Instance databáze serveru SQL spravované izolované z místní sítě
Tato topologie sítě použijte, pokud vaše prostředí vyžaduje jeden nebo více z následujících scénářů:
- Spravované Instance databáze Azure SQL je izolovaná od místní připojení, ale instanci služby migrace databáze Azure je připojen k místní síti.
- Zásady řízení přístupu na základě (RBAC) role jsou na místě a omezit přístup uživatelů ve stejném předplatném, který je hostitelem spravované Instance databáze Azure SQL.
- Virtuální sítě používá pro provádění příkazu spravované Instance databáze SQL Azure a Azure Service migrace databáze jsou v různých předplatných.

![Topologie sítě pro spravované Instance izolované z místní sítě](media\resource-network-topologies\mi-isolated-workload.png)

**Požadavky**
- Virtuální síť, který používá služba migrace databáze Azure pro tento scénář musí také připojený k místní síti pomocí ExpressRoute nebo VPN.
- Vytvoření sítě VNET partnerský vztah mezi virtuální sítě používá pro provádění příkazu spravované Instance databáze SQL Azure a služba migrace databáze Azure.


## <a name="cloud-to-cloud-migrations"></a>Cloudu do cloudu migrace
Tato topologie použijte, pokud zdroje systému SQL Server je hostovaná ve virtuálním počítači Azure.

![Topologie sítě pro Cloud-Cloud migrace](media\resource-network-topologies\cloud-to-cloud.png)

**Požadavky**
- Vytvoření sítě VNET partnerský vztah mezi virtuální sítě používá pro provádění příkazu spravované Instance databáze SQL Azure a služba migrace databáze Azure.

## <a name="see-also"></a>Viz také
- [Migrace systému SQL Server na spravované Instance databáze Azure SQL](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled požadavků na používání služby Azure databáze migrace](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí webu Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další postup
Přehled služby Azure databáze migrace a místní dostupnosti během verzi Public Preview, najdete v článku [co je Azure databáze migrace služby Preview](dms-overview.md). 