---
title: Zabezpečení databáze
description: V tomto kurzu se seznámíte s postupy a funkcemi pro zabezpečení Azure SQL Database, ať už se jedná o samostatnou databázi nebo ve fondu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 306a8d78caf36291573d021ed1d814aa4f7a2164
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642767"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Kurz: zabezpečení databáze v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> - Vytvoření pravidel brány firewall na úrovni serveru a databáze
> - Konfigurace správce Azure Active Directory (Azure AD)
> - Správa přístupu uživatele s ověřováním SQL, ověřováním Azure AD a zabezpečenými připojovacími řetězci
> - Povolení funkcí zabezpečení, jako je Azure Defender pro SQL, auditování, maskování dat a šifrování

Azure SQL Database zabezpečuje data tím, že vám umožní:

- Omezení přístupu pomocí pravidel brány firewall
- Použití ověřovacích mechanismů, které vyžadují identitu
- Ověřování pomocí členství a oprávnění na základě rolí
- Povolit funkce zabezpečení

> [!NOTE]
> Spravovaná instance Azure SQL je zabezpečená pomocí pravidel zabezpečení sítě a soukromých koncových bodů, jak je popsáno v tématu [spravovaná instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) a [Architektura připojení](../managed-instance/connectivity-architecture-overview.md).

Další informace najdete v článcích [Přehled zabezpečení Azure SQL Database](./security-overview.md) a [Možnosti](security-overview.md) .

> [!TIP]
> Následující Microsoft Learn modul vám pomůže získat zdarma informace o tom, jak [zabezpečit databázi v Azure SQL Database](/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- [Server](logical-servers.md) a jediná databáze
  - Vytvořte je pomocí [Azure Portal](single-database-create-quickstart.md), [CLI](az-cli-script-samples-content-guide.md)nebo [PowerShellu](powershell-script-content-guide.md) .

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Pro všechny kroky v tomto kurzu se přihlaste k [Azure Portal](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Vytvoření pravidel brány firewall

Databáze v SQL Database jsou chráněny branami firewall v Azure. Ve výchozím nastavení se všechna připojení k serveru a databázi odmítnou. Další informace najdete v tématu [pravidla brány firewall na úrovni serveru a databáze](firewall-configure.md).

**Pro nejbezpečnější** konfiguraci nastavte možnost **Povolení přístupu ke službám Azure** . Pak vytvořte [REZERVOVANÉ IP adresy (klasické nasazení)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) pro prostředek, který se potřebuje připojit, jako je třeba virtuální počítač Azure nebo cloudová služba, a povolte přístup k IP adrese jenom přes bránu firewall. Pokud používáte model nasazení [Správce prostředků](../../virtual-network/public-ip-addresses.md) , vyžaduje se pro každý prostředek vyhrazená veřejná IP adresa.

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nemůžete se připojit k serveru, pokud správce neotevře port 1433.

### <a name="set-up-server-level-firewall-rules"></a>Nastavení pravidel brány firewall na úrovni serveru

Pravidla brány firewall protokolu IP na úrovni serveru se vztahují na všechny databáze v rámci stejného serveru.

Nastavení pravidla brány firewall na úrovni serveru:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

    ![pravidlo brány firewall serveru](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Nezapomeňte zkopírovat plně kvalifikovaný název serveru (například *yourserver.Database.Windows.NET*) pro pozdější použití v tomto kurzu.

1. Na stránce **Přehled** vyberte **nastavit bránu firewall serveru**. Otevře se stránka **nastavení brány firewall** pro server.

   1. Na panelu nástrojů vyberte **Přidat IP adresu klienta** a přidejte tak aktuální IP adresu do nového pravidla brány firewall. Pravidlo může otevřít port 1433 pro jednu IP adresu nebo rozsah IP adres. Vyberte **Uložit**.

      ![nastavení pravidla brány firewall serveru](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Vyberte **OK** a zavřete stránku **nastavení brány firewall** .

Nyní se můžete pomocí zadané IP adresy nebo rozsahu IP adres připojit k jakékoli databázi na serveru.

### <a name="setup-database-firewall-rules"></a>Nastavit pravidla brány firewall databáze

Pravidla brány firewall na úrovni databáze se vztahují pouze na jednotlivé databáze. Tato pravidla se při převzetí služeb při selhání serveru uloží do databáze. Pravidla brány firewall na úrovni databáze lze konfigurovat pouze pomocí příkazů jazyka Transact-SQL (T-SQL) a až po konfiguraci pravidla brány firewall na úrovni serveru.

Nastavení pravidla brány firewall na úrovni databáze:

1. Připojte se k databázi, například pomocí [SQL Server Management Studio](connect-query-ssms.md).

1. V **Průzkumník objektů** klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

1. V okně dotazu přidejte tento příkaz a upravte IP adresu na svou veřejnou IP adresu:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na panelu nástrojů vyberte **provést** a vytvořte pravidlo brány firewall.

> [!NOTE]
> V SSMS můžete také vytvořit pravidlo brány firewall na úrovni serveru pomocí příkazu [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current&preserve-view=true) , i když musíte být připojeni k *hlavní* databázi.

## <a name="create-an-azure-ad-admin"></a>Vytvoření správce Azure AD

Ujistěte se, že používáte příslušnou spravovanou doménu Azure Active Directory (AD). Pokud chcete vybrat doménu služby Active Directory, použijte v pravém horním rohu Azure Portal. Tento proces potvrzuje, že se stejné předplatné používá pro Azure AD i pro logický SQL Server, který je hostitelem vaší databáze nebo datového skladu.

   ![zvolit – AD](./media/secure-database-tutorial/8choose-ad.png)

Nastavení správce Azure AD:

1. V Azure Portal na stránce **serveru SQL** vyberte možnost **Správce služby Active Directory**. Potom vyberte **nastavit správce**.

    ![Výběr Active Directory](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > K provedení této úlohy musíte být "globální správce".

1. Na stránce **přidat správce** vyhledejte a vyberte uživatele nebo skupinu AD a zvolte **Vybrat**. Seznam všech členů a skupin služby Active Directory a položky zobrazené šedě nejsou podporovány jako správci služby Azure AD. Přečtěte si o [funkcích a omezeních Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![vybrat správce](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Řízení přístupu na základě role Azure (Azure RBAC) se vztahuje jenom na portál a není šířené na SQL Server.

1. V horní části stránky **Správce služby Active Directory** vyberte **Uložit**.

    Proces změny správce může trvat několik minut. Nový správce se zobrazí v poli **Správce služby Active Directory** .

> [!NOTE]
> Při nastavování správce Azure AD nemůže nové jméno správce (uživatel nebo skupina) existovat jako přihlášení SQL Server nebo uživatel v *Hlavní* databázi. Pokud je k dispozici, instalace se nezdaří a vrátí zpět změny, což značí, že takový název správce již existuje. Vzhledem k tomu, že SQL Server přihlašovací jméno nebo uživatel není součástí služby Azure AD, nebude možné jakékoli úsilí připojit uživatele pomocí ověřování Azure AD.

Informace o konfiguraci služby Azure AD najdete v těchto tématech:

- [Integrace místních identit s Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Přidání vlastního názvu domény do Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure teď podporuje federaci s Windows serverem AD.](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Správa adresáře služby Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)
- [Správa Azure AD pomocí PowerShellu](/powershell/azure/)
- [Porty a protokoly vyžadované hybridní identitou](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Správa přístupu k databázi

Správa přístupu k databázi přidáním uživatelů do databáze nebo povolení přístupu uživatelů pomocí zabezpečených připojovacích řetězců. Připojovací řetězce jsou užitečné pro externí aplikace. Další informace najdete v tématech [Správa přihlašovacích údajů a uživatelských účtů](logins-create-manage.md) a [ověřování AD](authentication-aad-overview.md).

Chcete-li přidat uživatele, vyberte typ ověření databáze:

- **Ověřování SQL**, použijte uživatelské jméno a heslo pro přihlášení a jsou platné jenom v kontextu konkrétní databáze v rámci serveru.

- **Ověřování Azure AD**, používání identit spravovaných službou Azure AD

### <a name="sql-authentication"></a>Ověřování SQL

Přidání uživatele s ověřováním SQL:

1. Připojte se k databázi, například pomocí [SQL Server Management Studio](connect-query-ssms.md).

1. V **Průzkumník objektů** klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

1. V okně dotazu zadejte následující příkaz:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na panelu nástrojů vyberte **provést** a vytvořte uživatele.

1. Ve výchozím nastavení se uživatel může připojit k databázi, ale nemá žádná oprávnění ke čtení nebo zápisu dat. Pokud chcete tato oprávnění udělit, spusťte v novém okně dotazu následující příkazy:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Vytvořte účty bez oprávnění správce na úrovni databáze, pokud není potřeba spouštět úlohy správce, jako je vytváření nových uživatelů.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

Ověřování Azure Active Directory vyžaduje, aby byli uživatelé databáze vytvořeni jako obsažené. Uživatel databáze s omezením se mapuje na identitu v adresáři služby Azure AD přidruženém k databázi a nemá v *Hlavní* databázi žádné přihlašovací údaje. Identita Azure AD může být buď pro jednotlivé uživatele, nebo skupinu. Další informace najdete v tématech [Uživatelé databáze s omezením, zajištění přenosné databáze](/sql/relational-databases/security/contained-database-users-making-your-database-portable) a kontrola [kurzu Azure AD](authentication-aad-configure.md) o ověřování pomocí Azure AD.

> [!NOTE]
> Uživatele databáze (s výjimkou správců) nelze pomocí Azure Portal vytvořit. Role Azure se nešíří do serverů SQL, databází ani datových skladů. Používají se jenom ke správě prostředků Azure a nevztahují se na oprávnění databáze.
>
> Například role *přispěvatel SQL Server* neuděluje přístup pro připojení k databázi nebo datovému skladu. Toto oprávnění je nutné udělit v rámci databáze pomocí příkazů T-SQL.

> [!IMPORTANT]
> Speciální znaky, jako `:` je dvojtečka nebo ampersand, nejsou `&` podporovány v uživatelských jménech v `CREATE LOGIN` příkazech T-SQL a `CREATE USER` .

Postup přidání uživatele s ověřováním Azure AD:

1. Připojte se k serveru v Azure pomocí účtu Azure AD s aspoň *změnou libovolného oprávnění uživatele* .

1. V **Průzkumník objektů** klikněte pravým tlačítkem na databázi a vyberte **Nový dotaz**.

1. V okně dotazu zadejte následující příkaz a změňte `<Azure_AD_principal_name>` hlavní název uživatele Azure AD nebo zobrazovaný název skupiny Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Uživatelé Azure AD jsou označeni v metadatech databáze s typem `E (EXTERNAL_USER)` a typem `X (EXTERNAL_GROUPS)` pro skupiny. Další informace najdete v tématu [Sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Zabezpečené připojovací řetězce

Aby bylo zajištěno zabezpečené šifrované připojení mezi klientskou aplikací a SQL Database, musí být připojovací řetězec nakonfigurován na:

- Vyžádat šifrované připojení
- Certifikát serveru není důvěryhodný.

Připojení se naváže pomocí protokolu TLS (Transport Layer Security) a snižuje riziko útoku prostředníkem. Připojovací řetězce jsou k dispozici pro každou databázi a jsou předem nakonfigurované pro podporu klientských ovladačů, jako jsou ADO.NET, JDBC, ODBC a PHP. Informace o protokolu TLS a možnostech připojení najdete v části věnované [důležitým informacím o protokolu TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Postup při kopírování zabezpečeného připojovacího řetězce:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. Na stránce **Přehled** vyberte **Zobrazit databázové připojovací řetězce**.

1. Vyberte kartu ovladače a zkopírujte úplný připojovací řetězec.

    ![Připojovací řetězec pro ADO.NET](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Povolit funkce zabezpečení

Azure SQL Database poskytuje funkce zabezpečení, které jsou k dispozici pomocí Azure Portal. Tyto funkce jsou dostupné jak pro databázi, tak pro server, s výjimkou maskování dat, která je k dispozici pouze v databázi. Další informace najdete v tématu [Azure Defender pro SQL](azure-defender-for-sql.md), [auditování](../../azure-sql/database/auditing-overview.md), [Maskování dynamických dat](dynamic-data-masking-overview.md)a [transparentní šifrování dat](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender for SQL

Funkce Azure Defender pro SQL detekuje potenciální hrozby při jejich výskytu a poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelé mohou tyto podezřelé události prozkoumat pomocí funkce auditování a určit, zda má událost přístup, porušení nebo zneužití dat v databázi. Uživatelům se taky poskytuje přehled zabezpečení, který obsahuje posouzení ohrožení zabezpečení a nástroj pro zjišťování a zjišťování dat.

> [!NOTE]
> Ukázkovou hrozbou je vkládání SQL, což je proces, ve kterém útočníci vkládají do vstupů do aplikací škodlivý příkaz SQL. Aplikace pak může nevědomě spustit škodlivý systém SQL a umožnit útočníkům přístup k porušení nebo úpravě dat v databázi.

Povolení služby Azure Defender pro SQL:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. Na stránce **Přehled** vyberte odkaz **název serveru** . Otevře se stránka Server.

1. Na stránce **SQL Server** najděte část **zabezpečení** a vyberte **Security Center**.

   1. Pokud chcete tuto funkci povolit, vyberte **v** části **Azure Defender pro SQL** . Vyberte účet úložiště pro ukládání výsledků posouzení ohrožení zabezpečení. Pak vyberte **Uložit**.

      ![Navigační podokno](./media/secure-database-tutorial/threat-settings.png)

      Můžete také nakonfigurovat e-maily pro příjem výstrah zabezpečení, podrobností úložiště a typů detekce hrozeb.

1. Vraťte se na stránku databáze **SQL** databáze a v části **zabezpečení** vyberte **Security Center** . Zde najdete různé indikátory zabezpečení, které jsou pro databázi k dispozici.

    ![Stav hrozby](./media/secure-database-tutorial/threat-status.png)

Pokud se zjistí aktivity neobvyklé, obdržíte e-mail s informacemi o události. To zahrnuje charakter aktivity, databáze, serveru, času události, možných příčin a doporučených akcí pro prošetření a zmírnění potenciální hrozby. Pokud se takový e-mail přijme, vyberte odkaz **protokol auditování Azure SQL** a spusťte Azure Portal a zobrazte relevantní záznamy auditování pro čas události.

   ![E-mail detekce hrozeb](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Auditování

Funkce auditování sleduje události databáze a zapisuje je do protokolu auditu buď v úložišti Azure, Azure Monitor protokolů, nebo do centra událostí. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou indikovat potenciální narušení zabezpečení.

Povolení auditování:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. V části **zabezpečení** vyberte možnost **auditování**.

1. V části nastavení **auditování** nastavte následující hodnoty:

   1. Nastavte **Auditování** na **ZAPNUTO**.

   1. **Cíl protokolu auditu** vyberte jako kterýkoli z následujících:

       - **Úložiště**, účet úložiště Azure, kde se ukládají protokoly událostí a můžou se stahovat jako soubory *. XEL*

          > [!TIP]
          > Pro všechny auditované databáze použijte stejný účet úložiště, abyste získali maximum ze šablon sestav auditování.

       - **Log Analytics**, který automaticky ukládá události pro dotaz nebo další analýzu

           > [!NOTE]
           > K podpoře pokročilých funkcí, jako jsou analýzy, vlastní pravidla upozornění a aplikace Excel nebo Power BI export, se vyžaduje **Log Analytics pracovní prostor** . Bez pracovního prostoru je k dispozici pouze Editor dotazů.

       - **Centrum událostí**, které umožňuje směrování událostí pro použití v jiných aplikacích

   1. Vyberte **Uložit**.

      ![Nastavení auditování](./media/secure-database-tutorial/audit-settings.png)

1. Nyní můžete vybrat **Zobrazit protokoly auditu** a zobrazit data o událostech databáze.

    ![Záznamy auditu](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> Další informace o tom, jak dál přizpůsobit události auditu pomocí PowerShellu nebo REST API, najdete v tématu [SQL Database auditování](../../azure-sql/database/auditing-overview.md) .

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Funkce maskování dat automaticky skryje citlivá data ve vaší databázi.

Povolení maskování dat:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. V části **zabezpečení** vyberte **dynamické maskování dat**.

1. V části nastavení **Maskování dynamických dat** vyberte **Přidat masku** a přidejte pravidlo maskování. Azure automaticky vyplní dostupná schémata databáze, tabulky a sloupce, ze kterých se dají vybírat.

    ![Nastavení masky](./media/secure-database-tutorial/mask-settings.png)

1. Vyberte **Uložit**. Vybrané informace jsou nyní maskovány pro ochranu osobních údajů.

    ![Příklad masky](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparentní šifrování dat

Funkce šifrování automaticky šifruje vaše data v klidovém stavu a nevyžaduje žádné změny v aplikacích, které přistupují k zašifrované databázi. Pro nové databáze je šifrování ve výchozím nastavení zapnuté. Data můžete také šifrovat pomocí SSMS a funkce [Always Encrypted](always-encrypted-certificate-store-configure.md) .

Povolení nebo ověření šifrování:

1. V Azure Portal v nabídce na levé straně vyberte **databáze SQL** a na stránce **databáze SQL** vyberte svou databázi.

1. V části **zabezpečení** vyberte **transparentní šifrování dat**.

1. V případě potřeby nastavte **šifrování dat** na **zapnuto**. Vyberte **Uložit**.

    ![Transparentní šifrování dat](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Pokud chcete zobrazit stav šifrování, připojte se k databázi pomocí [SSMS](connect-query-ssms.md) a Dotazujte `encryption_state` sloupec zobrazení [Sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) . Stav `3` označuje, že databáze je zašifrovaná.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, abyste vylepšili zabezpečení databáze pomocí několika jednoduchých kroků. Naučili jste se:

> [!div class="checklist"]
>
> - Vytvoření pravidel brány firewall na úrovni serveru a databáze
> - Konfigurace správce Azure Active Directory (AD)
> - Správa přístupu uživatele s ověřováním SQL, ověřováním Azure AD a zabezpečenými připojovacími řetězci
> - Povolení funkcí zabezpečení, jako je Azure Defender pro SQL, auditování, maskování dat a šifrování

Přejděte k dalšímu kurzu, kde se dozvíte, jak implementovat geografickou distribuci.

> [!div class="nextstepaction"]
>[Implementace geograficky distribuované databáze](geo-distributed-application-configure-tutorial.md)