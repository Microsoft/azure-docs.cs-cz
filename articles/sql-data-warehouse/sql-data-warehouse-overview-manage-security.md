---
title: Zabezpečení databáze v SQL Data Warehouse | Microsoft Docs
description: Tipy pro zabezpečení databáze v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: c42b065a307d5e10882c621191318a667e78795c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Zabezpečení databáze v SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Tento článek vás provede základy zabezpečení databáze Azure SQL Data Warehouse. Konkrétně, získá tento článek jste začali s prostředky pro omezení přístupu, ochrany dat a monitorování aktivit v databázi.

## <a name="connection-security"></a>Zabezpečení připojení
Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Server i databáze používají pravidla brány firewall k zamítnutí pokusů o připojení z IP adres, které nejsou výslovně povolené. Povolit připojení z vaší aplikace nebo klientský počítač veřejnou IP adresu, musíte nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí portálu Azure, rozhraní API REST nebo PowerShell. Doporučujeme co nejvíce omezit rozsah IP adres povolených v serverové bráně firewall.  Pro přístup k Azure SQL Data Warehouse z místního počítače, zkontrolujte, zda že v bráně firewall na vaší sítí a místním počítači umožňuje odchozí komunikaci na portu TCP 1433.  

SQL Data Warehouse používá pravidla brány firewall na úrovni serveru. Pravidla brány firewall na úrovni databáze nepodporuje. Další informace najdete v tématu [brány firewall databáze Azure SQL Database][Azure SQL Database firewall], [příkaz sp_set_firewall_rule][sp_set_firewall_rule].

Ve výchozím nastavení jsou šifrované připojení k SQL Data Warehouse.  Změny nastavení připojení můžete zakázat šifrování se ignorují.

## <a name="authentication"></a>Authentication
Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Data Warehouse aktuálně podporuje ověřování systému SQL Server pomocí uživatelského jména a hesla a službou Azure Active Directory. 

Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů, můžete ověřovat pro všechny databáze na tomto serveru jako vlastníka databáze, nebo "dbo" pomocí ověřování systému SQL Server.

Však jako osvědčený postup, by měl uživatele ve vaší organizaci použít jiný účet k ověření. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížení rizika škodlivých aktivit, v případě, že kód aplikace bude zranitelný vůči útoku Injektáž SQL. 

Chcete-li vytvořit uživatele ověřeného SQL Server, připojte se ke **hlavní** databáze na serveru s vaše přihlašovací jméno správce serveru a vytvořte nové přihlašovací údaje serveru.  Kromě toho je vhodné vytvořit uživateli v hlavní databázi pro uživatele Azure SQL Data Warehouse. Vytváření uživatele v předloze umožňuje uživateli přihlásit se pomocí nástroje, například aplikace SSMS bez zadání názvu databáze.  Také to umožňuje, aby uživatelé používali Průzkumník objektů pokud chcete zobrazit všechny databáze na serveru SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Připojte se k vaší **databázi SQL Data Warehouse** s vaše přihlašovací jméno správce serveru a vytvořte uživatele databáze založené na přihlášení server, který jste vytvořili.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pokud chcete udělit uživatelské oprávnění k provedení další operace, jako je vytvoření přihlášení nebo vytvoření nových databází, přiřaďte uživatele do `Loginmanager` a `dbmanager` role v hlavní databázi. Další informace o těchto dalších rolí a ověřování k databázi SQL, najdete v části [Správa databází a přihlašovacích údajů ve službě Azure SQL Database][Managing databases and logins in Azure SQL Database].  Další informace najdete v tématu [připojení k SQL Data Warehouse pomocí pomocí Azure ověřování služby Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorizace
Autorizace odkazuje na co můžete dělat v databázi Azure SQL Data Warehouse. Autorizace oprávnění určuje členství v rolích a oprávnění. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Ke správě rolí, můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Způsoby dál omezit, co dělat s Azure SQL Data Warehouse uživatele:

* Podrobné [oprávnění] [ Permissions] umožňují řízení operací, které můžete u jednotlivých sloupců tabulky, zobrazení, schémata, postupy a další objekty v databázi. Pomocí oprávnění na podrobné úrovni mít většina řízení a přidělte minimální oprávnění, které jsou nezbytné. 
* [Rolí databáze] [ Database roles] jiného, než db_datareader a db_datawriter lze použít k vytvoření výkonnější aplikace uživatelské účty nebo méně výkonná účty pro správu. Předdefinované pevné databázové role poskytují snadný způsob, jak udělit oprávnění, ale může mít za následek přidělení více oprávnění, než je potřeba.
* [Uložené procedury] [ Stored procedures] umožňují omezit akce, které můžete provést na databázi.

Následující příklad uděluje přístup pro čtení na uživatelem definované schéma.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a logické servery z portálu Azure nebo pomocí rozhraní API služby Azure Resource Manager řídí přiřazení rolí portálu uživatelského účtu. Další informace najdete v tématu [řízení přístupu na základě rolí na portálu Azure][Role-based access control in Azure portal].

## <a name="encryption"></a>Šifrování
Azure SQL Data Warehouse transparentní dat šifrování (TDE) pomáhá chránit před ohrožením škodlivých aktivit pomocí šifrování a dešifrování dat v klidovém stavu.  Při šifrování databáze, přidružených záloh a souborů protokolů transakci jsou šifrované bez nutnosti změny aplikace. Šifrování TDE zašifruje úložiště celé databáze pomocí symetrický klíč s názvem šifrovací klíč databáze. 

V databázi SQL šifrovací klíč databáze je chráněn certifikát integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server databáze SQL. Microsoft automaticky otočí tyto certifikáty alespoň jednou za 90 dní. Šifrovacího algoritmu používaného funkcí SQL Data Warehouse je AES 256. Obecný popis TDE, najdete v části [transparentní šifrování dat][Transparent Data Encryption].

Můžete šifrovat databázi pomocí [portál Azure] [ Encryption with Portal] nebo [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Další postup
Podrobnosti a příklady o připojení k SQL Data Warehouse pomocí různých protokolů, najdete v tématu [připojit k SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
