---
title: Zabezpečení databáze
description: Tipy pro zabezpečení databáze v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692574"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Zabezpečení databáze v SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Tento článek vás provede základy zabezpečení databáze Azure SQL Data Warehouse. Tento článek vám konkrétně pomůže začít s prostředky pro omezení přístupu, ochranu dat a sledování aktivit v databázi.

## <a name="connection-security"></a>Zabezpečení připojení
Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Server i databáze používají pravidla brány firewall k zamítnutí pokusů o připojení z IP adres, které nejsou výslovně povolené. Aby bylo možné připojení z vaší aplikace nebo veřejné IP adresy klientského počítače, je třeba nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí Azure Portal, REST API nebo PowerShellu. Doporučujeme co nejvíce omezit rozsah IP adres povolených v serverové bráně firewall.  Pokud chcete získat přístup k Azure SQL Data Warehouse z místního počítače, zajistěte, aby brána firewall v síti a místní počítač umožňovala odchozí komunikaci na portu TCP 1433.  

SQL Data Warehouse používá pravidla brány firewall na úrovni serveru. Nepodporuje pravidla brány firewall na úrovni databáze. Další informace najdete v tématu [Azure SQL Database firewall][Azure SQL Database firewall] [sp_set_firewall_rule][sp_set_firewall_rule].

Připojení k vašemu SQL Data Warehouse jsou ve výchozím nastavení zašifrována.  Změna nastavení připojení pro zákaz šifrování je ignorována.

## <a name="authentication"></a>Ověřování
Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. SQL Data Warehouse aktuálně podporuje ověřování SQL Server s uživatelským jménem a heslem a Azure Active Directory. 

Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů se můžete na tomto serveru ověřit jako vlastník databáze nebo "dbo" prostřednictvím SQL Server ověřování.

V souladu s osvědčeným postupem by ale uživatelé vaší organizace měli k ověřování použít jiný účet. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížit rizika škodlivých aktivit v případě ohrožení kódu aplikace útokem prostřednictvím injektáže SQL. 

Pokud chcete vytvořit SQL Server ověřeného uživatele, připojte se k **Hlavní** databázi na serveru pomocí přihlášení správce serveru a vytvořte nové přihlášení k serveru.  Kromě toho je vhodné vytvořit uživatele v hlavní databázi pro uživatele Azure SQL Data Warehouse. Při vytvoření uživatele v hlavní databázi se uživatel může přihlásit pomocí nástrojů, jako je SSMS, bez zadání názvu databáze.  Umožňuje také použít Průzkumníka objektů k zobrazení všech databází v systému SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pak se připojte k **databázi SQL Data Warehouse** pomocí přihlašovacích údajů správce serveru a vytvořte uživatele databáze na základě přihlášení k serveru, které jste vytvořili.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pokud chcete uživateli poskytnout oprávnění k provádění dalších operací, jako je vytváření přihlašovacích údajů nebo vytváření nových databází, přiřaďte uživatele k rolím `Loginmanager` a `dbmanager` v hlavní databázi. Další informace o těchto dalších rolích a ověřování pro SQL Database najdete v tématu [Správa databází a přihlášení v Azure SQL Database][Managing databases and logins in Azure SQL Database].  Další informace najdete v tématu [připojení k SQL Data Warehouse pomocí ověřování Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorizace
Autorizace se vztahuje na to, co můžete dělat v rámci databáze Azure SQL Data Warehouse. Oprávnění k autorizaci se určují podle členství v rolích a oprávnění. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Ke správě rolí můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Existují způsoby, jak dále omezit, co může uživatel s Azure SQL Data Warehouse provádět:

* Přesnější [oprávnění][Permissions] umožňují řídit, které operace můžete provádět na jednotlivých sloupcích, tabulkách, zobrazeních, schématech, postupech a dalších objektech v databázi. K nejvyšší kontrole a udělení minimálních potřebných oprávnění použijte přesnější oprávnění. 
* [Databázové role][Database roles] jiné než db_datareader a db_datawriter se dají použít k vytvoření výkonnějších uživatelských účtů aplikace nebo méně výkonných účtů pro správu. Předdefinované pevné databázové role poskytují snadný způsob, jak udělit oprávnění, ale mohou mít za následek udělení více oprávnění, než je nutné.
* K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury][Stored procedures].

Následující příklad udělí přístup pro čtení uživatelsky definovanému schématu.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a logických serverů z Azure Portal nebo použití rozhraní Azure Resource Manager API se řídí přiřazením rolí uživatelského účtu portálu. Další informace najdete v tématu [řízení přístupu na základě role v Azure Portal][Role-based access control in Azure portal].

## <a name="encryption"></a>Šifrování
Azure SQL Data Warehouse transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit tím, že šifruje a dešifruje data v klidovém prostředí.  Když šifrujete databázi, přidružené zálohy a soubory protokolů transakcí jsou šifrovány, aniž by to vyžadovalo změny vašich aplikací. TDE šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze. 

V SQL Database je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je jedinečný pro každý SQL Database Server. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. Šifrovací algoritmus používaný SQL Data Warehouse je AES-256. Obecný popis TDE naleznete v tématu [transparentní šifrování dat][Transparent Data Encryption].

Databázi můžete šifrovat pomocí [Azure Portal][Encryption with Portal] nebo [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Další kroky
Podrobnosti a příklady připojení k vašemu SQL Data Warehouse s různými protokoly najdete v tématu [připojení k SQL Data Warehouse][Connect to SQL Data Warehouse].

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
