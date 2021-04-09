---
title: Příklady skriptu Azure PowerShell
description: Použijte příklady skriptů Azure PowerShell, které vám pomůžou vytvořit a spravovat Azure SQL Database a prostředky spravované instance Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690962"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Ukázky Azure PowerShell pro Azure SQL Database a Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a Azure SQL Managed instance vám umožní nakonfigurovat vaše databáze, instance a fondy pomocí Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro službu Azure SQL Database.

|Odkaz|Description|
|---|---|
|**Vytváření a konfigurace izolovaných databází a elastických fondů**||
| [Vytvoření jedné databáze a konfigurace pravidla brány firewall na úrovni serveru](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří izolovanou databázi a nakonfiguruje pravidlo brány firewall protokolu IP na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShell vytvoří elastické fondy, přesune databáze ve fondu a změní výpočetní velikosti.|
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Konfigurace a převzetí služeb při selhání jedné databáze pomocí aktivní geografické replikace](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro izolovanou databázi a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu pomocí aktivní geografické replikace](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro databázi v elastickém fondu a převezme její služby při selhání do sekundární repliky. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro izolovanou databázi](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří databázi a skupinu převzetí služeb při selhání, přidá databázi do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání na sekundární server. |
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání na sekundární server. |
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu pro izolovanou databázi, škáluje ji na vyšší výpočetní velikost a vytvoří pravidlo upozornění na jednu z metrik výkonu. |
| [Škálování elastického fondu](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu elastického fondu, škáluje ho na vyšší výpočetní velikost a vytvoří pravidlo upozornění na jednu z metrik výkonu. |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje zásady auditování a detekce hrozeb pro databázi. |
| **Obnovení, kopírování a import databáze**||
| [Obnovení databáze](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu obnoví databázi z geograficky redundantní zálohy a obnoví odstraněnou databázi na nejnovější zálohu. |
| [Zkopírování databáze na nový server](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu vytvoří kopii existující databáze na novém serveru. |
| [Import databáze ze souboru BacPac](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu importuje databázi do Azure SQL Database ze souboru BacPac. |
| **Synchronizace dat mezi databázemi**||
| [Synchronizace dat mezi databázemi](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje synchronizaci dat pro synchronizaci mezi několika databázemi v Azure SQL Database. |
| [Synchronizace dat mezi službou SQL Database a místním SQL Serverem](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje synchronizaci dat pro synchronizaci mezi databází v Azure SQL Database a místní databází SQL Server. |
| [Aktualizace schématu synchronizace pro Synchronizaci dat SQL](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu přidá položky do schématu synchronizace pro Synchronizaci dat nebo je z něj odebere. |
|||

Přečtěte si další informace o [rozhraní Azure PowerShell API pro jednu databázi](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Spravovaná instance Azure SQL](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure SQL Managed instance.

|Odkaz|Description|
|---|---|
|**Vytvoření a konfigurace spravovaných instancí**||
| [Vytvoření a Správa spravované instance](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Tento skript PowerShellu ukazuje, jak vytvořit a spravovat spravovanou instanci pomocí Azure PowerShell. |
| [Vytvoření a Správa spravované instance pomocí šablony Azure Resource Manager](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vám ukáže, jak vytvořit a spravovat spravovanou instanci pomocí Azure PowerShell a Azure Resource Manager šabloně.|
| [Obnovení databáze do spravované instance v jiné geografické oblasti](../managed-instance/scripts/restore-geo-backup.md) | Tento skript PowerShellu vytvoří zálohu jedné databáze a obnoví ji do jiné oblasti. To se označuje jako scénář obnovení po havárii geografického obnovení. |
| **Konfigurace transparentního šifrování dat**||
| [Správa transparentního šifrování dat ve spravované instanci pomocí vlastního klíče z Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje transparentní šifrování dat ve scénáři Bring Your Own Key pro spravovanou instanci Azure SQL pomocí klíče z Azure Key Vault.|
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance. |
|||

Přečtěte si další informace o [rutinách PowerShellu pro spravovanou instanci Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Další zdroje informací

Příklady uvedené na této stránce využívají [rutiny PowerShellu](/powershell/module/az.sql/) pro vytváření a správu prostředků Azure SQL. Další rutiny pro spouštění dotazů a provádění mnoha databázových úloh se nacházejí v modulu [SQLServer](/powershell/module/sqlserver/) . Další informace najdete v tématu [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).