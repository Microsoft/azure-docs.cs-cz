---
title: Kopírování databáze
description: Vytvořte v Azure SQL Database na stejném serveru nebo na jiném serveru reakční konzistentní kopii existující databáze.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: ''
ms.date: 07/29/2020
ms.openlocfilehash: f6a3ccbcdb3d29434b196dbf75dc61c4177de271
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284274"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopírování přetransakční kopie databáze v Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database poskytuje několik metod pro vytvoření kopie existující [databáze](single-database-overview.md) na stejném nebo jiném serveru. Databázi můžete kopírovat pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo T-SQL.

## <a name="overview"></a>Přehled

Kopie databáze je nekonzistentně konzistentní snímek zdrojové databáze k určitému bodu v čase po zahájení žádosti o zkopírování. Pro kopii můžete vybrat stejný server nebo jiný server. Také se můžete rozhodnout zachovat úroveň služby a výpočetní velikost zdrojové databáze nebo použít jinou výpočetní velikost v rámci stejné nebo jiné úrovně služby. Po dokončení kopírování se tato kopie bude plně funkční a nezávislá databáze. Přihlašovací jména, uživatele a oprávnění ve zkopírované databázi jsou spravovány nezávisle na zdrojové databázi. Kopie se vytvoří pomocí technologie geografické replikace. Po dokončení počáteční repliky se připojení geografické replikace automaticky ukončí. Všechny požadavky pro používání geografické replikace se vztahují i na operaci kopírování databáze. Podrobnosti najdete v tématu [Aktivní geografická replikace – přehled](active-geo-replication-overview.md) .

## <a name="logins-in-the-database-copy"></a>Přihlašovací údaje v kopii databáze

Když zkopírujete databázi na stejný server, můžete použít stejné přihlašovací údaje i v obou databázích. Objekt zabezpečení, který použijete ke kopírování databáze, se bude vlastníkem databáze v nové databázi.

Při kopírování databáze na jiný server se objekt zabezpečení, který inicioval operaci kopírování na cílovém serveru, stal vlastníkem nové databáze.

Bez ohledu na cílový server se všechny uživatele databáze, jejich oprávnění a identifikátory zabezpečení (SID) zkopírují do kopie databáze. Použití [uživatelů databáze s omezením](logins-create-manage.md) pro přístup k datům zajišťuje, že zkopírovaná databáze má stejné přihlašovací údaje uživatele, takže po dokončení kopie můžete k ní hned přistupovat pomocí stejných přihlašovacích údajů.

Pokud používáte pro přístup k datům přihlášení na úrovni serveru a kopírujete databázi na jiný server, nemusí přístup založený na přihlášení fungovat. K tomu může dojít, protože přihlašovací údaje na cílovém serveru neexistují nebo se jejich hesla a identifikátory zabezpečení (SID) liší. Další informace o správě přihlášení po zkopírování databáze na jiný server najdete v tématu [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md). Po úspěšném dokončení operace kopírování na jiném serveru a před přemapováním dalších uživatelů se může do zkopírované databáze přihlásit pouze přihlášení přidružené k vlastníkovi databáze nebo správce serveru. Chcete-li vyřešit přihlášení a vytvořit přístup k datům po dokončení operace kopírování, přečtěte si téma [řešení přihlášení](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Kopírování s použitím webu Azure Portal

Pokud chcete zkopírovat databázi pomocí Azure Portal, otevřete stránku pro vaši databázi a pak klikněte na **Kopírovat**.

   ![Kopie databáze](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopírování pomocí PowerShellu nebo rozhraní příkazového řádku Azure

Chcete-li zkopírovat databázi, použijte následující příklady.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro PowerShell použijte rutinu [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopie databáze je asynchronní operace, ale cílová databáze je vytvořena ihned po přijetí žádosti. Pokud potřebujete operaci kopírování zrušit, když stále probíhá, odstraňte cílovou databázi pomocí rutiny [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Úplný vzorový skript PowerShellu najdete v tématu [kopírování databáze na nový server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopie databáze je asynchronní operace, ale cílová databáze je vytvořena ihned po přijetí žádosti. Pokud potřebujete operaci kopírování zrušit, když stále probíhá, přetáhněte cílovou databázi pomocí příkazu [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Kopírování pomocí jazyka Transact-SQL

Přihlaste se k hlavní databázi pomocí přihlašovacích údajů správce serveru nebo přihlašovacích údajů, které vytvořila databázi, kterou chcete zkopírovat. Aby bylo kopírování databáze úspěšné, přihlášení, která nejsou správcem serveru, musí být členy této `dbmanager` role. Další informace o přihlášeních a připojení k serveru najdete v tématu [Správa přihlášení](logins-create-manage.md).

Spustit kopírování zdrojové databáze pomocí databáze pro [vytvoření... JAKO kopie](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) příkazu. Příkaz T-SQL pokračuje v běhu, dokud se nedokončí operace kopírování databáze.

> [!NOTE]
> Ukončení příkazu T-SQL neukončí operaci kopírování databáze. Chcete-li operaci ukončit, vyřaďte cílovou databázi.
>

### <a name="copy-to-the-same-server"></a>Kopírovat na stejný server

Přihlaste se k hlavní databázi pomocí přihlašovacích údajů správce serveru nebo přihlašovacích údajů, které vytvořila databázi, kterou chcete zkopírovat. Aby kopírování databáze bylo úspěšné, přihlášení, která nejsou správcem serveru, musí být členy této `dbmanager` role.

Tento příkaz zkopíruje Databáze1 do nové databáze s názvem databáze 2 na stejném serveru. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>Kopírovat na jiný server

Přihlaste se k hlavní databázi cílového serveru, kde se má vytvořit nová databáze. Použijte přihlašovací jméno, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze na zdrojovém serveru. Přihlášení na cílovém serveru musí být také členem `dbmanager` role nebo účtem správce serveru.

Tento příkaz zkopíruje Databáze1 na Server1 do nové databáze s názvem databáze 2 na Server2. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Oba servery brány firewall musí být nakonfigurované tak, aby umožňovaly příchozí připojení z IP adresy klienta, který vytvořil databázi T-SQL... JAKO kopie příkazu.

### <a name="copy-to-a-different-subscription"></a>Kopírovat do jiného předplatného

Pomocí postupu v části [kopírování SQL Database do jiného serveru](#copy-to-a-different-server) můžete zkopírovat databázi na server v jiném předplatném pomocí jazyka T-SQL. Ujistěte se, že používáte přihlášení, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze. Kromě toho musí být přihlašovací jméno členem `dbmanager` role nebo správce serveru na zdrojovém i cílovém serveru.

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell a rozhraní příkazového řádku Azure CLI nepodporují kopírování databáze do jiného předplatného.

> [!TIP]
> Kopírování databáze pomocí T-SQL podporuje kopírování databáze z předplatného v jiném tenantovi Azure. Tato direktiva se podporuje jenom v případě, že se k přihlášení k cílovému serveru používá přihlášení pomocí ověřování SQL.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Sledování průběhu operace kopírování

Pomocí dotazu na zobrazení [Sys. databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [Sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)a [Sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) monitorujte proces kopírování. V průběhu kopírování je sloupec **state_desc** zobrazení sys. databases pro novou databázi nastaven na **kopírování**.

* Pokud kopírování neproběhne úspěšně, je sloupec **state_desc** zobrazení sys. databases pro novou databázi nastaven na hodnotu **podezřelý**. Spusťte příkaz DROP v nové databázi a opakujte akci později.
* Pokud je kopírování úspěšné, sloupec **state_desc** zobrazení sys. databases pro novou databázi je nastaven na hodnotu **online**. Kopírování je dokončeno a nová databáze je běžná databáze, kterou lze změnit nezávisle na zdrojové databázi.

> [!NOTE]
> Pokud se rozhodnete zrušit kopírování během procesu, spusťte příkaz [drop Database](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) v nové databázi.

> [!IMPORTANT]
> Pokud potřebujete vytvořit kopii s podstatně menším cílem služby, než má zdroj, cílová databáze nemusí mít dostatek prostředků k dokončení procesu osazení a může způsobit selhání aplikace kopírování. V tomto scénáři použijte k vytvoření kopie na jiném serveru nebo jiné oblasti požadavek geografického obnovení. Další informace najdete v tématu [obnovení Azure SQL Database pomocí záloh databáze](recovery-using-backups.md#geo-restore) .

## <a name="azure-roles-to-manage-database-copy"></a>Role Azure pro správu kopie databáze

Chcete-li vytvořit kopii databáze, budete muset být v následujících rolích.

* Vlastník předplatného nebo
* SQL Server role přispěvatele nebo
* Vlastní role ve zdrojové a cílové databázi s následujícím oprávněním:

   Microsoft. SQL/servery/databáze/číst Microsoft. SQL/servery/databáze/zapisovat

Pokud chcete kopii databáze zrušit, budete muset být v následujících rolích.

* Vlastník předplatného nebo
* SQL Server role přispěvatele nebo
* Vlastní role ve zdrojové a cílové databázi s následujícím oprávněním:

   Microsoft. SQL/servery/databáze/číst Microsoft. SQL/servery/databáze/zapisovat

Pokud chcete spravovat kopii databáze pomocí Azure Portal, budete potřebovat taky následující oprávnění:

   Microsoft. Resources/Subscriptions/Resources/číst Microsoft. Resources/Subscriptions/Resources/Write Microsoft. Resources/nasazování/čtení Microsoft. Resources/Deployments/Write Microsoft. Resources/Deployments/

Pokud chcete zobrazit operace v rámci nasazení ve skupině prostředků na portálu, operace napříč více zprostředkovateli prostředků, včetně operací SQL, budete potřebovat tyto další role Azure:

   Microsoft. Resources/Subscriptions/ResourceGroups/nasazení/operace/čtení Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/operationstatuses/Read

## <a name="resolve-logins"></a>Vyřešit přihlášení

Jakmile je nová databáze na cílovém serveru online, pomocí příkazu [ALTER User](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) přemapujte uživatele z nové databáze na přihlašovací údaje na cílovém serveru. Pokud chcete vyřešit osamocené uživatele, přečtěte si téma [řešení potíží s osamocenými](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)uživateli. Viz také [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md).

Všichni uživatelé v nové databázi si uchovávají oprávnění, která měla ve zdrojové databázi. Uživatel, který inicioval kopii databáze, se stal vlastníkem databáze nové databáze. Po úspěšném dokončení kopírování a před přemapováním dalších uživatelů se může k nové databázi přihlásit pouze vlastník databáze.

Další informace o správě uživatelů a přihlášení po zkopírování databáze na jiný server najdete v tématu [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Chyby kopírování databáze

Při kopírování databáze v Azure SQL Database může dojít k následujícím chybám. Další informace najdete v tématu [Kopírování databáze služby Azure SQL Database](database-copy.md).

| Kód chyby | Závažnost | Popis |
| ---:| ---:|:--- |
| 40635 |16 |Klient s IP adresou%. &#x2a;LS je dočasně zakázaný. |
| 40637 |16 |Vytvoření kopie databáze je aktuálně zakázané. |
| 40561 |16 |Kopírování databáze se nezdařilo. Buď zdrojová, nebo cílová databáze neexistuje. |
| 40562 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze byla vyřazena. |
| 40563 |16 |Kopírování databáze se nezdařilo. Cílová databáze byla vyřazena. |
| 40564 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40565 |16 |Kopírování databáze se nezdařilo. Nepovoluje se více než jedna souběžná kopie databáze ze stejného zdroje. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |
| 40566 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40567 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40568 |16 |Kopírování databáze se nezdařilo. Zdrojová databáze se stala nedostupnou. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40569 |16 |Kopírování databáze se nezdařilo. Cílová databáze již není k dispozici. Vyřaďte prosím cílovou databázi a zkuste to znovu. |
| 40570 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |
| 40571 |16 |Kopírování databáze se nezdařilo z důvodu vnitřní chyby. Vyřaďte prosím cílovou databázi a zkuste to znovu později. |

## <a name="next-steps"></a>Další kroky

* Informace o přihlášeních najdete v tématech [Správa přihlášení](logins-create-manage.md) a [Správa zabezpečení Azure SQL Database po zotavení po havárii](active-geo-replication-security-configure.md).
* Informace o exportu databáze najdete v tématu [Export databáze do BacPac](database-export.md).
