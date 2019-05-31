---
title: Contoso migrace řady | Dokumentace Microsoftu
description: Poskytuje přehled scénářů společností Contoso používá k migraci jejich místní datacentra do Azure a strategie migrace.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: bf2a8dec919980db616cd927639cdd2f695720c5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238014"
---
# <a name="contoso-migration-series"></a>Seriál o migraci Contoso


Máme řady článků, které ukazuje, jak smyšlenou organizaci Contoso migruje místní infrastrukturu [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) cloudu. 

Obsahuje informace a scénáře, které ukazují, jak nastavit migraci infrastruktury a spouštět různé druhy migrace. Scénáře jejich složitost v průběhu. Články popisují, jak společnosti Contoso dokončení poslání migrace, ale ukazatele Obecné čtení a konkrétní pokyny jsou k dispozici v průběhu.

## <a name="migration-articles"></a>Články týkající se migrace

Články v této sérii jsou shrnuty v následující tabulce.  

- Každý scénář migrace doprovází mírně odlišné obchodních cílů, které určují strategii migrace.
- Pro každý scénář nasazení zajišťuje informace o obchodní faktory a cíle, navrhované architektura, postup k provedení migrace a doporučení pro čištění a další kroky po dokončení migrace.

**Článek** | **Podrobnosti** 
--- | --- 
[Článek 1: Přehled](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. 
[Článek 2: Nasazení infrastruktury Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. 
[Článek 3: Posouzení místních prostředků pro migraci do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. 
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migruje aplikace SmartHotel360. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. 
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso se dokončí migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí služby Site Recovery.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje svou aplikaci osTicket Linux do virtuálních počítačů Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. 
[Článek 9: Refaktorovat aplikace do webové aplikace Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migraci své aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure pomocí Pomocníka s migrací databáze.     
[Článek 10: Refaktorovat Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. 
[Článek 11: Refaktorovat a Team Foundation Server na službách Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](c https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. 
[Článek 13: Znovu sestavte aplikaci v Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB.  
[Článek 14: Škálování migrace do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. 


    

## <a name="next-steps"></a>Další postup

[Další informace o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrace do cloudu. 

