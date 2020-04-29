---
title: Řada migrace contoso | Microsoft Docs
description: Odkazy na příklady scénářů migrace společnosti Contoso, migrace do Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676331"
---
# <a name="contoso-migration-series"></a>Seriál o migraci Contoso


Máme řadu článků, které ukazují, jak fiktivní organizace contoso migruje svou místní infrastrukturu do cloudu [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

Řada obsahuje scénáře, které ukazují, jak nastavit migraci infrastruktury, a jak spustit různé typy migrace. Scénáře při jejich vývoji rostou v složitost. Články ukazují, jak společnost Contoso zpracovává migraci, ale obecné pokyny a ukazatele jsou k dispozici v celém.

## <a name="migration-articles"></a>Články migrace

Články v řadě jsou shrnuté v následující tabulce.  

- Jednotlivé scénáře migrace jsou založené na mírně odlišných podnikových požadavcích, které určují strategii migrace.
- Pro každý scénář nasazení poskytujeme informace o obchodních ovladačích a cílech, navržené architektuře, krocích k provedení migrace a doporučeních pro vyčištění a další kroky po dokončení migrace.


**Předmětu** | **Zobrazí** 
--- | --- 
[Článek 1: Přehled](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Přehled série článků, strategie migrace společnosti Contoso a ukázkových aplikací, které se používají v řadě. 
[Článek 2: nasazení infrastruktury Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Společnost Contoso připraví svoji místní infrastrukturu a infrastrukturu Azure pro migraci. Stejná infrastruktura se používá pro všechny články v řadě. 
[Článek 3: posouzení místních prostředků pro migraci do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso spustí posouzení své místní aplikace SmartHotel360 běžící na VMware. Společnost Contoso posuzuje virtuální počítače aplikace pomocí služby Azure Migrate a SQL Server databáze aplikace pomocí Data Migration Assistant.
[Článek 4: opětovné hostování aplikace na virtuálním počítači Azure a SQL Database spravované instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso spustí pro svou místní aplikaci SmartHotel360 migraci do Azure. Společnost Contoso migruje front-end virtuální počítač aplikace pomocí [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Společnost Contoso migruje databázi aplikace do spravované instance Azure SQL Database pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Článek 5: opětovné hostování aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Společnost Contoso migruje své virtuální počítače své aplikace SmartHotel360 do virtuálních počítačů Azure pomocí služby Azure Migrate. 
[Článek 6: opětovné hostování aplikace na virtuálních počítačích Azure a ve skupině dostupnosti služby SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Společnost Contoso migruje aplikaci SmartHotel360. Společnost Contoso používá Azure Migrate k migraci virtuálních počítačů aplikace. Používá Database Migration Service k migraci databáze aplikace do clusteru SQL Server, který je chráněn skupinou dostupnosti AlwaysOn. 
[Článek 7: opětovné hostování aplikace pro Linux na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Společnost Contoso dokončí migraci své aplikace osTicket pro Linux do virtuálních počítačů Azure pomocí Azure Migrate.
[Článek 8: opětovné hostování aplikace pro Linux na virtuálních počítačích Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Společnost Contoso migruje svou aplikaci pro Linux osTicket do virtuálních počítačů Azure pomocí Azure Migrate. Migruje databázi aplikace do služby Azure Database for MySQ pomocí Azure Database Migration Service (zahrnuje alternativní možnost použití aplikace MySQL Workbench).
[Článek 9: refaktorování aplikace ve webové aplikaci Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Společnost Contoso migruje svou aplikaci v SmartHotel360 do webové aplikace Azure a migruje databázi aplikace do instance Azure SQL Server pomocí Azure Database Migration Service.
[Článek 10: refaktorování aplikace pro Windows pomocí Azure App Services a spravované instance SQL](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Společnost Contoso migruje místní aplikaci pro Windows do webové aplikace Azure a migruje databázi aplikace do spravované instance Azure SQL pomocí Azure Database Migration Service.
[Článek 11: refaktorování aplikace pro Linux ve webové aplikaci Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Společnost Contoso migruje svou aplikaci pro Linux osTicket do webové aplikace Azure ve více oblastech Azure pomocí Azure Traffic Manager integrovaných s GitHubem pro průběžné doručování. Společnost Contoso migruje databázi aplikace do instance Azure Database for MySQL. 
[Článek 12: refaktoring Team Foundation Server v Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Společnost Contoso migruje své místní nasazení Team Foundation Server do Azure DevOps Services v Azure.
[Článek 13: opětovné sestavení aplikace v Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Společnost Contoso znovu sestaví svou aplikaci SmartHotel s využitím nejrůznějších funkcí a služeb Azure, včetně Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB.
[Článek 14: škálování migrace do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po vyzkoušení kombinací migrace se contoso připraví na škálování na plnou migraci do Azure.



## <a name="next-steps"></a>Další kroky

- [Přečtěte si o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migraci do cloudu.
- Přečtěte si o strategiích migrace pro jiné scénáře (páry zdroj/cíl) v [Průvodci migrací databáze](https://datamigration.microsoft.com/).
