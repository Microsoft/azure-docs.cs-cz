---
title: Příklady skriptů Azure CLI
description: Ukázkové skripty Azure CLI pro vytváření a správu serverů služby Azure SQL Database, elastických fondů, databází a bran firewall.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061719"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Ukázky v Azure CLI pro službu Azure SQL Database

Azure SQL Database můžete nakonfigurovat pomocí <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Elastické fondy & jedné databáze](#tab/single-database)

| | |
|---|---|
|**Vytvoření izolované databáze a elastického fondu**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-cli.md) | Vytvoří databázi SQL Azure a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-cli.md) | Vytvoří elastické fondy SQL, přesune databáze SQL Azure ve fondu a změní výpočetní velikosti. |
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-cli.md) | Po dotazování na informace o velikosti databáze škáluje službu Azure SQL Database na jinou výpočetní velikost. |
| [Škálování elastického fondu](scripts/sql-database-scale-pool-cli.md) | Škáluje elastický fond SQL na jinou výpočetní velikost. |
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Přidat jednu databázi do skupiny převzetí služeb při selhání](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Vytvoří databázi a skupinu převzetí služeb při selhání, přidá databázi do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání sekundárním serverem. |
| [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi SQL Azure a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Nakonfiguruje aktivní geografickou replikaci pro databázi SQL Azure v elastickém fondu SQL a pak ji převezme do sekundární repliky. |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfiguruje zásady auditování a detekce hrozeb pro databázi SQL Azure. |
| **Zálohování, obnovení, kopírování a import databáze**||
| [Zálohování databáze](scripts/sql-database-backup-database-cli.md)| Zálohuje databázi SQL Azure do zálohy úložiště Azure. |
| [Obnovení databáze](scripts/sql-database-restore-database-cli.md)| Obnoví databázi SQL Azure z geograficky redundantní zálohy a odstraní odstraněnou databázi SQL Azure do poslední zálohy. |
| [Kopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-cli.md) | Vytvoří kopii existující databáze SQL Azure na novém serveru SQL Azure. |
| [Import databáze ze souboru BacPac](scripts/sql-database-import-from-bacpac-cli.md)| Importuje databázi do Azure SQL serveru ze souboru *. BacPac* . |
|||

Přečtěte si další informace o [rozhraní Azure CLI API pro jednu databázi](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure CLI pro Azure SQL Database spravované instance.

| | |
|---|---|
| **Vytvoření Managed Instance**||
| [Vytvoření spravované instance](scripts/sql-database-create-configure-managed-instance-cli.md)| Vytvoří spravovanou instanci. |
| **Konfigurace transparentní šifrování dat (TDE)**||
| [Správa transparentní šifrování dat ve spravované instanci pomocí Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Nakonfiguruje transparentní šifrování dat (TDE) ve spravované instanci Azure SQL pomocí Azure Key Vault s různými klíčovými scénáři. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance. |
|||

Další příklady spravované instance najdete v tématu věnovaném [Vytvoření](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [aktualizaci](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [Přesunutí databáze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)a [práci se](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skripty.

Přečtěte si další informace o [rozhraní Azure CLI API spravované instance](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
