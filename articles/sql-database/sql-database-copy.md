---
title: Kopírování databáze
description: Vytvořte transakční konzistentní kopii existující databáze Azure SQL na stejném serveru nebo na jiném serveru.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473740"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopírování transakční konzistentní kopie databáze Azure SQL

Azure SQL Database poskytuje několik metod pro vytvoření transakční konzistentní kopie existující databáze Azure SQL ([jedna databáze](sql-database-single-database.md)) na stejném serveru nebo na jiném serveru. Databázi SQL můžete zkopírovat pomocí portálu Azure, PowerShellu nebo T-SQL.

## <a name="overview"></a>Přehled

Kopie databáze je snímek zdrojové databáze od okamžiku požadavku na kopírování. Můžete vybrat stejný server nebo jiný server. Také si můžete vybrat, aby jeho úroveň služby a výpočetní velikost, nebo použít jinou výpočetní velikost v rámci stejné úrovně služby (edice). Po dokončení kopírování se stane plně funkční, nezávislá databáze. V tomto okamžiku můžete upgradovat nebo downgrade na libovolnou edici. Přihlášení, uživatele a oprávnění lze spravovat nezávisle. Kopie je vytvořena pomocí technologie geografické replikace a po dokončení výsevu je propojení geografické replikace automaticky ukončeno. Všechny požadavky na použití geografické replikace platí pro operaci kopírování databáze. Podrobnosti najdete [v tématu Aktivní přehled geografické replikace.](sql-database-active-geo-replication.md)

> [!NOTE]
> [Automatické zálohování databáze](sql-database-automated-backups.md) se používá při vytváření kopie databáze.

## <a name="logins-in-the-database-copy"></a>Přihlášení v databázové kopii

Při kopírování databáze na stejný server databáze SQL lze stejné přihlášení použít v obou databázích. Zaregistrovaný objekt zabezpečení, který použijete ke kopírování databáze, se stane vlastníkem databáze v nové databázi. 

Při kopírování databáze na jiný databázový server SQL se objekt zabezpečení, který inicioval operaci kopírování na cílovém serveru, stane vlastníkem nové databáze. 

Bez ohledu na cílový server jsou do kopie databáze zkopírovány všichni uživatelé databáze, jejich oprávnění a identifikátory zabezpečení (SID). Použití [obsažených uživatelů databáze](sql-database-manage-logins.md) pro přístup k datům zajišťuje, že zkopírovaná databáze má stejná pověření uživatele, takže po dokončení kopírování k ní můžete okamžitě přistupovat se stejnými pověřeními.

Pokud pro přístup k datům použijete přihlášení na úrovni serveru a zkopírujete databázi na jiný server, nemusí přístup založený na přihlášení fungovat. K tomu může dojít, protože přihlášení neexistují na cílovém serveru nebo protože jejich hesla a identifikátory zabezpečení (SID) se liší. Informace o správě přihlášení při kopírování databáze na jiný databázový server SQL najdete v tématu [Správa zabezpečení databáze Azure SQL po zotavení po havárii](sql-database-geo-replication-security-config.md). Po operaci kopírování na jiný server proběhne úspěšně a před přemapování ostatních uživatelů se k zkopírované databázi může přihlásit pouze přihlášení přidružené k vlastníkovi databáze nebo správce serveru. Chcete-li vyřešit přihlášení a vytvořit přístup k datům po dokončení operace kopírování, [přečtěte si](#resolve-logins)informace o řešení přihlášení .

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopírování databáze pomocí portálu Azure

Pokud chcete databázi zkopírovat pomocí portálu Azure, otevřete stránku databáze a klikněte na **Kopírovat**.

   ![Kopie databáze](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Kopírování databáze pomocí PowerShellu nebo Azure CLI

Chcete-li zkopírovat databázi, použijte následující příklady.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro Prostředí PowerShell použijte rutinu [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopie databáze je asynchronní operace, ale cílová databáze je vytvořena ihned po přijetí požadavku. Pokud potřebujete zrušit operaci kopírování, zatímco ještě probíhá, přetáhněte cílovou databázi pomocí [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) rutina.

Úplný ukázkový skript prostředí PowerShell najdete [v tématu Kopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopie databáze je asynchronní operace, ale cílová databáze je vytvořena ihned po přijetí požadavku. Pokud potřebujete zrušit operaci kopírování, zatímco ještě probíhá, přetáhněte cílovou databázi pomocí příkazu [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Role RBAC pro správu kopie databáze

Chcete-li vytvořit kopii databáze, musíte být v následujících rolích.

- Vlastník předplatného nebo
- Role přispěvatele serveru SQL Server nebo
- Vlastní role ve zdrojové a cílové databázi s následujícím oprávněním:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Chcete-li zrušit kopii databáze, musíte být v následujících rolích.

- Vlastník předplatného nebo
- Role přispěvatele serveru SQL Server nebo
- Vlastní role ve zdrojové a cílové databázi s následujícím oprávněním:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Ke správě kopie databáze pomocí portálu Azure budete potřebovat také následující oprávnění:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Pokud chcete zobrazit operace v rámci nasazení ve skupině prostředků na portálu, operace napříč více zprostředkovateli prostředků, včetně operací SQL, budete potřebovat tyto další role RBAC:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Kopírování databáze pomocí aplikace Transact-SQL

Přihlaste se do hlavní databáze pomocí přihlášení správce serveru nebo přihlášení, které vytvořilo databázi, kterou chcete zkopírovat. Aby byla kopie databáze úspěšná, musí být členy role `dbmanager` přihlášení, která nejsou správcem serveru. Další informace o přihlášení a připojení k serveru naleznete v tématu [Správa přihlášení](sql-database-manage-logins.md).

Začněte kopírovat zdrojovou databázi pomocí [create database ... JAKO KOPIE prohlášení.](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) Příkaz T-SQL pokračuje v běhu, dokud není dokončena operace kopírování databáze.

> [!NOTE]
> Ukončení příkazu T-SQL neukončí operaci kopírování databáze. Chcete-li operaci ukončit, přetáhněte cílovou databázi.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopírování databáze SQL na stejný server

Přihlaste se do hlavní databáze pomocí přihlášení správce serveru nebo přihlášení, které vytvořilo databázi, kterou chcete zkopírovat. Aby bylo kopírování databáze úspěšné, musí být členy role přihlášení, která nejsou správcem `dbmanager` serveru.

Tento příkaz zkopíruje Database1 do nové databáze s názvem Database2 na stejném serveru. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopírování databáze SQL na jiný server

Přihlaste se do hlavní databáze cílového serveru, kde má být vytvořena nová databáze. Použijte přihlašovací jméno, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze na zdrojovém serveru. Přihlášení na cílovém serveru musí být `dbmanager` také členem role nebo přihlášení správce serveru.

Tento příkaz zkopíruje Database1 na serveru1 do nové databáze s názvem Database2 na serveru2. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Firewally obou serverů musí být nakonfigurovány tak, aby umožňovaly příchozí připojení z IP adresy klienta vydávajícího databázi T-SQL CREATE ... Jako kopie příkazu.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopírování databáze SQL do jiného předplatného

Pomocí kroků v části [Kopírování databáze SQL na jiný server](#copy-a-sql-database-to-a-different-server) můžete databázi zkopírovat na server SQL Database v jiném předplatném pomocí T-SQL. Ujistěte se, že používáte přihlašovací jméno, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze. Kromě toho musí být přihlášení členem `dbmanager` role nebo správce serveru na zdrojových i cílových serverech.

> [!NOTE]
> Portál [Azure](https://portal.azure.com), PowerShell a Azure CLI nepodporují databázovou kopii do jiného předplatného.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Sledování průběhu kopírování

Sledujte proces kopírování dotazováním na zobrazení [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)a [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Během kopírování je sloupec **state_desc** zobrazení sys.databases pro novou databázi nastaven na **kopírování**.

* Pokud se kopírování nezdaří, je sloupec **state_desc** zobrazení sys.databases pro novou databázi nastaven na **podezřelou**. Spusťte příkaz DROP v nové databázi a akci opakujte později.
* Pokud je kopírování úspěšné, **state_desc** sloupec zobrazení sys.databases pro novou databázi je nastavena na **ONLINE**. Kopírování je dokončeno a nová databáze je běžná databáze, kterou lze změnit nezávisle na zdrojové databázi.

> [!NOTE]
> Pokud se rozhodnete zrušit kopírování, zatímco probíhá, spusťte [příkaz DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) v nové databázi.

> [!IMPORTANT]
> Pokud potřebujete vytvořit kopii s podstatně menším cílem služby než zdroj, cílová databáze nemusí mít dostatečné prostředky k dokončení procesu seeding a může způsobit selhání kopírování opery. V tomto scénáři použijte požadavek geografického obnovení k vytvoření kopie na jiném serveru nebo v jiné oblasti. Další informace najdete [v tématu Obnovení databáze Azure SQL pomocí záloh databází.](sql-database-recovery-using-backups.md#geo-restore)

## <a name="resolve-logins"></a>Vyřešit přihlášení

Po nové databáze je online na cílovém serveru, použijte [příkaz ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) přemapovat uživatele z nové databáze na přihlášení na cílovém serveru. Informace o řešení osamocených uživatelů naleznete [v tématu Poradce při potížích s osamocené uživatele](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Viz taky [Jak spravovat zabezpečení databáze Azure SQL po zotavení po havárii](sql-database-geo-replication-security-config.md).

Všichni uživatelé v nové databázi si zachovávají oprávnění, která měli ve zdrojové databázi. Uživatel, který inicioval databázovou kopii, se stane vlastníkem databáze nové databáze. Po kopírování úspěšné a před ostatní uživatelé jsou přemapovány, pouze vlastník databáze můžete přihlásit do nové databáze.

Informace o správě uživatelů a přihlášení při kopírování databáze na jiný databázový server SQL najdete v tématu [Správa zabezpečení databáze Azure SQL po zotavení po havárii](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Chyby kopírování databáze

Při kopírování databáze v Azure SQL Database se mohou vyskytnou následující chyby. Další informace najdete v tématu [Kopírování databáze služby Azure SQL Database](sql-database-copy.md).

| Kód chyby | Severity | Popis |
| ---:| ---:|:--- |
| 40635 |16 |Klient s IP adresou %&#x2a;ls je dočasně zakázán. |
| 40637 |16 |Vytvoření kopie databáze je nyní zakázáno. |
| 40561 |16 |Kopírování databáze se nezdařilo. Zdrojová nebo cílová databáze neexistuje. |
| 40562 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze byla vynechána. |
| 40563 |16 |Kopírování databáze se nezdařilo. Cílová databáze byla vynechána. |
| 40564 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Přetažení cílové databáze a akci opakujte. |
| 40565 |16 |Kopírování databáze se nezdařilo. Není povoleno více než 1 souběžná kopie databáze ze stejného zdroje. Přetažení cílové databáze a akci opakujte později. |
| 40566 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Přetažení cílové databáze a akci opakujte. |
| 40567 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Přetažení cílové databáze a akci opakujte. |
| 40568 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze je nedostupná. Přetažení cílové databáze a akci opakujte. |
| 40569 |16 |Kopírování databáze se nezdařilo. Cílová databáze je nedostupná. Přetažení cílové databáze a akci opakujte. |
| 40570 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Přetažení cílové databáze a akci opakujte později. |
| 40571 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Přetažení cílové databáze a akci opakujte později. |

## <a name="next-steps"></a>Další kroky

- Informace o přihlášení najdete [v tématech Správa přihlášení](sql-database-manage-logins.md) a Jak spravovat zabezpečení databáze Azure SQL po zotavení po [havárii](sql-database-geo-replication-security-config.md).
- Informace o exportu databáze naleznete v [tématu Export databáze do bacpac](sql-database-export.md).
