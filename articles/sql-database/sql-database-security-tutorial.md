---
title: Zabezpečení jedné nebo sdružené databáze
description: Kurz, který vás naučí o technikách a funkcích pro zabezpečení jedné nebo sdružené databáze v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9e3c5c12157a007bcad59a78b4623ff4d5a0041f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238829"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Kurz: Zabezpečení jedné nebo sdružené databáze

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> - Vytvoření pravidel brány firewall na úrovni serveru a databáze
> - Konfigurace správce služby Azure Active Directory (AD)
> - Správa přístupu uživatelů pomocí ověřování SQL, ověřování Azure AD a zabezpečených připojovacích řetězců
> - Povolení funkcí zabezpečení, jako je pokročilé zabezpečení dat, auditování, maskování dat a šifrování

Azure SQL Database zabezpečuje data v jedné nebo sdružené databázi tím, že vám umožňuje:

- Omezení přístupu pomocí pravidel brány firewall
- Použití ověřovacích mechanismů, které vyžadují identitu
- Použití autorizace s členstvími a oprávněními založenými na rolích
- Povolení funkcí zabezpečení

> [!NOTE]
> Databáze Azure SQL na spravované instanci je zabezpečená pomocí pravidel zabezpečení sítě a privátních koncových bodů, jak je popsáno v [instanci spravované databází Azure SQL](sql-database-managed-instance-index.yml) a [architektuře připojení](sql-database-managed-instance-connectivity-architecture.md).

Další informace najdete v článcích [přehled zabezpečení azure sql database](/azure/sql-database/sql-database-security-index) a [možnosti.](sql-database-security-overview.md)

> [!TIP]
> Následující modul Microsoft Learn vám pomůže zdarma se dozvědět, jak [zabezpečit azure sql database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit kurz, ujistěte se, že máte následující předpoklady:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Server A databáze Azure SQL
  - Vytvoření pomocí [portálu Azure](sql-database-single-database-get-started.md), [cli](sql-database-cli-samples.md)nebo [PowerShellu](sql-database-powershell-samples.md)

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pro všechny kroky v kurzu se přihlaste na [portál Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Vytvoření pravidel brány firewall

Databáze SQL jsou chráněné bránou firewall v Azure. Ve výchozím nastavení jsou všechna připojení k serveru a databázi odmítnuta. Další informace najdete v [tématu Pravidla brány firewall na úrovni serveru Azure SQL Database a na úrovni databáze](sql-database-firewall-configure.md).

Nastavte **Povolit přístup ke službám Azure** na **OFF** pro nejbezpečnější konfiguraci. Potom vytvořte [vyhrazenou IP adresu (klasické nasazení)](../virtual-network/virtual-networks-reserved-public-ip.md) pro prostředek, který potřebuje připojení, jako je například virtuální počítač Azure nebo cloudová služba, a povolte přístup k této IP adrese pouze přes bránu firewall. Pokud používáte model nasazení [správce prostředků,](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) je pro každý prostředek vyžadována vyhrazená veřejná IP adresa.

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 1433 nemusí být povoleno bránou firewall vaší sítě. Pokud ano, nemůžete se připojit k serveru Azure SQL Database, pokud správce neotevře port 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Nastavení pravidel brány firewall serveru SQL Database

Pravidla brány firewall IP na úrovni serveru platí pro všechny databáze v rámci stejného serveru SQL Database.

Nastavení pravidla brány firewall na úrovni serveru:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

    ![pravidlo brány firewall serveru](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Nezapomeňte zkopírovat plně kvalifikovaný název serveru (například *yourserver.database.windows.net)* pro pozdější použití v kurzu.

1. Na stránce **Přehled** vyberte **Nastavit serverovou bránu firewall**. Otevře se stránka **Nastavení brány firewall** pro databázový server.

   1. **Chcete-li** přidat aktuální adresu IP do nového pravidla brány firewall, vyberte na panelu nástrojů přidat adresu IP klienta. Pravidlo může otevřít port 1433 pro jednu ADRESU IP nebo rozsah ADRES IP. Vyberte **Uložit**.

      ![nastavení pravidla brány firewall serveru](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Vyberte **OK** a zavřete stránku **Nastavení brány firewall.**

Nyní se můžete pomocí zadané IP adresy nebo rozsahu IP adres připojit k jakékoli databázi na serveru.

### <a name="setup-database-firewall-rules"></a>Nastavení pravidel brány firewall databáze

Pravidla brány firewall na úrovni databáze platí pouze pro jednotlivé databáze. Databáze zachová tato pravidla během převzetí služeb při selhání serveru. Pravidla brány firewall na úrovni databáze lze konfigurovat pouze pomocí příkazů Transact-SQL (T-SQL) a pouze po konfiguraci pravidla brány firewall na úrovni serveru.

Nastavení pravidla brány firewall na úrovni databáze:

1. Připojte se k databázi, například pomocí [sql server management studio](./sql-database-connect-query-ssms.md).

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na databázi a vyberte **nový dotaz**.

1. V okně dotazu přidejte tento příkaz a upravte IP adresu na veřejnou IP adresu:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na panelu nástrojů vyberte **Spustit** a vytvořte pravidlo brány firewall.

> [!NOTE]
> Můžete také vytvořit pravidlo brány firewall na úrovni serveru v SSMS pomocí příkazu [sp_set_firewall_rule,](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) i když musíte být připojeni k *hlavní* databázi.

## <a name="create-an-azure-ad-admin"></a>Vytvoření správce Azure AD

Ujistěte se, že používáte příslušnou spravovanou doménu služby Azure Active Directory (AD). Pokud chcete vybrat doménu Služby AD, použijte pravý horní roh portálu Azure. Tento proces potvrzuje, že stejné předplatné se používá pro Azure AD a SQL Server hostující databázi Azure SQL nebo datový sklad.

   ![vybrat-ad](./media/sql-database-security-tutorial/8choose-ad.png)

Nastavení správce Azure AD:

1. Na webu Azure Portal na stránce **serveru SQL** vyberte **správce služby Active Directory**. Dále vyberte **Nastavit správce**.

    ![Výběr Active Directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > K provedení tohoto úkolu musíte být správcem společnosti nebo globálním správcem.

1. Na stránce **Přidat správce** vyhledejte a vyberte uživatele nebo skupinu služby AD a zvolte **Vybrat**. Jsou uvedeni všichni členové a skupiny služby Active Directory a položky zašedlé nejsou podporovány jako správci služby Azure AD. Viz [Funkce a omezení Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![vybrat správce](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Řízení přístupu na základě rolí (RBAC) platí pouze pro portál a není rozšířena na SQL Server.

1. V horní části stránky **správce služby Active Directory** vyberte **Uložit**.

    Proces změny správce může trvat několik minut. Nový správce se zobrazí v poli **správce služby Active Directory.**

> [!NOTE]
> Při nastavování správce Azure AD, nový název správce (uživatel nebo skupina) nemůže existovat jako uživatel ověřování SQL Server v *hlavní* databázi. Pokud je k dispozici, instalace se nezdaří a vrátit zpět změny, označující, že takový název správce již existuje. Vzhledem k tomu, že uživatel ověřování SQL Server není součástí Azure AD, žádné úsilí o připojení uživatele pomocí ověřování Azure AD se nezdaří.

Informace o konfiguraci Azure AD najdete v tématu:

- [Integrace místních identit s Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Přidání vlastního názvu domény do Služby Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure teď podporuje federaci se službou Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Správa adresáře služby Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Správa Azure AD pomocí PowerShellu](/powershell/azure/overview?view=azureadps-2.0)
- [Porty a protokoly požadované hybridní identitou](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Správa přístupu k databázi

Spravujte přístup k databázi přidáním uživatelů do databáze nebo povolením přístupu uživatelů pomocí zabezpečených připojovacích řetězců. Připojovací řetězce jsou užitečné pro externí aplikace. Další informace najdete v [tématu Správa přihlášení a uživatelských účtů](sql-database-manage-logins.md) a [ověřování ve službě AD](sql-database-aad-authentication.md).

Chcete-li přidat uživatele, zvolte typ ověřování databáze:

- **Ověřování SQL**, použití uživatelského jména a hesla pro přihlášení a jsou platné pouze v kontextu konkrétní databáze v rámci serveru

- **Ověřování Azure AD**, použití identit spravovaných službou Azure AD

### <a name="sql-authentication"></a>Ověřování pomocí SQL

Přidání uživatele s ověřováním SQL:

1. Připojte se k databázi, například pomocí [sql server management studio](./sql-database-connect-query-ssms.md).

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na databázi a zvolte **Nový dotaz**.

1. V okně dotazu zadejte následující příkaz:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na panelu nástrojů vyberte **Spustit** a vytvořte uživatele.

1. Ve výchozím nastavení se uživatel může připojit k databázi, ale nemá žádná oprávnění ke čtení nebo zápisu dat. Chcete-li tato oprávnění udělit, proveďte v novém okně dotazu následující příkazy:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Vytvořte účty bez oprávnění správce na úrovni databáze, pokud nepotřebují provádět úlohy správce, jako je vytváření nových uživatelů.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

Ověřování azure active directory vyžaduje, aby uživatelé databáze jsou vytvořeny jako obsažené. Obsažený uživatel databáze mapuje na identitu v adresáři Azure AD přidružené k databázi a nemá žádné přihlášení v *hlavní* databázi. Identita Azure AD může být pro jednotlivého uživatele nebo skupiny. Další informace najdete [v tématu obsažené databáze uživatelů, aby databáze přenosné](https://msdn.microsoft.com/library/ff929188.aspx) a zkontrolujte kurz Azure [AD](./sql-database-aad-authentication-configure.md) o tom, jak ověřit pomocí Azure AD.

> [!NOTE]
> Uživatele databáze (s výjimkou správců) nelze vytvořit pomocí portálu Azure. Azure RBAC role se nerozšíří na servery SQL, databáze nebo datové sklady. Používají se jenom ke správě prostředků Azure a nevztahují se na oprávnění databáze.
>
> Například role *přispěvatele serveru SQL Server* neuděluje přístup k připojení k databázi nebo datovému skladu. Toto oprávnění musí být uděleno v rámci databáze pomocí příkazů T-SQL.

> [!IMPORTANT]
> Speciální znaky, `:` jako dvojtečka nebo ampersand `&` nejsou `CREATE LOGIN` podporovány v uživatelských jménech v T-SQL a `CREATE USER` příkazy.

Přidání uživatele pomocí ověřování Azure AD:

1. Připojte se k serveru Azure SQL pomocí účtu Azure AD s alespoň *oprávněním ALTER ANY USER.*

1. V **Průzkumníkovi objektů**klepněte pravým tlačítkem myši na databázi a vyberte **nový dotaz**.

1. V okně dotazu zadejte následující `<Azure_AD_principal_name>` příkaz a upravte hlavní název uživatele Služby Azure AD nebo zobrazovaný název skupiny Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Uživatelé Azure AD jsou označeny `E (EXTERNAL_USER)` v `X (EXTERNAL_GROUPS)` metadatech databáze s typem a typem pro skupiny. Další informace naleznete [v tématu sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Zabezpečené připojovací řetězce

Chcete-li zajistit zabezpečené, šifrované připojení mezi klientskou aplikací a databází SQL, musí být připojovací řetězec nakonfigurován takto:

- Žádost o šifrované připojení
- Nedůvěřovat certifikátu serveru

Připojení je navázáno pomocí zabezpečení transportní vrstvy (TLS) a snižuje riziko útoku prostředníkem. Připojovací řetězce jsou k dispozici pro databázi a jsou předem nakonfigurovány pro podporu klientských ovladačů, jako jsou ADO.NET, JDBC, ODBC a PHP. Informace o protokolu TLS a možnostech připojení najdete v části věnované [důležitým informacím o protokolu TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Kopírování zabezpečeného připojovacího řetězce:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. Na stránce **Přehled** vyberte **Zobrazit připojovací řetězce databáze**.

1. Vyberte kartu ovladače a zkopírujte kompletní připojovací řetězec.

    ![Připojovací řetězec pro ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Povolení funkcí zabezpečení

Azure SQL Database poskytuje funkce zabezpečení, ke kterým se přistupuje pomocí portálu Azure. Tyto funkce jsou k dispozici pro databázi i server, s výjimkou maskování dat, které je k dispozici pouze v databázi. Další informace naleznete [v tématech Rozšířené zabezpečení dat](sql-database-advanced-data-security.md), [Auditování](sql-database-auditing.md), [Dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md)a Transparentní šifrování [dat](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Pokročilé zabezpečení dat

Pokročilá funkce zabezpečení dat detekuje potenciální hrozby, jakmile k nim dojde, a poskytuje výstrahy zabezpečení na neobvyklé aktivity. Uživatelé mohou tyto podezřelé události prozkoumat pomocí funkce auditování a určit, zda se událost měla dostat k datům v databázi, narušit jejich zabezpečení nebo je zneužít. Uživatelům je také k dispozici přehled zabezpečení, který zahrnuje posouzení ohrožení zabezpečení a nástroj pro zjišťování a klasifikaci dat.

> [!NOTE]
> Příkladem hrozby je injektáž SQL, proces, při kterém útočníci vkládají škodlivý SQL do vstupů aplikací. Aplikace pak může nevědomky spustit škodlivý SQL a umožnit útočníkům přístup k porušení nebo úpravám dat v databázi.

Povolení rozšířeného zabezpečení dat:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. Na stránce **Přehled** vyberte odkaz **Název serveru.** Otevře se stránka databázového serveru.

1. Na stránce **serveru SQL** vyhledejte část **Zabezpečení** a vyberte rozšířené **zabezpečení dat**.

   1. Chcete-li tuto funkci povolit, vyberte **možnost ZAPNUTO** v části **Rozšířené zabezpečení dat.** Zvolte účet úložiště pro ukládání výsledků hodnocení ohrožení zabezpečení. Potom vyberte **Uložit**.

      ![Navigační podokno](./media/sql-database-security-tutorial/threat-settings.png)

      Můžete také nakonfigurovat e-maily tak, aby přijímabyly výstrahy zabezpečení, podrobnosti o úložišti a typy detekce hrozeb.

1. Vraťte se na stránku **databáze SQL** a v části **Zabezpečení** vyberte Rozšířené **zabezpečení dat.** Zde najdete různé bezpečnostní indikátory, které jsou k dispozici pro databázi.

    ![Stav ohrožení](./media/sql-database-security-tutorial/threat-status.png)

Pokud jsou zjištěny neobvyklé aktivity, obdržíte e-mail s informacemi o události. To zahrnuje povahu aktivity, databáze, serveru, času události, možných příčin a doporučených akcí k prošetření a zmírnění potenciální hrozby. Pokud takový e-mail obdržíte, vyberte odkaz **protokol auditování Azure SQL** a spusťte portál Azure portal a zobrazte příslušné záznamy auditování pro čas události.

   ![E-mail detekce hrozeb](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditování

Funkce auditování sleduje události databáze a zapisuje události do protokolu auditu v úložišti Azure, protokolech Azure Monitoru nebo do centra událostí. Auditování pomáhá udržovat dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které by mohly naznačovat potenciální narušení zabezpečení.

Povolení auditování:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. V části **Zabezpečení** vyberte **Auditování**.

1. V části **Nastavení auditování** nastavte následující hodnoty:

   1. Nastavte **auditování** na **ZAPNUTO**.

   1. Vyberte **cíl protokolu auditování** jako některou z následujících možností:

       - **Storage**, účet úložiště Azure, kde se ukládají protokoly událostí a lze je stáhnout jako *soubory XEL*

          > [!TIP]
          > Použijte stejný účet úložiště pro všechny auditované databáze, abyste získali co nejvíce z auditování šablon sestav.

       - **Log Analytics**, která automaticky ukládá události pro dotaz nebo další analýzu

           > [!NOTE]
           > Pracovní **prostor Log Analytics** je vyžadován pro podporu pokročilých funkcí, jako jsou analýzy, vlastní pravidla výstrah a exporty Excelu nebo Power BI. Bez pracovního prostoru je k dispozici pouze editor dotazů.

       - **Centrum událostí**, které umožňuje směrovat události pro použití v jiných aplikacích

   1. Vyberte **Uložit**.

      ![Nastavení auditování](./media/sql-database-security-tutorial/audit-settings.png)

1. Nyní můžete vybrat **zobrazit protokoly auditu** a zobrazit data událostí databáze.

    ![Záznamy auditu](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Podívejte se na [auditování databáze SQL o](sql-database-auditing.md) tom, jak dále přizpůsobit události auditu pomocí powershellu nebo rozhraní REST API.

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Funkce maskování dat automaticky skryje citlivá data v databázi.

Povolení maskování dat:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. V části **Zabezpečení** vyberte **dynamické maskování dat**.

1. V části **Nastavení maskování dynamických dat** vyberte **Přidat masku,** chcete-li přidat pravidlo maskování. Azure automaticky naplní dostupná schémata databáze, tabulky a sloupce, ze kterých si můžete vybrat.

    ![Nastavení masky](./media/sql-database-security-tutorial/mask-settings.png)

1. Vyberte **Uložit**. Vybrané informace jsou nyní maskovány pro ochranu osobních údajů.

    ![Příklad masky](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

Funkce šifrování automaticky šifruje data v klidovém stavu a nevyžaduje žádné změny v aplikacích přistupujících k šifrované databázi. U nových databází je šifrování ve výchozím nastavení zapnuto. Můžete také šifrovat data pomocí SSMS a [vždy šifrované](sql-database-always-encrypted.md) funkce.

Povolení nebo ověření šifrování:

1. Na webu Azure Portal vyberte **databáze SQL** z nabídky na levé straně a vyberte databázi na stránce **databáze SQL.**

1. V části **Zabezpečení** vyberte **Transparentní šifrování dat**.

1. V případě potřeby nastavte **šifrování dat** na **ZAPNUTO**. Vyberte **Uložit**.

    ![Transparentní šifrování dat](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Chcete-li zobrazit stav šifrování, připojte se `encryption_state` k databázi pomocí [ssms](./sql-database-connect-query-ssms.md) a dotaz sloupec zobrazení [sys.dm_database_encryption_keys.](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) Stav `3` označuje, že databáze je šifrována.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili zlepšit zabezpečení databáze pomocí několika jednoduchých kroků. Naučili jste se tyto postupy:

> [!div class="checklist"]
> - Vytvoření pravidel brány firewall na úrovni serveru a databáze
> - Konfigurace správce služby Azure Active Directory (AD)
> - Správa přístupu uživatelů pomocí ověřování SQL, ověřování Azure AD a zabezpečených připojovacích řetězců
> - Povolení funkcí zabezpečení, jako je pokročilé zabezpečení dat, auditování, maskování dat a šifrování

Přejdete k dalšímu kurzu, kde se dozvíte, jak implementovat geografickou distribuci.

> [!div class="nextstepaction"]
>[Implementace geograficky distribuované databáze](sql-database-implement-geo-distributed-database.md)
