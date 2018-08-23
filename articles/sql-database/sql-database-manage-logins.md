---
title: Přihlašovací údaje Azure SQL | Dokumentace Microsoftu
description: Další informace o správě zabezpečení SQL Database a SQL Data Warehouse, konkrétně jak spravovat databázi přístup a zabezpečení přihlašování prostřednictvím hlavního účtu úrovni serveru.
keywords: zabezpečení databáze SQL,správa zabezpečení databáze,zabezpečení přihlášení,zabezpečení databáze,přístup k databázi
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.prod_service: sql-database, sql-data-warehouse
ms.custom: security
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: carlrab
ms.openlocfilehash: 7dbd2585628c64f5baf7df6083e38217d00953be
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058340"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Řízení a udělování přístupu k databázi SQL Database a SQL Data Warehouse

Po konfiguraci pravidel brány firewall, budete moct připojit k Azure [SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jako jeden z účtů správce, jako vlastník databáze nebo jako uživatel v databázi.  

>  [!NOTE]  
>  Toto téma se vztahuje k serveru Azure SQL a SQL Database a SQL Data Warehouse databáze vytvořené na serveru Azure SQL. Pro zjednodušení se SQL Database používá k označení SQL Database i SQL Data Warehouse. 

> [!TIP]
> Podívejte se kurz [zabezpečení služby Azure SQL Database](sql-database-security-tutorial.md).

## <a name="unrestricted-administrative-accounts"></a>Neomezené účty pro správu
Jako správci fungují dva účty pro správu (**Správce serveru** a **Správce Active Directory**). Pokud chcete zjistit tyto účty správce pro svůj SQL server, otevřete web Azure Portal a přejděte k vlastnostem SQL serveru.

![Správci SQL serveru](./media/sql-database-manage-logins/sql-admins.png)

- **Správce serveru**   
Když vytvoříte Azure SQL server, musíte určit **Přihlášení správce serveru**. SQL server vytvoří tento účet v hlavní databázi jako přihlašovací. Tento účet používá pro připojení ověřování SQL Serveru (uživatelské jméno a heslo). Existovat může jenom jeden z těchto účtů.   
- **Správce Azure Active Directory**   
Jako správce je možné nakonfigurovat jeden účet Azure Active Directory, a to buď individuální účet, nebo účet skupiny zabezpečení. Zadání je volitelné konfigurace správce Azure AD, ale správce Azure AD **musí** nakonfigurované, pokud chcete, aby používaly účty Azure AD pro připojení k SQL Database. Další informace o konfigurování přístupu v Azure Active Directory najdete v tématech [Připojení ke službě SQL Database nebo SQL Data Warehouse pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md) a [Podpora nástroje SSMS pro ověřování Azure AD MFA ve službě SQL Database a SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
 

Účty **Správce serveru** a **Správce Azure AD** mají tyto charakteristiky:
- Jsou to jediné účty, které můžou automaticky připojit k libovolné databázi SQL na serveru. (Pro připojení k uživatelské databázi ostatní účty musí buď být vlastníkem databáze, nebo musí v uživatelské databázi mít uživatelský účet.)
- Tyto účty přistupují k uživatelským databázím jako uživatel `dbo` a mají pro ně veškerá oprávnění. (Vlastník databáze také k databázi přistupuje jako uživatel `dbo`.) 
- Nezadávejte `master` databáze jako `dbo` uživatele a mají omezené oprávnění v hlavní větvi. 
- Jsou **není** členy standard systému SQL Server `sysadmin` pevné role serveru, která není k dispozici ve službě SQL database.  
- Můžete vytvořit, alter a drop databáze, přihlášení a uživatelů v pravidlech brány firewall na hlavní a úroveň serveru.
- Můžete přidávat a odebírat členy `dbmanager` a `loginmanager` role.
- Můžete zobrazit `sys.sql_logins` systémové tabulky.

### <a name="configuring-the-firewall"></a>Konfigurace brány firewall
Pokud je nakonfigurovaná brána firewall na úrovni serveru pro určitou IP adresu nebo rozsah IP adres, může se **správce SQL serveru** a **správce Azure Active Directory** připojit k hlavní databázi a všem uživatelským databázím. Počáteční bránu firewall na úrovni serveru je možné nakonfigurovat na webu [Azure Portal](sql-database-get-started-portal.md), pomocí prostředí [PowerShell](sql-database-get-started-powershell.md) nebo pomocí rozhraní [REST API](https://msdn.microsoft.com/library/azure/dn505712.aspx). Po vytvoření připojení můžete konfigurovat další pravidla brány firewall na úrovni serveru také pomocí jazyka [Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Cesta pro přístup správce
Pokud je brána firewall na úrovni serveru správně nakonfigurovaná, může se **správce SQL serveru** a **správce Azure Active Directory** připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. Následující diagram znázorňuje typickou konfiguraci pro dva účty správce.

![Cesta pro přístup správce](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Při použití otevřeného portu brány firewall na úrovni serveru se můžou správci připojit k jakékoli databázi služby SQL Database.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Připojení k databázi pomocí aplikace SQL Server Management Studio
Návod, jak vytvořit server, databázi, pravidla brány firewall na úrovni serveru a pomocí aplikace SQL Server Management Studio odesílat dotazy na databázi, najdete v tématu [Začínáme se servery, databázemi a pravidly brány firewall služby Azure SQL Database s využitím webu Azure Portal a aplikace SQL Server Management Studio](sql-database-get-started-portal.md).

> [!IMPORTANT]
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="additional-server-level-administrative-roles"></a>Další správní role na úrovni serveru
Kromě správních rolí na úrovni serveru popsaných v předchozích částech poskytuje služba SQL Database v hlavní databázi dvě správní role s omezením přístupu, do kterých můžete přidávat uživatelské účty, a které udělují oprávnění k vytváření databází nebo správě přihlašování.

### <a name="database-creators"></a>Autoři databází
Jednou z těchto správních rolí je role **dbmanager**. Členové této role mohou vytvářet nové databáze. Pokud chcete použít tuto roli, vytvořte uživatele v databázi `master` a pak ho přidejte do databázové role **dbmanager**. K vytvoření databáze je nutné, aby uživatel byl uživatelem na základě přihlášení pro SQL Server v hlavní databázi nebo uživatelem databáze s omezením na základě uživatele Azure Active Directory.

1. Pomocí účtu správce se připojte k hlavní databázi.
2. Volitelný krok: Vytvořte přihlášení s ověřováním SQL Serveru pomocí příkazu [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx). Ukázka příkazu:
   
   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Při vytváření přihlášení nebo uživatele databáze s omezením použijte silné heslo. Další informace najdete v tématu [Silná hesla](https://msdn.microsoft.com/library/ms161962.aspx).
    
   Za účelem zvýšení výkonu se přihlášení (u hlavních účtů na úrovni serveru) dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete aktualizovat mezipaměť pro ověřování, podívejte se na informace v tématu [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. V hlavní databázi vytvořte uživatele pomocí příkazu [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx). Tímto uživatelem může být uživatel databáze s omezením s ověřováním služby Azure Active Directory (pokud jste nakonfigurovali prostředí s ověřováním pomocí služby Azure AD) nebo uživatel databáze s omezením s ověřováním SQL Serveru nebo uživatel s ověřováním SQL Serveru založeným na přihlášení s ověřováním SQL Serveru (vytvořený v předchozím kroku). Ukázky příkazů:
   
   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Do databázové role **dbmanager** přidejte nového uživatele pomocí příkazu [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Ukázky příkazů:
   
   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > Dbmanager je databázová role v hlavní databázi, takže do role dbmanager můžete přidat pouze uživatele databáze. Do role na úrovni databáze není možné přidat přihlášení na úrovni serveru.
    
5. V případě potřeby nakonfigurujte pravidlo brány firewall, aby se nový uživatel mohl připojit. (Na nového uživatele se může vztahovat už existující pravidlo brány firewall.)

Nový uživatel se teď může připojit k hlavní databázi a může vytvářet nové databáze. Účet použitý k vytvoření databáze se stává vlastníkem databáze.

### <a name="login-managers"></a>Správci přihlášení
Druhou správní rolí je role správce přihlášení. Členové této role mohou v hlavní databázi vytvářet nová přihlášení. Pokud chcete, můžete použít stejný postup (vytvořit přihlášení a uživatele a přidat uživatele do role **loginmanager**) a povolit tak uživateli vytvářet nová přihlášení v hlavní databázi. Tato přihlášení obvykle nejsou nutná, protože Microsoft doporučuje místo uživatelů s ověřováním na základě přihlášení používat uživatele databáze s omezením, kteří jsou ověřovaní na úrovni databáze. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Uživatelé bez oprávnění správce
Obecně platí, že účty bez oprávnění správce nepotřebují přístup k hlavní databázi. Uživatele databáze s omezením můžete vytvářet pomocí příkazu [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Tímto uživatelem může být uživatel databáze s omezením s ověřováním služby Azure Active Directory (pokud jste nakonfigurovali prostředí s ověřováním pomocí služby Azure AD) nebo uživatel databáze s omezením s ověřováním SQL Serveru nebo uživatel s ověřováním SQL Serveru založeným na přihlášení s ověřováním SQL Serveru (vytvořený v předchozím kroku). Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx). 

Pokud chcete vytvářet uživatele, připojte se k databázi a spusťte podobné příkazy jako v následujících příkladech:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Na začátku může uživatele vytvořit jenom jeden ze správců nebo vlastník databáze. Jestliže chcete autorizovat další uživatele, aby mohli vytvářet nové uživatele, udělte vybraným uživatelům oprávnění `ALTER ANY USER` pomocí příkazu, jako je například tento:

```sql
GRANT ALTER ANY USER TO Mary;
```

Pokud chcete dalším uživatelům umožnit úplnou kontrolu databáze, přidejte je do pevné databázové role **db_owner** pomocí příkazu `ALTER ROLE`.

```sql
ALTER ROLE db_owner ADD MEMBER Mary; 
```

> [!NOTE]
> Jedním z důvodů běžné a vytvořte uživatele databáze na základě přihlášení logický server je pro uživatele, kteří potřebují přístup k více databázím. Protože obsažena uživatele databáze s jednotlivými entitami, každé databázi udržuje vlastní uživatelské a vlastní heslo. To může způsobit režijní náklady a uživatel musí mějte na paměti, každý heslo pro každou databázi, může být untenable, pokud by bylo nutné změnit více hesel pro velký počet databází. Ale při použití SQL serveru přihlášení a vysokou dostupnost (aktivní geografickou replikaci a převzetí služeb při selhání skupiny), přihlašovací údaje SQL serveru musí nastavit ručně na každém serveru. V opačném případě uživatele databáze se už namapovat na přihlášení serveru poté, co dojde k převzetí služeb při a nebudou mít přístup k databázi příspěvek převzetí služeb při selhání. Další informace o konfiguraci přihlášení pro geografickou replikaci, najdete v tématu [konfigurovat a spravovat zabezpečení služby Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Konfigurace brány firewall na úrovni databáze
Uživatelé bez oprávnění správce by v rámci osvědčených postupů měli mít do databází, které používají, přístup pouze přes bránu firewall. Místo toho, abyste autorizovali jejich IP adresy pomocí brány firewall na úrovni serveru a umožnili jim tak přístup do všech databází, nakonfigurujte bránu firewall na úrovni databáze pomocí příkazu [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx). Bránu firewall na úrovni databáze nemůžete nakonfigurovat pomocí portálu.

### <a name="non-administrator-access-path"></a>Cesta pro přístup uživatelů bez oprávnění správce
Pokud je brána firewall na úrovni databáze správně nakonfigurovaná, můžou se uživatelé připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. Následující diagram znázorňuje typickou cestu pro přístup uživatelů bez oprávnění správce.

![Cesta pro přístup uživatelů bez oprávnění správce](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Skupiny a role
Efektivní správa přístupů využívá oprávnění přiřazená skupinám a rolím, nikoliv jednotlivým uživatelům. 

- Pokud používáte ověřování pomocí Azure Active Directory, přidejte uživatele služby Azure Active Directory do skupiny Azure Active Directory. Pro tuto skupinu vytvořte uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](https://msdn.microsoft.com/library/ms189121) a potom této databázové roli přiřaďte [oprávnění](https://msdn.microsoft.com/library/ms191291.aspx).

- Pokud používáte ověřování SQL Serveru, vytvořte v databázi uživatele databáze s omezením. Přidejte jednoho nebo více uživatelů databáze do [databázové role](https://msdn.microsoft.com/library/ms189121) a potom této databázové roli přiřaďte [oprávnění](https://msdn.microsoft.com/library/ms191291.aspx).

Mezi databázové role patří například předdefinované role **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** a **db_denydatareader**. Role **db_owner** se obvykle používá k udělení úplných oprávnění pouze několika uživatelům. Ostatní pevné databázové role jsou užitečné pro rychlé vytvoření jednoduché databáze ve vývojovém prostředí, ale nedoporučují se pro většinu databází v produkčním prostředí. Pevná databázová role **db_datareader** například uděluje přístup pro čtení pro všechny tabulky v databázi, což je obvykle více, než je skutečně nezbytné. Je mnohem lepší vytvořit vlastní databázové role definované uživatelem pomocí příkazu [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) a každé roli pečlivě udělit nejnižší oprávnění, které jsou nezbytná pro práci. Pokud je uživatel členem více rolí, všechna jejich oprávnění se agregují.

## <a name="permissions"></a>Oprávnění
Ve službě SQL Database je dostupných více než 100 oprávnění, která můžete jednotlivě přidělit nebo zamítnout. Mnohá z těchto oprávnění jsou vnořená. Oprávnění `UPDATE` pro schéma například zahrnuje oprávnění `UPDATE` pro každou tabulku v tomto schématu. Podobně jako ve většině systémů oprávnění má zamítnutí oprávnění přednost před udělením oprávnění a přepíše ho. Kvůli velkému počtu oprávnění a používání vnořených oprávnění může návrh vhodného systému oprávnění vyžadovat pečlivou studii, aby byla vaše databáze dobře chráněna. Začněte seznamem oprávnění podle tématu [Oprávnění (databázový stroj)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) a prohlédněte si [plakát](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) s přehledem oprávnění.


### <a name="considerations-and-restrictions"></a>Důležité informace a omezení
Při správě přihlášení a uživatelů ve službě SQL Database mějte na paměti následující:

* Když chcete provádět příkazy **, musíte být připojeni k** hlavní`CREATE/ALTER/DROP DATABASE` databázi.   
* Databázového uživatele, který odpovídá **správci serveru**, není možné změnit ani vyřadit. 
* Výchozím jazykem přihlášení **správce serveru** je americká angličtina.
* Příkazy `CREATE DATABASE` a `DROP DATABASE` mohou provádět jen správci (přihlášení **správce serveru** nebo správce Azure AD) a členové databázové role **dbmanager** v **hlavní** databázi.
* Při provádění příkazů `CREATE/ALTER/DROP LOGIN` musíte být připojení k hlavní databázi. Nedoporučuje se používat přihlášení. Použijte raději databázové uživatele s omezením.
* Pokud se chcete připojit k uživatelské databázi, musíte v připojovacím řetězci uvést název databáze.
* Příkazy `CREATE LOGIN`, `ALTER LOGIN` a `DROP LOGIN` mohou provádět jen hlavní přihlášení na úrovni serveru a členové databázové role **loginmanager** v **hlavní** databázi.
* Při provádění příkazů `CREATE/ALTER/DROP LOGIN` a `CREATE/ALTER/DROP DATABASE` v aplikaci ADO.NET není dovolené používat příkazy s parametry. Další informace viz [Příkazy a parametry](https://msdn.microsoft.com/library/ms254953.aspx).
* Při provádění příkazů `CREATE/ALTER/DROP DATABASE` a `CREATE/ALTER/DROP LOGIN` musí být každý příkaz jediným příkazem v dávce Transact-SQL. V opačném případě dojde k chybě. Následující příkaz Transact-SQL například zkontroluje, jestli databáze existuje. Pokud existuje, volá příkaz `DROP DATABASE`, který ji odebere. Příkaz `DROP DATABASE` ale není jediným příkazem v dávce, a proto provedení následujícího příkazu Transact-SQL způsobí chybu.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```

* Při provádění příkazu `CREATE USER` s možností `FOR/FROM LOGIN` musí jít o jediný příkaz v dávce Transact-SQL.
* Při provádění příkazu `ALTER USER` s možností `WITH LOGIN` musí jít o jediný příkaz v dávce Transact-SQL.
* Pokud chcete použít příkaz `CREATE/ALTER/DROP` pro vytvoření, změnu nebo odstranění uživatele, musíte mít v databázi oprávnění `ALTER ANY USER`.
* Pokud se vlastník databázové role pokusí přidat do této role jiného uživatele databáze (nebo ho z ní odebrat), může dojít k následující chybě: **Uživatel nebo role „Jméno“ v této databázi neexistuje.** Chyba je způsobená tím, že vlastník role daného uživatele nevidí. Problém vyřešíte tak, že vlastníkovi role udělíte oprávnění `VIEW DEFINITION` pro daného uživatele. 


## <a name="next-steps"></a>Další postup

- Další informace o pravidlech brány firewall najdete v tématu [Brána firewall služby Azure SQL Database](sql-database-firewall-configure.md).
- Přehled všech funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md).
- Podívejte se kurz [zabezpečení služby Azure SQL Database](sql-database-security-tutorial.md).
- Informace o zobrazeních a uložených procedurách najdete v tématu [Vytváření zobrazení a uložených procedur](https://msdn.microsoft.com/library/ms365311.aspx).
- Informace o udělování přístupu k databázovému objektu najdete v tématu [Udělování přístupu k databázovému objektu](https://msdn.microsoft.com/library/ms365327.aspx).
