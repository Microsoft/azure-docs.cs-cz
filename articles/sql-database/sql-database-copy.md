---
title: Zkopírujte Azure SQL database | Microsoft Docs
description: Vytvořte stavu transakční konzistence kopii existující databázi Azure SQL na stejný server nebo jiný server.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: 2217df046cf95ddcd12f6dcaa41b2c3f8b0090f6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646197"
---
# <a name="copy-an-azure-sql-database"></a>Kopírování databáze Azure SQL

Azure SQL Database nabízí několik metod pro vytváření stavu transakční konzistence kopii existující databázi Azure SQL na stejném serveru nebo jiný server. Můžete kopírovat databázi SQL pomocí portálu Azure, PowerShell nebo T-SQL. 

## <a name="overview"></a>Přehled

Kopie databáze je snímek zdrojové databáze od času požadavku kopírování. Můžete vybrat stejný server nebo jiný server, jeho vrstvy služeb a úroveň výkonu nebo úroveň různých výkonu v rámci stejné služby vrstvě (edice). Po dokončení kopírování bude databáze plně funkční, nezávislé. V tomto okamžiku můžete upgradovat nebo ponížit na libovolná edice. Přihlášení, uživatelů a oprávnění lze spravovat nezávisle.  

## <a name="logins-in-the-database-copy"></a>Přihlášení v kopii databáze

Při kopírování databáze do stejného logického serveru, stejné přihlášení lze použít v obou databází. Objekt, že slouží ke zkopírování databáze zabezpečení se změní na vlastníka databáze na novou databázi. Všichni uživatelé databáze, oprávnění k nim a jejich identifikátory zabezpečení (SID) se zkopírují do kopie databáze.  

Při kopírování databáze do různých logického serveru, na novém serveru objekt zabezpečení se změní na vlastníka databáze na novou databázi. Pokud používáte [obsažené uživatelů databáze](sql-database-manage-logins.md) pro přístup k datům, zajistěte, že primární a sekundární databáze vždy měl stejných uživatelských přihlašovacích údajů, tak, aby po dokončení kopírování budete mít okamžitě přístup se stejnými pověřeními. 

Pokud používáte [Azure Active Directory](../active-directory/active-directory-whatis.md), můžete zcela eliminovat potřebu správy přihlašovací údaje do kopie. Ale při kopírování databáze na nový server, přístup na základě přihlášení nemusí fungovat, protože přihlášení nejsou k dispozici na novém serveru. Další informace o správě přihlášení při kopírovat databázi na jiný logický Server najdete v tématu [jak spravovat zabezpečení databáze Azure SQL po obnovení po havárii](sql-database-geo-replication-security-config.md). 

Po úspěšném provedení kopírování a předtím, než jsou přemapování jiných uživatelů, pouze přihlášení, která iniciovala kopírování, vlastník databáze může přihlásit k nové databázi. Po dokončení operace kopírování, odstranění přihlášení, naleznete v článku [vyřešit přihlášení](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopírovat databázi pomocí portálu Azure

Kopírování databáze pomocí portálu Azure, otevřete stránku pro vaši databázi a pak klikněte na tlačítko **kopie**. 

   ![Kopie databáze](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopírování databáze pomocí prostředí PowerShell

Při kopírování databáze pomocí prostředí PowerShell, použijte [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) rutiny. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Pro dokončení ukázkový skript, najdete v části [kopírovat databázi na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopírovat databázi pomocí jazyka Transact-SQL

Přihlaste se k hlavní databázi pomocí hlavního přihlášení úrovni serveru nebo přihlášení, který vytvořili databázi, kterou chcete zkopírovat. Přihlášení, které nejsou úrovni serveru hlavní databáze kopírování proběhla úspěšně, musí být členy dbmanager role. Další informace o připojení k serveru a přihlášení najdete v tématu [spravovat přihlášení](sql-database-manage-logins.md).

Spusťte kopírování zdrojová databáze s [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) příkaz. Provádění tento příkaz spustí proces kopírování databáze. Vzhledem k tomu, že asynchronní proces kopírování databáze je, vrátí příkaz CREATE DATABASE před dokončením kopírování databáze.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopírovat databázi SQL na stejném serveru
Přihlaste se k hlavní databázi pomocí hlavního přihlášení úrovni serveru nebo přihlášení, který vytvořili databázi, kterou chcete zkopírovat. Přihlášení, které nejsou úrovni serveru hlavní databáze kopírování proběhla úspěšně, musí být členy dbmanager role.

Tento příkaz zkopíruje Database1 pro novou databázi s názvem Database2 na stejném serveru. V závislosti na velikosti vaší databáze kopírování operace může trvat delší dobu.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopírovat databázi SQL na jiný server

Přihlaste se k databázi hlavního cílového serveru, serveru databáze SQL, kde má být vytvořen nové databáze. Pomocí přihlášení, která má stejné jméno a heslo jako vlastník databáze zdrojové databáze na zdrojovém serveru databáze SQL. Přihlášení na cílovém serveru musí také být členem dbmanager role nebo hlavní přihlášení na úrovni serveru.

Tento příkaz zkopíruje Database1 na serveru1 pro novou databázi s názvem Database2 na serveru2. V závislosti na velikosti vaší databáze kopírování operace může trvat delší dobu.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Sledujte průběh operace kopírování

Monitorujte proces kopírování pomocí dotazu na zobrazení sys.databases a sys.dm_database_copies zobrazení. Probíhá kopírování, **state_desc** sloupec zobrazení sys.databases zobrazení pro novou databázi je nastavený na **kopírování**.

* Pokud je kopírování selže, **state_desc** sloupec zobrazení sys.databases zobrazení pro novou databázi je nastavený na **podezření**. Spusťte příkaz DROP na novou databázi a opakujte akci později.
* Pokud je kopírování úspěšné, **state_desc** sloupec zobrazení sys.databases zobrazení pro novou databázi je nastavený na **ONLINE**. Kopírování je dokončena a nové databáze je standardní databázi, která lze změnit nezávislé na zdrojové databáze.

> [!NOTE]
> Pokud se rozhodnete zrušit kopírování během probíhající, provést [příkazu DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) příkaz na nové databáze. Alternativně provádění příkazu DROP DATABASE na zdrojové databáze zruší také proces kopírování.
> 

## <a name="resolve-logins"></a>Vyřešení přihlášení

Po nové databáze je online na cílovém serveru, použijte [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) příkaz přemapování uživatelům přihlášení na cílovém serveru z nové databáze. Osamocené uživatele vyřešit, najdete v tématu [řešení potíží s osamocených uživatelů](https://msdn.microsoft.com/library/ms175475.aspx). Viz také [jak spravovat zabezpečení databáze Azure SQL po obnovení po havárii](sql-database-geo-replication-security-config.md).

Všichni uživatelé v nové databáze zachovat oprávnění, která měla ve zdrojové databázi. Uživatel, který zahájil kopírování databáze se stane vlastník databáze nové databáze a je přiřazen nový identifikátor zabezpečení (SID). Po úspěšném provedení kopírování a předtím, než jsou přemapování jiných uživatelů, pouze přihlášení, která iniciovala kopírování, vlastník databáze může přihlásit k nové databázi.

Další informace o správě uživatelů a přihlášení při kopírování databáze do různých logického serveru najdete v tématu [jak spravovat zabezpečení databáze Azure SQL po obnovení po havárii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Další postup

* Informace o přihlášení, naleznete v části [spravovat přihlášení](sql-database-manage-logins.md) a [jak spravovat zabezpečení databáze Azure SQL po obnovení po havárii](sql-database-geo-replication-security-config.md).
* Exportu databáze, najdete v článku [databázi exportovat do souboru BACPAC](sql-database-export.md).
