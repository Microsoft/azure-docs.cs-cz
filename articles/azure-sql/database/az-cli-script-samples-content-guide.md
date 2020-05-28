---
title: Příklady skriptů Azure CLI
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Příklady skriptů Azure CLI pro vytvoření a správu Azure SQL Database a spravované instance Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: f5a8e5c9af9f3dc27b517c386c4abae24dc45239
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053415"
---
# <a name="azure-cli-samples-for-azure-sql-database--sql-managed-instance"></a>Ukázky v Azure CLI pro Azure SQL Database & spravované instance SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a SQL Managed instance se dají nakonfigurovat pomocí <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro správu databází s jedním a sdruženým fondem v Azure SQL Database. 

| | |
|---|---|
|**Vytvoření databáze Azure SQL Database**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/create-and-configure-database-cli.md) | Vytvoří SQL Database a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/move-database-between-elastic-pools-cli.md) | Vytvoří elastické fondy, přesune databáze SQL ve fondu a změní výpočetní velikosti. |
|**Škálování Azure SQL Database**||
| [Škálování izolované databáze](scripts/monitor-and-scale-database-cli.md) | Po dotazování na informace o velikosti databáze škáluje SQL Database na jinou výpočetní velikost. |
| [Škálování elastického fondu](scripts/scale-pool-cli.md) | Škáluje elastický fond SQL na jinou výpočetní velikost. |
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Přidat jednu databázi do skupiny převzetí služeb při selhání](scripts/add-database-to-failover-group-cli.md)| Vytvoří databázi a skupinu převzetí služeb při selhání, přidá databázi do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi SQL Azure a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi SQL Azure v elastickém fondu SQL a pak ji převezme do sekundární repliky. |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfiguruje zásady auditování a detekce hrozeb pro databázi SQL Azure. |
| **Zálohování, obnovení, kopírování a import databáze**||
| [Zálohování databáze](../../sql-database/scripts/sql-database-backup-database-cli.md)| Zálohuje databázi SQL do zálohy úložiště Azure. |
| [Obnovení databáze](../../sql-database/scripts/sql-database-restore-database-cli.md)| Obnoví databázi SQL z geograficky redundantní zálohy a obnoví z poslední zálohy odstraněnou databázi SQL. |
| [Kopírování databáze na nový server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Vytvoří kopii existující databáze SQL na novém serveru. |
| [Import databáze ze souboru BacPac](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importuje databázi do SQL Database ze souboru *. BacPac* . |
|||

Přečtěte si další informace o [rozhraní Azure CLI API pro jednu databázi](single-database-manage.md#azure-cli).

# <a name="azure-sql-managed-instance"></a>[Spravovaná instance Azure SQL](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro spravovanou instanci Azure SQL.

| | |
|---|---|
| **Vytvoření spravované instance SQL**||
| [Vytvoření spravované instance SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Vytvoří spravovanou instanci SQL. |
| **Konfigurace transparentní šifrování dat (TDE)**||
| [Správa transparentní šifrování dat ve spravované instanci SQL pomocí Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Nakonfiguruje transparentní šifrování dat (TDE) ve spravované instanci SQL pomocí Azure Key Vault s různými klíčovými scénáři. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci SQL](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Vytvoří dvě spravované instance SQL, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance SQL do sekundární spravované instance SQL. |
|||

Další příklady spravované instance SQL najdete v tématu věnovaném [Vytvoření](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [aktualizaci](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [Přesunutí databáze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)a [práci se](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skripty.

Přečtěte si další informace o [rozhraní Azure CLI API spravované instance SQL](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-manage-managed-instances).

---
