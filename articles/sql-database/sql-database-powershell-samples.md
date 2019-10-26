---
title: Ukázkové skripty Azure PowerShellu pro službu SQL Database | Microsoft Docs
description: Ukázkové skripty Azure PowerShellu, které vám pomůžou vytvářet a spravovat servery služby Azure SQL Database, elastické fondy, databáze a brány firewall.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 27b4a6f5614b79b2e9d8f3730441d702f2d7cc77
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933273"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Ukázky v Azure PowerShellu pro službu Azure SQL Database

Azure SQL Database umožňuje konfigurovat databáze, instance a fondy pomocí Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="single-database-and-elastic-poolstabsingle-database"></a>[Izolovaná databáze a elastické fondy](#tab/single-database)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro službu Azure SQL Database.

| |  |
|---|---|
|**Vytváření a konfigurace izolovaných databází a elastických fondů**||
| [Vytvoření jedné databáze a konfigurace pravidla brány firewall databázového serveru](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří izolovanou databázi Azure SQL a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří elastické fondy Azure SQL Database, přesune databáze ve fondu a změní velikost výpočetních prostředků.|
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro izolovanou databázi Azure SQL a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro databázi Azure SQL v elastickém fondu SQL a převezme její služby při selhání do sekundární repliky. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro izolovanou databázi](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří databázi a skupinu převzetí služeb při selhání přidá databázi do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání na sekundární server. | 
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání na sekundární server. | 
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu databáze Azure SQL, škáluje ji na vyšší velikost výpočetních prostředků a vytvoří pravidlo upozornění na jednu z metrik výkonu. |
| [Škálování elastického fondu](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu elastického fondu databáze Azure SQL, škáluje ji na vyšší velikost výpočetních prostředků a vytvoří pravidlo upozornění na jednu z metrik výkonu. |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje zásady auditování a detekce hrozeb pro databázi Azure SQL. |
| **Obnovení, kopírování a import databáze**||
| [Obnovení databáze](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu obnoví databázi Azure SQL z geograficky redundantní zálohy a odstraněnou databázi Azure SQL z poslední zálohy. |
| [Kopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu vytvoří kopii stávající databáze Azure SQL na novém serveru Azure SQL. |
| [Import databáze ze souboru bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu importuje databázi na server SQL Azure ze souboru bacpac. |
| **Synchronizace dat mezi databázemi**||
| [Synchronizace dat mezi databázemi SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje Synchronizaci dat pro synchronizaci mezi několika databázemi Azure SQL. |
| [Synchronizace dat mezi službou SQL Database a místním SQL Serverem](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje Synchronizaci dat pro synchronizaci mezi databází Azure SQL a místní databází SQL Serveru. |
| [Aktualizace schématu synchronizace pro Synchronizaci dat SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu přidá položky do schématu synchronizace pro Synchronizaci dat nebo je z něj odebere. |
|||

Přečtěte si další informace o [rozhraní Izolovaná databáze Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Spravovaná instance](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové Azure PowerShell skripty pro Azure SQL Database spravovanou instanci.

| |  |
|---|---|
|**Vytvoření a konfigurace spravovaných instancí**||
| [Vytvoření a správa spravované instance](scripts/sql-database-create-configure-managed-instance-powershell.md) | Teto skript PowerShellu ukazuje, jak vytvořit a spravovat spravovanou instanci pomocí Azure PowerShellu. |
| [Vytvoření a Správa spravované instance pomocí šablony Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu ukazuje, jak vytvořit a spravovat spravovanou instanci pomocí šablony Azure PowerShell a Azure Resource Manager.|
| [Obnovení databáze do spravované instance v jiné geografické oblasti](scripts/sql-managed-instance-restore-geo-backup.md) | Tento skript PowerShellu přebírá zálohu jedné databáze a obnoví ji do jiné oblasti. To se označuje jako scénář zotavení po havárii geografického obnovení. |
| **Konfigurace transparentní šifrování dat (TDE)**||
| [Správa transparentní šifrování dat ve spravované instanci pomocí vlastního klíče z Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje transparentní šifrování dat (TDE) ve scénáři Bring Your Own Key pro spravovanou instanci Azure SQL pomocí klíče z Azure Key Vault|
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance. | 
|||

Přečtěte si další informace o [spravované instanci Azure PowerShell rozhraní API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Další zdroje informací:

Příklady uvedené na této stránce využívají [rutiny Azure SQL Database](/powershell/module/az.sql/) pro vytváření a správu prostředků Azure SQL. Další rutiny pro spouštění dotazů a provádění mnoha databázových úloh se nachází v modulu [SQLServer](/powershell/module/sqlserver/) . Další informace najdete v tématu [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
