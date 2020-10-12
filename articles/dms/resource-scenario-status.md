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
ms.openlocfilehash: 77c54d6ea463b5a8a4952d243886b80f38312d27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291482"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stav migračních scénářů, které podporuje Azure Database Migration Service

Azure Database Migration Service je navržený tak, aby podporoval různé scénáře migrace (páry zdroj/cíl) pro offline (jednorázovou) i online (nepřetržitou synchronizaci) migrace. Pokrytí scénáře, které poskytuje Azure Database Migration Service, se v průběhu času rozšiřuje. V pravidelných intervalech se přidávají nové scénáře. Tento článek popisuje scénáře migrace, které jsou aktuálně podporovány nástrojem Azure Database Migration Service a stav (privátní verze Preview, verze Public Preview nebo Obecná dostupnost) pro každý scénář.

## <a name="offline-versus-online-migrations"></a>Offline online migrace

Pomocí Azure Database Migration Service můžete provést offline nebo online migraci. Při *offline* migraci začíná výpadek aplikace ve stejnou dobu, kdy se spustí migrace. Pokud chcete omezit prostoje na dobu potřebnou k obnově prostředí po dokončení migrace, použijte *online* migraci. Doporučuje se otestovat offline migraci, abyste zjistili, jestli je výpadky přijatelné; Pokud ne, proveďte online migraci.

## <a name="migration-scenario-status"></a>Stav scénáře migrace

Stav migračních scénářů, které Azure Database Migration Service podporuje, se liší v závislosti na čase. Obecně jsou scénáře vydávány v **privátní verzi Preview**. Účast v privátní verzi Preview vyžaduje, aby zákazníci odeslali jmenování prostřednictvím [webu DMS Preview](https://aka.ms/dms-preview). Po použití privátní verze Preview se stav scénáře změní na **Public Preview**. Azure Database Migration Service uživatelé můžou vyzkoušet scénáře migrace ve verzi Public Preview přímo z uživatelského rozhraní. Nevyžaduje se žádné registraci.  Nicméně scénáře migrace ve verzi Public Preview nemusí být k dispozici ve všech oblastech a mohou před finální verzí podléhat dalším změnám. Po zobrazení verze Public Preview se stav scénáře změní na **všeobecně dostupné**. Všeobecná dostupnost (GA) je konečný stav vydaných verzí a funkce je kompletní a dostupná pro všechny uživatele.

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
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | SLUŽBA RDS SQL | ✔ | GA |
|   | Oracle | × |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | SLUŽBA RDS SQL | ✔ | GA |
|   | Oracle | × |  |
| **Virtuální počítač Azure SQL** | SQL Server | × |   |
|   | Oracle  | × |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | Služba RDS MySQL | ✔ | GA |
| **Azure DB pro PostgreSQL – jeden server** | PostgreSQL | ✔ | GA |
|   | Azure DB pro PostgreSQL – jeden server * | ✔ | GA |
|   | PostgreSQL RDS | ✔ | GA |
|   | Oracle | ✔ | Verze Public Preview |
| **Azure DB pro PostgreSQL – škálovatelné (Citus)** | PostgreSQL | ✔ | GA |
|   | PostgreSQL RDS | ✔ | GA |

* Podporováno pro PostgreSQL verze 10 a novější.

## <a name="next-steps"></a>Další kroky

Přehled Azure Database Migration Service a regionální dostupnosti najdete v článku [co je to Azure Database Migration Service](dms-overview.md).
