---
title: Zabezpečit databázi ve službě SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro vývoj řešení pro zabezpečení databáze ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: c3844d378b44d292b9a7eb631fa896d5f6e61dbe
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472180"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Zabezpečit databázi ve službě SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Tento článek vás provede základy zabezpečení databáze Azure SQL Data Warehouse. Konkrétně tento článek vám pomůže začít s prostředky pro omezení přístupu, ochranu dat a monitorování aktivit u databáze.

## <a name="connection-security"></a>Zabezpečení připojení
Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Server i databáze používají pravidla brány firewall k zamítnutí pokusů o připojení z IP adres, které nejsou výslovně povolené. Povolit připojení z vaší aplikace nebo veřejnou IP adresu klientského počítače, musíte nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí webu Azure portal, rozhraní REST API nebo PowerShell. Doporučujeme co nejvíce omezit rozsah IP adres povolených v serverové bráně firewall.  Pro přístup k Azure SQL Data Warehouse ze svého místního počítače, ujistěte se, že brána firewall na vaší síti i místní počítač umožňují odchozí komunikaci na portu TCP 1433.  

SQL Data Warehouse používá pravidla brány firewall na úrovni serveru. Pravidla brány firewall na úrovni databáze nepodporuje. Další informace najdete v tématu [brány firewall Azure SQL Database][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Ve výchozím nastavení jsou šifrované připojení k SQL Data Warehouse.  Změny nastavení připojení můžete zakázat šifrování se ignorují.

## <a name="authentication"></a>Authentication
Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Data Warehouse v současné době podporuje ověřování systému SQL Server pomocí uživatelského jména a hesla a službou Azure Active Directory. 

Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů, můžete ověřovat k jakékoli databázi na daném serveru jako vlastník databáze neboli "dbo" pomocí ověřování systému SQL Server.

Ale jako osvědčený postup, musí uživatelé ve vaší organizaci použít jiný účet k ověření. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížíte riziko škodlivých aktivit v případě, že váš kód aplikace je zranitelný vůči útoku prostřednictvím injektáže SQL. 

Chcete-li vytvořit uživatele ověření serveru SQL, připojte se k **hlavní** databáze na serveru se vaše přihlašovací jméno správce serveru a vytvořte nové přihlašovací údaje serveru.  Kromě toho je vhodné vytvořit uživatele v hlavní databázi uživatelů Azure SQL Data Warehouse. Vytvoření uživatele v hlavní větvi umožňuje uživateli přihlášení pomocí nástrojů, jako je SSMS bez zadání názvu databáze.  Umožňuje také jejich použití Průzkumníku objektů chcete-li zobrazit všechny databáze na SQL serveru.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Poté se připojte k vaší **databázi SQL Data Warehouse** se vaše přihlašovací jméno správce serveru a vytvořte uživatele databáze na základě přihlášení serveru jste vytvořili.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Uživatel oprávnění k provedení další operace, jako je vytváření přihlašovacích účtů nebo vytvoření nových databází, přiřaďte mu uživateli `Loginmanager` a `dbmanager` role v hlavní databázi. Další informace o těchto dalších rolí a ověřování do služby SQL Database najdete v tématu [Správa databází a přihlášení ve službě Azure SQL Database][Managing databases and logins in Azure SQL Database].  Další informace najdete v tématu [připojení k SQL Data Warehouse pomocí Active Directory ověřování služby Azure][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorizace
Autorizace určuje, co můžete dělat v databázi Azure SQL Data Warehouse. Povolení oprávnění určuje oprávnění a členství v rolích. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Ke správě rolí, můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Způsoby jak ještě více omezit, co může uživatel provádět v rámci Azure SQL Data Warehouse:

* Detailní [oprávnění] [ Permissions] vám umožní operace, které můžete s jednotlivými sloupci, tabulkami, zobrazeními ovládacího prvku, schémata, postupy a dalších objektů v databázi. Většina ovládací prvek a přidělili minimální oprávnění potřebná pomocí přesnějších oprávnění. 
* [Databázové role] [ Database roles] jiné než db_datareader a db_datawriter lze použít k vytvoření uživatelských účtů aplikace s větším nebo menším. Integrované pevné databázové role poskytují snadný způsob, jak udělit oprávnění, ale může vést k poskytování více oprávnění než je potřeba.
* [Uložené procedury] [ Stored procedures] slouží k omezení akcí, které můžete provést na databázi.

Následující příklad uděluje oprávnění ke čtení pro uživatelem definované schéma.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a logických serverů na webu Azure Portal nebo pomocí rozhraní API Azure Resource Manageru se řídí rolemi uživatelský účet na portálu společnosti. Další informace najdete v tématu [řízení přístupu na základě rolí na webu Azure portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Šifrování
Azure SQL Data Warehouse transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit pomocí šifrování a dešifrování dat v klidovém stavu.  Pokud chcete databázi zašifrovat, přidružené zálohy a soubory transakčních protokolů jsou šifrované nevyžaduje žádné změny vašich aplikací. Transparentní šifrování dat šifruje úložiště celou databázi pomocí symetrický klíč s názvem šifrovací klíč databáze. 

Ve službě SQL Database šifrovací klíč databáze je chráněno certifikátem integrovaného serveru. Certifikát integrovaného serveru je jedinečný pro každý server SQL Database. Microsoft automaticky otočí tyto certifikáty nejméně každých 90 dní. Šifrovací algoritmus používaný serverem SQL Data Warehouse je AES-256. Obecný popis transparentní šifrování dat, naleznete v tématu [transparentního šifrování dat][Transparent Data Encryption].

Můžete šifrovat vaší databáze pomocí [webu Azure portal] [ Encryption with Portal] nebo [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Další postup
Podrobnosti a příklady o připojení ke službě SQL Data Warehouse pomocí různých protokolů, najdete v tématu [připojení k SQL Data Warehouse][Connect to SQL Data Warehouse].

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
