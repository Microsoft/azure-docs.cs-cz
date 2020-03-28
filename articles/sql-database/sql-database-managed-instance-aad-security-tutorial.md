---
title: Zabezpečení spravované instance s objekty serveru Azure AD (přihlášení)
description: Další informace o technikách a funkcích pro zabezpečení spravované instance v Azure SQL Database a použití principů serveru Azure AD (přihlášení)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: bd65a21c2aa21643c76966410931949db7d17ad6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73822796"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Kurz: Zabezpečení spravované instance v Azure SQL Database pomocí objektů zabezpečení serveru Azure AD (přihlášení)

Spravovaná instance poskytuje téměř všechny funkce zabezpečení, které má nejnovější místně sql server (Enterprise Edition) Database Engine:

- Omezení přístupu v izolovaném prostředí
- Použití ověřovacích mechanismů, které vyžadují identitu (Azure AD, ověřování SQL)
- Použití autorizace s členstvími a oprávněními založenými na rolích
- Povolení funkcí zabezpečení

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření objektu zabezpečení serveru služby Azure Active Directory (AD) (přihlášení) pro spravovanou instanci
> - Udělit oprávnění k objektům zabezpečení serveru Azure AD (přihlášení) ve spravované instanci
> - Vytvoření uživatelů Azure AD z objektů zabezpečení serveru Azure AD (přihlášení)
> - Přiřazení oprávnění uživatelům Azure AD a správa zabezpečení databáze
> - Použití zosobnění s uživateli Azure AD
> - Použití dotazů mezi databázemi s uživateli Azure AD
> - Informace o funkcích zabezpečení, jako je ochrana před hrozbami, auditování, maskování dat a šifrování

Další informace najdete v článcích [přehled ustavené instance Azure SQL Database](sql-database-managed-instance-index.yml) a [možnosti.](sql-database-managed-instance.md)

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kurz, ujistěte se, že máte následující předpoklady:

- [Sql Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Instance spravované azure SQL database
  - Postupujte podle tohoto článku: [Úvodní příručka: Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md)
- Možnost přístupu ke spravované instanci a [zřízení správce Azure AD pro spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Další informace naleznete v tématu:
    - [Připojení aplikace ke spravované instanci](sql-database-managed-instance-connect-app.md) 
    - [Architektura připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md)
    - [Konfigurace a správa ověřování Azure Active Directory pomocí SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Omezení přístupu ke spravované instanci

Spravované instance lze přistupovat prostřednictvím privátní IP adresy. Podobně jako izolované místní prostředí SQL Serveru, aplikace nebo uživatelé potřebují přístup k síti spravovaných instancí (VNet) před navázáním připojení. Další informace naleznete v následujícím článku [Připojení aplikace ke spravované instanci](sql-database-managed-instance-connect-app.md).

Je také možné nakonfigurovat koncový bod služby na spravované instanci, která umožňuje veřejná připojení, stejným způsobem jako Azure SQL Database. Další informace naleznete v následujícím článku [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL Database](sql-database-managed-instance-public-endpoint-configure.md).

> [!NOTE] 
> I když jsou povoleny koncové body služby, [pravidla brány firewall databáze SQL](sql-database-firewall-configure.md) neplatí. Spravovaná instance má vlastní [vestavěnou bránu firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) pro správu připojení.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Vytvoření objektu zabezpečení serveru Azure AD (přihlášení) pro spravovanou instanci pomocí SSMS

První objekt zabezpečení serveru Azure AD (přihlášení) lze vytvořit standardní účet SQL Server `sysadmin`(non-azure AD), který je , nebo správce Azure AD pro spravovanou instanci vytvořenou během procesu zřizování. Další informace najdete [v tématu Zřízení správce služby Azure Active Directory pro vaši spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Tato funkce se změnila od [ga objektů zabezpečení serveru Azure AD](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

V následujících článcích najdete příklady připojení ke spravované instanci:

- [Úvodní příručka: Konfigurace virtuálního počítače Azure pro připojení ke spravované instanci](sql-database-managed-instance-configure-vm.md)
- [Úvodní příručka: Konfigurace připojení bodu k webu ke spravované instanci z místního prostředí](sql-database-managed-instance-configure-p2s.md)

1. Přihlaste se ke spravované instanci pomocí standardního účtu `sysadmin` SQL Serveru (mimo azure AD), který je správcem azure ad pro MI, pomocí [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.

3. V okně dotazu použijte následující syntaxi k vytvoření přihlášení pro místní účet Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení nativeuser@aadsqlmi.onmicrosoft.compro účet .

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na panelu nástrojů vyberte **Spustit,** chcete-li vytvořit přihlášení.

5. Zkontrolujte nově přidané přihlášení spuštěním následujícího příkazu T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Další informace naleznete v tématu [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Udělení oprávnění k povolení vytváření přihlášení spravované instance

Chcete-li vytvořit další objekty serveru Azure AD (přihlášení), sql server role nebo oprávnění musí být udělena na primární (SQL nebo Azure AD).

### <a name="sql-authentication"></a>Ověřování pomocí SQL

- Pokud je přihlášení sql principal, pouze přihlášení, `sysadmin` které jsou součástí role můžete použít příkaz vytvořit k vytvoření přihlášení pro účet Azure AD.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

- Chcete-li povolit nově vytvořený hlavní server Azure AD (přihlášení) možnost vytvářet další přihlášení pro `sysadmin` ostatní `securityadmin` uživatele Azure AD, skupiny nebo aplikace, udělte přihlašovací nebo serverrole. 
- Minimálně **alter jakékoli přihlášení** oprávnění musí být uděleno na serveru Azure AD instanční (přihlášení) k vytvoření dalších objektů zabezpečení serveru Azure AD (přihlášení). 
- Ve výchozím nastavení je standardní oprávnění udělená nově vytvořeným objektům serveru Azure AD (přihlášení) v hlavním serveru: **CONNECT SQL** a **ZOBRAZIT LIBOVOLNOU DATABÁZI**.
- Role `sysadmin` serveru může být udělena mnoha objektům serveru Azure AD (přihlášení) v rámci spravované instance.

Přidání přihlášení do `sysadmin` role serveru:

1. Přihlaste se znovu ke spravované instanci nebo použijte existující připojení `sysadmin`s správcem Azure AD nebo SQL Principal, který je .

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.

1. Udělte hlavní objekt serveru Azure `sysadmin` AD (přihlášení) roli serveru pomocí následující syntaxe T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Následující příklad uděluje `sysadmin` roli serveru pro přihlášenínativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Vytvoření dalších objektů zabezpečení serveru Azure AD (přihlášení) pomocí SSMS

Po vytvoření hlavního mocnina serveru Azure AD `sysadmin` (přihlášení) a poskytuje s oprávněními, že přihlášení můžete vytvořit další přihlášení pomocí **from external provider** klauzule s CREATE **LOGIN**.

1. Připojte se ke spravované instanci pomocí hlavního serveru Azure AD (přihlášení) pomocí sql server Management Studio. Zadejte název hostitele spravované instance. Pro ověřování v SSMS existují tři možnosti, ze kterých si můžete vybrat při přihlášení pomocí účtu Azure AD:

   - Služba Active Directory – univerzální program s podporou vícefaktorové pomoci
   - Služba Active Directory – heslo
   - Služba Active Directory – integrovaná </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Další informace naleznete v následujícím článku: [Univerzální ověřování s databází SQL a datovým skladem SQL (podpora SSMS pro vícefaktorové ověřování)](sql-database-ssms-mfa-authentication.md)

1. Vyberte **službu Active Directory – univerzální s podporou vícefaktorové pomoci**. Tím se vyvolá přihlašovací okno vícefaktorového ověřování (MFA). Přihlaste se pomocí hesla Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. V **Průzkumníku objektů**SSMS klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.
1. V okně dotazu použijte následující syntaxi k vytvoření přihlášení pro jiný účet Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení pro uživatele bob@aadsqlmi.netAzure AD , jehož doména aadsqlmi.net je federována s aadsqlmi.onmicrosoft.com Azure AD.

    Spusťte následující příkaz T-SQL. Federované účty Azure AD jsou spravované instance náhrady pro místní přihlášení systému Windows a uživatelů.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Vytvořte databázi ve spravované instanci pomocí syntaxe [CREATE DATABASE.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) Tato databáze se použije k testování přihlášení uživatelů v další části.
    1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.
    1. V okně dotazu použijte následující syntaxi k vytvoření databáze s názvem **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Vytvořte přihlášení spravované instance pro skupinu ve službě Azure AD. Skupina bude muset existovat ve službě Azure AD, než budete moci přidat přihlášení do spravované instance. Viz [Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Vytvořte skupinu _moje skupina_ a přidejte členy do této skupiny.

1. Otevřete nové okno dotazu v aplikaci SQL Server Management Studio.

    Tento příklad předpokládá, že existuje skupina s názvem _mygroup_ ve službě Azure AD. Spusťte následující příkaz:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Jako test se přihlaste ke spravované instanci pomocí nově vytvořeného přihlášení nebo skupiny. Otevřete nové připojení ke spravované instanci a při ověřování použijte nové přihlášení.
1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz** pro nové připojení.
1. Zkontrolujte oprávnění serveru pro nově vytvořený objekt zabezpečení serveru Azure AD (přihlášení) provedením následujícího příkazu:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Uživatelé typu Host Azure AD jsou podporovány pro přihlášení spravovaných instancí, pouze při přidání jako součást skupiny Azure AD. Uživatel hosta Azure AD je účet, který je pozván do služby Azure AD, do které spravovaná instance patří, z jiného azure ad. Například joe@contoso.com (účet Azure AD) nebo steve@outlook.com (účet MSA) lze přidat do skupiny v aadsqlmi Azure AD. Jakmile jsou uživatelé přidáni do skupiny, přihlášení lze vytvořit v **hlavní** databázi spravované instance pro skupinu pomocí syntaxe **CREATE LOGIN.** Uživatelé typu Host, kteří jsou členy této skupiny, joe@contoso.com se mohou steve@outlook.comke spravované instanci připojit pomocí svých aktuálních přihlášení (například nebo ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Vytvoření uživatele Azure AD z hlavního zabezpečení serveru Azure AD (přihlášení) a udělení oprávnění

Autorizace pro jednotlivé databáze funguje ve spravované instanci podobně jako u místního serveru SQL Server. Uživatele lze vytvořit z existujícího přihlášení v databázi a získat oprávnění k této databázi nebo přidat do role databáze.

Teď, když jsme vytvořili databázi s názvem **MyMITestDB**, a přihlášení, které má pouze výchozí oprávnění, dalším krokem je vytvořit uživatele z tohoto přihlášení. V současné době přihlášení můžete připojit ke spravované instanci a zobrazit všechny databáze, ale nelze pracovat s databázemi. Pokud se přihlásíte pomocí účtu Azure AD, který má výchozí oprávnění, a pokusíte se rozbalit nově vytvořenou databázi, zobrazí se následující chyba:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Další informace o udělení oprávnění k databázi naleznete v [tématu Začínáme s oprávněními databázového stroje](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Vytvoření uživatele Azure AD a vytvoření ukázkové tabulky

1. Přihlaste se ke `sysadmin` spravované instanci pomocí účtu pomocí aplikace SQL Server Management Studio.
1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.
1. V okně dotazu použijte následující syntaxi k vytvoření uživatele Azure AD z hlavního uživatele serveru Azure AD (přihlášení):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Následující příklad vytvoří bob@aadsqlmi.net uživatele z přihlášeníbob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Je také podporována k vytvoření uživatele Azure AD z hlavního uživatele serveru Azure AD (přihlášení), který je skupina.

    Následující příklad vytvoří přihlášení pro skupinu Azure AD _mygroup,_ která existuje ve vašem Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Všichni uživatelé, kteří patří do **mygroup,** mají přístup k databázi **MyMITestDB.**

    > [!IMPORTANT]
    > Při vytváření **USER** z hlavního uživatele serveru Azure AD (přihlášení), zadejte user_name jako stejné login_name z **LOGIN**.

    Další informace naleznete v tématu [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. V novém okně dotazu vytvořte testovací tabulku pomocí následujícího příkazu T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Vytvořte připojení v SSMS s uživatelem, který byl vytvořen. Všimněte si, že nemůžete vidět tabulku **TestTable,** která byla vytvořena `sysadmin` dříve. Musíme poskytnout uživateli oprávnění ke čtení dat z databáze.

1. Aktuální oprávnění uživatele můžete zkontrolovat provedením následujícího příkazu:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Přidání uživatelů do rolí na úrovni databáze

Pro uživatele zobrazit data v databázi, můžeme poskytnout [role na úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles) pro uživatele.

1. Přihlaste se ke `sysadmin` spravované instanci pomocí účtu pomocí aplikace SQL Server Management Studio.

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.

1. Udělte uživateli `db_datareader` Azure AD databázovou roli pomocí následující syntaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Následující příklad poskytuje bob@aadsqlmi.net uživateli a skupině `db_datareader` _mygroup_ oprávnění k databázi **MyMITestDB:**

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Zkontrolujte, zda uživatel Služby Azure AD, který byl vytvořen v databázi existují spuštěním následující příkaz:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Vytvořte nové připojení ke spravované instanci s `db_datareader` uživatelem, který byl přidán do role.
1. Rozbalte databázi v **Průzkumníkovi objektů** a zobcete tabulku.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Otevřete nové okno dotazu a spusťte následující příkaz SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Vidíte data z tabulky? Měli byste vidět sloupce, které jsou vráceny.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Zosobnění objektů zabezpečení serveru Azure AD (přihlášení)

Spravovaná instance podporuje zosobnění objektů zabezpečení serveru Azure AD (přihlášení).

### <a name="test-impersonation"></a>Testovat zosobnění

1. Přihlaste se ke `sysadmin` spravované instanci pomocí účtu pomocí aplikace SQL Server Management Studio.

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.

1. V okně dotazu vytvořte novou uloženou proceduru pomocí následujícího příkazu:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Pomocí následujícího příkazu zjistíte, že uživatel, kterého zosobňujete při provádění uložené procedury, je **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Otestujte zosobnění pomocí příkazu EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Pouze objekty sql server na úrovni (přihlášení), `sysadmin` které jsou součástí role můžete provést následující operace zaměřené na objekty Azure AD: 
> - SPUSTIT JAKO UŽIVATEL
> - PROVÉST JAKO PŘIHLÁŠENÍ

## <a name="using-cross-database-queries-in-managed-instances"></a>Použití dotazů mezi databázemi ve spravovaných instancích

Dotazy mezi databázemi jsou podporované pro účty Azure AD s objekty zabezpečení serveru Azure AD (přihlášení). Chcete-li otestovat dotaz mezi databázemi se skupinou Azure AD, musíme vytvořit jinou databázi a tabulku. Vytvoření jiné databáze a tabulky můžete přeskočit, pokud již existuje.

1. Přihlaste se ke `sysadmin` spravované instanci pomocí účtu pomocí aplikace SQL Server Management Studio.
1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na server a zvolte **Nový dotaz**.
1. V okně dotazu vytvořte pomocí následujícího příkazu databázi s názvem **MyMITestDB2** a tabulku s názvem **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. V novém okně dotazu spusťte následující příkaz k vytvoření uživatelské _skupiny mygroup_ v nové databázi **MyMITestDB2**a udělte _mygroup_oprávnění SELECT pro tuto databázi :

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Přihlaste se ke spravované instanci pomocí SQL Server Management Studio jako člen skupiny Azure AD _mygroup_. Otevřete nové okno dotazu a spusťte příkaz SELECT mezi databázemi:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Měli byste vidět výsledky tabulky z **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>Další scénáře podporované pro objekty zabezpečení serveru Azure AD (přihlášení)

- Sql Agent správy a spuštění úloh jsou podporovány pro objekty serveru Azure AD (přihlášení).
- Operace zálohování a obnovení databáze lze provést pomocí objektů zabezpečení serveru Azure AD (přihlášení).
- [Auditování](sql-database-managed-instance-auditing.md) všech příkazů souvisejících s objekty zabezpečení serveru Azure AD (přihlášení) a události ověřování.
- Vyhrazené připojení správce pro objekty serveru Azure AD (přihlášení), které jsou členy role `sysadmin` serveru.
- Principy serveru Azure AD (přihlášení) jsou podporovány pomocí [sqlcmd Utility](/sql/tools/sqlcmd-utility) a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) nástroj.
- Přihlašovací aktivační události jsou podporovány pro události přihlášení pocházející z objektů zabezpečení serveru Azure AD (přihlášení).
- Service Broker a DB mail lze nastavit pomocí objektů zabezpečení serveru Azure AD (přihlášení).


## <a name="next-steps"></a>Další kroky

### <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Úplný seznam způsobů zabezpečení databáze najdete v následujícím článku [funkce funkce funkce spravované instance.](sql-database-managed-instance.md#azure-sql-database-security-features) Jsou popsány následující funkce zabezpečení:

- [Auditování spravované instance](sql-database-managed-instance-auditing.md) 
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](sql-database-managed-instance-threat-detection.md) 
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security) 
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Možnosti spravované instance

Úplný přehled možností spravované instance najdete v tématu:

> [!div class="nextstepaction"]
> [Možnosti spravované instance](sql-database-managed-instance.md)
