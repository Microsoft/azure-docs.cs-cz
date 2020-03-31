---
title: Stav scénáře migrace databází
titleSuffix: Azure Database Migration Service
description: Přečtěte si o stavu scénářů migrace podporovaných službou Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254920"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stav scénářů migrace podporovaných službou Migrace databáze Azure

Služba Migrace databáze Azure je navržena tak, aby podporovala různé scénáře migrace (páry zdroj/cíl) pro offline (jednorázové) i online (nepřetržité synchronizace) migrace. Pokrytí scénáře poskytované službou Migrace databáze Azure se v průběhu času prodlužuje. Nové scénáře jsou přidávány v pravidelných intervalech. Tento článek identifikuje scénáře migrace aktuálně podporované službou Migrace databáze Azure a stav (privátní verze Preview, verze public preview nebo obecná dostupnost) pro každý scénář.

## <a name="offline-versus-online-migrations"></a>Offline versus online migrace

Se službou Azure Database Migration Service můžete provést migraci offline nebo online. Při *offline* migraci začíná prostoje aplikací ve stejnou dobu, kdy se migrace spustí. Chcete-li omezit prostoje na dobu potřebnou k přechodu na nové prostředí po dokončení migrace, použijte migraci *online.* Doporučujeme otestovat offline migraci, abyste zjistili, zda je prostoje přijatelné. pokud ne, proveďte online migraci.

## <a name="migration-scenario-status"></a>Stav scénáře migrace

Stav scénářů migrace podporovaných službou Migrace databáze Azure se časem liší. Obecně platí, že scénáře jsou nejprve vydány v **privátní verzi Preview**. Účast v privátní verzi Preview vyžaduje, aby zákazníci předložili nominaci prostřednictvím [webu DMS Preview](https://aka.ms/dms-preview). Po verzi Private Preview se stav scénáře změní na **public Preview**. Uživatelé služby Azure Database Migration Service si můžou vyzkoušet scénáře migrace ve verzi Public Preview přímo z uživatelského rozhraní. Registrace není nutná.  Scénáře migrace ve verzi Public Preview však nemusí být k dispozici ve všech oblastech a mohou projít dalšími změnami před konečnou verzí. Po verzi Public Preview se stav scénáře změní na **obecně dostupnost**. Obecná dostupnost (GA) je stav konečné verze a funkce je kompletní a přístupná všem uživatelům.

## <a name="migration-scenario-support"></a>Podpora scénářů migrace

V následujících tabulkách jsou uvedeny scénáře migrace, které jsou podporované při používání služby Migrace databáze Azure.

> [!NOTE]
> Pokud se níže uvedený scénář nezobrazuje v uživatelském rozhraní, požádejte o další informace alias [Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

> [!IMPORTANT]
> Pokud chcete zobrazit všechny scénáře aktuálně podporované službou Migrace databáze Azure v privátní verzi Preview, podívejte se na [web DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Podpora offline (jednorázové) migrace

V následující tabulce je uvedena podpora služby Migrace databáze Azure pro offline migrace.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Virtuální počítač Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Online (nepřetržitá synchronizace) podpora migrace

V následující tabulce je uvedena podpora služby Migrace databáze Azure pro migrace online.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Soukromý náhled |
| **Virtuální počítač Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Veřejná verze Preview |

## <a name="next-steps"></a>Další kroky

Přehled služby migrace databáze Azure a místní dostupnosti najdete v článku [Co je služba migrace databáze Azure](dms-overview.md).
