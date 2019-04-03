---
title: Stav scénáře migrace databáze | Dokumentace Microsoftu
description: Další informace o stavu migrace scénáře podporované službou Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7017566092530dce2359d45314ac00dca63b8ad0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886229"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stav migrace scénáře podporované službou Azure Database Migration Service
Azure Database Migration Service je navržen pro podporu různých scénářů migrace (párů zdroj/cíl) pro obě do offline režimu (jednorázová) a migrace online (Průběžná synchronizace). V čase se rozšiřuje pokrytí scénáři poskytuje Azure Database Migration Service. V pravidelných intervalech se neustále přidávají nové scénáře. Tento článek identifikuje scénáře migrace, které jsou aktuálně podporovány službou Azure Database Migration Service a stav (privátní verze Preview, ve verzi Public Preview nebo obecně k dispozici) pro jednotlivé scénáře.

## <a name="offline-versus-online-migrations"></a>V režimu offline a online migrace
S Azure Database Migration Service můžete provést offline nebo online migrace. S *offline* migrace, výpadky aplikací začíná ve stejnou dobu, který se spustí na migraci. Chcete-li omezit prostoje pro čas potřebný k přímé na nové prostředí po dokončení migrace, použijte *online* migrace. Doporučuje se test při offline migraci k určení, zda Doba výpadku je přijatelná; v opačném případě proveďte online migrace.

## <a name="migration-scenario-status"></a>Stav scénáře migrace
Stav migrace scénáře podporované službou Azure Database Migration Service se liší podle času. Obecně platí, jsou scénáře poprvé zavedené ve **ve verzi Private Preview**. Účast ve verzi Private Preview vyžaduje, aby zákazníci odeslání nominace prostřednictvím [DMS ve verzi Preview webu](https://aka.ms/dms-preview). Stav scénáře po privátní verze Preview, se změní na **ve verzi Public Preview**. Uživatelé Azure Database Migration Service můžete vyzkoušet scénáře migrace ve verzi Public Preview přímo z uživatelského rozhraní. Bez zápisu je povinný.  Scénáře migrace ve verzi Public Preview však nemusí být dostupné ve všech oblastech a mohou být další změny před finální verzi. Stav scénáře od veřejné verze Preview, se změní na **obecně k dispozici**. Obecně dostupná (GA) je stav vydání finální verze a funkce je kompletní a přístupná pro všechny uživatele. 

## <a name="migration-scenario-support"></a>Podpora scénáře migrace
Následující tabulky popisují, jaké scénáře migrace jsou podporované při použití služby Azure Database Migration Service.

> [!NOTE]
> Pokud scénář označen jako podporovaný níže se nezobrazí v uživatelském rozhraní, obraťte se prosím [Data migrační tým](mailto:datamigrationteam@microsoft.com) pro další informace.

> [!IMPORTANT]
> Zobrazit scénáře, které jsou aktuálně podporovány službou Azure Database Migration Service ve verzi Private Preview, najdete v článku [DMS ve verzi Preview webu](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Podpora migrace offline (jednorázová)
V následující tabulce jsou uvedeny Azure Database Migration Service podporu pro offline migrace.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Virtuální počítač Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Ve verzi Public Preview |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Podpora migrace online (Průběžná synchronizace)
V následující tabulce jsou uvedeny Azure Database Migration Service podporu, ve verzi Public Preview nebo obecně k dispozici pro online migraci.

| Cíl  | Zdroj | Podpora | Status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Ve verzi Private Preview |
| **Virtuální počítač Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Ve verzi Public Preview |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Ve verzi Private Preview |

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md).
