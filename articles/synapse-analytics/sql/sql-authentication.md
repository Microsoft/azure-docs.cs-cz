---
title: Ověřování SQL
description: Přečtěte si o ověřování SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 460fed7244ba8094da41ae6b5b8161de3d9efe65
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317268"
---
# <a name="sql-authentication"></a>Ověřování SQL

Azure synapse Analytics má dva SQL Form-faktory, které vám umožňují řídit spotřebu prostředků. Tento článek vysvětluje, jak tyto dva formy určují ověřování uživatelů.

K autorizaci synapse SQL můžete použít dva typy autorizace:

- Azure Active Directory autorizaci
- Autorizace SQL

Azure Active Directory umožňuje mít jedno místo pro správu uživatelů. Autorizace SQL umožňuje starším aplikacím, aby používaly synapse SQL, dobře známým způsobem.

## <a name="administrative-accounts"></a>Účty pro správu

Jako správci fungují dva účty pro správu ( **Správce serveru** a **Správce Active Directory** ). Pro identifikaci těchto účtů správců pro váš SQL Server otevřete Azure Portal a přejděte na kartu vlastnosti synapse SQL.

![Správci SQL serveru](./media/sql-authentication/sql-admins.png)

- **Správce serveru**

  Když vytvoříte Azure synapse Analytics, musíte pojmenovat **přihlašovací jméno správce serveru**. SQL server vytvoří tento účet v hlavní databázi jako přihlašovací. Tento účet používá pro připojení ověřování SQL Serveru (uživatelské jméno a heslo). Existovat může jenom jeden z těchto účtů.

- **Správce Azure Active Directory**

  Jako správce je možné nakonfigurovat jeden účet Azure Active Directory, a to buď individuální účet, nebo účet skupiny zabezpečení. Je volitelná konfigurace správce Azure AD, ale pokud chcete použít účty Azure AD pro připojení k synapse SQL, **musí** být nakonfigurovaný správce Azure AD.

Účty správců **serveru** a správce **Azure AD** mají následující vlastnosti:

- Jsou jedinými účty, které se můžou automaticky připojit k jakémukoli SQL Database na serveru. (Pro připojení k uživatelské databázi ostatní účty musí buď být vlastníkem databáze, nebo musí v uživatelské databázi mít uživatelský účet.)
- Tyto účty přistupují k uživatelským databázím jako uživatel `dbo` a mají pro ně veškerá oprávnění. (Vlastník databáze také k databázi přistupuje jako uživatel `dbo`.)
- Nezadávejte `master` databázi jako `dbo` uživatele a v hlavní databázi máte omezená oprávnění.
- Nejsou **členy standardní** `sysadmin` role serveru SQL Server pevné, což není v SQL Database k dispozici.  
- Může vytvářet, měnit a odstraňovat databáze, přihlášení, uživatele v hlavní databázi a pravidla brány firewall na úrovni serveru.
- Může přidat nebo odebrat členy do `dbmanager` rolí a `loginmanager` .
- Může zobrazit `sys.sql_logins` systémovou tabulku.

## <a name="serverless-sql-pool-preview"></a>[Neserverový fond SQL (Preview)](#tab/serverless)

Chcete-li spravovat uživatele, kteří mají přístup k fondu SQL bez serveru, můžete použít následující pokyny.

Pokud chcete vytvořit přihlášení k fondu SQL bez serveru, použijte tuto syntaxi:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Po přihlášení můžete uživatele vytvořit v jednotlivých databázích v rámci koncového bodu fondu SQL bez serveru a udělit těmto uživatelům požadovaná oprávnění. Chcete-li vytvořit použití, můžete použít následující syntaxi:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Po vytvoření přihlášení a uživatele můžete k udělení práv použít syntaxi Regular SQL Server.

## <a name="sql-pool"></a>[Fond SQL](#tab/provisioned)

### <a name="administrator-access-path"></a>Cesta pro přístup správce

Pokud je brána firewall na úrovni serveru správně nakonfigurovaná, může se **správce SQL serveru** a **správce Azure Active Directory** připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. 

Následující diagram znázorňuje typickou konfiguraci pro tyto dva účty správce:
 
![Konfigurace dvou účtů pro správu](./media/sql-authentication/1sql-db-administrator-access.png)

Při použití otevřeného portu brány firewall na úrovni serveru se můžou správci připojit k jakékoli databázi služby SQL Database.

### <a name="database-creators"></a>Autoři databází

Jednou z těchto rolí pro správu je role **dbmanager** . Členové této role mohou vytvářet nové databáze. Pokud chcete použít tuto roli, vytvořte uživatele v databázi `master` a pak ho přidejte do databázové role **dbmanager**. 

Chcete-li vytvořit databázi, musí být uživatel uživatelem na základě přihlášení SQL Server v `master` databázi nebo uživatel databáze s omezením na základě Azure Active Directory uživatele.

1. Pomocí účtu správce se připojte k `master` databázi.
2. Pomocí příkazu [Create Login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Vytvořte přihlašovací údaje pro ověření SQL Server. Ukázka příkazu:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Při vytváření přihlášení nebo uživatele databáze s omezením použijte silné heslo. Další informace najdete v tématu [Silná hesla](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

   Za účelem zvýšení výkonu se přihlášení (u hlavních účtů na úrovni serveru) dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete aktualizovat mezipaměť pro ověřování, podívejte se na informace v tématu [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

3. Vytvořte uživatele databáze pomocí příkazu [Create User](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . Uživatel může být Azure Active Directory ověřování, které obsahuje uživatele databáze (Pokud jste nakonfigurovali prostředí pro ověřování Azure AD), nebo pokud uživatel s omezením ověřování SQL Server obsahuje uživatele databáze nebo ověřování SQL Server na základě přihlašovacího jména SQL Server (vytvořené v předchozím kroku). Ukázkové příkazy:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Přidejte nového uživatele do role databáze **dbmanager** v `master` nástroji pomocí procedury [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest) (Všimněte si, že příkaz [ALTER role](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) není v SQL zřízené) podporován. Ukázky příkazů:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > Dbmanager je databázová role v hlavní databázi, takže do role dbmanager můžete přidat pouze uživatele databáze. Do role na úrovni databáze není možné přidat přihlášení na úrovni serveru.

5. V případě potřeby nakonfigurujte pravidlo brány firewall, aby se nový uživatel mohl připojit. (Na nového uživatele se může vztahovat už existující pravidlo brány firewall.)

Nyní se uživatel může připojit k `master` databázi a může vytvářet nové databáze. Účet použitý k vytvoření databáze se stává vlastníkem databáze.

### <a name="login-managers"></a>Správci přihlášení

Druhou správní rolí je role správce přihlášení. Členové této role mohou v hlavní databázi vytvářet nová přihlášení. Pokud chcete, můžete použít stejný postup (vytvořit přihlášení a uživatele a přidat uživatele do role **loginmanager** ) a povolit tak uživateli vytvářet nová přihlášení v hlavní databázi. Obvykle přihlášení nejsou nutná, protože společnost Microsoft doporučuje používat uživatele databáze s omezením, které se ověřují na úrovni databáze namísto použití uživatelů na základě přihlašovacích údajů. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

---

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

Chcete-li poskytnout dalším uživatelům úplnou kontrolu nad databází, zajistěte, aby byly členy **db_owner** pevné databázové role.

V Azure SQL Database nebo synapse bez serveru použijte `ALTER ROLE` příkaz.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

V vyhrazeném fondu SQL použijte [Sp_addrolemember exec](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Jedním z běžných důvodů, proč vytvořit uživatele databáze na základě přihlášení k serveru, je pro uživatele, kteří potřebují přístup k více databázím. Vzhledem k tomu, že uživatelé databáze s omezením jsou jednotlivé entity, udržuje každá databáze vlastní uživatele a vlastní heslo. To může způsobit režii, protože uživatel si pak musí pamatovat každé heslo pro každou databázi a může se stát untenableou změnou více hesel pro mnoho databází. Pokud ale používáte SQL Server přihlašovacích údajů a vysokou dostupnost (aktivní geografickou replikaci a skupiny převzetí služeb při selhání), SQL Server přihlášení musí být nastavená na každém serveru ručně. V opačném případě již nebude uživatel databáze po převzetí služeb při selhání mapován na přihlašovací jméno serveru a nebude moci získat přístup k databázi po převzetí služeb při selhání. 

Další informace o konfiguraci přihlášení pro geografickou replikaci najdete v tématu  [Konfigurace a správa Azure SQL Database zabezpečení pro geografické obnovení nebo převzetí služeb při selhání](../../azure-sql/database/active-geo-replication-security-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="configuring-the-database-level-firewall"></a>Konfigurace brány firewall na úrovni databáze

Uživatelé bez oprávnění správce by v rámci osvědčených postupů měli mít do databází, které používají, přístup pouze přes bránu firewall. Místo toho, abyste autorizovali jejich IP adresy pomocí brány firewall na úrovni serveru a umožnili jim tak přístup do všech databází, nakonfigurujte bránu firewall na úrovni databáze pomocí příkazu [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Bránu firewall na úrovni databáze nemůžete nakonfigurovat pomocí portálu.

### <a name="non-administrator-access-path"></a>Cesta pro přístup uživatelů bez oprávnění správce

Pokud je brána firewall na úrovni databáze správně nakonfigurovaná, můžou se uživatelé připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. Následující diagram znázorňuje typickou cestu pro přístup uživatelů bez oprávnění správce.

![Cesta pro přístup uživatelů bez oprávnění správce](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Skupiny a role

Efektivní správa přístupů využívá oprávnění přiřazená skupinám a rolím, nikoliv jednotlivým uživatelům.

- Pokud používáte ověřování pomocí Azure Active Directory, přidejte uživatele služby Azure Active Directory do skupiny Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a potom této databázové roli přiřaďte [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

- Pokud používáte ověřování SQL Serveru, vytvořte v databázi uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a potom této databázové roli přiřaďte [oprávnění](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Mezi databázové role patří například předdefinované role **db_owner** , **db_ddladmin** , **db_datawriter** , **db_datareader** , **db_denydatawriter** a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. 

Pevná databázová role **db_datareader** například uděluje přístup pro čtení pro všechny tabulky v databázi, což je obvykle více, než je skutečně nezbytné. 

Je mnohem lepší použít příkaz [Create role](https://msdn.microsoft.com/library/ms187936.aspx) k vytvoření vlastních uživatelských rolí definovaných uživatelem a pečlivě udělit každé roli minimální oprávnění potřebná pro potřeby podniku. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.

## <a name="permissions"></a>Oprávnění

Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. 

Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. 

Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.

### <a name="considerations-and-restrictions"></a>Důležité informace a omezení

Při správě přihlášení a uživatelů v SQL Database Vezměte v úvahu následující body:

- Při provádění příkazů musíte být připojeni k **Hlavní** databázi `CREATE/ALTER/DROP DATABASE` .
- Uživatele databáze, který odpovídá přihlašovacímu účtu **Správce serveru** , nejde změnit ani vyřadit.
- Výchozím jazykem přihlášení **správce serveru** je americká angličtina.
- Příkazy `CREATE DATABASE` a `DROP DATABASE` mohou provádět jen správci (přihlášení **správce serveru** nebo správce Azure AD) a členové databázové role **dbmanager** v **hlavní** databázi.
- Při provádění příkazů `CREATE/ALTER/DROP LOGIN` musíte být připojení k hlavní databázi. Nedoporučuje se používat přihlášení. Použijte raději databázové uživatele s omezením.
- Pokud se chcete připojit k uživatelské databázi, musíte v připojovacím řetězci uvést název databáze.
- Příkazy `CREATE LOGIN`, `ALTER LOGIN` a `DROP LOGIN` mohou provádět jen hlavní přihlášení na úrovni serveru a členové databázové role **loginmanager** v **hlavní** databázi.
- Při provádění `CREATE/ALTER/DROP LOGIN` příkazů a `CREATE/ALTER/DROP DATABASE` v aplikaci ADO.NET se použití parametrizovaných příkazů nepovoluje. Další informace viz [Příkazy a parametry](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
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
- Pokud se vlastník databázové role pokusí přidat do této role jiného uživatele databáze (nebo ho z ní odebrat), může dojít k následující chybě: **Uživatel nebo role „Jméno“ v této databázi neexistuje.** K této chybě dochází, protože uživatel není viditelný pro vlastníka. Problém vyřešíte tak, že vlastníkovi role udělíte oprávnění `VIEW DEFINITION` pro daného uživatele. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).
 
