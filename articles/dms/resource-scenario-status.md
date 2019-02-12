---
title: Stav scénáře migrace databáze | Dokumentace Microsoftu
description: Další informace o stavu migrace scénáře podporované službou Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/11/2019
ms.openlocfilehash: 39a7f7ce6660016f00e36c5f6619eba2fa461023
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993159"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Stav migrace scénáře podporované službou Azure Database Migration Service
Azure Database Migration Service je navržen pro podporu různých scénářů migrace (párů zdroj/cíl) pro obě do offline režimu (jednorázová) a migrace online (Průběžná synchronizace). V čase se rozšiřuje pokrytí scénáři poskytuje Azure Database Migration Service. V pravidelných intervalech se neustále přidávají nové scénáře. Tento článek identifikuje scénáře migrace, které jsou aktuálně podporovány službou Azure Database Migration Service a stav (privátní [nebo omezené] ve verzi Preview ve verzi Public Preview a obecně dostupné) nebo jednotlivé scénáře.

## <a name="offline-versus-online-migrations"></a>V režimu offline a online migrace
Při migraci databází do Azure s využitím Azure Database Migration Service můžete provést offline nebo online migrace. S *offline* migrace, výpadky aplikací začíná ve stejnou dobu, který se spustí na migraci. Pro *online* migrace, odstávky jsou omezené na čas potřebný k přímé na nové prostředí po dokončení migrace. Doporučuje se test při offline migraci k určení, zda Doba výpadku je přijatelná; Pokud ne, provedení online migrace.

## <a name="migration-scenario-status"></a>Stav scénáře migrace
Stav jednotlivých scénářů migrace podporované službou Azure Database Migration Service se liší podle času. Obecně platí, jsou scénáře poprvé zavedené ve **ve verzi Private Preview**, a využití výhod funkcí vyžaduje zákazníka k odeslání nominace prostřednictvím [DMS ve verzi Preview webu](https://aka.ms/dms-preview). Po dokončení ve verzi Private Preview scénář stav se změní na **ve verzi Public Preview**. Všichni uživatelé služby Azure Database Migration Service můžete využít výhod scénářů migrace, které jsou k dispozici ve verzi Public Preview. Ale scénář migrace nemusí být dostupné ve všech oblastech a funkce mohou být další změny před finální verzí. Pokud scénář migrace stane **obecně k dispozici**, finální vydání stav, funkčnost je kompletní a přístupná pro všechny uživatele služby Azure Database Migration Service. 

## <a name="migration-scenario-support"></a>Podpora scénáře migrace

Následující tabulky popisují, které scénáře migrace jsou podporované při používání Azure Database Migration Service.

> [!NOTE]
> Pokud scénář označen jako podporovaný níže se nezobrazí v uživatelském rozhraní, obraťte se prosím [Data migrační tým](mailto:datamigrationteam@microsoft.com) pro další informace.

### <a name="offline-one-time-migration-support"></a>Podpora migrace offline (jednorázová)
V následující tabulce jsou uvedeny Azure Database Migration Service podporu pro offline migraci.

| Cíl  | Zdroj | Podpora |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server | ✔ |
|   | Oracle  |   |
| **Azure Cosmos DB**  | MongoDB | ✔ |
| **Azure DB for MySQL**  | MySQL |  |
|   | RDS MySQL  |  |
| **Azure DB for PostgreSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Podpora migrace online (Průběžná synchronizace)
Podpora Azure Database Migration Service online migrace naleznete v následující tabulce.

| Cíl  | Zdroj | Podpora |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL VM**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Azure Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB for MySQL**  | MySQL | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB for PostgreSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>Další postup
Přehled služby Azure Database Migration Service a dostupnosti v jednotlivých oblastech najdete v článku [co je Azure Database Migration Service](dms-overview.md). 
