---
title: Azure SQL Database managed instance zabezpečení pomocí přihlášení Azure AD | Dokumentace Microsoftu
description: Další informace o techniky a funkce zabezpečení ve službě Azure SQL Database managed instance a pomocí přihlášení Azure AD
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 402e10d9b99dbf0eeba8aac27071e4d78fdf0f01
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984507"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-logins"></a>Kurz: Managed instance zabezpečení ve službě Azure SQL Database pomocí přihlášení Azure AD

Mi poskytuje téměř všechny funkce zabezpečení, které nejnovější SQL serveru v místním, že má databázový stroj (Enterprise Edition):

- Omezení přístupu v izolovaném prostředí
- Použití mechanismů ověřování vyžadujících identitu (Azure AD, ověřování SQL)
- Autorizace pomocí členství na základě role a oprávnění
- Povolení funkcí zabezpečení

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvořte přihlašovací údaje Azure Active Directory (AD) pro spravovanou instanci
> - Udělení oprávnění k přihlášení Azure AD do spravované instance
> - Vytvoření uživatele služby Azure AD z přihlašovací údaje Azure AD
> - Přiřazení oprávnění pro uživatele Azure AD a Správa zabezpečení databáze
> - Použití zosobnění se službou Azure AD uživatelům
> - Mezidatabázové dotazy pomocí uživatele Azure AD
> - Další informace o funkcích zabezpečení, jako je ochrana před internetovými útoky, auditování, maskování dat a šifrování

> [!NOTE]
> Probíhá přihlášení Azure AD pro spravované instance **ve verzi public preview**.

Další informace najdete v tématu [Azure SQL Database managed instance přehled](sql-database-managed-instance-index.yml) a [možnosti](sql-database-managed-instance.md) článků.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu, nezapomeňte, že jsou splněné následující požadavky:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Spravované instance Azure SQL Database
  - Postupujte podle tohoto článku: [Rychlé zprovoznění: Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-get-started.md)
- Přístup k vaší spravované instance a [zřízení správce Azure AD pro spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Další informace naleznete v tématu:
    - [Připojení aplikace k managed instance](sql-database-managed-instance-connect-app.md) 
    - [Architektura připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md)
    - [Konfigurace a Správa služby Azure Active Directory ověřování pomocí SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Omezení přístupu k vaší spravované instance

Spravovaná instance je přístupný pouze prostřednictvím privátní IP adresu. Neexistují žádné koncové body služby, které jsou k dispozici pro připojení k managed instance z mimo síť spravovanou instanci. Mnohem jako izolované prostředí v místním systému SQL Server, aplikacím nebo uživatelům potřebují přístup k síti spravované instance (VNet) předtím, než je možné navázat připojení. Další informace najdete v tématu v následujícím článku [připojení aplikace k managed instance](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Protože spravované instance je přístupný pouze uvnitř jeho virtuální síti, [pravidel brány firewall SQL Database](sql-database-firewall-configure.md) nebudou použity. Managed instance má svůj vlastní [integrované firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-login-for-a-managed-instance-using-ssms"></a>Vytvoření přihlášení Azure AD pro managed instance pomocí SSMS

Při prvním přihlášení Azure AD musí být vytvořena standardní účet serveru SQL Server (jiné než azure AD), který je `sysadmin`. Naleznete v následujících článcích pro příklady připojení pro spravovanou instanci:

- [Rychlé zprovoznění: Konfigurace virtuálního počítače Azure se připojit k managed instance](sql-database-managed-instance-configure-vm.md)
- [Rychlé zprovoznění: Konfigurace připojení typu point-to-site z místní do spravované instance](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> Správce služby Azure AD používané k nastavení spravovanou instanci nelze použít k vytvoření přihlášení Azure AD do spravované instance. Musíte vytvořit pomocí účtu systému SQL Server, který je první přihlášení k Azure AD `sysadmin`. Jedná se o dočasné omezení, která bude odebrána po zavedení všeobecné dostupnosti budou přihlašovací údaje Azure AD Pokud se pokusíte vytvořit přihlášení pomocí účtu správce Azure AD se zobrazí následující chyba: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Přihlaste se k vaší spravované instance pomocí standardní účet systému SQL Server (jiné než azure AD), který je `sysadmin`s použitím [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.

3. V okně dotazu použijte následující syntaxi pro vytvoření přihlášení pro místní účet Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení pro účet nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na panelu nástrojů vyberte **Execute** k vytvoření přihlášení.

5. Kontrola nově přidané přihlášení, spuštěním následujícího příkazu T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Další informace najdete v tématu [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Udělení oprávnění k povolení tvorby přihlášení spravované instance

K vytvoření dalších přihlášení Azure AD, musí mít udělen rolí systému SQL Server nebo oprávnění instančnímu objektu (SQL nebo Azure AD).

### <a name="sql-authentication"></a>Ověřování pomocí SQL

- Pokud je přihlášení objektu zabezpečení SQL, pouze přihlašovací údaje, které jsou součástí `sysadmin` role může používat příkazu pro vytvoření k vytvoření přihlášení k účtu služby Azure AD.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

- Pokud chcete povolit nově vytvořený Azure AD přihlášení možnost vytvářet další přihlašovací jména jiných uživatelů Azure AD, skupiny nebo aplikace, udělit přihlášení `sysadmin` nebo `securityadmin` role serveru. 
- Minimálně **ALTER ANY LOGIN** musí být udělena oprávnění pro přihlášení k Azure AD k vytvoření dalších přihlášení Azure AD. 
- Ve výchozím nastavení standardní oprávnění udělenou k nově vytvořeným v hlavní databázi přihlášení Azure AD je: **PŘIPOJENÍ SQL** a **ZOBRAZÍ všechny databáze**.
- `sysadmin` Role serveru lze udělit práva mnoho přihlášení Azure AD v rámci managed instance.

Chcete-li přidat přihlášení na `sysadmin` role serveru:

1. Přihlaste se do spravované instance znovu, nebo použít existující připojení s SQL objekt zabezpečení, který je `sysadmin`.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.

1. Přihlášení k Azure AD udělit `sysadmin` roli serveru pomocí následující syntaxe jazyka T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    Následující příklad uděluje oprávnění `sysadmin` role serveru k přihlášení nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-logins-using-ssms"></a>Vytvořit další přihlašovací údaje Azure AD pomocí aplikace SSMS

Po přihlášení k Azure AD byl vytvořen a součástí `sysadmin` oprávnění tohoto přihlášení můžete vytvořit pomocí dalších přihlašovacích údajů **z EXTERNÍHO poskytovatele** klauzule **CREATE LOGIN**.

1. Připojte se k spravované instance pomocí přihlášení Azure AD pomocí aplikace SQL Server Management Studio. Zadejte název hostitele spravované instance. Ověřování v aplikaci SSMS existují tři možnosti, jak vybírat při přihlášení pomocí účtu Azure AD:

    - Active Directory – univerzální podporující vícefaktorové ověřování
    - Active Directory – heslo
    - Active Directory – integrované </br>

    ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

    Další informace najdete v následujícím článku: [Univerzální ověřování s využitím služeb SQL Database a SQL Data Warehouse (podpora SSMS pro MFA)](sql-database-ssms-mfa-authentication.md)

1. Vyberte **univerzální podporující vícefaktorové ověřování služby Active Directory –**. Otevře se okno přihlášení Vícefaktorové ověřování (MFA). Přihlásit pomocí hesla Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. V aplikaci SSMS **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.
1. V okně dotazu vytvořte přihlašovací údaje pro jiný účet služby Azure AD pomocí následující syntaxi:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení pro uživatele Azure AD bob@aadsqlmi.net, jehož aadsqlmi.net domény je Federovaná pomocí služby Azure AD aadsqlmi.onmicrosoft.com.

    Spuštěním následujícího příkazu T-SQL. Federované Azure AD účty jsou spravované instance nahrazení pro místní Windows přihlášení a uživatelů.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Vytvoření databáze do spravované instance pomocí [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) syntaxe. Tato databáze se použije k testování přihlášení uživatelů v další části.
    1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.
    1. V okně dotazu použijte následující syntaxi pro vytvoření databáze s názvem **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Vytvoření spravované instance přihlášení pro skupinu ve službě Azure AD. Skupiny bude muset existovat ve službě Azure AD předtím, než přidáte přihlášení do spravované instance. Zobrazit [vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Vytvořte skupinu _mygroup_ a přidání členů do této skupiny.

1. Otevřete nové okno dotazu v aplikaci SQL Server Management Studio.

    Tento příklad předpokládá, že existují skupinu s názvem _mygroup_ ve službě Azure AD. Spusťte následující příkaz:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Jako test Přihlaste se ke spravované instance s nově vytvořené přihlašovací jméno nebo skupinu. Otevřít nové připojení pro spravovanou instanci a použití nového přihlašovacího jména při ověřování.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz** nového připojení.
1. Zkontrolujte oprávnění serveru pro nově vytvořený přihlášení k Azure AD spuštěním následujícího příkazu:

    ```sql
    SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
    GO
    ```

> [!NOTE]
> Azure AD uživatele typu Host jsou podporovány pro spravovanou instanci přihlášení pouze v případě přidána jako součást skupiny Azure AD. Uživatel typu Host Azure AD je účet, který je pozvaný do služby Azure AD, které spravované instance patří, z jiné služby Azure AD. Například joe@contoso.com (účet Azure AD) nebo steve@outlook.com (účet MSA) můžete přidat do skupiny v Azure AD aadsqlmi. Jakmile se uživatelé přidají do skupiny, přihlašovací údaje mohou být vytvořeny v spravovanou instanci **hlavní** databáze pro skupinu pomocí **CREATE LOGIN** syntaxe. Uživatelé typu Host, kteří jsou členy této skupiny můžete připojit do spravované instance pomocí své aktuální přihlašovací údaje (například joe@contoso.com nebo steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-login-and-give-permissions"></a>Vytvoření uživatele služby Azure AD z přihlášení k Azure AD a udělení oprávnění

Autorizace na jednotlivé databáze funguje podobně stejně jako ve spravované instanci jak to funguje se službou SQL Server v místním. Uživatel může vytvořit ze stávající přihlašovací údaje v databázi a být součástí oprávnění pro tuto databázi nebo přidat do databázové role.

Teď, když jsme vytvořili databázi s názvem **MyMITestDB**, a přihlašovací údaje, který má pouze výchozí oprávnění, dalším krokem je vytvoření uživatele z tohoto přihlášení. V tomto okamžiku přihlášení můžete připojit k managed instance a zobrazit všechny databáze, ale nemůžou komunikovat s databázemi. Pokud se přihlaste pomocí účtu služby Azure AD, který má výchozí oprávnění a zkuste rozšířit nově vytvořenou databázi, se zobrazí následující chyba:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Další informace o udělení oprávnění k databázi, naleznete v tématu [Začínáme s oprávněními modul databáze](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Vytvoření uživatele služby Azure AD a vytvořit ukázkové tabulky

1. Přihlaste se k vaší spravované instance pomocí `sysadmin` účtu pomocí SQL Server Management Studio.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.
1. V okně dotazu použijte následující syntaxi pro vytvoření uživatele služby Azure AD z přihlášení k Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Následující příklad vytvoří uživatele bob@aadsqlmi.net z přihlášení bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Je také podporována pro vytvoření uživatele služby Azure AD z přihlášení k Azure AD, který je skupina.

    Následující příklad vytvoří přihlášení pro skupiny služby Azure AD _mygroup_ , který existuje ve službě Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Všichni uživatelé, kteří patří do **mygroup** se dostanete **MyMITestDB** databáze.

    > [!IMPORTANT]
    > Při vytváření **uživatele** z přihlášení k Azure AD, zadejte uživatelské_jméno jako stejný login_name z **přihlášení**.

    Další informace najdete v tématu [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. V novém okně dotazu vytvořte tabulku testu pomocí následujícího příkazu T-SQL:

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

1. Vytvoření připojení v aplikaci SSMS s uživatelem, který byl vytvořen. Všimněte si, že nelze najdete v tabulce **TestTable** , který byl vytvořen `sysadmin` dříve. Potřebujeme pro poskytování uživatel s oprávněním ke čtení dat z databáze.

1. Můžete zkontrolovat aktuální oprávnění, se kterým má uživatel spuštěním následujícího příkazu:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Přidání uživatelů do role na úrovni databáze

Pro uživatele, který chcete zobrazit data v databázi, můžeme poskytnout [role na úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles) uživateli.

1. Přihlaste se k vaší spravované instance pomocí `sysadmin` účtu pomocí SQL Server Management Studio.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.

1. Azure AD uživateli udělit `db_datareader` databázové role pomocí následující syntaxe jazyka T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    Následující příklad uvádí uživatele bob@aadsqlmi.net a skupinou _mygroup_ s `db_datareader` oprávnění **MyMITestDB** databáze:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Kontrola uživatele Azure AD, který byl vytvořen v databázi existují spuštěním následujícího příkazu:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Vytvořit nové připojení do spravované instance s uživatelem, který byl přidán do `db_datareader` role.
1. Rozbalte databázi v **Průzkumník objektů** zobrazíte v tabulce.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Otevřete nové okno dotazu a spusťte následující příkaz:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Je možné zobrazit data z tabulky? Měli byste vidět sloupce se vrací.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Zosobnění hlavních účtů na úrovni serveru služby Azure AD (přihlášení)

Spravovaná instance podporuje zosobnění hlavních účtů na úrovni serveru služby Azure AD (přihlášení).

### <a name="test-impersonation"></a>Test zosobnění

1. Přihlaste se k vaší spravované instance pomocí `sysadmin` účtu pomocí SQL Server Management Studio.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.

1. V okně dotazu následujícím příkazem vytvořte novou úložnou proceduru:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Pomocí následujícího příkazu zobrazíte, že uživatel je už zosobnění při spouštění uložené procedury je **bob@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Zosobnění otestovat pomocí příkazu Spustit jako přihlášení:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Pouze SQL serverové objekty zabezpečení (přihlašovací údaje), které jsou součástí `sysadmin` role můžete provádět následující operace cílení na objekty zabezpečení Azure AD: 
> - SPUŠTĚNÍ JAKO UŽIVATEL
> - SPUSTIT JAKO PRO PŘIHLÁŠENÍ

## <a name="using-cross-database-queries-in-managed-instances"></a>Pomocí mezidatabázové dotazy do spravované instance

Mezidatabázové dotazy jsou podporovány pro účty Azure AD se přihlašovací údaje Azure AD. Pokud chcete otestovat dotaz napříč databázemi s skupiny Azure AD, potřebujeme vytvořit jinou databázi a tabulku. Nemusíte vytvářet jiné databáze a tabulky, pokud již neexistuje.

1. Přihlaste se k vaší spravované instance pomocí `sysadmin` účtu pomocí SQL Server Management Studio.
1. V **Průzkumník objektů**, klikněte pravým tlačítkem na server a zvolte **nový dotaz**.
1. V okně dotazu následujícím příkazem vytvořte databázi s názvem **MyMITestDB2** a tabulku s názvem **TestTable2**:

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

1. V novém okně dotazu spusťte následující příkaz pro vytvoření uživatele _mygroup_ v nové databázi **MyMITestDB2**a udělíte oprávnění SELECT pro databáze na _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Přihlásit se do spravované instance pomocí SQL Server Management Studio jako člen skupiny Azure AD _mygroup_. Otevřete nové okno dotazu a spusťte příkaz SELECT mezidatabázové:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Uvidíte výsledky tabulky z **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-logins-public-preview"></a>Další scénáře podporované pro přihlášení Azure AD (public preview) 

- Spuštění agenta serveru SQL správy a úloh jsou podporovány pro přihlášení Azure AD.
- Databáze zálohování a obnovení operací může provést přihlášení Azure AD.
- [Auditování](sql-database-managed-instance-auditing.md) z všechny příkazy související s Azure AD přihlášení a události ověřování.
- Vyhrazené připojení správce pro přihlášení Azure AD, které jsou členy `sysadmin` role serveru.
- Jsou podporovány pomocí přihlášení Azure AD [Nástroj sqlcmd](/sql/tools/sqlcmd-utility) a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) nástroj.
- Přihlašovací aktivační události jsou podporovány pro události přihlášení pocházející z přihlášení Azure AD.
- Služba Service Broker a DB e-mailu může být také nastaven pomocí přihlášení Azure AD.


## <a name="next-steps"></a>Další postup

### <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Přečtěte si následující [managed instance funkce zabezpečení funkce](sql-database-managed-instance.md#azure-sql-database-security-features) článku s úplným seznamem způsoby, jak zabezpečit vaši databázi. Jsou popsány následující funkce zabezpečení:

- [Auditování spravované instance](sql-database-managed-instance-auditing.md) 
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](sql-database-managed-instance-threat-detection.md) 
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovní řádků](/sql/relational-databases/security/row-level-security) 
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Možnosti spravované instance

Úplný přehled o spravované instanci funkce naleznete v tématu:

> [!div class="nextstepaction"]
> [Možnosti spravované instance](sql-database-managed-instance.md)
