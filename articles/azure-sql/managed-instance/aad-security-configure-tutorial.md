---
title: Zabezpečení spravované instance SQL pomocí objektů zabezpečení serveru Azure AD (přihlášení)
description: Seznamte se s technikami a funkcemi pro zabezpečení spravované instance Azure SQL a používání objektů zabezpečení serveru Azure AD (přihlášení).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 50544507f9d83c216bb6c18e004c5ce7ad1ca346
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639847"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Kurz: Zabezpečení ve službě Azure SQL Managed Instance s využitím objektů zabezpečení (přihlášení) serveru Azure AD
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance poskytuje skoro všechny funkce zabezpečení, které má nejnovější databázový stroj SQL Server (Enterprise Edition):

- Omezení přístupu v izolovaném prostředí
- Použití ověřovacích mechanismů, které vyžadují identitu: Azure Active Directory (Azure AD) a ověřování SQL
- Ověřování pomocí členství a oprávnění na základě rolí
- Povolit funkce zabezpečení

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření objektu zabezpečení serveru Azure AD (přihlášení) pro spravovanou instanci
> - Udělení oprávnění pro objekty zabezpečení serveru Azure AD (přihlášení) ve spravované instanci
> - Vytváření uživatelů Azure AD z objektů zabezpečení serveru Azure AD (přihlášení)
> - Přiřazení oprávnění pro uživatele Azure AD a správu zabezpečení databáze
> - Použití zosobnění s uživateli Azure AD
> - Použití mezidatabázových dotazů s uživateli Azure AD
> - Přečtěte si o funkcích zabezpečení, jako je ochrana před hrozbami, auditování, maskování dat a šifrování.

Další informace najdete v tématu [Přehled spravované instance Azure SQL](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Spravovaná instance
  - Postupujte podle tohoto článku: [rychlý Start: vytvoření spravované instance](instance-create-quickstart.md)
- Přístup k spravované instanci a [zřízení správce Azure AD pro spravovanou instanci](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Další informace najdete v následujících tématech:
  - [Připojení aplikace ke spravované instanci](connect-application-instance.md)
  - [Architektura připojení spravované instance SQL](connectivity-architecture-overview.md)
  - [Konfigurace a Správa ověřování Azure Active Directory pomocí SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Omezení přístupu 

K spravovaným instancím je možné přistupovat prostřednictvím privátní IP adresy. Podobně jako izolované SQL Server prostředí potřebují aplikace nebo uživatelé přístup k síti spravované instance SQL (VNet), než může být navázáno připojení. Další informace najdete v tématu [připojení aplikace k spravované instanci SQL](connect-application-instance.md).

Je také možné nakonfigurovat koncový bod služby ve spravované instanci, která umožňuje veřejné připojení stejným způsobem jako u Azure SQL Database.
Další informace najdete v tématu [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL](public-endpoint-configure.md).

> [!NOTE]
> I s povolenými koncovými body služby [Azure SQL Database pravidla brány firewall](../database/firewall-configure.md) neplatí. Spravovaná instance Azure SQL má svou vlastní [vestavěnou bránu firewall](management-endpoint-verify-built-in-firewall.md) pro správu připojení.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Vytvoření objektu zabezpečení serveru Azure AD (přihlášení) pomocí SSMS

První objekt zabezpečení serveru Azure AD (přihlášení) se dá vytvořit pomocí standardního účtu správce SQL (mimo Azure AD), `sysadmin` nebo správce Azure AD pro spravovanou instanci vytvořenou během procesu zřizování. Další informace najdete v tématu [zřízení správce Azure Active Directory pro spravovanou instanci SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Příklady připojení ke spravované instanci SQL najdete v následujících článcích:

- [Rychlý Start: konfigurace virtuálního počítače Azure pro připojení ke spravované instanci SQL](connect-vm-instance-configure.md)
- [Rychlý Start: Konfigurace připojení typu Point-to-site k spravované instanci SQL z místního prostředí](point-to-site-p2s-configure.md)

1. Přihlaste se ke svojí spravované instanci pomocí standardního přihlašovacího účtu SQL (mimo Azure AD), který je `sysadmin` správcem Azure AD pro spravovanou instanci SQL, a to pomocí [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.

3. V okně dotazu vytvořte pomocí následující syntaxe přihlašovací údaje pro místní účet služby Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení pro účet nativeuser@aadsqlmi.onmicrosoft.com .

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na panelu nástrojů vyberte **provést** a vytvořte přihlášení.

5. Ověřte nově přidávané přihlášení spuštěním následujícího příkazu T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Snímek obrazovky karty výsledky v Průzkumník objektů S s M S zobrazuje název, principal_id, SID, typ a type_desc nově přidaného přihlášení.](./media/aad-security-configure-tutorial/native-login.png)

Další informace najdete v tématu [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)údajů.

## <a name="grant-permissions-to-create-logins"></a>Udělení oprávnění k vytváření přihlašovacích údajů

Aby bylo možné vytvářet další objekty zabezpečení serveru Azure AD (přihlášení), musí být SQL Server rolí nebo oprávnění udělena objektu zabezpečení (SQL nebo Azure AD).

### <a name="sql-authentication"></a>Ověřování SQL

- Pokud je přihlášení objektem zabezpečení SQL, `sysadmin` můžou k vytvoření přihlašovacích údajů pro účet Azure AD používat jenom přihlášení, která jsou součástí této role.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

- Pokud chcete nově vytvořenému objektu zabezpečení serveru Azure AD (přihlášení) povolit možnost vytvářet další přihlášení pro ostatní uživatele, skupiny nebo aplikace Azure AD, udělte `sysadmin` roli přihlášení nebo `securityadmin` serveru.
- Aby bylo možné vytvářet další objekty zabezpečení serveru Azure AD (přihlášení), musí být pro objekt zabezpečení serveru Azure AD (přihlášení) k dispozici alespoň Změna oprávnění k **přihlášení** .
- Ve výchozím nastavení má standardní oprávnění udělené nově vytvořeným objektům zabezpečení serveru Azure AD (přihlášení) v hlavní databázi: **Connect SQL** a **zobrazení jakékoli databáze**.
- `sysadmin`Role serveru může být udělena mnoha objektům zabezpečení serveru Azure AD (přihlášeními) v rámci spravované instance.

Přidání přihlašovacích údajů do `sysadmin` role serveru:

1. Přihlaste se ke spravované instanci znovu nebo použijte existující připojení ke Správci služby Azure AD nebo objektu zabezpečení SQL, který je `sysadmin` .

1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.

1. Pomocí následující syntaxe T-SQL udělte objektu zabezpečení serveru Azure AD (přihlášení) `sysadmin` roli serveru:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    V následujícím příkladu udělí `sysadmin` role serveru přihlašovací údaje. nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Vytvoření dalších objektů zabezpečení serveru Azure AD (přihlášení) pomocí SSMS

Po vytvoření objektu zabezpečení serveru Azure AD (přihlášení) a jeho `sysadmin` použití s oprávněním může přihlášení vytvořit další přihlášení pomocí klauzule **z externího poskytovatele** s příkazem **vytvořit přihlášení**.

1. Připojte se ke spravované instanci pomocí objektu zabezpečení serveru Azure AD (přihlášení) pomocí SQL Server Management Studio. Zadejte název hostitele spravované instance SQL. Pro ověřování v SSMS existují tři možnosti, ze kterých si můžete vybrat, když se přihlásíte pomocí účtu Azure AD:

   - Active Directory – univerzální s podporou vícefaktorového ověřování
   - Active Directory – heslo
   - Integrovaná se službou Active Directory </br>

     ![Snímek obrazovky dialogového okna připojit k serveru v s s/M s se službou Active Directory – v rozevíracím seznamu ověřování je vybraná možnost univerzální s podporou MFA.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Další informace najdete v tématu [univerzální ověřování (podpora SSMS pro Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Vyberte **Active Directory – univerzální s podporou vícefaktorového ověřování**. Tím se zobrazí okno přihlášení Multi-Factor Authentication. Přihlaste se pomocí svého hesla služby Azure AD.

    ![Snímek obrazovky okna přihlašovacího Multi-Factor Authentication s kurzorem v poli zadat heslo](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. V SSMS **Průzkumník objektů** klikněte pravým tlačítkem na server a vyberte **Nový dotaz**.
1. V okně dotazu použijte k vytvoření přihlašovacího jména pro jiný účet služby Azure AD následující syntaxi:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Tento příklad vytvoří přihlášení pro uživatele Azure AD bob@aadsqlmi.net , jehož doména aadsqlmi.NET je federované s doménou Azure ad aadsqlmi.onmicrosoft.com.

    Spusťte následující příkaz T-SQL. Účty federované služby Azure AD jsou nahrazeními spravované instance SQL pro místní přihlášení a uživatele Windows.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Vytvořte databázi ve spravované instanci pomocí syntaxe [Create Database](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . Tato databáze bude sloužit k testování přihlášení uživatelů v následující části.
    1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.
    1. V okně dotazu vytvořte pomocí následující syntaxe databázi s názvem **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Vytvoří přihlašovací jméno spravované instance SQL pro skupinu ve službě Azure AD. Aby bylo možné přidat přihlášení do spravované instance SQL, bude nutné, aby tato skupina existovala ve službě Azure AD. Přečtěte si téma [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Vytvořte skupinu _MYGROUP_ a do této skupiny přidejte členy.

1. Otevřete nové okno dotazu v SQL Server Management Studio.

    V tomto příkladu se předpokládá, že existuje skupina s názvem _MYGROUP_ ve službě Azure AD. Spusťte následující příkaz:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Jako test se přihlaste ke spravované instanci s nově vytvořeným přihlášením nebo skupinou. Otevřete nové připojení ke spravované instanci a při ověřování použijte nové přihlašovací údaje.
1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte **Nový dotaz** pro nové připojení.
1. Ověřte oprávnění serveru k nově vytvořenému objektu zabezpečení serveru Azure AD (přihlášení) spuštěním následujícího příkazu:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Uživatelé Azure AD hosta se podporují pro přihlašovací údaje spravované instance SQL jenom v případě, že se přidají jako součást skupiny Azure AD. Uživatel typu Host služby Azure AD je účet, který je pozván do instance služby Azure AD, ke které patří spravovaná instance, z jiné instance služby Azure AD. Například joe@contoso.com (účet Azure AD) nebo steve@outlook.com (účet Microsoft) se dají přidat do skupiny v instanci Azure AD aadsqlmi. Po přidání uživatelů do skupiny se přihlašovací jméno dá vytvořit v **hlavní** databázi spravované instance SQL pro skupinu pomocí syntaxe **Vytvoření přihlašovacích** údajů. Uživatelé typu Host, kteří jsou členy této skupiny, se mohou připojit ke spravované instanci pomocí jejich aktuálních přihlášení (například joe@contoso.com nebo steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Vytvoření uživatele služby Azure AD z objektu zabezpečení serveru Azure AD (přihlášení)

Autorizace pro jednotlivé databáze funguje v podstatě stejným způsobem jako ve spravované instanci SQL, stejně jako u databází v SQL Server. Uživatele je možné vytvořit z existujícího přihlašovacího jména v databázi a poskytnout je s oprávněními k této databázi nebo přidat k databázové roli.

Teď, když jsme vytvořili databázi s názvem **MyMITestDB** a přihlašovací jméno, které má jenom výchozí oprávnění, je dalším krokem vytvoření uživatele z tohoto přihlašovacího jména. V tuto chvíli se přihlašovací jméno může připojit ke spravované instanci a zobrazit všechny databáze, ale nemůže s databázemi pracovat. Pokud se přihlásíte pomocí účtu Azure AD, který má výchozí oprávnění, a pokusíte se rozšířit nově vytvořenou databázi, zobrazí se následující chyba:

![Snímek obrazovky s chybovou zprávou z Průzkumník objektů S S M M S, který čte "databáze MyMITestDB není přístupná. (ObjectExplorer)".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Další informace o udělení oprávnění databáze najdete v tématu [Začínáme s oprávněním databázového stroje](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Vytvoření uživatele Azure AD a vytvoření ukázkové tabulky

1. Přihlaste se ke svojí spravované instanci pomocí `sysadmin` účtu pomocí SQL Server Management Studio.
1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.
1. Pomocí následující syntaxe v okně dotazu vytvořte uživatele Azure AD z objektu zabezpečení serveru Azure AD (přihlášení):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    Následující příklad vytvoří uživatele bob@aadsqlmi.net z přihlašovacích údajů. bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Také se podporuje vytvoření uživatele služby Azure AD z objektu zabezpečení serveru Azure AD (přihlášení), který je skupina.

    Následující příklad vytvoří přihlášení pro skupinu Azure AD _MYGROUP_ , která existuje ve vaší instanci Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Všichni uživatelé, kteří patří do *MYGROUP* , mají přístup k databázi **MyMITestDB** .

    > [!IMPORTANT]
    > Při vytváření **uživatele** z objektu zabezpečení serveru Azure AD (přihlášení) zadejte user_name jako stejný Login_name od **přihlášení**.

    Další informace najdete v tématu [Vytvoření uživatele](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

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

1. Vytvořte připojení v SSMS pomocí vytvořeného uživatele. Všimnete si, že nemůžete zobrazit tabulku **test** Table vytvořenou `sysadmin` dříve. Musíme uživateli poskytnout oprávnění ke čtení dat z databáze.

1. Aktuální oprávnění, které uživatel má, můžete ověřit spuštěním následujícího příkazu:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Přidání uživatelů do rolí na úrovni databáze

Aby mohl uživatel zobrazit data v databázi, můžeme uživatelům poskytnout role na [úrovni databáze](/sql/relational-databases/security/authentication-access/database-level-roles) .

1. Přihlaste se ke svojí spravované instanci pomocí `sysadmin` účtu pomocí SQL Server Management Studio.

1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.

1. Udělte uživateli Azure AD `db_datareader` databázovou roli pomocí následující syntaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    V následujícím příkladu je uveden uživatel bob@aadsqlmi.net a skupina _MYGROUP_ s `db_datareader` oprávněními k databázi **MyMITestDB** :

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Ověřte, že uživatel Azure AD, který byl vytvořen v databázi, existuje spuštěním následujícího příkazu:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Vytvořte nové připojení ke spravované instanci pomocí uživatele, který byl přidán do `db_datareader` role.
1. Rozbalením databáze v **Průzkumník objektů** zobrazíte tabulku.

    ![Snímek obrazovky z Průzkumník objektů v S S M S. zobrazení struktury složek pro tabulky v MyMITestDB. Dbo. Složka pro testování je zvýrazněna.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Otevřete nové okno dotazu a spusťte následující příkaz SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Je možné zobrazit data z tabulky? Měli byste vidět vracené sloupce.

    ![Snímek obrazovky se záložkou výsledky v Průzkumník objektů S M S zobrazující záhlaví sloupců tabulky AccountNum, City, Name a State.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Zosobnění objektů zabezpečení na úrovni serveru Azure AD (přihlášení)

Spravovaná instance SQL podporuje zosobnění objektů zabezpečení na úrovni serveru Azure AD (přihlášení).

### <a name="test-impersonation"></a>Zosobnění testu

1. Přihlaste se ke svojí spravované instanci pomocí `sysadmin` účtu pomocí SQL Server Management Studio.

1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.

1. V okně dotazu vytvořte pomocí následujícího příkazu novou uloženou proceduru:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Pomocí následujícího příkazu zjistíte, že uživatel, kterého zosobňujete při provádění uložené procedury, je **bob \@ aadsqlmi.NET**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Zosobnění testu pomocí příkazu Spustit jako pro přihlášení:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> `sysadmin`Následující operace zaměřené na objekty zabezpečení Azure AD můžou spouštět jenom objekty zabezpečení na úrovni SQL serveru (přihlášení), které jsou součástí této role:
>
> - SPUSTIT JAKO UŽIVATEL
> - SPUSTIT JAKO PŘIHLAŠOVACÍ ÚDAJE

## <a name="use-cross-database-queries"></a>Použití mezidatabázových dotazů

Mezidatabázové dotazy jsou podporovány pro účty Azure AD s objekty zabezpečení serveru Azure AD (přihlašovací údaje). Abychom mohli otestovat dotaz mezi databázemi pomocí skupiny Azure AD, musíme vytvořit další databázi a tabulku. Můžete přeskočit vytvoření další databáze a tabulky, pokud již existuje.

1. Přihlaste se ke svojí spravované instanci pomocí `sysadmin` účtu pomocí SQL Server Management Studio.
1. V **Průzkumník objektů** klikněte pravým tlačítkem myši na server a vyberte možnost **Nový dotaz**.
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

1. V novém okně dotazu spusťte následující příkaz, který vytvoří uživatele _MYGROUP_ v nové databázi **MyMITestDB2** a UDĚLÍ této databázi oprávnění SELECT pro _MYGROUP_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Přihlaste se ke spravované instanci pomocí SQL Server Management Studio jako člen skupiny Azure AD _MYGROUP_. Otevřete nové okno dotazu a spusťte příkaz pro výběr mezi databázemi:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Měli byste vidět výsledky tabulky z **TestTable2**.

## <a name="additional-supported-scenarios"></a>Další podporované scénáře

- Správa agenta SQL a provádění úloh se podporují pro objekty zabezpečení serveru Azure AD (přihlášení).
- Operace zálohování a obnovení databáze můžou provádět objekty zabezpečení serveru Azure AD (přihlášení).
- [Auditování](auditing-configure.md) všech příkazů souvisejících s objekty zabezpečení serveru Azure AD (přihlašovacími údaji) a událostmi ověřování.
- Vyhrazené připojení správce pro objekty zabezpečení serveru Azure AD (přihlašovací údaje), které jsou členy `sysadmin` role serveru.
- Objekty zabezpečení serveru Azure AD (přihlášení) se podporují pomocí nástroje [Sqlcmd](/sql/tools/sqlcmd-utility) a nástroje [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) .
- Aktivační události přihlášení jsou podporovány pro události přihlášení přicházející z objektů zabezpečení serveru Azure AD (přihlášení).
- E-maily Service Broker a DB je možné nastavit pomocí objektů zabezpečení serveru Azure AD (přihlášení).

## <a name="next-steps"></a>Další kroky

### <a name="enable-security-features"></a>Povolit funkce zabezpečení

Úplný seznam způsobů, jak zabezpečit databázi, najdete v článku [funkce zabezpečení spravované instance SQL](sql-managed-instance-paas-overview.md#security-features) . Projednávají se následující funkce zabezpečení:

- [Auditování spravované instance SQL](auditing-configure.md)
- [Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detekce hrozeb](threat-detection-configure.md)
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security)
- [Transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Možnosti spravované instance SQL

Úplný přehled funkcí spravované instance SQL najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Možnosti spravované instance SQL](sql-managed-instance-paas-overview.md)