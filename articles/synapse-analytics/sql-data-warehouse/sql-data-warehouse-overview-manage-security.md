---
title: Zabezpečení databáze
description: Tipy pro zabezpečení databáze a vývoj řešení v synapse sql fondu prostředku.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 0c30294f2ca139a602074a980810e7c6737c4e2d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742989"
---
# <a name="secure-a-database-in-azure-synapse"></a>Zabezpečení databáze v Azure Synapse

> [!div class="op_single_selector"]
>
> * [Přehled zabezpečení](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Šifrování (portál)](sql-data-warehouse-encryption-tde.md)
> * [Šifrování (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Tento článek vás provede základy zabezpečení vašeho fondu SYNApse SQL. Zejména tento článek vás nastartuje s prostředky pro omezení přístupu, ochranu dat a monitorování aktivit v databázi zřízené pomocí fondu SQL.

## <a name="connection-security"></a>Zabezpečení připojení

Zabezpečení připojení spočívá v použití pravidel brány firewall a šifrovaného připojení k omezení a zabezpečení připojení k databázi.

Pravidla brány firewall používají server i databáze k odmítnutí pokusů o připojení z adres IP, které nebyly explicitně uvedeny na seznamu povolených adres. Chcete-li povolit připojení z veřejné IP adresy vaší aplikace nebo klientského počítače, musíte nejprve vytvořit pravidlo brány firewall na úrovni serveru pomocí portálu Azure, rozhraní REST API nebo prostředí PowerShell.

Doporučujeme co nejvíce omezit rozsah IP adres povolených v serverové bráně firewall.  Chcete-li získat přístup k fondu SQL z místního počítače, zajistěte, aby brána firewall v síti a místní počítač umožňoval odchozí komunikaci na portu TCP 1433.  

Azure Synapse Analytics používá pravidla brány firewall IP na úrovni serveru. Nepodporuje pravidla brány firewall IP na úrovni databáze. Další informace najdete v tématu [Pravidla brány firewall Azure SQL Database](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Připojení k fondu SQL jsou ve výchozím nastavení šifrována.  Změna nastavení připojení za účelem zakázání šifrování je ignorována.

## <a name="authentication"></a>Authentication

Ověřování se týká způsobu, jakým prokážete svou identitu při připojování k databázi. Fond SQL aktuálně podporuje ověřování serveru SQL Server s uživatelským jménem a heslem a pomocí služby Azure Active Directory.

Když jste vytvářeli logický server databáze, zadali jste uživatelské jméno a heslo účtu „server admin“. Pomocí těchto pověření můžete ověřit do libovolné databáze na tomto serveru jako vlastník databáze nebo "dbo" prostřednictvím ověřování serveru SQL Server.

Jako osvědčený postup by však uživatelé vaší organizace měli k ověření použít jiný účet. Tímto způsobem můžete omezit oprávnění udělená aplikaci a snížit riziko škodlivé aktivity v případě, že kód aplikace je zranitelný vůči útoku injektáží SQL.

Chcete-li vytvořit uživatele SQL Server Authenticated, připojte se k **hlavní** databázi na serveru pomocí přihlášení správce serveru a vytvořte nové přihlášení k serveru.  Je vhodné také vytvořit uživatele v hlavní databázi. Vytvoření uživatele v hlavním serveru umožňuje uživateli přihlásit pomocí nástrojů, jako je SSMS bez zadání názvu databáze.  Umožňuje jim také použít průzkumník objektů k zobrazení všech databází na serveru SQL.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Potom se připojte k **databázi fondu SQL** pomocí přihlášení správce serveru a vytvořte uživatele databáze na základě přihlášení serveru, které jste vytvořili.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Chcete-li uživateli udělit oprávnění k provádění dalších operací, jako je `Loginmanager` `dbmanager` vytváření přihlášení nebo vytváření nových databází, přiřaďte uživatele k rolím a v hlavní databázi.

Další informace o těchto dalších rolích a ověřování databáze SQL najdete [v tématu Správa databází a přihlášení v Azure SQL Database](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Další informace o připojení pomocí služby Azure Active Directory najdete [v tématu Připojení pomocí azure active directory authentication](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Autorizace

Autorizace označuje, co můžete v databázi provést po ověření a připojení. Oprávnění k autorizaci jsou určena členstvím rolí a oprávněními. Doporučený postup je udělit uživatelům co nejmenší možná oprávnění. Chcete-li spravovat role, můžete použít následující uložené procedury:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Účet správce serveru, který používáte k připojení, je členem skupiny db_owner. Tato skupina může s databází provádět všechny operace. Tento účet uložte kvůli nasazení upgradovaných schémat a dalším možnostem správy. Použijte účet „ApplicationUser“, který má omezenější oprávnění a umožňuje připojit se z aplikace k databázi s nejnižšími oprávněními, jaké aplikace potřebuje.

Existují způsoby, jak dále omezit, co může uživatel v databázi dělat:

* Podrobná [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) umožňují řídit, které operace lze provést s jednotlivými sloupci, tabulkami, zobrazeními, schématy, procedurami a dalšími objekty v databázi. Použijte podrobná oprávnění, abyste měli co největší kontrolu a udělili minimální potřebná oprávnění.
* [Databázové role](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) jiné než db_datareader a db_datawriter lze použít k vytvoření výkonnějších uživatelských účtů aplikací nebo méně výkonných účtů správy. Předdefinované role pevné databáze poskytují snadný způsob udělení oprávnění, ale může mít za následek udělení více oprávnění, než je nutné.
* K omezení akcí, které je možné s databází provádět, můžete použít [uložené procedury](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Následující příklad uděluje přístup pro čtení do uživatelského schématu.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Správa databází a logických serverů z webu Azure Portal nebo pomocí rozhraní API Azure Resource Manager uvládne přiřazení rolí vašeho uživatelského účtu portálu. Další informace najdete [v tématu Řízení přístupu na základě rolí na webu Azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Šifrování

Transparentní šifrování dat (TDE) pomáhá chránit před hrozbou škodlivé aktivity šifrováním a dešifrováním dat v klidovém stavu. Při šifrování databáze jsou přidružené zálohy a soubory protokolu transakcí zašifrovány bez nutnosti jakýchkoli změn v aplikacích. Transparentní šifrování dat šifruje úložiště celé databáze pomocí symetrického klíče nazývaného šifrovací klíč databáze.

V databázi SQL je šifrovací klíč databáze chráněn integrovaným certifikátem serveru. Vestavěný certifikát serveru je jedinečný pro každý server databáze SQL. Společnost Microsoft automaticky otočí tyto certifikáty alespoň každých 90 dní. Použitý šifrovací algoritmus je AES-256. Obecný popis tde naleznete v tématu [Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Databázi můžete šifrovat pomocí [portálu Azure nebo](sql-data-warehouse-encryption-tde.md) [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Další kroky

Podrobnosti a příklady připojení ke skladu pomocí různých protokolů naleznete v tématu [Připojení k fondu SQL](sql-data-warehouse-connect-overview.md).
