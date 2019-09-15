---
title: Poznámky k verzi Azure SQL Database | Microsoft Docs
description: Seznamte se s novými funkcemi a vylepšeními služby Azure SQL Database a v dokumentaci k Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001506"
---
# <a name="sql-database-release-notes"></a>Poznámky k verzi SQL Database

V tomto článku jsou uvedené SQL Database funkce, které jsou aktuálně ve verzi Public Preview. SQL Database aktualizacích a vylepšeních najdete v článku [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Aktualizace a vylepšení dalších služeb Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkce ve verzi Public Preview

### <a name="single-databasetabsingle-database"></a>[Samostatná databáze](#tab/single-database)

| Funkce | Podrobnosti |
| ---| --- |
| Urychlené obnovení databáze s izolovanými databázemi a elastickými fondy | Informace najdete v tématu [urychlení obnovení databáze](sql-database-accelerated-database-recovery.md).|
|Přibližný počet DISTINCT|Informace najdete v tématu [přibližný počet jedinečných](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Režim dávky na Rowstore (pod úrovní kompatibility 150)|Informace najdete v tématu [režim dávky v Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Zjišťování a klasifikace dat  |Informace najdete v tématu [Azure SQL Database a SQL Data Warehouse klasifikaci & zjišťování dat](sql-database-data-discovery-and-classification.md).|
| Elastické úlohy databáze | Informace najdete v tématu [vytváření, konfigurace a Správa elastických úloh](elastic-jobs-overview.md). |
| Elastické dotazy | Informace najdete v tématu [Přehled elastického dotazu](sql-database-elastic-query-overview.md). |
| Elastické transakce | [Distribuované transakce v cloudových databázích](sql-database-elastic-transactions-overview.md) |
|Zpětná vazba o udělení paměti (režim řádku) (v části úroveň kompatibility 150)|Informace najdete v článku o [zpětné vazbě přidělení paměti (režim řádku)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor dotazů v Azure Portal |Informace najdete v tématu [použití Editoru dotazů SQL Azure Portal k připojení a dotazování dat](sql-database-connect-query-portal.md).|
| Služba R Services/strojové učení s izolovanými databázemi a elastickými fondy |Informace najdete v tématu [Machine Learning Services v Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Bezserverová výpočetní úroveň | Informace najdete v tématu [SQL Database bez serveru (Preview)](sql-database-serverless.md).|
|Analýza SQL|Informace najdete v tématu [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Odložená kompilace proměnné tabulky (pod úrovní kompatibility 150)|Informace naleznete v tématu [odložená kompilace proměnné tabulky](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Spravovaná instance](#tab/managed-instance)

| Funkce | Podrobnosti |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Fondy instancí</a> | Pohodlný a cenově výhodný způsob migrace menších instancí SQL do cloudu. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparentní šifrování dat (TDE) s Bring Your Own Key (BYOK)</a> |Informace najdete v tématu [Azure SQL transparentní šifrování dat s klíči spravovanými zákazníky v Azure Key Vault: Podpora](transparent-data-encryption-byok-azure-sql.md)Bring Your Own Key.|
| <a href="https://aka.ms/managed-instance-failover-groups">Geografické skupiny distribuovaných převzetí služeb při selhání</a> | Ponechte kopii instance v jiné oblasti a ujistěte se, že budou data dostupná i v případě scénáře regionální havárie. |
| <a href="https://aka.ms/managed-instance-aadlogins">Instance objektů zabezpečení serveru Azure AD na úrovni instance (přihlášení)</a> | Vytvořte přihlášení na úrovni serveru pomocí příkazu <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">vytvořit přihlášení z externího poskytovatele</a> . |
| [Transakční replikace](sql-database-managed-instance-transactional-replication.md) | Změny v tabulkách replikujte do jiných databází umístěných na spravovaných instancích, v izolovaných databázích nebo instancích SQL Server, nebo aktualizujte tabulky, když dojde ke změně některých řádků v jiných spravovaných instancích nebo instanci SQL Server. Informace najdete v tématu [Konfigurace replikace v databázi spravované instance Azure SQL Database](replication-with-sql-database-managed-instance.md). |
| Detekce hrozeb |Informace najdete v tématu [konfigurace detekce hrozeb v Azure SQL Database Managed instance](sql-database-managed-instance-threat-detection.md).|
| Znovu vytvořit vyřazené databáze se spravovanými instancemi |Informace najdete v tématu [opětovné vytvoření vyřazených databází ve spravované instanci Azure SQL](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>Opravené známé problémy

- **Srpna 2019** – obsažené databáze jsou ve spravované instanci plně podporovány.

## <a name="updates"></a>Aktuální zprávy

Seznam SQL Database aktualizací a vylepšení najdete v tématu [aktualizace služby SQL Database](https://azure.microsoft.com/updates/?product=sql-database).

Aktualizace a vylepšení pro všechny služby Azure najdete v tématu [aktualizace služby](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Přispívat k obsahu

Pokud chcete přispět k dokumentaci Azure SQL Database, přečtěte si [příručku pro přispěvatele docs](https://docs.microsoft.com/contribute/).
