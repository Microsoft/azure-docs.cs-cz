---
title: Zabezpečení samostatného nebo databázi ve fondu ve službě Azure SQL Database | Dokumentace Microsoftu
description: Další informace o techniky a funkce pro zabezpečení samostatné nebo databázi ve fondu ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 8a0c474ab610d89bd702613bc778ad29523a4192
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732715"
---
# <a name="tutorial-secure-a-standalone-or-pooled-database"></a>Kurz: Zabezpečení samostatného nebo databázi ve fondu

Azure SQL Database chrání data v samostatné nebo databázi ve fondu, neboť umožňuje:

- Omezení přístupu pomocí pravidel brány firewall
- Použití mechanismů ověřování vyžadujících identitu
- Autorizace pomocí členství na základě role a oprávnění
- Povolení funkcí zabezpečení

> [!NOTE]
> Na spravované instanci Azure SQL database je zabezpečený pomocí pravidla zabezpečení sítě a privátní koncové body, jak je popsáno v [Azure SQL database managed instance](sql-database-managed-instance-index.yml) a [architektura připojení](sql-database-managed-instance-connectivity-architecture.md).

Můžete zvýšit zabezpečení vaší databáze pomocí několika jednoduchých kroků. V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření pravidla brány firewall na úrovni serveru a databáze
> - Konfigurace správce Azure Active Directory (AD)
> - Správa přístupu uživatelů pomocí ověřování SQL, ověřování Azure AD a zabezpečený připojovací řetězce
> - Povolení funkcí zabezpečení, jako je například pokročilé dat zabezpečení, auditování a maskování dat a šifrování

Další informace najdete v tématu [Přehled zabezpečení služby Azure SQL Database](/azure/sql-database/sql-database-security-index) a [možnosti](sql-database-security-overview.md) článků.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu, nezapomeňte, že jsou splněné následující požadavky:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Azure SQL server a databáze služby
  - Vytvoření pomocí [webu Azure portal](sql-database-get-started-portal.md), [rozhraní příkazového řádku](sql-database-cli-samples.md), nebo [prostředí PowerShell](sql-database-powershell-samples.md)

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pro všechny kroky v tomto kurzu, přihlaste se k [webu Azure portal](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Vytvoření pravidel brány firewall

Databáze SQL jsou chráněné bránou firewall v Azure. Ve výchozím nastavení všechna připojení k serveru a databázi odmítají, s výjimkou připojení z ostatních služeb Azure. Další informace najdete v tématu [pravidla brány firewall na úrovni serveru a databáze Azure SQL Database](sql-database-firewall-configure.md).

Nastavte **povolit přístup ke službám Azure** k **OFF** nejbezpečnější konfiguraci. Pak vytvořte [vyhrazené IP (klasické nasazení)](../virtual-network/virtual-networks-reserved-public-ip.md) pro prostředek, který je potřeba připojit, jako je například virtuální počítač Azure nebo cloudovou službu a povolit pouze tuto IP adresu přístup přes bránu firewall. Pokud používáte [resource Manageru](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) model nasazení, vyhrazené veřejné IP adresy je povinné pro každý zdroj.

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete připojit k serveru Azure SQL Database, dokud správce otevře port 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Nastavení pravidel brány firewall databáze SQL serveru

Pravidla brány firewall na úrovni serveru se vztahují ke všem databázím na stejném serveru SQL Database.

Nastavit pravidlo brány firewall na úrovni serveru:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

    ![pravidlo brány firewall serveru](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Nezapomeňte si zkopírovat váš plně kvalifikovaný název serveru (například *yourserver.database.windows.net*) pro použití v pozdější části kurzu.

1. Na **přehled** stránce **nastavit bránu firewall serveru**. **Nastavení brány Firewall** otevře se stránka pro databázový server.

    1. Vyberte **přidat IP adresu klienta** na panelu nástrojů přidat vaši aktuální IP adresu na nové pravidlo brány firewall. Pravidlo může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres. Vyberte **Uložit**.

    ![nastavení pravidla brány firewall serveru](./media/sql-database-security-tutorial/server-firewall-rule2.png)

    1. Vyberte **OK** a zavřít **nastavení brány Firewall** stránky.

Nyní se můžete pomocí zadané IP adresy nebo rozsahu IP adres připojit k jakékoli databázi na serveru.

> [!IMPORTANT]
> Ve výchozím nastavení, přístup přes bránu firewall služby SQL Database je povoleno pro všechny služby Azure, v části **povolit přístup ke službám Azure**. Zvolte **OFF** můžete zakázat přístup pro všechny služby Azure.

### <a name="setup-database-firewall-rules"></a>Nastavení pravidel brány firewall databáze

Pravidla brány firewall na úrovni databáze platí jenom pro jednotlivé databáze. Tato pravidla jsou přenositelné a bude následovat databáze při selhání serveru. Pravidla brány firewall na úrovni databáze lze konfigurovat pouze pomocí příkazů jazyka Transact-SQL (T-SQL) a až poté, co jste nakonfigurovali pravidlo brány firewall na úrovni serveru.

Chcete-li nastavit pravidlo brány firewall na úrovni databáze:

1. Připojení k databázi, například pomocí [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na databázi a vyberte **nový dotaz**.

1. V okně dotazu přidejte tento příkaz a upravit IP adresu na veřejnou IP adresu:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na panelu nástrojů vyberte **Execute** k vytvoření pravidla brány firewall.

> [!NOTE]
> Můžete také vytvořit pravidlo brány firewall na úrovni serveru v aplikaci SSMS s využitím [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) příkazu, ale musíte být připojeni k *hlavní* databáze.

## <a name="create-an-azure-ad-admin"></a>Vytvoření správce Azure AD

Ujistěte se, že používáte příslušné spravované doméně Azure Active Directory (AD). Vyberte doménu AD, použijte pravém horním rohu webu Azure portal. Tento proces potvrdí, stejné předplatné se používá pro obě služby Azure AD a SQL serveru, který hostuje vaši databázi ani na datový sklad Azure SQL.

   ![Zvolte ad](./media/sql-database-security-tutorial/8choose-ad.png)

Nastavení správce Azure AD:

1. Na webu Azure portal na **systému SQL server** stránce **správce Active Directory**. Dále vyberte **nastavit správce**.

    ![Výběr Active Directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Musíte být buď "Správce společnosti" nebo "Globální správce" k provedení této úlohy.

1. Na **přidat správce** stránce, hledání a vyberte skupina nebo uživatel AD a zvolte **vyberte**. Všichni členové a skupiny služby Active Directory jsou uvedené, položek zobrazena šedě, nejsou podporovány jako správce Azure AD. Zobrazit [funkcí Azure AD a omezení](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![Vyberte správce](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Jenom řízení přístupu na základě role (RBAC) se vztahuje na portál a se nerozšíří do systému SQL Server.

1. V horní části **správce Active Directory** stránce **Uložit**.

    Proces změny správce může trvat několik minut. Nový správce se zobrazí v **správce Active Directory** pole.

> [!NOTE]
> Při nastavování správce Azure AD, nový název správce (uživatel nebo skupina) nemůže existovat jako uživatel s ověřováním systému SQL Server v *hlavní* databáze. Pokud jsou k dispozici, instalace se nezdaří a vrátit zpět změny, označující, že takové jméno správce již existuje. Protože uživatel ověřování serveru SQL Server není součástí služby Azure AD, žádné úsilí pro připojení uživatele pomocí ověřování Azure AD se nezdaří.

Informace o konfiguraci Azure AD najdete tady:

- [Integrace místních identit s Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Přidání vlastního názvu domény do Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure teď podporuje federace se službou Windows Server AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Správa adresáře služby Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Správa služby Azure AD pomocí Powershellu](/powershell/azure/overview?view=azureadps-2.0)
- [Porty a protokoly vyžaduje hybridní identita](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Spravovat přístup k databázi

Spravujte přístup k databázi přidání uživatele do databáze nebo povolením přístupu uživatelů pomocí zabezpečený připojovací řetězce. Připojovací řetězce jsou užitečné pro externí aplikace. Další informace najdete v tématu [řízení přístupu Azure SQL](sql-database-control-access.md) a [ověřování AD](sql-database-aad-authentication.md).

Chcete-li přidat uživatele, zvolte typ ověřování databáze:

- **Ověřování SQL**, použijte uživatelské jméno a heslo pro přihlášení a platí jenom v kontextu konkrétní databáze v rámci serveru

- **Ověřování Azure AD**, použití identity spravované v Azure AD

### <a name="sql-authentication"></a>Ověřování pomocí SQL

Pokud chcete přidat uživatele s ověřováním SQL:

1. Připojení k databázi, například pomocí [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na databázi a zvolte **nový dotaz**.

1. V okně dotazu zadejte následující příkaz:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na panelu nástrojů vyberte **Execute** pro vytvoření uživatele.

1. Ve výchozím nastavení se uživatel může připojit k databázi, ale nemá žádná oprávnění ke čtení nebo zápisu dat. Udělení oprávnění v nové okně dotazu spusťte následující příkazy:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Vytvořte účty bez oprávnění správce na úrovni databáze, pokud potřebují spustit Správce úloh, jako je vytváření nových uživatelů.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

Ověřování pomocí Azure Active Directory vyžaduje, že obsažený vytváření uživatelů databáze. Uživatel databáze s omezením se mapuje na identitu v adresáři Azure AD přidružený k databázi a nemá žádné přihlášení v *hlavní* databáze. Identity Azure AD může být buď pro jednotlivé uživatele nebo skupinu. Další informace najdete v tématu [uživatele databáze s omezením, ujistěte se, vaši databázi přenosné](https://msdn.microsoft.com/library/ff929188.aspx) a projděte si [kurz služby Azure AD](./sql-database-aad-authentication-configure.md) o tom, jak ověřovat pomocí služby Azure AD.

> [!NOTE]
> Nelze vytvořit uživatele databáze (s výjimkou správci) pomocí webu Azure portal. Role Azure RBAC nejsou rozšířena do SQL servery, databáze nebo datového skladu. Tyto slouží pouze ke správě prostředků Azure a se nevztahují na oprávnění databáze.
>
> Například *Přispěvatel SQL serveru* role neudělují přístupu pro připojení k databázi ani na datový sklad. Toto oprávnění musí být poskytovány v databázi pomocí příkazů T-SQL.

> [!IMPORTANT]
> Speciální znaky, jako je dvojtečka `:` nebo ampersand `&` nejsou podporovány v uživatelská jména v T-SQL `CREATE LOGIN` a `CREATE USER` příkazy.

Pokud chcete přidat uživatele s ověřováním Azure AD:

1. Připojte se k serveru Azure SQL pomocí účtu služby Azure AD s alespoň *ALTER ANY USER* oprávnění.

1. V **Průzkumník objektů**, klikněte pravým tlačítkem na databázi a vyberte **nový dotaz**.

1. V okně dotazu zadejte následující příkaz a upravit `<Azure_AD_principal_name>` hlavní název uživatele Azure AD nebo zobrazovaného názvu skupiny Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Uživatelé Azure AD jsou označeny jako metadata databáze s typem `E (EXTERNAL_USER)` a typ `X (EXTERNAL_GROUPS)` pro skupiny. Další informace najdete v tématu [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Zabezpečený připojovací řetězce

K zajištění zabezpečené a šifrované připojení mezi klientskou aplikaci a SQL database, připojovací řetězec musí být nakonfigurován pro:

- Vyžadoval šifrované připojení
- Není důvěřovat certifikátu serveru

Připojení se naváže s využitím zabezpečení TLS (Transport Layer) a snižuje riziko útoku man-in-the-middle. Připojovací řetězce jsou k dispozici na databázi a jsou předem nakonfigurované pro podporu ovladače klienta, jako je například technologie ADO.NET, JDBC, ODBC a PHP. Informace o protokolu TLS a možnostech připojení najdete v části věnované [důležitým informacím o protokolu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Zkopírování zabezpečeného připojovacího řetězce:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. Na **přehled** stránce **zobrazit databázové připojovací řetězce**.

1. Vyberte kartu ovladač a zkopírujte úplný připojovací řetězec.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Azure SQL Database poskytuje funkce zabezpečení, které jsou přístupné pomocí webu Azure portal. Tyto funkce jsou k dispozici pro databáze a serveru, s výjimkou maskování dat, která je k dispozici pouze v databázi. Další informace najdete v tématu [rozšířené zabezpečení dat](sql-database-advanced-data-security.md), [auditování](sql-database-auditing.md), [dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md), a [transparentní šifrování dat](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

Funkce zabezpečení pokročilé dat zjištění potenciálních hrozeb, dojde k a poskytuje výstrahy zabezpečení na neobvyklé aktivity. Uživatelé můžou prozkoumat tyto podezřelé události pomocí funkce auditování a zjistit, zda událost pro přístup, narušení nebo zneužití dat v databázi. Uživatelé jsou taky k dispozici přehled zabezpečení, která zahrnuje posouzení ohrožení zabezpečení a nástroj pro zjišťování a klasifikace dat.

> [!NOTE]
> Před internetovými útoky příkladu je útok prostřednictvím injektáže SQL, proces, kde útočníci injektovat škodlivý SQL do vstupů aplikace. Aplikace můžete neúmyslně spuštění škodlivého SQL a útočníkům umožnit přístup k porušení zabezpečení nebo úpravy dat v databázi.

Pokud chcete povolit rozšířené data zabezpečení:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. Na **přehled** stránky, vyberte **název serveru** odkaz. Otevře se stránka serveru databáze.

1. Na **systému SQL server** stránky, vyhledejte **zabezpečení** a vyberte **rozšířené zabezpečení dat**.

    1. Vyberte **ON** pod **rozšířené zabezpečení dat** k povolení této funkce. Zvolte účet úložiště pro ukládání výsledků posouzení ohrožení zabezpečení. Potom vyberte **Uložit**.

    ![Navigační podokno](./media/sql-database-security-tutorial/threat-settings.png)

    Můžete také nakonfigurovat e-mailů dostávat výstrahy zabezpečení a podrobnosti o úložišti a typy detekce hrozeb.

1. Vraťte se na **databází SQL** stránky databáze a vyberte **rozšířené zabezpečení dat** pod **zabezpečení** oddílu. Tady najdete různé ukazatele zabezpečení dostupné pro databázi.

    ![Stav hrozby](./media/sql-database-security-tutorial/threat-status.png)

Pokud se zjistí neobvyklé aktivity, obdržíte e-mail s informacemi o události. To zahrnuje povaze aktivity, databázi, server, čas události, možné příčiny a doporučené akce k prošetření a zmírnění potenciálního ohrožení. Pokud takový e-mail, vyberte **protokol auditování SQL Azure** odkaz ke spuštění na webu Azure portal a zobrazí relevantní záznamy auditování pro čas události.

   ![E-mail detekce hrozeb](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditování

Tato funkce sleduje události databáze a zapisuje události do protokolu auditu v úložišti buď Azure log analytics nebo centra událostí. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit potenciální narušení zabezpečení.

Pokud chcete povolit auditování:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. V **zabezpečení** vyberte **auditování**.

1. V části **auditování** nastavení, nastavte následující hodnoty:

    1. Nastavte **auditování** k **ON**.

    1. Vyberte **cíl protokolu auditu** jako některý z následujících akcí:

        - **Úložiště**, účet úložiště Azure, kde se ukládají protokoly událostí a lze stáhnout jako *xel* soubory

           > [!TIP]
           > Chcete-li získat maximum z šablon sestav auditování pomocí stejného účtu úložiště pro všechny auditované databáze.

        - **Log Analytics**, které automaticky uloží události pro dotaz nebo další analýzu

            > [!NOTE]
            > A **pracovní prostor Log analytics** je nezbytný pro podporu pokročilých funkcí, jako je například analýzy, vlastních pravidel výstrah a export Excelu nebo Power BI. Bez pracovní prostor je k dispozici pouze v editoru dotazů.

        - **Centrum událostí**, což umožňuje událostí bude směrovat pro použití v jiných aplikacích

    1. Vyberte **Uložit**.

    ![Nastavení auditu](./media/sql-database-security-tutorial/audit-settings.png)

1. Teď můžete vybrat **zobrazit protokoly auditu** chcete zobrazit údaje o událostech databáze.

    ![Záznamy auditu](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Zobrazit [auditování služby SQL database](sql-database-auditing.md) o tom, jak dále přizpůsobit událostí auditu pomocí Powershellu nebo rozhraní REST API.

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Funkci maskování dat se automaticky skrýt citlivá data v databázi.

Pokud chcete povolit maskování dat:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. V **zabezpečení** vyberte **maskování dynamických dat**.

1. V části **dynamické maskování dat** nastavení, vyberte **přidat masku** přidáte pravidlo maskování. Azure se automaticky vyplní dostupné databázová schémata, tabulky a sloupce lze vybírat.

    ![Nastavení masky](./media/sql-database-security-tutorial/mask-settings.png)

1. Vyberte **Uložit**. Vybrané informace nyní maskovaná pro ochranu osobních údajů.

    ![Například maska](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

Funkce šifrování automaticky šifruje vaše data v klidovém stavu a nevyžaduje žádné změny k aplikacím přístup k šifrované databázi. Pro nové databáze je ve výchozím nastavení šifrování. Můžete taky k šifrování dat pomocí aplikace SSMS a [s funkcí Always encrypted](sql-database-always-encrypted.md) funkce.

K povolení nebo ověřit šifrování:

1. Na webu Azure portal, vyberte **databází SQL** z nabídky na levé straně a vyberte svou databázi na **databází SQL** stránky.

1. V **zabezpečení** vyberte **transparentní šifrování dat**.

1. V případě potřeby nastavte **šifrování dat** k **ON**. Vyberte **Uložit**.

    ![Transparentní šifrování dat](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Chcete-li zobrazit stav šifrování, připojit k databázi pomocí [SSMS](./sql-database-connect-query-ssms.md) a dotazování `encryption_state` sloupec [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017) zobrazení. Stav `3` označuje je databáze šifrovaná.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak zlepšit zabezpečení vaší databáze pomocí několika jednoduchých krocích. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření pravidla brány firewall na úrovni serveru a databáze
> - Konfigurace správce Azure Active Directory (AD)
> - Správa přístupu uživatelů pomocí ověřování SQL, ověřování Azure AD a zabezpečený připojovací řetězce
> - Povolení funkcí zabezpečení, jako je například pokročilé dat zabezpečení, auditování a maskování dat a šifrování

Přejděte k dalšímu kurzu, kde se naučíte, jak implementovat geografická distribuce.

> [!div class="nextstepaction"]
>[Implementace geograficky distribuované databáze](sql-database-implement-geo-distributed-database.md)
