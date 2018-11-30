---
title: Kopírovat databázi Azure SQL | Dokumentace Microsoftu
description: Vytvoření transakčně konzistentní kopie stávající databázi Azure SQL na stejném serveru nebo na jiný server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: f3da1a8cef5abc8fd30a0dc7760005ad5fff5446
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335365"
---
# <a name="copy-an-transactionally-consistent-copy-of-an-azure-sql-database"></a>Zkopírujte transakčně konzistentní kopie databáze Azure SQL

Azure SQL Database nabízí několik metod pro vytvoření transakčně konzistentní kopie stávající databázi Azure SQL na stejný server nebo jiný server. Zkopírování databáze SQL pomocí webu Azure portal, Powershellu nebo T-SQL. 

## <a name="overview"></a>Přehled

Kopie databáze je snímek zdrojové databáze k datu požadavku kopírování. Můžete vybrat na stejný server nebo jiný server, úroveň služby a velikost výpočetního nebo různými výpočetními velikost v rámci stejné úrovně služeb (edice). Jakmile se kopírování dokončí, bude plně funkční, nezávislé databáze. V tomto okamžiku můžete upgradovat nebo downgradovat pro všechny edice. Přihlášení, uživatelů a oprávnění se dají spravovat nezávisle.  

> [!NOTE]
> [Automatizované zálohování databáze](sql-database-automated-backups.md) se používají při vytvoření kopie databáze.

## <a name="logins-in-the-database-copy"></a>Přihlašovacích údajů ve službě kopie databáze

Při kopírování databáze do stejného logického serveru stejné přihlašovací údaje je možné s oběma databázemi. Objekt že pomocí zkopírovat databázi zabezpečení stává vlastníkem databáze na novou databázi. Všichni uživatelé databáze, oprávnění k nim a jejich identifikátory zabezpečení (SID) se zkopírují do kopie databáze.  

Při kopírování databáze do různých logický server, na nový server zaregistrovaný objekt zabezpečení stává vlastníkem databáze na novou databázi. Pokud používáte [uživatelé databáze s omezením](sql-database-manage-logins.md) pro přístup k datům, ujistěte se, že primární a sekundární databáze vždy mají stejné přihlašovací údaje uživatele, tak, aby po dokončení kopírování budete mít okamžitě přístup pomocí stejných přihlašovacích údajů . 

Pokud používáte [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), můžete zcela eliminovat potřebu správy přihlašovací údaje v kopii. Ale při kopírování databáze na nový server přístup na základě přihlášení nemusí fungovat, protože přihlášení neexistují na novém serveru. Další informace o správě přihlášení při kopírování databáze do různých logický server, naleznete v tématu [Správa zabezpečení služby Azure SQL database po zotavení po havárii](sql-database-geo-replication-security-config.md). 

Po úspěšném kopírování a předtím, než ostatní uživatelé budou přemapovány, pouze přihlášení, která iniciovala kopírování, vlastník databáze přihlásit k nové databázi. K vyřešení přihlášení po dokončení operace kopírování, zobrazit [vyřešit přihlášení](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopírování databáze pomocí webu Azure portal

Pokud chcete zkopírovat databázi pomocí webu Azure portal, otevřete stránku pro vaši databázi a pak klikněte na **kopírování**. 

   ![Kopírování databáze](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopírování databáze pomocí prostředí PowerShell

Pokud chcete zkopírovat databázi s použitím prostředí PowerShell, použijte [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) rutiny. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Skript úplnou ukázku najdete v tématu [zkopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopírování databáze s použitím příkazů jazyka Transact-SQL

Přihlaste se k hlavní databázi pomocí hlavního přihlášení úrovni serveru nebo přihlášení, které vytvořili databázi, kterou chcete zkopírovat. Uživatelé, kteří nejsou hlavním přihlášením na úrovni serveru databáze kopírování úspěšné, musí být členem dbmanager role. Další informace o přihlášeních a připojení k serveru najdete v tématu [Správa přihlašování](sql-database-manage-logins.md).

Zahájit kopírování zdrojová databáze s [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) příkazu. Spouštění tohoto příkazu se zahájí proces kopírování databáze. Kopírování databáze je asynchronního procesu, vrátí příkaz CREATE DATABASE před dokončením kopírování databáze.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopírování databáze SQL database na stejný server
Přihlaste se k hlavní databázi pomocí hlavního přihlášení úrovni serveru nebo přihlášení, které vytvořili databázi, kterou chcete zkopírovat. Uživatelé, kteří nejsou hlavním přihlášením na úrovni serveru databáze kopírování úspěšné, musí být členem dbmanager role.

Tento příkaz zkopíruje databáze 1 pro novou databázi s názvem databáze 2 na stejném serveru. V závislosti na velikosti databáze kopírování operace může trvat nějakou dobu.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopírování databáze SQL na jiný server

Přihlaste se k hlavní databázi cílového serveru, kde se má vytvořit novou databázi serveru SQL database. Použijte přihlašovací údaje, která má stejné jméno a heslo jako vlastník databáze zdrojové databáze na zdrojovém serveru SQL database. Přihlašovací jméno na cílovém serveru musí také být členem dbmanager role nebo hlavního přihlášení úrovni serveru.

Tento příkaz zkopíruje databáze 1 na serveru1 do nové databáze s názvem databáze 2 na serveru2. V závislosti na velikosti databáze kopírování operace může trvat nějakou dobu.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Sledovat průběh operace kopírování

Pomocí dotazu na zobrazení sys.databases a sys.dm_database_copies monitorujte proces kopírování. Probíhá kopírování, **state_desc** sloupec zobrazení sys.databases pro novou databázi je nastaven na **kopírování**.

* Pokud kopírování selže, **state_desc** sloupec zobrazení sys.databases pro novou databázi je nastaven na **podezření**. Spusťte příkaz DROP na novou databázi a zkuste to znovu později.
* Pokud je kopírování úspěšné, **state_desc** sloupec zobrazení sys.databases pro novou databázi je nastaven na **ONLINE**. Kopírování je kompletní a novou databázi je standardní databázi, která lze změnit nezávisle na zdrojové databázi.

> [!NOTE]
> Pokud se rozhodnete zrušit kopírování, zatímco bude probíhat, spusťte [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) příkaz na novou databázi. Další možností spouštění příkazu DROP DATABASE ve zdrojové databázi zruší také proces kopírování.
> 

## <a name="resolve-logins"></a>Vyřešení přihlášení

Po online na cílovém serveru novou databázi pomocí [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) příkaz přemapování uživatelům přihlášení na cílovém serveru z nové databáze. Osamocené uživatele vyřešit, najdete v článku [osamocené uživatele vyřešit](https://msdn.microsoft.com/library/ms175475.aspx). Viz také [Správa zabezpečení služby Azure SQL database po zotavení po havárii](sql-database-geo-replication-security-config.md).

Všichni uživatelé v nové databázi zachovat oprávnění, která měly ve zdrojové databázi. Uživatel, který inicioval kopie databáze se stává vlastníkem databáze nové databáze a je přiřazen nový identifikátor zabezpečení (SID). Po úspěšném kopírování a předtím, než ostatní uživatelé budou přemapovány, pouze přihlášení, která iniciovala kopírování, vlastník databáze přihlásit k nové databázi.

Další informace o správě uživatelů a přihlašovacích údajů při kopírování databáze do různých logický server, naleznete v tématu [Správa zabezpečení služby Azure SQL database po zotavení po havárii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Další postup

* Informace o přihlašování najdete v tématu [Správa přihlašování](sql-database-manage-logins.md) a [Správa zabezpečení služby Azure SQL database po zotavení po havárii](sql-database-geo-replication-security-config.md).
* Export databáze, najdete v článku [Export databáze do souboru BACPAC](sql-database-export.md).
