---
title: Příklady skriptů Azure PowerShellu
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
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73821405"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Ukázky v Azure PowerShellu pro službu Azure SQL Database

Azure SQL Database umožňuje konfigurovat databáze, instance a fondy pomocí Azure PowerShellu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat prostředí PowerShell místně, tento kurz vyžaduje AZ PowerShell 1.4.0 nebo novější. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="single-database-and-elastic-pools"></a>[Jednotné databáze a elastické fondy](#tab/single-database)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro službu Azure SQL Database.

| |  |
|---|---|
|**Vytvoření a konfigurace jednotlivých databází a elastických fondů**||
| [Vytvoření jedné databáze a konfigurace pravidla brány firewall databázového serveru](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří izolovanou databázi Azure SQL a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří elastické fondy Azure SQL Database, přesune databáze ve fondu a změní velikost výpočetních prostředků.|
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro izolovanou databázi Azure SQL a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro databázi Azure SQL v elastickém fondu SQL a převezme její služby při selhání do sekundární repliky. |
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro jednu databázi](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript prostředí PowerShell vytvoří databázi a skupinu převzetí služeb při selhání, přidá databázi do skupiny převzetí služeb při selhání a provede testy převzetí služeb při selhání na sekundární server. | 
| [Konfigurace skupiny převzetí služeb při selhání pro elastický fond](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript prostředí PowerShell vytvoří databázi, přidá ji do elastického fondu, přidá elastický fond do skupiny převzetí služeb při selhání a provede testy převzetí služeb při selhání na sekundárním serveru. | 
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

Další informace o [rozhraní API Azure PowerShell u jedné databáze](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro Azure SQL Database – spravovanou instanci.

| |  |
|---|---|
|**Vytváření a konfigurace spravovaných instancí**||
| [Vytvoření a správa spravované instance](scripts/sql-database-create-configure-managed-instance-powershell.md) | Teto skript PowerShellu ukazuje, jak vytvořit a spravovat spravovanou instanci pomocí Azure PowerShellu. |
| [Vytvoření a správa spravované instance pomocí šablony Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript prostředí PowerShell ukazuje, jak vytvořit a spravovat spravovanou instanci pomocí šablony Azure PowerShell a Azure Resource Manager.|
| [Obnovení databáze do spravované instance v jiné geografické oblasti](scripts/sql-managed-instance-restore-geo-backup.md) | Tento skript prostředí PowerShell přebírá zálohu jedné databáze a obnovuje ji do jiné oblasti. To se označuje jako scénář zotavení po havárii geografického obnovení. |
| **Konfigurace transparentního šifrování dat (TDE)**||
| [Správa transparentního šifrování dat ve spravované instanci pomocí vlastního klíče z trezoru klíčů Azure](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu konfiguruje transparentní šifrování dat (TDE) ve scénáři Bring Your Own Key pro azure SQL managed instance pomocí klíče z Trezoru klíčů Azure|
|**Konfigurace skupiny převzetí služeb při selhání**||
| [Konfigurace skupiny převzetí služeb při selhání pro spravovanou instanci](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript prostředí PowerShell vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak testuje převzetí služeb při selhání z primární spravované instance na sekundární spravovanou instanci. | 
|||

Přečtěte si další informace o [rozhraní API Azure PowerShell u spravované instance.](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)

---

## <a name="additional-resources"></a>Další zdroje

Příklady uvedené na této stránce používají [rutiny Azure SQL Database](/powershell/module/az.sql/) pro vytváření a správu prostředků Azure SQL. Další rutiny pro spouštění dotazů a provádění mnoha databázových úloh jsou umístěny v modulu [sqlserver.](/powershell/module/sqlserver/) Další informace naleznete v tématu [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
