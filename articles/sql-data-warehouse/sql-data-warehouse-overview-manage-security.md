---
title: Zabezpečení databáze
description: Tipy pro zabezpečení databáze a vývoj řešení v prostředku fondu SQL analýzy SQL
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
tags: azure-synapse
ms.openlocfilehash: 89ec405a348e3ace851fd5f5e17283a8036692a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257352"
---
# <a name="secure-a-database-in-azure-synapse"></a>Zabezpečení databáze v Azure synapse
> [!div class="op_single_selector"]
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Ověřování](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Tento článek vás provede základy zabezpečení fondu SQL ve službě SQL Analytics. Tento článek vám konkrétně pomůže začít s prostředky pro omezení přístupu, ochranou dat a monitorováním aktivit v databázi zřízené pomocí fondu SQL.

## <a name="connection-security"></a>Zabezpečení připojení
Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Pravidla brány firewall používá server i databáze k zamítnutí pokusů o připojení z IP adres, které nejsou explicitně na seznamu povolených. Aby bylo možné připojení z vaší aplikace nebo veřejné IP adresy klientského počítače, je třeba nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí Azure Portal, REST API nebo PowerShellu. 

Doporučujeme co nejvíce omezit rozsah IP adres povolených v serverové bráně firewall.  Pokud chcete získat přístup k fondu SQL z místního počítače, zajistěte, aby brána firewall v síti a místní počítač umožňovala odchozí komunikaci na portu TCP 1433.  

Azure synapse Analytics používá pravidla brány firewall IP na úrovni serveru. Nepodporuje pravidla brány firewall protokolu IP na úrovni databáze. Další informace najdete v tématu věnovaném [Azure SQL Database pravidlům brány firewall](../sql-database/sql-database-firewall-configure.md) .

Připojení k vašemu fondu SQL jsou ve výchozím nastavení zašifrována.  Změna nastavení připojení pro zákaz šifrování je ignorována.

## <a name="authentication"></a>Ověřování
Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. Fond SQL aktuálně podporuje SQL Server ověřování s uživatelským jménem a heslem a s Azure Active Directory. 

Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto přihlašovacích údajů se můžete na tomto serveru ověřit jako vlastník databáze nebo "dbo" prostřednictvím SQL Server ověřování.

V souladu s osvědčeným postupem by ale uživatelé vaší organizace měli k ověřování použít jiný účet. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížit rizika škodlivých aktivit v případě ohrožení kódu aplikace útokem prostřednictvím injektáže SQL. 

Pokud chcete vytvořit SQL Server ověřeného uživatele, připojte se k **Hlavní** databázi na serveru pomocí přihlášení správce serveru a vytvořte nové přihlášení k serveru.  V hlavní databázi je vhodné vytvořit také uživatele. Při vytvoření uživatele v hlavní databázi se uživatel může přihlásit pomocí nástrojů, jako je SSMS, bez zadání názvu databáze.  Umožňuje také použít Průzkumníka objektů k zobrazení všech databází v systému SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pak se připojte k **databázi fondu SQL** pomocí přihlašovacích údajů správce serveru a vytvořte uživatele databáze na základě přihlášení k serveru, které jste vytvořili.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pokud chcete uživateli poskytnout oprávnění k provádění dalších operací, jako je vytváření přihlašovacích údajů nebo vytváření nových databází, přiřaďte uživatele k rolím `Loginmanager` a `dbmanager` v hlavní databázi. 

Další informace o těchto dalších rolích a ověřování pro SQL Database najdete v tématu [Správa databází a přihlášení v Azure SQL Database](../sql-database/sql-database-manage-logins.md).  Další informace o připojení pomocí Azure Active Directory najdete v tématu [připojení pomocí ověřování Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorizace
Autorizace se vztahuje na to, co můžete v databázi dělat po ověření a připojení. Oprávnění k autorizaci se určují podle členství v rolích a oprávnění. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Ke správě rolí můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Existují způsoby, jak dále omezit to, co může uživatel v rámci databáze provádět:

* Přesnější [oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) umožňují řídit, které operace můžete provádět na jednotlivých sloupcích, tabulkách, zobrazeních, schématech, postupech a dalších objektech v databázi. K nejvyšší kontrole a udělení minimálních potřebných oprávnění použijte přesnější oprávnění. 
* [Role databáze](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) jiné než db_datareader a db_datawriter lze použít k vytvoření výkonnějších uživatelských účtů aplikace nebo méně výkonných účtů pro správu. Předdefinované pevné databázové role poskytují snadný způsob, jak udělit oprávnění, ale mohou mít za následek udělení více oprávnění, než je nutné.
* K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15).

Následující příklad udělí přístup pro čtení uživatelsky definovanému schématu.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a logických serverů z Azure Portal nebo použití rozhraní Azure Resource Manager API se řídí přiřazením rolí uživatelského účtu portálu. Další informace najdete v tématu [řízení přístupu na základě role v Azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure).

## <a name="encryption"></a>Šifrování
Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit tím, že šifruje a dešifruje vaše data v klidovém prostředí. Když šifrujete databázi, přidružené zálohy a soubory protokolů transakcí jsou šifrovány, aniž by to vyžadovalo změny vašich aplikací. TDE šifruje úložiště celé databáze pomocí symetrického klíče, který se nazývá šifrovací klíč databáze. 

V SQL Database je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je jedinečný pro každý SQL Database Server. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. Použitý šifrovací algoritmus je AES-256. Obecný popis TDE naleznete v tématu [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15).

Databázi můžete šifrovat pomocí [Azure Portal](sql-data-warehouse-encryption-tde.md) nebo [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Další kroky
Podrobnosti a příklady připojení k vašemu skladu pomocí různých protokolů najdete v tématu [připojení k fondu SQL](sql-data-warehouse-connect-overview.md).
