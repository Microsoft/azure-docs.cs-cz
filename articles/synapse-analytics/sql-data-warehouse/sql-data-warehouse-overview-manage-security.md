---
title: Zabezpečení vyhrazeného fondu SQL (dřív SQL DW)
description: Tipy pro zabezpečení vyhrazeného fondu SQL (dříve SQL DW) a vývoj řešení ve službě Azure synapse Analytics.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 7e2d4b47f8f37a8e6d69a7846b8b0f92247121da
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685187"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Zabezpečení vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics

> [!div class="op_single_selector"]
>
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Tento článek vás provede základy zabezpečení vyhrazeného fondu SQL (dříve SQL DW). Tento článek vám konkrétně pomůže začít s prostředky pro omezení přístupu, ochranu dat a monitorování aktivit pomocí vyhrazeného fondu SQL (dřív SQL DW).

## <a name="connection-security"></a>Zabezpečení připojení

Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Pravidla brány firewall jsou používána [logickým serverem SQL](../../azure-sql/database/logical-servers.md) i jeho databázemi k zamítnutí pokusů o připojení z IP adres, které nebyly explicitně schváleny. Aby bylo možné připojení z vaší aplikace nebo veřejné IP adresy klientského počítače, je třeba nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí Azure Portal, REST API nebo PowerShellu.

V souladu s osvědčeným postupem byste měli omezit rozsahy IP adres povolené přes bránu firewall na úrovni serveru co nejvíce.  Pokud chcete získat přístup k vašemu rezervovanému fondu SQL (dřív SQL DW) z místního počítače, ujistěte se, že brána firewall v síti a místní počítač umožňují odchozí komunikaci na portu TCP 1433.  

Vyhrazený fond SQL (dřív SQL DW) používá pravidla brány firewall IP na úrovni serveru. Nepodporuje pravidla brány firewall protokolu IP na úrovni databáze. Další informace najdete v tématu věnovaném [Azure SQL Database pravidlům brány firewall](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

Ve výchozím nastavení jsou připojení k vašemu vyhrazenému fondu SQL (dříve SQL DW) zašifrovaná.  Změna nastavení připojení pro zákaz šifrování je ignorována.

## <a name="authentication"></a>Authentication

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. Vyhrazený fond SQL (dřív SQL DW) aktuálně podporuje SQL Server ověřování s uživatelským jménem a heslem a s Azure Active Directory.

Při vytváření serveru pro vaši databázi jste zadali přihlašovací jméno správce serveru pomocí uživatelského jména a hesla. Pomocí těchto přihlašovacích údajů se můžete na tomto serveru ověřit jako vlastník databáze nebo "dbo" prostřednictvím SQL Server ověřování.

V souladu s osvědčeným postupem by ale uživatelé vaší organizace měli k ověřování použít jiný účet. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížit rizika škodlivých aktivit v případě ohrožení kódu aplikace útokem prostřednictvím injektáže SQL.

Pokud chcete vytvořit SQL Server ověřeného uživatele, připojte se k **Hlavní** databázi na serveru pomocí přihlášení správce serveru a vytvořte nové přihlášení k serveru.  V hlavní databázi je vhodné vytvořit také uživatele. Při vytvoření uživatele v hlavní databázi se uživatel může přihlásit pomocí nástrojů, jako je SSMS, bez zadání názvu databáze.  Zároveň jim umožňuje zobrazit všechny databáze na serveru pomocí Průzkumníka objektů.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pak se připojte k vašemu rezervovanému **fondu SQL (dříve SQL DW)** pomocí přihlašovacích údajů správce serveru a vytvořte uživatele databáze na základě přihlášení k serveru, které jste vytvořili.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pokud chcete uživateli poskytnout oprávnění k provádění dalších operací, jako je vytváření přihlašovacích údajů nebo vytváření nových databází, přiřaďte uživatele `Loginmanager` k `dbmanager` rolím a v hlavní databázi.

Další informace o těchto dalších rolích a ověřování pro SQL Database najdete v tématu [Správa databází a přihlášení v Azure SQL Database](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Další informace o připojení pomocí Azure Active Directory najdete v tématu [připojení pomocí ověřování Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorizace

Autorizace se vztahuje na to, co můžete v databázi dělat po ověření a připojení. Oprávnění k autorizaci se určují podle členství v rolích a oprávnění. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Ke správě rolí můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Existují způsoby, jak dále omezit to, co může uživatel v rámci databáze provádět:

* Přesnější [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) umožňují řídit, které operace můžete provádět na jednotlivých sloupcích, tabulkách, zobrazeních, schématech, postupech a dalších objektech v databázi. K nejvyšší kontrole a udělení minimálních potřebných oprávnění použijte přesnější oprávnění.
* [Role databáze](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) jiné než db_datareader a db_datawriter lze použít k vytvoření výkonnějších uživatelských účtů aplikace nebo méně výkonných účtů pro správu. Předdefinované pevné databázové role poskytují snadný způsob, jak udělit oprávnění, ale mohou mít za následek udělení více oprávnění, než je nutné.
* K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Následující příklad udělí přístup pro čtení uživatelsky definovanému schématu.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a serverů z Azure Portal nebo používání rozhraní API Azure Resource Manager je řízena přiřazeními rolí uživatelského účtu portálu. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí webu Azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Šifrování

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivých aktivit tím, že šifruje a dešifruje vaše data v klidovém prostředí. Když šifrujete databázi, přidružené zálohy a soubory protokolů transakcí jsou šifrovány, aniž by to vyžadovalo změny vašich aplikací. Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze.

V SQL Database je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Integrovaný certifikát serveru je pro každý server jedinečný. Microsoft tyto certifikáty automaticky přetočí nejméně každých 90 dnů. Použitý šifrovací algoritmus je AES-256. Obecný popis TDE naleznete v tématu [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Databázi můžete šifrovat pomocí [Azure Portal](sql-data-warehouse-encryption-tde.md) nebo [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Další kroky

Podrobnosti a příklady připojení k vašemu skladu pomocí různých protokolů najdete v tématu [připojení k vyhrazenému fondu SQL (dřív SQL DW)](sql-data-warehouse-connect-overview.md).
