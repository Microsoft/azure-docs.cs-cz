---
title: Řada migrace contoso | Microsoft Docs
description: Poskytuje přehled strategie migrace a scénářů používaných společností Contoso k migraci místního datacentra do Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939181"
---
# <a name="contoso-migration-series"></a>Seriál o migraci Contoso


Máme řadu článků, které demonstrují, jak fiktivní organizace contoso migruje místní infrastrukturu do [Microsoft Azureho](https://azure.microsoft.com/overview/what-is-azure/) cloudu. 

Řada obsahuje informace a scénáře, které ilustrují způsob nastavení migrace infrastruktury a spouštění různých typů migrace. Scénáře při jejich vývoji rostou v složitost. V článcích se dozvíte, jak společnost Contoso dokončuje migraci, ale v celém rozsahu jsou k dispozici ukazatelé pro obecné čtení a konkrétní pokyny.

## <a name="migration-articles"></a>Články migrace

Články v řadě jsou shrnuté v následující tabulce.  

- Každý scénář migrace je založený na trochu jiných obchodních cílech, které určují migrační strategii.
- Pro každý scénář nasazení poskytujeme informace o obchodních aspektech a cílech, navrženou architekturu, kroky pro realizaci migrace, doporučení pro vyčištění a další kroky po dokončení migrace.

**Článek** | **Podrobnosti** 
--- | --- 
[Článek 1: Přehled](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Přehled série článků, strategie migrace společnosti Contoso a ukázkových aplikací, které se používají v řadě. 
[Článek 2: nasazení infrastruktury Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Společnost Contoso připraví svoji místní infrastrukturu a infrastrukturu Azure pro migraci. Stejná infrastruktura se používá pro všechny články migrace v řadě. 
[Článek 3: posouzení místních prostředků pro migraci do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso spustí posouzení své místní aplikace SmartHotel360 běžící na VMware. Společnost Contoso posuzuje virtuální počítače aplikace pomocí služby Azure Migrate a SQL Server databáze aplikace pomocí Data Migration Assistant.
[Článek 4: opětovné hostování aplikace na virtuálním počítači Azure a SQL Database spravované instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso spustí pro svou místní aplikaci SmartHotel360 migraci do Azure. Společnost Contoso migruje front-end virtuální počítač aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Společnost Contoso migruje databázi aplikace do spravované instance Azure SQL Database pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Článek 5: opětovné hostování aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Společnost Contoso migruje své virtuální počítače své aplikace SmartHotel360 do virtuálních počítačů Azure pomocí služby Site Recovery. 
[Článek 6: opětovné hostování aplikace na virtuálních počítačích Azure a ve skupině dostupnosti služby SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Společnost Contoso migruje aplikaci SmartHotel360. Společnost Contoso používá Site Recovery k migraci virtuálních počítačů aplikace. Používá Database Migration Service k migraci databáze aplikace do clusteru SQL Server, který je chráněn skupinou dostupnosti AlwaysOn. 
[Článek 7: opětovné hostování aplikace pro Linux na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Společnost Contoso dokončí migraci své aplikace osTicket pro Linux do virtuálních počítačů Azure pomocí služby Site Recovery.
[Článek 8: opětovné hostování aplikace pro Linux na virtuálních počítačích Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Společnost Contoso migruje svou aplikaci pro Linux osTicket do virtuálních počítačů Azure pomocí Site Recovery. Migruje databázi aplikace do služby Azure Database for MySQL pomocí MySQL Workbenche. 
[Článek 9: refaktorování aplikace ve webové aplikaci Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Společnost Contoso migruje svou aplikaci v SmartHotel360 do webové aplikace Azure a migruje databázi aplikace do instance Azure SQL Server s využitím databáze Pomocník s migrací.     
[Článek 10: refaktorování aplikace pro Linux ve webové aplikaci Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Společnost Contoso migruje svou aplikaci pro Linux osTicket do webové aplikace Azure ve více oblastech Azure pomocí Azure Traffic Manager integrovaných s GitHubem pro průběžné doručování. Společnost Contoso migruje databázi aplikace do instance Azure Database for MySQL. 
[Článek 11: refaktoring Team Foundation Server v Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Společnost Contoso migruje své místní nasazení Team Foundation Server do Azure DevOps Services v Azure.
[Článek 12: Změna architekta aplikace v kontejnerech Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Společnost Contoso migruje svou aplikaci SmartHotel do Azure. Pak přearchitektí webovou vrstvu aplikace jako kontejner Windows spuštěný v Azure Service Fabric a databáze s Azure SQL Database.
[Článek 13: opětovné sestavení aplikace v Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso znovu sestaví svou aplikaci SmartHotel pomocí škály možností a služeb Azure, včetně Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB.
[Článek 14: škálování migrace do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po vyzkoušení kombinací migrace se contoso připraví na škálování na plnou migraci do Azure.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migraci do cloudu.
- Další informace o strategiích migrace pro jiné scénáře (páry zdroj/cíl) najdete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).
