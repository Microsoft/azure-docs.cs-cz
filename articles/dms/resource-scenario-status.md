---
title: Stav scénáře migrace databází
titleSuffix: Azure Database Migration Service
description: Přečtěte si o stavu scénářů migrace podporovaných nástrojem Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 6c1a0853dc59b2e2ceabfd47d81aac364a2b5716
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589427"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stav migračních scénářů, které podporuje Azure Database Migration Service

Azure Database Migration Service je navržený tak, aby podporoval různé scénáře migrace (páry zdroj/cíl) pro offline (jednorázovou) i online (nepřetržitou synchronizaci) migrace. Pokrytí scénáře, které poskytuje Azure Database Migration Service, se v průběhu času rozšiřuje. V pravidelných intervalech se přidávají nové scénáře. Tento článek popisuje scénáře migrace, které jsou aktuálně podporovány nástrojem Azure Database Migration Service a stav (privátní verze Preview, verze Public Preview nebo Obecná dostupnost) pro každý scénář.

## <a name="offline-versus-online-migrations"></a>Offline online migrace

Pomocí Azure Database Migration Service můžete provést offline nebo online migraci. Při *offline* migraci začíná výpadek aplikace ve stejnou dobu, kdy se spustí migrace. Pokud chcete omezit prostoje na dobu potřebnou k obnově prostředí po dokončení migrace, použijte *online* migraci. Doporučuje se otestovat offline migraci, abyste zjistili, jestli je výpadky přijatelné; Pokud ne, proveďte online migraci.

## <a name="migration-scenario-support"></a>Podpora scénářů migrace

Následující tabulky ukazují, které scénáře migrace jsou podporované při použití Azure Database Migration Service.

> [!NOTE]
> Pokud se v uživatelském rozhraní nezobrazí scénář uvedený jako podporovaný, obraťte se prosím na alias služby [Azure Database migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) , kde najdete další informace.

> [!IMPORTANT]
> Chcete-li zobrazit všechny scénáře, které jsou aktuálně podporovány nástrojem Azure Database Migration Service v privátní verzi Preview, přečtěte si [Web DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Podpora migrace v režimu offline (jednorázová)

Následující tabulka ukazuje podporu offline migrací Azure Database Migration Service.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | SLUŽBA RDS SQL | × |  |
|   | Oracle | × |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | SLUŽBA RDS SQL | × |  |
|   | Oracle | × |   |
| **Virtuální počítač Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle | × |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | × |   |
|   | Služba RDS MySQL | × |   |
| **Azure DB pro PostgreSQL – jeden server** | PostgreSQL | × |
|  | PostgreSQL RDS | × |   |
| **Azure DB pro PostgreSQL – škálovatelné (Citus)** | PostgreSQL | × |
|  | PostgreSQL RDS | × |   |

### <a name="online-continuous-sync-migration-support"></a>Podpora migrace online (nepřetržitá synchronizace)

Následující tabulka uvádí Azure Database Migration Service podporu pro online migrace.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | × |  |
|   | SLUŽBA RDS SQL | × |  |
|   | Oracle | × |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | SLUŽBA RDS SQL | × |  |
|   | Oracle | × |  |
| **Virtuální počítač Azure SQL** | SQL Server | × |   |
|   | Oracle  | × |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | Služba RDS MySQL | ✔ | GA |
| **Azure DB pro PostgreSQL – jeden server** | PostgreSQL | ✔ | GA |
|   | Azure DB pro PostgreSQL – jeden server | ✔ | GA |
|   | PostgreSQL RDS | ✔ | GA |
|   | Oracle | ✔ | Verze Public Preview (aby byla zastaralá od 1. května 2021) |
| **Azure DB pro PostgreSQL – škálovatelné (Citus)** | PostgreSQL | ✔ | GA |
|   | PostgreSQL RDS | ✔ | GA |

> [!IMPORTANT]
> Scénář migrace "Oracle to Azure Database for PostgreSQL" (aktuálně ve verzi Preview) už nebude k dispozici po 1.2021. května. Budeme dál poskytovat podporu prostřednictvím alternativních nástrojů (například Ora2pg) a poskytněte nejlepší možnosti migrace pro Oracle pro PostgreSQL migrace. Osvědčené postupy pro migraci najdete v tématu [Průvodce migrací Oracle to Azure Database for PostgreSQL](https://aka.ms/OracletoPGguide).


## <a name="next-steps"></a>Další kroky

Přehled Azure Database Migration Service a regionální dostupnosti najdete v článku [co je to Azure Database Migration Service](dms-overview.md).
