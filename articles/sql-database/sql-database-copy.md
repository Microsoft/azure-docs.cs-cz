---
title: Kopírování databáze SQL Azure | Microsoft Docs
description: Vytvořte reakční konzistentní kopii existující databáze SQL Azure na stejném nebo jiném serveru.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: dc82ace4c5bf1204075bfa5e34d0b1489a3acb98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876276"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopírování reakční konzistentní kopie databáze SQL Azure

Azure SQL Database poskytuje několik metod pro vytvoření reakční konzistentní kopie existující databáze SQL Azure (izolovaná[databáze](sql-database-single-database.md)) na jednom nebo jiném serveru. Databázi SQL můžete kopírovat pomocí Azure Portal, PowerShellu nebo T-SQL. 

## <a name="overview"></a>Přehled

Kopie databáze je snímek zdrojové databáze v době žádosti o zkopírování. Můžete vybrat stejný server nebo jiný server. Můžete také zvolit, že se má zachovat vrstva služby a výpočetní velikost, nebo použít jinou výpočetní velikost v rámci stejné úrovně služby (edice). Po dokončení kopírování se tato kopie bude plně funkční a nezávislá databáze. V tuto chvíli můžete upgradovat nebo downgradovat na libovolnou edici. Přihlašovací jména, uživatele a oprávnění se dají spravovat nezávisle.  

> [!NOTE]
> [Automatizované zálohy databáze](sql-database-automated-backups.md) se používají při vytváření kopie databáze.

## <a name="logins-in-the-database-copy"></a>Přihlašovací údaje v kopii databáze

Když zkopírujete databázi do stejného serveru SQL Database, můžete použít stejné přihlašovací údaje i v obou databázích. Objekt zabezpečení, který použijete ke kopírování databáze, se bude vlastníkem databáze v nové databázi. Všichni uživatelé databáze, jejich oprávnění a identifikátory zabezpečení (SID) se zkopírují do kopie databáze.  

Při kopírování databáze na jiný SQL Database Server se objekt zabezpečení na novém serveru bude vlastníkem databáze v nové databázi. Pokud používáte [uživatele databáze](sql-database-manage-logins.md) s omezením pro přístup k datům, ujistěte se, že primární i sekundární databáze mají vždy stejné přihlašovací údaje uživatele, takže po dokončení kopie můžete k ní hned přistupovat pomocí stejných přihlašovacích údajů. 

Pokud používáte [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), můžete úplně eliminovat nutnost spravovat přihlašovací údaje v kopii. Když ale kopírujete databázi na nový server, přístup na základě přihlašovacích údajů nemusí fungovat, protože přihlášení na novém serveru neexistují. Další informace o správě přihlášení po zkopírování databáze na jiný SQL Database Server najdete v tématu [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md). 

Po úspěšném dokončení kopírování a před přemapováním dalších uživatelů se může k nové databázi přihlásit pouze přihlášení, které iniciovalo kopírování, vlastníkem databáze. Chcete-li vyřešit přihlášení po dokončení operace kopírování, přečtěte si téma [řešení přihlášení](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopírování databáze pomocí Azure Portal

Pokud chcete zkopírovat databázi pomocí Azure Portal, otevřete stránku pro vaši databázi a pak klikněte na **Kopírovat**. 

   ![Kopie databáze](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopírování databáze pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K zkopírování databáze pomocí prostředí PowerShell použijte rutinu [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kompletní vzorový skript najdete v tématu [kopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopírování databáze pomocí jazyka Transact-SQL

Přihlaste se k hlavní databázi pomocí hlavního přihlášení na úrovni serveru nebo přihlašovacích údajů, které vytvořila databázi, kterou chcete zkopírovat. Aby kopírování databáze bylo úspěšné, přihlášení, která nejsou objekty zabezpečení na úrovni serveru, musí být členy role dbmanager. Další informace o přihlášeních a připojení k serveru najdete v tématu [Správa přihlášení](sql-database-manage-logins.md).

Spusťte kopírování zdrojové databáze pomocí příkazu [Create Database](https://msdn.microsoft.com/library/ms176061.aspx) . Spuštění tohoto příkazu zahájí proces kopírování databáze. Vzhledem k tomu, že kopírování databáze je asynchronní proces, příkaz CREATE DATABASE vrátí před dokončením kopírování databáze.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopírování databáze SQL na stejný server

Přihlaste se k hlavní databázi pomocí hlavního přihlášení na úrovni serveru nebo přihlašovacích údajů, které vytvořila databázi, kterou chcete zkopírovat. Aby kopírování databáze bylo úspěšné, přihlášení, která nejsou objekty zabezpečení na úrovni serveru, musí být členy role dbmanager.

Tento příkaz zkopíruje Databáze1 do nové databáze s názvem databáze 2 na stejném serveru. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopírování databáze SQL na jiný server

Přihlaste se k hlavní databázi cílového serveru, SQL Database Server, na kterém se má nová databáze vytvořit. Použijte přihlašovací jméno, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze na zdrojovém serveru SQL Database. Přihlášení na cílovém serveru musí být také členem role dbmanager nebo se může jednat o přihlášení hlavního objektu na úrovni serveru.

Tento příkaz zkopíruje Databáze1 na Server1 do nové databáze s názvem databáze 2 na Server2. V závislosti na velikosti databáze může dokončení operace kopírování nějakou dobu trvat.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Obě brány firewall serverů musí být nakonfigurované tak, aby umožňovaly příchozí připojení z IP adresy klienta, který vystavil příkaz T-SQL COPY.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopírování databáze SQL do jiného předplatného

Pomocí kroků popsaných v předchozí části můžete zkopírovat databázi na server SQL Database v jiném předplatném. Ujistěte se, že používáte přihlašovací jméno, které má stejné jméno a heslo jako vlastník databáze zdrojové databáze, a je členem role dbmanager nebo je přihlášení k hlavnímu objektu na úrovni serveru. 

> [!NOTE]
> [Azure Portal](https://portal.azure.com) nepodporuje kopírování do jiného předplatného, protože portál volá rozhraní API ARM a používá certifikáty odběrů k přístupu k oběma serverům zapojeným do geografické replikace.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Sledování průběhu operace kopírování

Pomocí dotazu na zobrazení sys. databases a sys. DM _database_copies monitorujte proces kopírování. I když probíhá kopírování, sloupec **state_desc** zobrazení sys. databases pro novou databázi je nastaven na **kopírování**.

* Pokud se kopírování nezdařilo, sloupec **state_desc** zobrazení sys. databases pro novou databázi je nastaven na hodnotu **podezření**. Spusťte příkaz DROP v nové databázi a opakujte akci později.
* Pokud je kopírování úspěšné, sloupec **state_desc** zobrazení sys. databases pro novou databázi je nastaven na hodnotu **online**. Kopírování je dokončeno a nová databáze je běžná databáze, kterou lze změnit nezávisle na zdrojové databázi.

> [!NOTE]
> Pokud se rozhodnete zrušit kopírování během procesu, spusťte příkaz [drop Database](https://msdn.microsoft.com/library/ms178613.aspx) v nové databázi. Také spuštění příkazu DROP DATABASE na zdrojové databázi také zruší proces kopírování.

## <a name="resolve-logins"></a>Vyřešit přihlášení

Jakmile je nová databáze na cílovém serveru online, pomocí příkazu [ALTER User](https://msdn.microsoft.com/library/ms176060.aspx) přemapujte uživatele z nové databáze na přihlašovací údaje na cílovém serveru. Pokud chcete vyřešit osamocené uživatele, přečtěte si téma [řešení potíží s osamocenými](https://msdn.microsoft.com/library/ms175475.aspx)uživateli. Viz také [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).

Všichni uživatelé v nové databázi si uchovávají oprávnění, která měla ve zdrojové databázi. Uživatel, který inicioval kopii databáze, se stal vlastníkem databáze nové databáze a je mu přiřazen nový identifikátor zabezpečení (SID). Po úspěšném dokončení kopírování a před přemapováním dalších uživatelů se může k nové databázi přihlásit pouze přihlášení, které iniciovalo kopírování, vlastníkem databáze.

Další informace o správě uživatelů a přihlášení po zkopírování databáze na jiný SQL Database Server najdete v tématu [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Další postup

* Informace o přihlášeních najdete v tématech [Správa přihlášení](sql-database-manage-logins.md) a [Správa zabezpečení služby Azure SQL Database po zotavení po havárii](sql-database-geo-replication-security-config.md).
* Informace o exportu databáze najdete v tématu [Export databáze do BacPac](sql-database-export.md).
