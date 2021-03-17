---
title: Příklady skriptů Azure CLI
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Příklady skriptů Azure CLI pro vytvoření a správu Azure SQL Database a spravované instance Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563338"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Ukázky v Azure CLI pro Azure SQL Database a SQL Managed instance 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a SQL Managed instance můžete nakonfigurovat pomocí rozhraní příkazového <a href="/cli/azure">řádku Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro správu databází s jedním a sdruženým fondem v Azure SQL Database. 

|Oblast|Popis|
|---|---|
|**Vytváření databází v Azure SQL Database**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/create-and-configure-database-cli.md) | Vytvoří SQL Database a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/move-database-between-elastic-pools-cli.md) | Vytvoří elastické fondy, přesune databáze ve fondu a změní výpočetní velikosti. |
|**Škálování databází v Azure SQL Database**||
| [Škálování izolované databáze](scripts/monitor-and-scale-database-cli.md) | Po dotazování na informace o velikosti databáze škáluje databázi v SQL Database na jinou výpočetní velikost. |
| [Škálování elastického fondu](scripts/scale-pool-cli.md) | Škáluje elastický fond SQL na jinou výpočetní velikost. |
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Přidání jedné databáze do skupiny převzetí služeb při selhání](scripts/add-database-to-failover-group-cli.md)| Vytvoří databázi a skupinu převzetí služeb při selhání, přidá databázi do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace a převzetí služeb při selhání pro izolovanou databázi pomocí aktivní geografické replikace](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi v Azure SQL Database a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu pomocí aktivní geografické replikace](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi v elastickém fondu a pak ji převezme do sekundární repliky. |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfiguruje zásady auditování a detekce hrozeb pro databázi v Azure SQL Database. |
| **Zálohování, obnovení, kopírování a import databáze**||
| [Zálohování databáze](../../sql-database/scripts/sql-database-backup-database-cli.md)| Zálohuje databázi v SQL Database do zálohy úložiště Azure. |
| [Obnovení databáze](../../sql-database/scripts/sql-database-restore-database-cli.md)| Obnoví databázi v SQL Database z geograficky redundantní zálohy a obnoví odstraněnou databázi na nejnovější zálohu. |
| [Zkopírování databáze na nový server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Vytvoří kopii existující databáze v SQL Database na novém serveru. |
| [Import databáze ze souboru BACPAC](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importuje databázi do SQL Database ze souboru BACPAC. |
|||

Přečtěte si další informace o [rozhraní Azure CLI API pro jednu databázi](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Spravovaná instance Azure SQL](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro spravovanou instanci Azure SQL.

|Oblast|Popis|
|---|---|
| **Vytvoření spravované instance SQL**||
| [Vytvoření spravované instance SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Vytvoří spravovanou instanci SQL. |
| **Konfigurace transparentní šifrování dat (TDE)**||
| [Správa transparentní šifrování dat ve spravované instanci SQL pomocí Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Nakonfiguruje transparentní šifrování dat (TDE) ve spravované instanci SQL pomocí Azure Key Vault s různými klíčovými scénáři. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci SQL](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Vytvoří dvě instance spravované instance SQL, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance SQL do sekundární spravované instance SQL. |
|||

Další příklady spravované instance SQL najdete v tématu [Vytvoření](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [aktualizace](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [Přesunutí databáze](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)a [práce se](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skripty.

Přečtěte si další informace o [rozhraní Azure CLI API spravované instance SQL](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---