---
title: Přihlášení a uživatelé
description: Přečtěte si o SQL Database a správě zabezpečení synapse v Azure, konkrétně o správě přístupu k databázi a zabezpečení přihlášení prostřednictvím hlavního účtu na úrovni serveru.
keywords: zabezpečení databáze SQL,správa zabezpečení databáze,zabezpečení přihlášení,zabezpečení databáze,přístup k databázi
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/06/2020
tags: azure-synapse
ms.openlocfilehash: 79a31e5b8e3433af7879fcde8597173f25bf96b7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360027"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-azure-synapse-analytics"></a>Řízení a udělení přístupu k databázi SQL Database a Azure synapse Analytics

Po konfiguraci pravidel brány firewall se můžete připojit k Azure [SQL Database](sql-database-technical-overview.md) a [Azure synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) jako jeden z účtů správce, jako je vlastník databáze nebo jako uživatel databáze v databázi.  

> [!NOTE]  
> Toto téma se vztahuje na Azure SQL Server a SQL Database a Azure synapse vytvořené na Azure SQL serveru. Pro zjednodušení se SQL Database používá při odkazování na SQL Database a Azure synapse.
> [!TIP]
> Kurz najdete v tématu [zabezpečení Azure SQL Database](sql-database-security-tutorial.md). Tento kurz se nevztahuje na **Azure SQL Database spravovanou instanci**.

## <a name="unrestricted-administrative-accounts"></a>Neomezené účty pro správu

Jako správci fungují dva účty pro správu (**Správce serveru** a **Správce Active Directory**). Pro identifikaci těchto účtů správců pro SQL Server otevřete Azure Portal a přejděte na kartu vlastnosti vašeho SQL serveru nebo SQL Database.

![Správci SQL serveru](media/sql-database-manage-logins/sql-admins.png)

- **Správce serveru**

  Když vytvoříte Azure SQL server, musíte určit **Přihlášení správce serveru**. SQL server vytvoří tento účet v hlavní databázi jako přihlašovací. Tento účet používá pro připojení ověřování SQL Serveru (uživatelské jméno a heslo). Existovat může jenom jeden z těchto účtů.

  > [!NOTE]
  > Pokud chcete resetovat heslo pro správce serveru, přejděte na [Azure Portal](https://portal.azure.com), klikněte na **SQL servery**, vyberte server ze seznamu a potom klikněte na **resetovat heslo**.

- **Správce Azure Active Directory**

  Jako správce je možné nakonfigurovat jeden účet Azure Active Directory, a to buď individuální účet, nebo účet skupiny zabezpečení. Je volitelné nakonfigurovat správce Azure AD, ale pokud chcete použít účty Azure AD pro připojení k SQL Database, **musí** být nakonfigurovaný správce Azure AD. Další informace o konfiguraci přístupu Azure Active Directory najdete v tématu [připojení k SQL Database nebo Azure synapse pomocí Azure Active Directoryho ověřování](sql-database-aad-authentication.md) a [SSMS podpory pro Azure AD MFA s SQL Database a Azure synapse](sql-database-ssms-mfa-authentication.md).

Účty správců **serveru** a správce **Azure AD** mají následující vlastnosti:

- Jsou jedinými účty, které se můžou automaticky připojit k jakémukoli SQL Database na serveru. (Pro připojení k uživatelské databázi ostatní účty musí buď být vlastníkem databáze, nebo musí v uživatelské databázi mít uživatelský účet.)
- Tyto účty přistupují k uživatelským databázím jako uživatel `dbo` a mají pro ně veškerá oprávnění. (Vlastník databáze také k databázi přistupuje jako uživatel `dbo`.) 
- Nezadávejte `master` databázi jako `dbo`ho uživatele a mít v hlavní databázi omezená oprávnění. 
- Nejsou **členy standardní** role serveru SQL Server `sysadmin` pevné, což není k dispozici ve službě SQL Database.  
- Může vytvářet, měnit a odstraňovat databáze, přihlášení, uživatele v hlavní databázi a pravidla brány firewall na úrovni serveru.
- Může přidat nebo odebrat členy do rolí `dbmanager` a `loginmanager`.
- Může zobrazit `sys.sql_logins` systémovou tabulku.
- Nelze přejmenovat.
- Pokud chcete změnit účet správce Azure AD, použijte portál nebo Azure CLI.
- Účet správce serveru nelze později změnit.

### <a name="configuring-the-firewall"></a>Konfigurace brány firewall

Pokud je nakonfigurovaná brána firewall na úrovni serveru pro určitou IP adresu nebo rozsah IP adres, může se **správce SQL serveru** a **správce Azure Active Directory** připojit k hlavní databázi a všem uživatelským databázím. Počáteční bránu firewall na úrovni serveru je možné nakonfigurovat na webu [Azure Portal](sql-database-single-database-get-started.md), pomocí prostředí [PowerShell](sql-database-powershell-samples.md) nebo pomocí rozhraní [REST API](https://msdn.microsoft.com/library/azure/dn505712.aspx). Po vytvoření připojení můžete nakonfigurovat další pravidla brány firewall IP na úrovni serveru pomocí [jazyka Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Cesta pro přístup správce

Pokud je brána firewall na úrovni serveru správně nakonfigurovaná, může se **správce SQL serveru** a **správce Azure Active Directory** připojit pomocí klientských nástrojů, jako jsou SQL Server Management Studio nebo SQL Server Data Tools. Jenom nejnovější nástroje poskytují všechny funkce a možnosti. Následující diagram znázorňuje typickou konfiguraci pro dva účty správce.

![Konfigurace dvou účtů pro správu](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Při použití otevřeného portu brány firewall na úrovni serveru se můžou správci připojit k jakékoli databázi služby SQL Database.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Připojení k databázi pomocí aplikace SQL Server Management Studio

Návod, jak vytvořit server, databázi, pravidla brány firewall na úrovni serveru a použít SQL Server Management Studio k dotazování databáze, najdete v tématu [Začínáme s Azure SQL Database servery, databázemi a pravidly brány firewall pomocí Azure Portal a SQL Server Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Další správní role na úrovni serveru

>[!IMPORTANT]
>Tato část se nevztahuje na **Azure SQL Database spravovanou instanci** , protože tyto role jsou specifické pro **Azure SQL Database**.

Kromě správních rolí na úrovni serveru popsaných v předchozích částech poskytuje služba SQL Database v hlavní databázi dvě správní role s omezením přístupu, do kterých můžete přidávat uživatelské účty, a které udělují oprávnění k vytváření databází nebo správě přihlašování.

### <a name="database-creators"></a>Autoři databází

Jednou z těchto správních rolí je role **dbmanager**. Členové této role mohou vytvářet nové databáze. Pokud chcete použít tuto roli, vytvořte uživatele v databázi `master` a pak ho přidejte do databázové role **dbmanager**. Aby bylo možné vytvořit databázi, musí být uživatel uživatelem založený na SQL Server přihlášení v databázi `master` nebo uživatel databáze s omezením na základě Azure Active Directory uživatele.

1. Pomocí účtu správce se připojte k databázi `master`.
2. Pomocí příkazu [Create Login](https://msdn.microsoft.com/library/ms189751.aspx) Vytvořte přihlašovací údaje pro ověření SQL Server. Ukázka příkazu:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Při vytváření přihlášení nebo uživatele databáze s omezením použijte silné heslo. Další informace najdete v tématu [Silná hesla](https://msdn.microsoft.com/library/ms161962.aspx).

   Za účelem zvýšení výkonu se přihlášení (u hlavních účtů na úrovni serveru) dočasně ukládají do mezipaměti na úrovni databáze. Pokud chcete aktualizovat mezipaměť pro ověřování, podívejte se na informace v tématu [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. V databázi `master` vytvořte uživatele pomocí příkazu [Create User](https://msdn.microsoft.com/library/ms173463.aspx) . Uživatel může být Azure Active Directory ověřování, které obsahuje uživatele databáze (Pokud jste nakonfigurovali prostředí pro ověřování Azure AD), nebo pokud uživatel s omezením ověřování SQL Server obsahuje uživatele databáze nebo ověřování SQL Server na základě SQL Server přihlášení ověřování (vytvořené v předchozím kroku) Ukázkové příkazy:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Přidejte nového uživatele do role databáze **dbmanager** v `master` pomocí příkazu [ALTER role](https://msdn.microsoft.com/library/ms189775.aspx) . Ukázky příkazů:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager je databázová role v hlavní databázi, takže do role dbmanager můžete přidat pouze uživatele databáze. Do role na úrovni databáze není možné přidat přihlášení na úrovni serveru.

5. V případě potřeby nakonfigurujte pravidlo brány firewall, aby se nový uživatel mohl připojit. (Na nového uživatele se může vztahovat už existující pravidlo brány firewall.)

Nyní se uživatel může připojit k databázi `master` a může vytvářet nové databáze. Účet použitý k vytvoření databáze se stává vlastníkem databáze.

### <a name="login-managers"></a>Správci přihlášení

Druhou správní rolí je role správce přihlášení. Členové této role mohou v hlavní databázi vytvářet nová přihlášení. Pokud chcete, můžete použít stejný postup (vytvořit přihlášení a uživatele a přidat uživatele do role **loginmanager**) a povolit tak uživateli vytvářet nová přihlášení v hlavní databázi. Tato přihlášení obvykle nejsou nutná, protože Microsoft doporučuje místo uživatelů s ověřováním na základě přihlášení používat uživatele databáze s omezením, kteří jsou ověřovaní na úrovni databáze. Další informace najdete v tématu [Uživatelé databáze s omezením – zajištění přenositelnosti databáze](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Uživatelé bez oprávnění správce

Obecně platí, že účty bez oprávnění správce nepotřebují přístup k hlavní databázi. Uživatele databáze s omezením můžete vytvářet pomocí příkazu [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Uživatel může být Azure Active Directory ověřování, které obsahuje uživatele databáze (Pokud jste nakonfigurovali prostředí pro ověřování Azure AD), nebo pokud uživatel s omezením ověřování SQL Server obsahuje uživatele databáze nebo ověřování SQL Server na základě přihlášení SQL Server (vytvořené v předchozím kroku). Další informace najdete v části [Uživatelé databáze s omezením – vytvoření přenosné databáze](https://msdn.microsoft.com/library/ff929188.aspx). 

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

V Azure SQL Database použijte příkaz `ALTER ROLE`.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Ve službě Azure synapse použijte [Sp_addrolemember exec](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> Jedním z běžných důvodů, proč vytvořit uživatele databáze na základě přihlášení k serveru SQL Database, je pro uživatele, kteří potřebují přístup k více databázím. Vzhledem k tomu, že uživatelé databáze s omezením jsou jednotlivé entity, udržuje každá databáze vlastní uživatele a vlastní heslo. To může způsobit režii, protože uživatel si pak musí pamatovat každé heslo pro každou databázi a může se stát untenableou změnou více hesel pro mnoho databází. Pokud ale používáte SQL Server přihlašovacích údajů a vysokou dostupnost (aktivní geografickou replikaci a skupiny převzetí služeb při selhání), SQL Server přihlášení musí být nastavená na každém serveru ručně. V opačném případě již nebude uživatel databáze po převzetí služeb při selhání mapován na přihlašovací jméno serveru a nebude moci získat přístup k databázi po převzetí služeb při selhání. Další informace o konfiguraci přihlášení pro geografickou replikaci najdete v tématu [Konfigurace a Správa zabezpečení Azure SQL Database pro geografické obnovení nebo převzetí služeb při selhání](sql-database-geo-replication-security-config.md).

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

- Když chcete provádět příkazy **, musíte být připojeni k** hlavní`CREATE/ALTER/DROP DATABASE` databázi.   
- Databázového uživatele, který odpovídá **správci serveru**, není možné změnit ani vyřadit. 
- Výchozím jazykem přihlášení **správce serveru** je americká angličtina.
- Příkazy **a** mohou provádět jen správci (přihlášení **správce serveru** nebo správce Azure AD) a členové databázové role **dbmanager** v `CREATE DATABASE`hlavní`DROP DATABASE` databázi.
- Při provádění příkazů `CREATE/ALTER/DROP LOGIN` musíte být připojení k hlavní databázi. Nedoporučuje se používat přihlášení. Použijte raději databázové uživatele s omezením.
- Pokud se chcete připojit k uživatelské databázi, musíte v připojovacím řetězci uvést název databáze.
- Příkazy **,**  a **mohou provádět jen hlavní přihlášení na úrovni serveru a členové databázové role**loginmanager`CREATE LOGIN` v `ALTER LOGIN`hlavní`DROP LOGIN` databázi.
- Při provádění příkazů `CREATE/ALTER/DROP LOGIN` a `CREATE/ALTER/DROP DATABASE` v aplikaci ADO.NET není dovolené používat příkazy s parametry. Další informace viz [Příkazy a parametry](https://msdn.microsoft.com/library/ms254953.aspx).
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

- Další informace o pravidlech brány firewall najdete v tématu [Brána firewall služby Azure SQL Database](sql-database-firewall-configure.md).
- Přehled všech funkcí zabezpečení služby SQL Database najdete v [přehledu zabezpečení SQL](sql-database-security-overview.md).
- Kurz najdete v tématu [zabezpečení Azure SQL Database](sql-database-security-tutorial.md).
- Informace o zobrazeních a uložených procedurách najdete v tématu [Vytváření zobrazení a uložených procedur](https://msdn.microsoft.com/library/ms365311.aspx).
- Informace o udělování přístupu k databázovému objektu najdete v tématu [Udělování přístupu k databázovému objektu](https://msdn.microsoft.com/library/ms365327.aspx).
