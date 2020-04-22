---
title: Řada migrace Contoso | Dokumenty společnosti Microsoft
description: Odkazy na scénáře migrace na příklad ucontoso pro migraci do Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676331"
---
# <a name="contoso-migration-series"></a>Seriál o migraci Contoso


Máme řadu článků, které ukazují, jak fiktivní organizace Contoso migruje svou místní infrastrukturu do cloudu [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Řada obsahuje scénáře, které ilustrují, jak nastavit migraci infrastruktury a jak spustit různé typy migrace. Scénáře rostou ve složitosti, jak postupují. Články ukazují, jak společnost Contoso zpracovává migraci, ale obecné pokyny a ukazatele jsou k dispozici v celém textu.

## <a name="migration-articles"></a>Články o migraci

Články v sérii jsou shrnuty v následující tabulce.  

- Každý scénář migrace je řízen mírně odlišnými obchodními požadavky, které určují strategii migrace.
- Pro každý scénář nasazení poskytujeme informace o obchodních ovladačích a cílech, navrhované architektuře, krocích k provedení migrace a doporučení pro vyčištění a další kroky po dokončení migrace.


**Článku** | **Podrobnosti** 
--- | --- 
[Článek 1: Přehled](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Přehled řady článků, strategie migrace společnosti Contoso a ukázkových aplikací, které se v řadě používají. 
[Článek 2: Nasazení infrastruktury Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso připravuje svou místní infrastrukturu a infrastrukturu Azure pro migraci. Stejná infrastruktura se používá pro všechny články v řadě. 
[Článek 3: Posouzení místních prostředků pro migraci do Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Společnost Contoso provádí hodnocení své místní aplikace SmartHotel360 spuštěné na vmware. Contoso vyhodnocuje virtuální počítače aplikací pomocí služby Azure Migrate a databáze serveru SQL Server aplikace pomocí Pomocníka pro migraci dat.
[Článek 4: Rehost aplikace na Azure VM a SQL Database spravované instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso pro svou místní aplikaci SmartHotel360 spouští migraci výtahu a přestřiku do Azure. Contoso migruje front-endový virtuální počítač aplikace pomocí [Migrace Azure](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migruje databázi aplikací do instance spravované databáze Azure SQL pomocí [služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Článek 5: Rehost aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje své virtuální počítače aplikace SmartHotel360 do virtuálních počítačů Azure pomocí služby Azure Migrate. 
[Článek 6: Rehost aplikace na virtuálních počítačích Azure a ve skupině dostupnosti SQL Server AlwaysOn](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso migruje aplikaci SmartHotel360. Contoso používá Azure Migrate k migraci virtuálních počítačů aplikace. Služba migrace databáze používá k migraci databáze aplikací do clusteru SERVERU SQL Server, který je chráněn skupinou dostupnosti AlwaysOn. 
[Článek 7: Rehost linuxové aplikace na virtuálních počítačích Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso dokončí migraci zdviženého a posunu své aplikace Linux osTicket do virtuálních počítačů Azure pomocí Migrace Azure.
[Článek 8: Rehost linuxové aplikace na virtuálních počítačích Azure a Azure Database pro MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje svou linuxovou aplikaci osTicket do virtuálních virtuálních aplikací Azure pomocí Migrace Azure. Migruje databázi aplikací do databáze Azure pro MySQ pomocí služby Migrace databáze Azure (zahrnuje alternativní možnost pomocí MySQL Workbench).
[Článek 9: Refaktoring aplikace ve webové aplikaci Azure a Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migruje svou aplikaci SmartHotel360 do webové aplikace Azure a migruje databázi aplikací do instance Azure SQL Serveru pomocí služby Migrace databáze Azure.
[Článek 10: Refaktoring aplikace pro Windows pomocí Azure App Services a SPRAVOVANÉ instance SQL](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migruje místní aplikaci založenou na Windows do webové aplikace Azure a migruje databázi aplikací do spravované instance Azure SQL pomocí služby Migrace databáze Azure.
[Článek 11: Refaktoring linuxové aplikace ve webové aplikaci Azure a Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migruje svou linuxovou aplikaci osTicket do webové aplikace Azure ve více oblastech Azure pomocí Azure Traffic Manageru integrovaného s GitHubem pro nepřetržité doručování. Contoso migruje databázi aplikací do instance Azure Database for MySQL. 
[Článek 12: Refaktorteam Foundation Server na Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migruje své místní nasazení team foundation serveru do služeb Azure DevOps services v Azure.
[Článek 13: Opětovné sestavení aplikace v Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso obnovuje svou aplikaci SmartHotel pomocí řady funkcí a služeb Azure, včetně Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB.
[Článek 14: Škálování migrace do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po vyzkoušení kombinace migrace contoso připravuje škálování na úplnou migraci do Azure.



## <a name="next-steps"></a>Další kroky

- [Přečtěte si o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migraci do cloudu.
- Informace o strategiích migrace pro jiné scénáře (páry zdroj/cíl) naleznete v [Průvodci migrací databáze](https://datamigration.microsoft.com/).
