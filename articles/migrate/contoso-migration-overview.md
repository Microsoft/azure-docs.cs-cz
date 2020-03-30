---
title: Řada migrace Contoso | Dokumenty společnosti Microsoft
description: Obsahuje přehled strategie migrace a scénářů, které contoso používá k migraci jejich místního datového centra do Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78939181"
---
# <a name="contoso-migration-series"></a>Seriál o migraci Contoso


Máme řadu článků, které ukazují, jak fiktivní organizace Contoso migruje místní infrastrukturu do cloudu [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Řada obsahuje informace a scénáře, které ilustrují, jak nastavit migraci infrastruktury a spustit různé typy migrace. Scénáře rostou ve složitosti, jak postupují. Články ukazují, jak společnost Contoso dokončí svou migrační misi, ale v celém textu jsou k dispozici odkazy na obecné čtení a konkrétní pokyny.

## <a name="migration-articles"></a>Články o migraci

Články v sérii jsou shrnuty v následující tabulce.  

- Každý scénář migrace je založený na trochu jiných obchodních cílech, které určují migrační strategii.
- Pro každý scénář nasazení poskytujeme informace o obchodních aspektech a cílech, navrženou architekturu, kroky pro realizaci migrace, doporučení pro vyčištění a další kroky po dokončení migrace.

**Článku** | **Podrobnosti** 
--- | --- 
[Článek 1: Přehled](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Přehled řady článků, strategie migrace společnosti Contoso a ukázkových aplikací, které se v řadě používají. 
[Článek 2: Nasazení infrastruktury Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso připravuje místní infrastrukturu a infrastrukturu Azure pro migraci. Stejná infrastruktura se používá pro všechny články migrace v řadě. 
[Článek 3: Posouzení místních prostředků pro migraci do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Společnost Contoso provádí hodnocení své místní aplikace SmartHotel360 spuštěné na vmware. Contoso vyhodnocuje virtuální počítače aplikací pomocí služby Azure Migrate a databáze serveru SQL Server aplikace pomocí Pomocníka pro migraci dat.
[Článek 4: Rehost aplikace na Azure VM a SQL Database spravované instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso pro svou místní aplikaci SmartHotel360 spouští migraci výtahu a přestřiku do Azure. Contoso migruje front-endový virtuální počítač aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migruje databázi aplikací do instance spravované databáze Azure SQL pomocí [služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Článek 5: Rehost aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje své virtuální počítače aplikace SmartHotel360 do virtuálních počítačů Azure pomocí služby Site Recovery. 
[Článek 6: Rehost aplikace na virtuálních počítačích Azure a ve skupině dostupnosti SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migruje aplikaci SmartHotel360. Společnost Contoso používá k migraci virtuálních aplikací site recovery. Služba migrace databáze používá k migraci databáze aplikací do clusteru SERVERU SQL Server, který je chráněn skupinou dostupnosti AlwaysOn. 
[Článek 7: Rehost linuxové aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso dokončí migraci zdvižení a posunu své aplikace Linux osTicket do virtuálních stránek Azure pomocí služby Site Recovery.
[Článek 8: Rehost linuxové aplikace na virtuálních počítačích Azure a Azure Database pro MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje svou linuxovou aplikaci osTicket do virtuálních virtuálních stránek Azure pomocí site recovery. Migruje databázi aplikace do služby Azure Database for MySQL pomocí MySQL Workbenche. 
[Článek 9: Refaktoring aplikace ve webové aplikaci Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migruje svou aplikaci SmartHotel360 do webové aplikace Azure a migruje databázi aplikací do instance Azure SQL Serveru pomocí Pomocníka pro migraci databáze.     
[Článek 10: Refaktoring linuxové aplikace ve webové aplikaci Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migruje svou linuxovou aplikaci osTicket do webové aplikace Azure ve více oblastech Azure pomocí Azure Traffic Manageru integrovaného s GitHubem pro nepřetržité doručování. Contoso migruje databázi aplikací do instance Azure Database for MySQL. 
[Článek 11: Refaktorovat Team Foundation Server na Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migruje své místní nasazení team foundation serveru do služeb Azure DevOps services v Azure.
[Článek 12: Rearchitect aplikace v kontejnerech Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migruje svou aplikaci SmartHotel do Azure. Pak rearchitects webové vrstvy aplikace jako kontejner Windows běží v Azure Service Fabric a databáze s Azure SQL Database.
[Článek 13: Opětovné sestavení aplikace v Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso obnovuje svou aplikaci SmartHotel pomocí řady funkcí a služeb Azure, včetně Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB.
[Článek 14: Škálování migrace do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po vyzkoušení kombinace migrace contoso připravuje škálování na úplnou migraci do Azure.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migraci do cloudu.
- Informace o strategiích migrace pro jiné scénáře (páry zdroj/cíl) naleznete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).
