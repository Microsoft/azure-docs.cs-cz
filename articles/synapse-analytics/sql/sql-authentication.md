---
title: Ověřování SQL
description: Přečtěte si o ověřování SQL v Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 2b80efa30ac7e04b9eb21dd6f8a39ab4ee90adf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424850"
---
# <a name="sql-authentication"></a>Ověřování SQL

Azure Synapse Analytics má dva faktory formuláře SQL, které umožňují řídit spotřebu prostředků. Tento článek vysvětluje, jak dva faktory formuláře řídí ověřování uživatele.

Chcete-li autorizovat synapse SQL, můžete použít dva typy autorizace:

- Autorizace AAD
- Autorizace SQL

Autorizace AAD závisí na azure active directory a umožňuje mít jedno místo pro správu uživatelů. Sql autorizace umožňuje starší aplikace používat Synapse SQL v dobře známým způsobem.

## <a name="administrative-accounts"></a>Správní účty

Jako správci fungují dva účty pro správu (**Správce serveru** a **Správce Active Directory**). Chcete-li identifikovat tyto účty správce pro váš sql server, otevřete portál Azure a přejděte na kartu Vlastnosti vašeho Synapse SQL.

![Správci SQL serveru](./media/sql-authentication/sql-admins.png)

- **Správce serveru**

  Při vytváření Azure Synapse Analytics, musíte určit **přihlášení správce serveru**. SQL server vytvoří tento účet v hlavní databázi jako přihlašovací. Tento účet používá pro připojení ověřování SQL Serveru (uživatelské jméno a heslo). Existovat může jenom jeden z těchto účtů.

- **Správce Azure Active Directory**

  Jako správce je možné nakonfigurovat jeden účet Azure Active Directory, a to buď individuální účet, nebo účet skupiny zabezpečení. Je volitelné nakonfigurovat správce Azure AD, ale správce Azure AD **musí** být nakonfigurován, pokud chcete použít účty Azure AD pro připojení k Synapse SQL.

Účty **správce serveru** a azure **ad správce** mají následující charakteristiky:

- Jsou pouze účty, které se mohou automaticky připojit k libovolné databázi SQL na serveru. (Pro připojení k uživatelské databázi ostatní účty musí buď být vlastníkem databáze, nebo musí v uživatelské databázi mít uživatelský účet.)
- Tyto účty přistupují k uživatelským databázím jako uživatel `dbo` a mají pro ně veškerá oprávnění. (Vlastník databáze také k databázi přistupuje jako uživatel `dbo`.)
- Nezadávejte `master` databázi `dbo` jako uživatel a mají omezená oprávnění v hlavním serveru.
- Nejsou **not** členy standardní role `sysadmin` pevného serveru SQL Server, která není k dispozici v databázi SQL.  
- Můžete vytvářet, měnit a přepínat databáze, přihlášení, uživatele v hlavních pravidlech a pravidlech brány firewall IP na úrovni serveru.
- Můžete přidat a odebrat `loginmanager` členy a `dbmanager` role.
- Může zobrazit `sys.sql_logins` systémovou tabulku.

## <a name="sql-on-demand-preview"></a>SQL na vyžádání (náhled)

Chcete-li spravovat uživatele, kteří mají přístup k SQL na vyžádání, můžete použít níže uvedené pokyny.

Chcete-li vytvořit přihlášení k SQL na vyžádání, použijte následující syntaxi:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Jakmile přihlášení existuje, můžete vytvořit uživatele v jednotlivých databázích uvnitř koncového bodu NA VYŽÁDÁNÍ SQL a udělit těmto uživatelům požadovaná oprávnění. Chcete-li vytvořit použití, můžete použít následující syntaxi:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Po vytvoření přihlášení a uživatele můžete použít běžnou syntaxi serveru SQL Server k udělení práv.

## <a name="sql-pool"></a>Fond SQL

### <a name="administrator-access-path"></a>Cesta pro přístup správce

Pokud je brána firewall na úrovni serveru správně nakonfigurovaná, může se **správce SQL serveru** a **správce Azure Active Directory** připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. 

Následující diagram znázorňuje typickou konfiguraci pro dva účty správce:
 
![konfigurace dvou správních účtů](./media/sql-authentication/1sql-db-administrator-access.png)

Při použití otevřeného portu brány firewall na úrovni serveru se můžou správci připojit k jakékoli databázi služby SQL Database.

### <a name="database-creators"></a>Autoři databází

Jednou z těchto rolí pro správu je role **dbmanager.** Členové této role mohou vytvářet nové databáze. Pokud chcete použít tuto roli, vytvořte uživatele v databázi `master` a pak ho přidejte do databázové role **dbmanager**. 

Chcete-li vytvořit databázi, musí být uživatel uživatel na `master` základě přihlášení sql serveru v databázi nebo obsahoval databázového uživatele na základě uživatele služby Azure Active Directory.

1. Pomocí účtu správce se `master` připojte k databázi.
2. Vytvořte přihlášení ověřování serveru SQL Server pomocí příkazu [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Ukázka příkazu:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Při vytváření přihlášení nebo uživatele databáze s omezením použijte silné heslo. Další informace najdete v tématu [Silná hesla](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

   Za účelem zvýšení výkonu se přihlášení (u hlavních účtů na úrovni serveru) dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete aktualizovat mezipaměť pro ověřování, podívejte se na informace v tématu [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

3. V `master` databázi vytvořte uživatele pomocí příkazu [CREATE USER.](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Uživatelem může být uživatel azure active directory obsahoval databázový uživatel (pokud jste nakonfigurovali prostředí pro ověřování Azure AD), nebo sql server ověřování obsahoval uživatele databáze, nebo sql server ověřování uživatele na základě přihlášení ověřování SQL Server (vytvořené v předchozím kroku.) Ukázkové příkazy:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Přidejte nového uživatele do role `master` databáze **dbmanager** v příkazu [ALTER ROLE.](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Ukázky příkazů:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary;
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager je databázová role v hlavní databázi, takže do role dbmanager můžete přidat pouze uživatele databáze. Do role na úrovni databáze není možné přidat přihlášení na úrovni serveru.

5. V případě potřeby nakonfigurujte pravidlo brány firewall, aby se nový uživatel mohl připojit. (Na nového uživatele se může vztahovat už existující pravidlo brány firewall.)

Nyní se uživatel může `master` připojit k databázi a může vytvářet nové databáze. Účet použitý k vytvoření databáze se stává vlastníkem databáze.

### <a name="login-managers"></a>Správci přihlášení

Druhou správní rolí je role správce přihlášení. Členové této role mohou v hlavní databázi vytvářet nová přihlášení. Pokud chcete, můžete použít stejný postup (vytvořit přihlášení a uživatele a přidat uživatele do role **loginmanager**) a povolit tak uživateli vytvářet nová přihlášení v hlavní databázi. Tato přihlášení obvykle nejsou nutná, protože Microsoft doporučuje místo uživatelů s ověřováním na základě přihlášení používat uživatele databáze s omezením, kteří jsou ověřovaní na úrovni databáze. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="non-administrator-users"></a>Uživatelé bez oprávnění správce

Obecně platí, že účty bez oprávnění správce nepotřebují přístup k hlavní databázi. Uživatele databáze s omezením můžete vytvářet pomocí příkazu [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

Tímto uživatelem může být uživatel databáze s omezením s ověřováním služby Azure Active Directory (pokud jste nakonfigurovali prostředí s ověřováním pomocí služby Azure AD) nebo uživatel databáze s omezením s ověřováním SQL Serveru nebo uživatel s ověřováním SQL Serveru založeným na přihlášení s ověřováním SQL Serveru (vytvořený v předchozím kroku).  

Pokud chcete vytvářet uživatele, připojte se k databázi a spusťte podobné příkazy jako v následujících příkladech:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Na začátku může uživatele vytvořit jenom jeden ze správců nebo vlastník databáze. Jestliže chcete autorizovat další uživatele, aby mohli vytvářet nové uživatele, udělte vybraným uživatelům oprávnění `ALTER ANY USER` pomocí příkazu, jako je například tento:

```sql
GRANT ALTER ANY USER TO Mary;
```

Chcete-li poskytnout další uživatelé úplnou kontrolu nad databází, aby byly členem **db_owner** pevné databázové role.

V Azure SQL Database `ALTER ROLE` použijte příkaz.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Ve fondu SQL použijte [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Jedním z běžných důvodů pro vytvoření uživatele databáze na základě přihlášení serveru SQL Database je pro uživatele, kteří potřebují přístup k více databázím. Vzhledem k tomu, že uživatelé obsažené databáze jsou jednotlivé entity, každá databáze udržuje své vlastní uživatele a své vlastní heslo. To může způsobit režii, protože uživatel si pak musí pamatovat každé heslo pro každou databázi a může se stát neudržitelným, když musí změnit více hesel pro mnoho databází. Však při použití SQL Server přihlášení a vysokou dostupnost (aktivní geografické replikace a převzetí služeb při selhání skupiny), SQL Server přihlášení musí být nastavena ručně na každém serveru. V opačném případě nebude uživatel databáze již mapován na přihlášení k serveru po převzetí služeb při selhání a nebude mít přístup k převzetí služeb při selhání databázi. 

Další informace o konfiguraci přihlášení pro geografickou replikaci najdete [v tématu Konfigurace a správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](../../sql-database/sql-database-geo-replication-security-config.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="configuring-the-database-level-firewall"></a>Konfigurace brány firewall na úrovni databáze

Uživatelé bez oprávnění správce by v rámci osvědčených postupů měli mít do databází, které používají, přístup pouze přes bránu firewall. Místo toho, abyste autorizovali jejich IP adresy pomocí brány firewall na úrovni serveru a umožnili jim tak přístup do všech databází, nakonfigurujte bránu firewall na úrovni databáze pomocí příkazu [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Bránu firewall na úrovni databáze nemůžete nakonfigurovat pomocí portálu.

### <a name="non-administrator-access-path"></a>Cesta pro přístup uživatelů bez oprávnění správce

Pokud je brána firewall na úrovni databáze správně nakonfigurovaná, můžou se uživatelé připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. Následující diagram znázorňuje typickou cestu pro přístup uživatelů bez oprávnění správce.

![Cesta pro přístup uživatelů bez oprávnění správce](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Skupiny a role

Efektivní správa přístupů využívá oprávnění přiřazená skupinám a rolím, nikoliv jednotlivým uživatelům.

- Pokud používáte ověřování pomocí Azure Active Directory, přidejte uživatele služby Azure Active Directory do skupiny Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a potom této databázové roli přiřaďte [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

- Pokud používáte ověřování SQL Serveru, vytvořte v databázi uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a potom této databázové roli přiřaďte [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Mezi databázové role patří například předdefinované role **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. 

Pevná databázová role **db_datareader** například uděluje přístup pro čtení pro všechny tabulky v databázi, což je obvykle více, než je skutečně nezbytné. 

Je mnohem lepší vytvořit vlastní databázové role definované uživatelem pomocí příkazu [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) a každé roli pečlivě udělit nejnižší oprávnění, které jsou nezbytná pro práci. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.

## <a name="permissions"></a>Oprávnění

Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. 

Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. 

Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.

### <a name="considerations-and-restrictions"></a>Důležité informace a omezení

Při správě přihlášení a uživatelů v databázi SQL zvažte následující body:

- Při provádění příkazů **master** musíte být připojeni `CREATE/ALTER/DROP DATABASE` k hlavní databázi.
- Databázového uživatele, který odpovídá **správci serveru**, není možné změnit ani vyřadit.
- Výchozím jazykem přihlášení **správce serveru** je americká angličtina.
- Příkazy `CREATE DATABASE` a `DROP DATABASE` mohou provádět jen správci (přihlášení **správce serveru** nebo správce Azure AD) a členové databázové role **dbmanager** v **hlavní** databázi.
- Při provádění příkazů `CREATE/ALTER/DROP LOGIN` musíte být připojení k hlavní databázi. Nedoporučuje se používat přihlášení. Použijte raději databázové uživatele s omezením.
- Pokud se chcete připojit k uživatelské databázi, musíte v připojovacím řetězci uvést název databáze.
- Příkazy `CREATE LOGIN`, `ALTER LOGIN` a `DROP LOGIN` mohou provádět jen hlavní přihlášení na úrovni serveru a členové databázové role **loginmanager** v **hlavní** databázi.
- Při provádění příkazů `CREATE/ALTER/DROP LOGIN` a `CREATE/ALTER/DROP DATABASE` v aplikaci ADO.NET není dovolené používat příkazy s parametry. Další informace viz [Příkazy a parametry](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Při provádění příkazů `CREATE/ALTER/DROP DATABASE` a `CREATE/ALTER/DROP LOGIN` musí být každý příkaz jediným příkazem v dávce Transact-SQL. V opačném případě dojde k chybě. Následující příkaz Transact-SQL například zkontroluje, jestli databáze existuje. Pokud existuje, volá příkaz `DROP DATABASE`, který ji odebere. Příkaz `DROP DATABASE` ale není jediným příkazem v dávce, a proto provedení následujícího příkazu Transact-SQL způsobí chybu.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Místo toho použijte následující příkaz Transact-SQL:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Při provádění příkazu `CREATE USER` s možností `FOR/FROM LOGIN` musí jít o jediný příkaz v dávce Transact-SQL.
- Při provádění příkazu `ALTER USER` s možností `WITH LOGIN` musí jít o jediný příkaz v dávce Transact-SQL.
- Pokud chcete použít příkaz `CREATE/ALTER/DROP` pro vytvoření, změnu nebo odstranění uživatele, musíte mít v databázi oprávnění `ALTER ANY USER`.
- Pokud se vlastník databázové role pokusí přidat do této role jiného uživatele databáze (nebo ho z ní odebrat), může dojít k následující chybě: **Uživatel nebo role „Jméno“ v této databázi neexistuje.** Chyba je způsobená tím, že vlastník role daného uživatele nevidí. Problém vyřešíte tak, že vlastníkovi role udělíte oprávnění `VIEW DEFINITION` pro daného uživatele. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).
 
