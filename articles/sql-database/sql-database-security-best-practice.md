---
title: Příručka pro řešení běžných požadavků na zabezpečení | Dokumenty společnosti Microsoft
titleSuffix: Azure SQL Database
description: Tento článek obsahuje běžné požadavky na zabezpečení a osvědčené postupy v Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 39747ac0a7133562bed526f44e30bf4a656127c0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673613"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Playbook pro řešení běžných požadavků na zabezpečení pomocí Azure SQL Database

> [!NOTE]
> Tento dokument obsahuje osvědčené postupy, jak vyřešit běžné požadavky na zabezpečení. Ne všechny požadavky platí pro všechna prostředí a měli byste se podívat do databáze a týmu zabezpečení, na kterých funkcích chcete implementovat.

## <a name="solving-common-security-requirements"></a>Řešení běžných požadavků na zabezpečení

Tento dokument obsahuje pokyny, jak vyřešit běžné požadavky na zabezpečení pro nové nebo existující aplikace pomocí Azure SQL Database. Je to organizováno vysoce postavenými bezpečnostními oblastmi. Řešení konkrétních hrozeb naleznete v části [Společné bezpečnostní hrozby a potenciální skutečnosti snižující závažnost rizika.](#common-security-threats-and-potential-mitigations) Přestože některá z předložených doporučení jsou použitelné při migraci aplikací z místního do Azure, scénáře migrace nejsou zaměření tohoto dokumentu.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Nabídky nasazení Azure SQL Database, které jsou v této příručce zahrnuty

- [DATABÁZE SQL:](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) [jednotlivé databáze](sql-database-single-database.md) a [elastické fondy](sql-database-elastic-pool.md) na [serverech Azure SQL Database](sql-database-servers.md)
- [Spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Nabídky nasazení SQL nejsou zahrnuty v této příručce

- Azure SQL Data Warehouse
- Virtuální počítače Azure SQL (IaaS)
- SQL Server v místním prostředí

### <a name="audience"></a>Cílová skupina

Zamýšlené cílové skupiny pro tuto příručku jsou zákazníci čelí otázky o tom, jak zabezpečit Azure SQL Database. Role, které se zajímají o tento článek o osvědčených postupech, zahrnují mimo jiné:

- Architekti zabezpečení
- Správci zabezpečení
- Kontroloři shody
- Úředníci pro ochranu osobních údajů
- Bezpečnostní inženýři

### <a name="using-this-guide"></a><a id="using"></a>Použití této příručky

Tento dokument je určen jako doprovod k naší existující dokumentaci [zabezpečení Azure SQL Database.](sql-database-security-overview.md)

Pokud není uvedeno jinak, doporučujeme dodržovat všechny osvědčené postupy uvedené v každé části k dosažení příslušného cíle nebo požadavku. Aby byly splněny konkrétní standardy zabezpečení nebo osvědčené postupy, jsou důležité kontroly dodržování předpisů uvedeny v části Požadavky nebo cíle, kdykoli je to možné. Jedná se o bezpečnostní normy a předpisy, které jsou uvedeny v tomto dokumentu:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Řízení přístupu, kryptografie
- [Postupy microsoft operational security assurance (OSA):](https://www.microsoft.com/en-us/securityengineering/osa/practices)Praxe #1-6 a #9
- [Zvláštní publikace NIST 800-53 Bezpečnostní kontroly](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Váš názor

Plánujeme pokračovat v aktualizaci doporučení a osvědčených postupů uvedených zde. Zadejte vstup nebo jakékoli opravy pro tento dokument pomocí odkazu **Zpětná vazba** v dolní části tohoto článku.

## <a name="authentication"></a>Authentication

Ověřování je proces dokazování, že uživatel je tím, za koho se považuje. Azure SQL Database podporuje dva typy ověřování:

- Ověřování pomocí SQL
- Ověřování pomocí Azure Active Directory

> [!NOTE]
> Ověřování azure active directory nemusí být podporováno pro všechny nástroje a aplikace třetích stran.

### <a name="central-management-for-identities"></a>Centrální správa identit

Centrální správa identit nabízí následující výhody:

- Spravujte skupinové účty a spravujte uživatelská oprávnění bez duplikování přihlášení na serverech a databázích Azure SQL Database.
- Zjednodušená a flexibilní správa oprávnění.
- Správa aplikací ve velkém měřítku.

**Jak implementovat**:

- Pro centralizovanou správu identit použijte ověřování azure active directory (Azure AD).

**Osvědčené postupy**:

- Vytvořte klienta Azure AD a [vytvořte uživatele, kteří](../active-directory/fundamentals/add-users-azure-active-directory.md) budou zastupovat lidské uživatele a vytvářet [instanční objekty](../active-directory/develop/app-objects-and-service-principals.md) představující aplikace, služby a nástroje pro automatizaci. Instanční objekty jsou ekvivalentní účtům služeb v systému Windows a Linuxu. 

- Přiřaďte přístupová práva k prostředkům k objektům zabezpečení Azure AD prostřednictvím přiřazení skupin: Vytvořte skupiny Azure AD, udělte přístup ke skupinám a přidejte do skupin jednotlivé členy. V databázi vytvořte obsažené uživatele databáze, kteří mapují vaše skupiny Azure AD. Chcete-li přiřadit oprávnění uvnitř databáze, vložte uživatele do databázových rolí s příslušnými oprávněními.
  - Podívejte se na [články, Konfigurace a správa ověřování Azure Active Directory pomocí SQL](sql-database-aad-authentication-configure.md) a použití Azure [AD pro ověřování pomocí SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > Ve spravované instanci můžete také vytvořit přihlášení, které mapují na objekty Azure AD v hlavní databázi. Viz [CREATE LOGIN (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)

- Použití skupin Azure AD zjednodušuje správu oprávnění a vlastníka skupiny a vlastník prostředků můžete přidat nebo odebrat členy do nebo ze skupiny. 

- Vytvořte samostatnou skupinu pro správce Azure AD pro servery SQL DB.

  - V článku [Zřízení správce služby Azure Active Directory pro váš databázový server Azure SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Sledujte změny členství ve skupinách Azure AD pomocí sestav auditování Azure AD. 

- Pro spravovanou instanci je potřeba samostatný krok k vytvoření správce Azure AD. 
  - V článku [Zřízení správce služby Azure Active Directory pro vaši spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Ověřování Azure AD se zaznamená v protokolech auditu Azure SQL, ale ne v protokolech přihlášení Azure AD.
> - Oprávnění RBAC udělená v Azure se nevztahují na oprávnění Azure SQL DB. Tato oprávnění musí být vytvořena nebo mapována ručně v databázi SQL pomocí existujících oprávnění SQL.
> - Na straně klienta Azure AD ověřování potřebuje přístup k internetu nebo prostřednictvím uživatelem definované trasy (UDR) do virtuální sítě.
> - Přístupový token Azure AD je uložen v mezipaměti na straně klienta a jeho životnost závisí na konfiguraci tokenu. Podívejte se na článek [Konfigurovatelné životnosti tokenů ve službě Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Pokyny k řešení problémů s ověřováním azure ad najdete v následujícím blogu:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> Uvedeno v: OSA Practice #2, ŘÍZENÍ PŘÍSTUPU ISO (AC)

Azure Multi-Factor Authentication (MFA) pomáhá poskytuje další zabezpečení tím, že vyžaduje více než jednu formu ověřování.

**Jak implementovat**:

- [Povolte vícefaktorové ověřování](../active-directory/authentication/concept-mfa-howitworks.md) ve službě Azure AD pomocí podmíněného přístupu a použijte interaktivní ověřování. 

- Alternativou je povolit vícefaktorové povolení pro celou doménu Azure AD nebo AD.

**Osvědčené postupy**:

- Aktivujte podmíněný přístup ve službě Azure AD (vyžaduje předplatné Premium). 
  - Podívejte se na článek [Podmíněný přístup ve službě Azure AD](../active-directory/conditional-access/overview.md).  

- Vytvořte skupiny Azure AD a povolte zásady MFA pro vybrané skupiny pomocí podmíněného přístupu Azure AD. 
  - Viz článek [Plánování nasazení podmíněného přístupu](../active-directory/conditional-access/plan-conditional-access.md). 

- MFA může být povolena pro celý Azure AD nebo pro celou službu Active Directory federované s Azure AD. 

- Použijte režim ověřování Azure AD Interactive pro SQL DB, kde je požadováno heslo interaktivně, následované ověřováním vícefaktorové ověřování:      
  - Používejte univerzální ověřování v SSMS. Podívejte se na článek [Použití vícefaktorového ověřování AAD s Azure SQL Database a Azure SQL Data Warehouse (podpora SSMS pro MFA).](sql-database-ssms-mfa-authentication.md)
  - Použití interaktivního ověřování podporovaného v datových nástrojích serveru SQL Server (SSDT). V článku [podpora Azure Active Directory v nástrojích SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Použijte jiné nástroje SQL podporující vícefaktorové analýzy. 
    - Podpora Průvodce SSMS pro export/extrahovat/nasadit databázi  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): možnost '/ua' 
    - [sqlcmd Utility](https://docs.microsoft.com/sql/tools/sqlcmd-utility): option -G (interaktivní)
    - [nástroj bcp:](https://docs.microsoft.com/sql/tools/bcp-utility)možnost -G (interaktivní) 

- Implementujte své aplikace pro připojení k Azure SQL Database pomocí interaktivního ověřování s podporou MFA. 
  - Podívejte se na článek [Připojení k Azure SQL Database s Azure Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Tento režim ověřování vyžaduje identity založené na uživateli. V případech, kdy se používá model důvěryhodné identity, který obcházejí individuální ověřování uživatelů Azure AD (např. pomocí spravované identity pro prostředky Azure), neplatí mfa.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimalizace používání ověřování na základě hesla pro uživatele

> Uvedeno v: OSA Practice #4, ISO Access Control (AC)

Metody ověřování založené na heslech jsou slabší formou ověřování. Pověření mohou být ohrožena nebo omylem rozdány.

**Jak implementovat**:

- Použijte integrované ověřování Azure AD, které eliminuje používání hesel.

**Osvědčené postupy**:

- Používejte ověřování jedním přihlášením pomocí pověření systému Windows. Federate místní domény služby AD s Azure AD a používat integrované ověřování windows (pro počítače spojené s doménou s Azure AD).
  - Podívejte se na [článek, Podpora SSMS pro integrované ověřování Azure AD](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimalizace používání ověřování na základě hesla pro aplikace 

> Uvedeno v: OSA Practice #4, ISO Access Control (AC)

**Jak implementovat**:

- Povolte spravovanou identitu Azure. Můžete také použít integrované ověřování nebo ověřování založené na certifikátech. 

**Osvědčené postupy**:

- Používejte [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Spravovaná identita přiřazená systémem](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Spravovaná identita přiřazená uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Použití Azure SQL Database ze služby app service se spravovanou identitou (bez změn kódu)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Pro aplikaci použijte ověřování na základě certifikátu. 
  - Viz tento [kód ukázku](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Ověřování Azure AD použijte pro integrovanou federovovovnou doménu a počítač spojený s doménou (viz část výše).
  - Podívejte se na [ukázkovou aplikaci pro integrované ověřování](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Ochrana hesel a tajných kódů

V případech, kdy se hesla nelze vyhnout, zkontrolujte, zda jsou zabezpečená.

**Jak implementovat**:

- K ukládání hesel a tajných klíčů použijte Azure Key Vault. Kdykoli je to možné, použijte mfa pro Azure SQL Database s uživateli Azure AD.

**Osvědčené postupy**:

- Pokud se vyhnout hesla nebo tajné klíče nejsou možné, uložte uživatelská hesla a tajné kódy aplikací v trezoru klíčů Azure a spravovat přístup prostřednictvím zásad přístupu key vault. 

- Různé architektury pro vývoj aplikací mohou také nabízet mechanismy specifické pro architekturu pro ochranu tajných kódů v aplikaci. Například: [ASP.NET základní aplikace](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Použití ověřování SQL pro starší aplikace 

Ověřování SQL odkazuje na ověřování uživatele při připojování k Azure SQL Database pomocí uživatelského jména a hesla. Přihlášení bude nutné vytvořit v každém serveru SQL Database nebo spravované instanci a uživatele vytvořeného v každé databázi.

**Jak implementovat**:

- Použijte ověřování SQL.

**Osvědčené postupy**:

- Jako správce serveru vytvářejte přihlašovací údaje a uživatele. Pokud nepoužíváte obsažené uživatele databáze s hesly, všechna hesla jsou uložena v hlavní databázi.
  - Viz článek [Řízení a udělení přístupu k databázi sql database a SQL Data Warehouse](sql-database-manage-logins.md).

## <a name="access-management"></a>Správa přístupu

Správa přístupu je proces řízení a správy přístupu oprávněných uživatelů a oprávnění k Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Uplatňovat zásadu nejnižšího privilegia

> Uvedeno v: FedRamp kontroly AC-06, NIST: AC-6, OSA Praxe #3

Princip nejnižších oprávnění uvádí, že uživatelé by neměli mít více oprávnění, než je potřeba k dokončení svých úkolů. Další informace naleznete v článku [Dostatečná správa](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Jak implementovat**:

Přiřaďte k dokončení požadovaných úkolů pouze [potřebná oprávnění:](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)

- V rovině dat SQL: 
    - Použití podrobných oprávnění a uživatelem definovaných databázových rolí (nebo rolí serveru v MI): 
        1. Vytvoření požadovaných rolí
            - [VYTVOŘIT ROLI](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [VYTVOŘIT ROLI SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Vytvořit požadované uživatele
            - [VYTVOŘIT UŽIVATELE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Přidání uživatelů jako členů do rolí 
            - [ZMĚNIT ROLI](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ZMĚNIT ROLI SERVERU](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Potom přiřaďte oprávnění k rolím. 
            - [Udělit](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Ujistěte se, že nepřiřazujete uživatele k nepotřebným rolím.

- Ve Správci prostředků Azure:
  - Použijte předdefinované role, pokud jsou k dispozici nebo vlastní role RBAC a přiřaďte potřebná oprávnění.
    - [Integrované role pro Azure](../role-based-access-control/built-in-roles.md) 
    - [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)

**Osvědčené postupy**:

Následující osvědčené postupy jsou volitelné, ale budou mít za následek lepší možnosti správy a podpory strategie zabezpečení: 

- Pokud je to možné, začněte s co nejmenší sadou oprávnění a začněte přidávat oprávnění jeden po druhém, pokud existuje skutečná nutnost (a odůvodnění) - na rozdíl od opačného přístupu: přijímání oprávnění krok za krokem. 

- Zdržet se přiřazování oprávnění jednotlivým uživatelům. Místo toho používejte role (databázové nebo serverové role) konzistentně. Role výrazně pomáhá při vytváření sestav a řešení potíží oprávnění. (Azure RBAC podporuje pouze přiřazení oprávnění prostřednictvím rolí.) 

- Vytvořte a používejte vlastní role s přesnými potřebnými oprávněními. Typické role, které se používají v praxi: 
  - Nasazení zabezpečení 
  - Správce 
  - Developer 
  - Podpůrný personál 
  - Auditor 
  - Automatizované procesy 
  - koncový uživatel 
  
- Předdefinované role používejte pouze v případě, že oprávnění rolí přesně odpovídají potřebným oprávněním pro uživatele. Uživatele můžete přiřadit k více rolím. 

- Nezapomeňte, že oprávnění v databázovém stroji serveru SQL Server lze použít na následující obory. Čím menší je rozsah, tím menší je dopad udělených oprávnění: 
  - Server Azure SQL Database (speciální role v hlavní databázi) 
  - databáze 
  - Schéma
      - Je vhodné použít schémata udělit oprávnění uvnitř databáze. (viz také: [Schema-design pro SQL Server: doporučení pro návrh schématu s ohledem na zabezpečení](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objekt (tabulka, zobrazení, postup atd.) 
  > [!NOTE]
  > Není doporučeno použít oprávnění na úrovni objektu, protože tato úroveň přidává zbytečné složitosti celkové implementace. Pokud se rozhodnete použít oprávnění na úrovni objektu, měla by být jasně zdokumentována. Totéž platí pro oprávnění na úrovni sloupce, které jsou ještě méně doporučované ze stejných důvodů. Uvědomte si také, že ve výchozím nastavení [deny](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) na úrovni tabulky nepřepíše grant na úrovni sloupce. To by vyžadovalo aktivaci [společné konfigurace serveru shody s požadavky.](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option)

- Provádějte pravidelné kontroly pomocí [funkce Posouzení ohrožení zabezpečení (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) a otestujte příliš mnoho oprávnění.

### <a name="implement-separation-of-duties"></a>Provedení oddělení povinností

> Uvedeno v: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Oddělení povinností, nazývané také oddělení povinností, popisuje požadavek na rozdělení citlivých úkolů do více úkolů, které jsou přiřazeny různým uživatelům. Oddělení povinností pomáhá předcházet narušení bezpečnosti dat.

**Jak implementovat**:

- Určit požadovanou úroveň oddělení cel. Příklady: 
  - Mezi vývojovým/testovacím a výrobním prostředím 
  - Úkoly na úrovni správy správce databáze (DBA) vs. úlohy pro vývojáře v citlivých úlohách vs. 
    - Příklady: Auditor, vytvoření zásad zabezpečení pro zabezpečení na úrovni rolí (RLS), Implementace objektů databáze SQL s oprávněními DDL.

- Identifikujte komplexní hierarchii uživatelů (a automatizovaných procesů), kteří přistupují k systému.

- Vytvořte role podle potřebných skupin uživatelů a přiřaďte k rolím oprávnění. 
  - Pro úlohy na úrovni správy na webu Azure Portal nebo prostřednictvím automatizace prostředí PowerShell použijte role RBAC. Buď najděte předdefinovanou roli odpovídající požadavku, nebo vytvořte vlastní roli RBAC pomocí dostupných oprávnění. 
  - Vytvořte role serveru pro úlohy celého serveru (vytváření nových přihlášení, databází) ve spravované instanci. 
  - Vytvořte databázové role pro úlohy na úrovni databáze.

- U některých citlivých úkolů zvažte vytvoření speciálních uložených procedur podepsaných certifikátem k provádění úkolů jménem uživatelů. 
  - Příklad: [Kurz: Podepisování uložených procedur pomocí certifikátu](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementujte transparentní šifrování dat (TDE) pomocí klíčů spravovaných zákazníky v trezoru klíčů Azure, abyste umožnili oddělení povinností mezi vlastníkem dat a vlastníkem zabezpečení. 
  - Podívejte se na článek [Konfigurace klíčů spravovaných zákazníky pro šifrování Azure Storage z webu Azure Portal](../storage/common/storage-encryption-keys-portal.md). 

- Chcete-li zajistit, aby dba nelze zobrazit data, která je považována za vysoce citlivé a stále můžete provést úkoly DBA, můžete použít vždy šifrované s oddělení rolí. 
  - Podívejte se na články, [Přehled správy klíčů pro vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [Zřizování klíčů s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)a Otočení [hlavního klíče sloupce s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- V případech, kdy to není možné alespoň ne bez velkých nákladů a úsilí, které mohou způsobit, že systém je téměř nepoužitelný, lze provést kompromisy a zmírnit pomocí kompenzačních ovládacích prvků, jako jsou: 
  - Lidský zásah do procesů. 
  - Auditní stopy – další informace o auditování naleznete v [tématu Audit kritické události zabezpečení](#audit-critical-security-events).

**Osvědčené postupy**:

- Ujistěte se, že různé účty se používají pro vývoj/ test a produkční prostředí. Různé účty pomáhají dodržovat oddělení testovacích & výrobních systémů.

- Zdržet se přiřazování oprávnění jednotlivým uživatelům. Místo toho používejte role (databázové nebo serverové role) konzistentně. S rolemi výrazně pomáhá při vytváření sestav a řešení potíží oprávnění.

- Předdefinované role použijte v případě, že oprávnění přesně odpovídají potřebným oprávněním – pokud sjednocení všech oprávnění z více předdefinovaných rolí vede ke 100% shodě, můžete přiřadit více rolí současně. 

- Vytvořte a používejte vlastní role, když předdefinované role udělují příliš mnoho oprávnění nebo nedostatečná oprávnění. 

- Přiřazení rolí lze také provést dočasně, označované také jako dynamické oddělení povinností (DSD), buď v rámci kroků úlohy sql agenta v T-SQL nebo pomocí Azure PIM pro role RBAC. 

- Ujistěte se, že správci dba nemají přístup k šifrovacíklíče nebo úložiště klíčů a správci zabezpečení s přístupem ke klíčům nemají přístup k databázi zase. 

- Vždy se ujistěte, že máte záznam auditu pro akce související se zabezpečením. 

- Můžete načíst definici předdefinované role RBAC zobrazit oprávnění a vytvořit vlastní roli na základě výňatky a kumulace z nich prostřednictvím prostředí PowerShell.

- Vzhledem k tomu, že každý člen db_owner databázové role může změnit nastavení zabezpečení, jako je transparentní šifrování dat (TDE), nebo změnit SLO, toto členství by mělo být uděleno s opatrností. Existuje však mnoho úkolů, které vyžadují oprávnění db_owner. Úloha jako změna nastavení databáze, jako je například změna možností DB. Auditování hraje klíčovou roli v každém řešení.

- Není možné omezit oprávnění db_owner a proto zabránit účtu správce zobrazení uživatelských dat. Pokud je v databázi vysoce citlivá data, vždy šifrované lze bezpečně zabránit db_owners nebo jiné DBA z jeho zobrazení.

> [!NOTE]
> Dosažení oddělení povinností (SoD) je náročné pro úkoly související se zabezpečením nebo odstraňování problémů. Ostatní oblasti, jako je vývoj a role koncových uživatelů, se snadněji oddělují. Většina ovládacích prvků souvisejících s dodržováním předpisů umožňuje použití alternativních řídicích funkcí, jako je auditování, když jiná řešení nejsou praktická.

Pro čtenáře, kteří se chtějí ponořit hlouběji do SoD, doporučujeme následující zdroje: 

- Pro Azure SQL Database:  
  - [Řízení a udělení přístupu k databázi sql database a sql data warehouse](sql-database-manage-logins.md)
  - [Oddělení povinností motoru pro vývojáře aplikací](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Oddělení cel v SQL Serveru 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Podepisování uložených procedur na SQL Serveru](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Pro správu prostředků Azure:
  - [Integrované role pro Azure](../role-based-access-control/built-in-roles.md) 
  - [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
  - [Použití správy privilegovaných identit Azure AD pro zvýšený přístup](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Provádět pravidelné kontroly kódu

> Uvedeno v: PCI: 6.3.2, SOC: SDL-3 

Oddělení povinností není omezeno na data v databázi, ale zahrnuje kód aplikace. Škodlivý kód může potenciálně obejít ovládací prvky zabezpečení. Před nasazením vlastního kódu do produkčního prostředí je nezbytné zkontrolovat, co se nasazuje.

**Jak implementovat**:

- Použijte databázový nástroj, jako je Azure Data Studio, který podporuje správě zdrojového kódu. 

- Implementujte proces nasazení odděleného kódu.

- Před spácháním do hlavní větve, osoba (jiné než autor kódu sám) musí zkontrolovat kód pro potenciální zvýšení rizika oprávnění, stejně jako škodlivé změny dat na ochranu proti podvodům a nepoctiví přístup. To lze provést pomocí mechanismů správy zdrojového kódu.

**Osvědčené postupy**:

- Standardizace: Pomáhá implementovat standardní postup, který je třeba dodržovat pro všechny aktualizace kódu. 

- Posouzení ohrožení zabezpečení obsahuje pravidla, která kontrolují nadměrná oprávnění, použití starých šifrovacích algoritmů a další problémy se zabezpečením v rámci schématu databáze. 

- Další kontroly lze provést v qa nebo testovací prostředí pomocí rozšířené ochrany před hrozbami, která vyhledá kód, který je zranitelný vůči vkládání SQL.

- Příklady toho, na co si dát pozor: 
  - Vytvoření uživatele nebo změna nastavení zabezpečení z v rámci automatického nasazení aktualizace kódu SQL. 
  - Uložená procedura, která v závislosti na zadaných parametrech aktualizuje peněžní hodnotu v buňce nevyhovujícím způsobem. 

- Ujistěte se, že osoba provádějící kontrolu je fyzická osoba jiná než původní autor kódu a znalost i v hodnocení kódu a bezpečném kódování.

- Ujistěte se, že znáte všechny zdroje změn kódu. Kód může být v T-SQL skripty. Mohou být příkazy ad hoc, které mají být provedeny nebo nasazeny ve formách zobrazení, funkce, aktivační události a uložené procedury. Může být součástí definice úloh y agenta SQL (steps). Může být také spuštěn a v rámci balíčků SSIS, Azure Data Factory a dalších služeb.

## <a name="data-protection"></a>Ochrana dat

Ochrana dat je sada možností pro zabezpečení důležitých informací před ohrožením zabezpečení šifrováním nebo zamlžením.

> [!NOTE]
> Microsoft osvědčuje Azure SQL Database jako fips 140-2 úroveň 1 kompatibilní. To se provádí po ověření přísné ho použití fips 140-2 úroveň 1 přijatelné algoritmy a FIPS 140-2 Úroveň 1 ověřené instance těchto algoritmů, včetně konzistence s požadované délky klíčů, správa klíčů, generování klíčů a úložiště klíčů. Toto ověření má našim zákazníkům umožnit reagovat na potřebu nebo požadavek na použití ověřených instancí FIPS 140-2 úrovně 1 při zpracování dat nebo dodání systémů nebo aplikací. Definujeme termíny "FIPS 140-2 Level 1 kompatibilní" a "FIPS 140-2 Level 1 dodržování" používané ve výše uvedeném prohlášení prokázat jejich zamýšlené použitelnosti na americké a kanadské vlády použití různých termínů "FIPS 140-2 Level 1 ověřen." 


### <a name="encrypt-data-in-transit"></a>Šifrování dat při přenosu

> Uvedeno v: OSA Practice #6, ISO Control Family: Kryptografie

Chrání data při pohybu dat mezi klientem a serverem. Informace naleznete v oblasti [zabezpečení sítě](#network-security).

### <a name="encrypt-data-at-rest"></a>Šifrování neaktivních uložených dat

> Uvedeno v: OSA Practice #6, ISO Control Family: Kryptografie

Šifrování v klidovém stavu je kryptografická ochrana dat při trvalém v databázi, protokolu a záložních souborech.

**Jak implementovat**:

- [Transparentní šifrování databáze (TDE)](transparent-data-encryption-azure-sql.md) se spravovanými klíči služby jsou ve výchozím nastavení povolené pro všechny databáze vytvořené po roce 2017 v Azure SQL Database.
- Ve spravované instanci, pokud je databáze vytvořena z operace obnovení pomocí místního serveru, bude dodrženo nastavení TDE původní databáze. Pokud původní databáze nemá povoleno TDE povoleno, doporučujeme, aby TDE ručně zapnout pro spravovanou instanci.

**Osvědčené postupy**:

- Neuklápejte data, která vyžadují šifrování v klidovém stavu v hlavní databázi. Hlavní databázi nelze zašifrovat pomocí TDE.

- Klíče spravované zákazníkem v azure key vaultu, pokud potřebujete větší transparentnost a podrobnou kontrolu nad ochranou TDE. Azure Key Vault umožňuje kdykoli odvolat oprávnění k vykreslení databáze nepřístupné. Můžete centrálně spravovat ochrany TDE spolu s dalšími klíči nebo otočit ochrana TDE podle vlastního plánu pomocí Azure Key Vault.

- Pokud používáte klíče spravované zákazníky v azure key vault, postupujte podle článků, [pokyny pro konfiguraci TDE s Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) a jak [nakonfigurovat Geo-DR s Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Ochrana citlivých dat, která se používají před vysoce privilegovanými a neoprávněnými uživateli

Data, která se používají, jsou data uložená v paměti databázového systému během provádění SQL dotazů. Pokud databáze ukládá citlivá data, může být vaše organizace povinna zajistit, aby vysoce privilegovaní uživatelé nemohli zobrazit citlivá data v databázi. Uživatelé s vysokými oprávněními, například operátoři společnosti Microsoft nebo správci databází ve vaší organizaci, by měli být schopni spravovat databázi, ale měli by mít možnost zobrazit a potenciálně exfiltrovat citlivá data z paměti procesu serveru SQL Server nebo dotazováním na databázi.

Zásady, které určují, která data jsou citlivá a zda citlivá data musí být šifrována v paměti a není přístupná správcům ve formátu prostého textu, jsou specifické pro vaši organizaci a předpisy pro dodržování předpisů, které je třeba dodržovat. Přečtěte si související požadavek: [Identifikujte a označte citlivá data](#identify-and-tag-sensitive-data).

**Jak implementovat**:

- Pomocí [vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) zajistit citlivá data není vystavena ve formátu prostého textu v Azure SQL Database, a to i v paměti / v použití. Vždy šifrované chrání data z správců databází (DBA) a cloudových správců (nebo chybných aktérů, kteří mohou zosobnit vysoce privilegované, ale neoprávněné uživatele) a poskytuje větší kontrolu nad tím, kdo má přístup k vašim datům.

**Osvědčené postupy**:

- Vždy šifrované není náhradou za šifrování dat v klidovém stavu (TDE) nebo při přenosu (SSL/TLS). Vždy šifrované by neměly být používány pro necitlivá data, aby se minimalizoval výkon a dopad na funkčnost. Pro komplexní ochranu dat v klidovém stavu, při přenosu a při provozu se doporučuje používat vždy šifrované ve spojení se zabezpečením TDE a transportní vrstvou (TLS). 

- Před nasazením vždy šifrované v produkční databázi vyhodnoťte dopad šifrování identifikovaných sloupců citlivých dat. Obecně vždy šifrované snižuje funkčnost dotazů na šifrované sloupce a má další omezení, uvedené v [vždy šifrované - podrobnosti o funkci](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Proto budete muset předělat aplikaci znovu implementovat funkce, dotaz nepodporuje, na straně klienta nebo/a refaktorovat schéma databáze, včetně definice uložené procedury, funkce, zobrazení a aktivační události. Existující aplikace nemusí pracovat se šifrovanými sloupci, pokud nedodržují omezení a omezení vždy šifrované. Zatímco ekosystém nástrojů, produktů a služeb společnosti Microsoft podporujících vždy šifrované roste, řada z nich nefunguje se šifrovanými sloupci. Šifrování sloupce může mít také vliv na výkon dotazu v závislosti na charakteristikách úlohy. 

- Správa vždy šifrované klíče s oddělení rolí, pokud používáte vždy šifrované k ochraně dat před škodlivými dba. Při oddělení rolí vytvoří správce zabezpečení fyzické klíče. DBA vytvoří objekt y metadat hlavního klíče sloupce a objekty metadat šifrovacího klíče sloupce popisující fyzické klíče v databázi. Během tohoto procesu správce zabezpečení nepotřebuje přístup k databázi a DBA nepotřebuje přístup k fyzickým klíčům ve formátu prostého textu. 
  - Podrobnosti najdete v článku [Správa klíčů s oddělením rolí.](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) 

- Uložte hlavní klíče sloupců do služby Azure Key Vault pro snadnou správu. Nepoužívejte úložiště certifikátů Windows (a obecně řešení distribuovaného úložiště klíčů, na rozdíl od řešení pro správu centrálních klíčů), která ztěžují správu klíčů. 

- Pečlivě si promyslete kompromisy použití více klíčů (šifrovací klíče hlavního klíče sloupce nebo sloupce). Udržujte malý počet klíčů, abyste snížili náklady na správu klíčů. Jeden hlavní klíč sloupce a jeden sloupec šifrovací klíč na databázi je obvykle dostačující v prostředí chudinské oblasti stavu (není uprostřed střídání klíčů). Další klíče můžete potřebovat, pokud máte různé skupiny uživatelů, z nichž každá používá různé klíče a přistupuje k různým datům.  

- Otočte hlavní klíče sloupců podle požadavků na dodržování předpisů. Pokud potřebujete také otočit šifrovací klíče sloupců, zvažte použití šifrování online, abyste minimalizovali prostoje aplikací. 
  - Podívejte se na článek, [výkon a dostupnost aspekty](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Deterministické šifrování použijte, pokud je třeba podporovat výpočty (rovnost) dat. V opačném případě použijte randomizované šifrování. Nepoužívejte deterministické šifrování pro datové sady s nízkou entropií nebo datové sady s veřejně známou distribucí. 

- Pokud máte obavy, že k vašim datům budou mít třetí strany přístup legálně bez vašeho souhlasu, ujistěte se, že všechny aplikace a nástroje, které mají přístup ke klíčům a datům ve formátu prostého textu, běží mimo Microsoft Azure Cloud. Bez přístupu ke klíčům nebude mít třetí strana žádný způsob, jak data dešifrovat, pokud neobejde šifrování.

- Vždy šifrované nepodporuje snadno udělení dočasného přístupu ke klíčům (a chráněným datům). Například pokud potřebujete sdílet klíče s DBA povolit DBA provést některé operace čištění na citlivá a šifrovaná data. Jediným způsobem, jak spolehlivost odvolat přístup k datům z dba bude otočit klíče šifrování sloupce a hlavní klíče sloupce chrání data, což je nákladná operace. 

- Chcete-li získat přístup k hodnotám prostého textu v zašifrovaných sloupcích, musí mít uživatel přístup k cmk, který chrání sloupce, který je nakonfigurován v úložišti klíčů, který drží CMK. Uživatel také musí mít **zobrazit libovolný sloupec master key definice** a zobrazit libovolný sloupec šifrování klíč **definice** databáze oprávnění.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Řízení přístupu uživatelů aplikací k citlivým datům prostřednictvím šifrování

Šifrování lze použít jako způsob, jak zajistit, aby data mohli zobrazit nebo aktualizovat pouze konkrétní uživatelé aplikací, kteří mají přístup ke kryptografickým klíčům.

**Jak implementovat**:

- Použijte šifrování na úrovni buňky (CLE). Podrobnosti najdete v článku [Šifrovat sloupec dat.](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) 
- Používejte vždy šifrované, ale uvědomte si jeho omezení. Omezení jsou uvedena níže.

**Osvědčené postupy**

Při použití CLE:

- Řízení přístupu ke klíčům prostřednictvím oprávnění sql a rolí. 

- Pro šifrování dat použijte AES (doporučeno AES 256). Algoritmy, jako RC4, DES a TripleDES, jsou zastaralé a neměly by být používány z důvodu známých chyb zabezpečení. 

- Chraňte symetrické klíče pomocí asymetrických klíčů/certifikátů (nikoli hesel), abyste se vyhnuli používání 3DES. 

- Buďte opatrní při migraci databáze pomocí šifrování na úrovni buněk prostřednictvím exportu nebo importu (bacpac soubory). 
  - Podívejte se na článek [Doporučení pro použití šifrování na úrovni buněk v Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) o tom, jak zabránit ztrátě klíčů při migraci dat a další pokyny pro osvědčené postupy.

Mějte na paměti, že vždy šifrované je primárně určen k ochraně citlivá data v použití od uživatelů s vysokými oprávněními Azure SQL Database (cloud operátoři, DBA) - viz [Ochrana citlivých dat v používání před vysoce privilegovanými, neoprávněnými uživateli](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Buďte si vědomi následujících problémů při použití vždy šifrované k ochraně dat před uživateli aplikací:

- Ve výchozím nastavení udržují všechny klientské ovladače společnosti Microsoft podporující vždy šifrované globální (jednu na aplikaci) mezipaměť šifrovacích klíčů sloupců. Jakmile ovladač klienta získá šifrovací klíč sloupce ve formátu prostého textu kontaktováním úložiště klíčů obsahujícího hlavní klíč sloupce, je šifrovací klíč sloupce ve formátu prostého textu uložen do mezipaměti. Díky izolaci dat od uživatelů víceuživatelské aplikace náročné. Pokud vaše aplikace zosobňuje koncové uživatele při interakci s úložiště klíčů (například Azure Key Vault), poté, co dotaz uživatele naplní mezipaměť pomocí šifrovacího klíče sloupce, následný dotaz, který vyžaduje stejný klíč, ale je spuštěn jiným uživatelem použije klíč uložený v mezipaměti. Ovladač nezavolá do úložiště klíčů a nezkontroluje, zda má druhý uživatel oprávnění k přístupu k šifrovacímu klíči sloupců. V důsledku toho uživatel uvidí šifrovaná data i v případě, že uživatel nemá přístup ke klíčům. Chcete-li dosáhnout izolace uživatelů v rámci víceuživatelské aplikace, můžete zakázat ukládání šifrovacího klíče sloupce do mezipaměti. Zakázání ukládání do mezipaměti způsobí další režie výkonu, protože ovladač bude muset kontaktovat úložiště klíčů pro každou operaci šifrování nebo dešifrování dat.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Ochrana dat před neoprávněným zobrazením uživateli aplikací při zachování datového formátu
Další technikou, která brání neoprávněným uživatelům v zobrazení dat, je zamlžovat nebo maskovat data při zachování datových typů a formátů, aby bylo zajištěno, že uživatelské aplikace mohou pokračovat v zpracování a zobrazení dat.

**Jak implementovat**:

- K zamlžení sloupců tabulky použijte [dynamické maskování dat.](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

> [!NOTE]
> Vždy šifrované nefunguje s dynamickým zakrytím dat. Není možné šifrovat a maskovat stejný sloupec, což znamená, že je třeba stanovit prioritu ochrany dat v používání vs maskování dat pro uživatele aplikace prostřednictvím dynamického maskování dat.

**Osvědčené postupy**:

> [!NOTE]
> Dynamické maskování dat nelze použít k ochraně dat před uživateli s vysokými oprávněními. Maskovací zásady se nevztahují na uživatele s přístupem pro správu, jako je db_owner.

- Nepovolujte uživatelům aplikací spouštět ad-hoc dotazy (protože mohou být schopni obejít dynamické maskování dat).  
  - Podrobnosti naleznete v článku [Vynechání maskování pomocí technik odvození nebo hrubé síly.](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques)  

- Pomocí zásad správného řízení přístupu (pomocí oprávnění SQL, rolí, RLS) omezte oprávnění uživatelů k aktualizaci v maskovaných sloupcích. Vytvoření masky ve sloupci nezabrání aktualizacím tohoto sloupce. Uživatelé, kteří obdrží maskovaná data při dotazování na maskovaný sloupec, mohou data aktualizovat, pokud mají oprávnění k zápisu.    

-  Dynamické maskování dat nezachová statistické vlastnosti maskovaných hodnot. To může mít vliv na výsledky dotazu (například dotazy obsahující filtrování predikáty nebo spojení na maskovaná data).

## <a name="network-security"></a>Zabezpečení sítě
Zabezpečení sítě označuje ovládací prvky přístupu a osvědčené postupy pro zabezpečení dat při přenosu do Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Konfigurace klienta pro bezpečné připojení k Azure SQL Database 
Osvědčené postupy, jak zabránit klientským počítačům a aplikacím s dobře známými chybami zabezpečení (například pomocí starších protokolů TLS a šifrovacích sad) v připojení k Azure SQL Database.

**Jak implementovat**:

- Ujistěte se, že klientské počítače, které se připojují k Azure SQL Database, používají [zabezpečení transportní vrstvy (TLS).](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit)

**Osvědčené postupy**:

- Konfigurace všech aplikací a nástrojů pro připojení k SQL DB s povoleným šifrováním 
  - Šifrovat = Zapnuto, TrustServerCertificate = Vypnuto (nebo ekvivalentní s ovladači jiných výrobců). 

- Pokud vaše aplikace používá ovladač, který nepodporuje TLS nebo podporuje starší verzi TLS, pokud je to možné, vyměňte ovladač. Pokud to není možné, pečlivě vyhodnoťte bezpečnostní rizika. 

- Snižte způsoby útoku prostřednictvím chyb zabezpečení v položkách SSL 2.0, SSL 3.0, TLS 1.0 a TLS 1.1 zakázáním jejich zabezpečení na klientských počítačích, které se připojují k nastavení registru Azure SQL Database per [Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Zkontrolujte šifry sady k dispozici na straně [klienta: Cipher Suites v TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Konkrétně zakázat 3DES na [konfiguraci TLS Cipher Suite Order](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Pro Azure SQL Database šifrování je vynuceno pro proxy server a přesměrovat typy připojení. Pokud používáte spravovanou instanci, použijte typ připojení **proxy** (výchozí), protože to vynucuje šifrování ze strany serveru. Typ připojení **Přesměrování** aktuálně nepodporuje vynucení šifrování a je k dispozici pouze u privátních připojení IP. 

- Další informace naleznete v [tématu Architektura připojení Azure SQL – zásady připojení](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Minimalizace povrchu útoku
Minimalizujte počet funkcí, které mohou být napadeny uživatelem se zlými úmysly. Implementujte ovládací prvky přístupu k síti pro Azure SQL Database.

> Uvedeno v: OSA praxe #5

**Jak implementovat**:

Na serveru Azure SQL Database (obsahující montonovou databázi nebo elastické fondy):
- Nastavte povolit přístup ke službám Azure na OFF.

- Použijte koncové body služby Virtuální sítě a pravidla brány firewall virtuální sítě.

- Použijte soukromý odkaz (náhled).

Ve spravované instanci:
- Postupujte podle pokynů v [části Požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Osvědčené postupy**:

- Omezení přístupu k Azure SQL Database připojením na privátní koncový bod (například pomocí cesty privátní data): 
  - Spravovaná instance může být izolovaná uvnitř virtuální sítě, aby se zabránilo externímu přístupu. Aplikace a nástroje, které jsou ve stejné nebo partnerské virtuální sítě ve stejné oblasti můžete přistupovat přímo. Aplikace a nástroje, které jsou v jiné oblasti můžete použít připojení virtuální sítě k virtuální síti nebo peering okruhu ExpressRoute k navázání připojení. Zákazník by měl pomocí skupin zabezpečení sítě (NSG) omezit přístup přes port 1433 pouze na prostředky, které vyžadují přístup ke spravované instanci 
  - Pro sql database server (obsahující jednotlivé databáze nebo elastické fondy) použijte funkci [Private Link,](sql-database-private-endpoint-overview.md) která poskytuje vyhrazenou privátní IP adresu pro sql database server uvnitř vaší virtuální sítě. Koncové body [služby VNet service s pravidly brány firewall sítě VNet](sql-database-vnet-service-endpoint-rule-overview.md) můžete také omezit přístup k serverům databáze SQL.
  - Mobilní uživatelé by měli používat připojení VPN z bodu na místo k připojení přes datovou cestu.
  - Uživatelé připojení k místní síti by měli používat připojení VPN mezi lokalitami nebo ExpressRoute k připojení přes datovou cestu.

- K Azure SQL Database se dostanete připojením k veřejnému koncovému bodu (například pomocí cesty veřejných dat). Je třeba zvážit následující osvědčené postupy: 
  - U databázového serveru SQL použijte [pravidla brány firewall IP](sql-database-firewall-configure.md) k omezení přístupu pouze na autorizované adresy IP.
  - Pro spravovanou instanci použijte skupiny zabezpečení sítě (NSG) k omezení přístupu přes port 3342 pouze na požadované prostředky. Další informace najdete [v tématu Bezpečné použití spravované instance Azure SQL Database s veřejnými koncovými body](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Veřejný koncový bod spravované instance není ve výchozím nastavení povolen a musí být explicitně povolen. Pokud zásady společnosti zakazuje použití veřejných koncových bodů, použijte [Azure Policy,](../governance/policy/overview.md) abyste zabránili povolení veřejných koncových bodů na prvním místě.

- Nastavení součástí Azure Networking: 
  - Postupujte podle [doporučených postupů Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).
  - Plánování konfigurace virtuální sítě (VNet) podle osvědčených postupů popsaných v [nejčastějších dotazech (FAQ)](../virtual-network/virtual-networks-faq.md) a plánu virtuální sítě Azure Virtuální síť. 
  - Segmentovat virtuální síť do více podsítí a přiřadit prostředky pro podobnou roli ke stejné podsíti (například front-end vs back-end prostředky).
  - Pomocí [skupin zabezpečení sítě (NSGs)](../virtual-network/security-overview.md) můžete řídit provoz mezi podsítěmi uvnitř hranice virtuální sítě Azure.
  - Povolte [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) pro vaše předplatné monitorovat příchozí a odchozí síťový provoz.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Konfigurace Power BI pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- Pro Power BI Desktop použijte soukromou datovou cestu, kdykoli je to možné. 

- Ujistěte se, že se Power BI Desktop připojuje pomocí Protokolu TLS1.2 nastavením klíče registru v klientském počítači podle nastavení registru [Zabezpečení transportní vrstvy (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Omezte přístup k datům pro konkrétní uživatele pomocí [zabezpečení na úrovni řádků (RLS) pomocí Power BI](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Pro službu Power BI použijte [místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-onprem)a mějte na paměti [omezení a důležité informace](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Konfigurace služby App Service pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- Pro jednoduchou webovou aplikaci vyžaduje připojení přes veřejný koncový bod nastavení **Povolit služby Azure** na ZAPNUTO. 

- [Integrujte svou aplikaci s virtuální sítí Azure](../app-service/web-sites-integrate-with-vnet.md) pro připojení privátní datové cesty ke spravované instanci. Volitelně můžete také nasadit webovou aplikaci s [prostředím služby App Service (ASE).](../app-service/environment/intro.md) 

- Pro Web App s ASE nebo Virtuální síť Integrované webové aplikace připojení k databázi na serveru SQL Database, můžete použít [koncové body služby Virtuální sítě a Pravidla brány firewall virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) omezit přístup z konkrétní virtuální sítě a podsítě. Pak nastavte **Povolit služby Azure** na VYPNUTO. Službu ASE můžete také připojit ke spravované instanci prostředpou privátní datové cesty.  

- Ujistěte se, že vaše webová aplikace je nakonfigurovaná podle [článku, Doporučené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí služby Azure App Service](../security/security-paas-applications-using-app-services.md). 

- Nainstalujte [bránu firewall webových aplikací (WAF)](../application-gateway/waf-overview.md) a chraňte webovou aplikaci před běžnými zneužitími a chybami zabezpečení.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Konfigurace hostingu virtuálních virtuálních počítačů Azure pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- Pomocí kombinace povolit a odepřít pravidla na nsg virtuálních počítačích Azure řídit, které oblasti přístup z virtuálního počítače.

- Ujistěte se, že váš virtuální počítač je nakonfigurovaný podle článku, [doporučené postupy zabezpečení pro úlohy IaaS v Azure](../security/azure-security-iaas.md).

- Ujistěte se, že všechny virtuální aplikace jsou přidružené k určité virtuální síti a podsíti. 

- Vyhodnoťte, zda potřebujete výchozí trasu 0.0.0.0/Internet podle pokynů [k vynucenému tunelování](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Pokud ano – například podsíť front-endu – ponechejte výchozí trasu.
  - Pokud ne – například střední vrstva nebo back-endová podsíť – povolte vynucené tunelování, aby žádný provoz nepřešel přes Internet, aby se dostal do místního (neboli mezimístní). 

- Pokud používáte partnerský vztah nebo se připojujete k místnímu připojení, implementujte [volitelné výchozí trasy.](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) 

- Implementujte [uživatelem definované trasy,](../virtual-network/virtual-networks-udr-overview.md#user-defined) pokud potřebujete odeslat veškerý provoz ve virtuální síti do síťového virtuálního zařízení pro kontrolu paketů. 

- Koncové [body služby Virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) slouží k zabezpečenému přístupu ke službám PaaS, jako je Azure Storage prostřednictvím páteřní sítě Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Ochrana před útoky ddos (Distributed Denial of Service)
Útoky distribuovaného odmítnutí služby (DDoS) jsou pokusy uživatele se zlými úmysly odeslat záplavu síťového provozu do azure SQL database s cílem zahltit infrastrukturu Azure a způsobit, že odmítne platná přihlášení a úlohy.

> Uvedeno v: OSA Praxe #9

**Jak implementovat**:

Ochrana DDoS je automaticky povolena jako součást platformy Azure. Zahrnuje monitorování provozu vždy na a zmírňování útoků na úrovni sítě na úrovni sítě na veřejných koncových bodech v reálném čase. 

- Azure [DDoS Protection](../virtual-network/ddos-protection-overview.md) slouží ke sledování veřejných IP adres přidružených k prostředkům nasazeným ve virtuálních sítích.

- Pomocí [rozšířené ochrany před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md) zjišťujte útoky typu DoS (Denial of Service) proti databázím.

**Osvědčené postupy**:

- Postupujte podle postupů popsaných v [části Minimalizovat attack surface](#minimize-attack-surface) pomáhá minimalizovat hrozby útoku DDoS. 

- Výstraha pověření SQL hrubé **hodové protihrozby** pomáhá detekovat útoky hrubou silou. V některých případech výstraha může dokonce rozlišit penetrační testování úloh. 

- Pro hostitelské aplikace virtuálních zařízení Azure, které se připojují k databázi SQL: 
  - Postupujte podle doporučení omezit přístup prostřednictvím koncových bodů směřujících k Internetu v Centru zabezpečení Azure. 
  - Škálovací sady virtuálních strojů slouží ke spuštění více instancí vaší aplikace na virtuálních počítačích Azure. 
  - Zakažte protokolRDP a SSH z Internetu, abyste zabránili útoku hrubou silou. 

## <a name="monitoring-logging-and-auditing"></a>Monitorování, protokolování a auditování  
Tato část odkazuje na funkce, které vám pomohou odhalit neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Popisuje také osvědčené postupy pro konfiguraci auditování databáze ke sledování a zachycení událostí databáze.

### <a name="protect-databases-against-attacks"></a>Ochrana databází před útoky 
Pokročilá ochrana před hrozbami umožňuje detekovat potenciální hrozby a reagovat na ně tak, jak k nim dochází, a to poskytováním výstrah zabezpečení na neobvyklé aktivity.

**Jak implementovat**:

- Pomocí [rozšířené ochrany před internetovými hrozbami pro SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) můžete zjistit neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo o zneužití databáze, včetně:
  - Útok injektáží SQL.
  - Krádeže/únik přihlašovacích údajů.
  - Zneužití privilegií.
  - Exfiltrace dat.

**Osvědčené postupy**:

- Nakonfigurujte [rozšířené zabezpečení dat (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) pro Azure SQL Database pro konkrétní databázový server SQL nebo spravovanou instanci. Službu ADS můžete také nakonfigurovat pro všechny servery SQL Database a spravované instance v předplatném přepnutím na [úroveň Azure Security Center Standard](../security-center/security-center-pricing.md). 

- Pro úplné šetření zkušenosti se doporučuje povolit [auditování databáze SQL](sql-database-auditing.md). Pomocí auditování můžete sledovat události databáze a zapsat je do protokolu auditu v pracovním prostoru Azure Storage nebo Azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Auditování kritických událostí zabezpečení
Sledování událostí databáze vám pomůže pochopit databázovou aktivitu. Můžete získat přehled o nesrovnalostech a anomáliích, které by mohly naznačovat obchodní problémy nebo podezření na narušení zabezpečení. Umožňuje také a usnadňuje dodržování norem shody. 

**Jak implementovat**:

- Povolte [auditování databáze SQL](sql-database-auditing.md) ke sledování událostí databáze a jejich zápis do protokolu auditu v účtu Azure Storage, pracovním prostoru Log Analytics (preview) nebo v centrech událostí (preview). 

- Protokoly auditu lze zapsat do účtu Úložiště Azure, do pracovního prostoru Log Analytics pro spotřebu protokoly Azure Monitor nebo do centra událostí pro využití centra událostí. Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu budou zapsány do každé z nich. 

**Osvědčené postupy**:

- Konfigurací [auditování databáze SQL](sql-database-auditing.md) na databázovém serveru pro auditování událostí budou auditovány všechny existující a nově vytvořené databáze na tomto serveru.
- Ve výchozím nastavení zásady auditování zahrnuje všechny akce (dotazy, uložené procedury a úspěšné a neúspěšné přihlášení) proti databázím, což může mít za následek velký objem protokolů auditu. Zákazníkům se doporučuje [konfigurovat auditování pro různé typy akcí a skupin akcí pomocí prostředí PowerShell](sql-database-auditing.md#manage-auditing). Konfigurace to pomůže řídit počet auditovaných akcí a minimalizovat riziko ztráty událostí. Vlastní konfigurace auditu umožňuje zákazníkům zachytit pouze potřebná data auditu.
- Protokoly auditu lze spotřebovat přímo na [webu Azure Portal](https://portal.azure.com/)nebo z umístění úložiště, které bylo nakonfigurované. 


> [!NOTE]
> Povolení auditování služby Log Analytics bude účtovány náklady na základě míry požití. Mějte prosím na paměti související náklady s použitím této [možnosti](https://azure.microsoft.com/pricing/details/monitor/), nebo zvažte ukládání protokolů auditu v účtu úložiště Azure. 

**Další zdroje**:

- [Auditování databáze SQL](sql-database-auditing.md)
- [Auditování serveru SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Zabezpečené protokoly auditu
Omezte přístup k účtu úložiště pro podporu oddělení povinností a oddělit DBA od auditorů. 

**Jak implementovat**:

- Při ukládání protokolů auditování do služby Azure Storage se ujistěte, že přístup k účtu úložiště je omezen na minimální principy zabezpečení. Určit, kdo má přístup k účtu úložiště.
    - Další informace najdete [v tématu Autorizace přístupu k Úložišti Azure](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Osvědčené postupy**:

- Řízení přístupu k cíli auditu je klíčovým konceptem při oddělení DBA od auditorů. 

- Při auditování přístupu k citlivým datům zvažte zabezpečení dat šifrováním dat, aby se zabránilo úniku informací pro auditora. Další informace naleznete v části [Ochrana citlivých dat, která se používají před vysoce privilegovanými neoprávněnými uživateli](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Správa zabezpečení

Tato část popisuje různé aspekty a osvědčené postupy pro správu stavu zabezpečení databází. Obsahuje osvědčené postupy pro zajištění, aby vaše databáze byly nakonfigurovány tak, aby splňovaly bezpečnostní standardy, pro zjišťování a klasifikaci a sledování přístupu k potenciálně citlivým datům v databázích. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Ujistěte se, že databáze jsou nakonfigurovány tak, aby splňovaly osvědčené postupy zabezpečení 

Proaktivně vylepšujte zabezpečení databáze zjišťováním a nápravou potenciálních chyb zabezpečení databáze.

**Jak implementovat**:

- Povolte [funkci SQL Assessment](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) prohledat v databázi problémy se zabezpečením a automaticky se v databázích automaticky spouštět.

**Osvědčené postupy**:

- Zpočátku spusťte VA v databázích a iterát tím, že sanace selhání kontroly, které jsou proti doporučené postupy zabezpečení. Nastavte směrné plány pro přijatelné konfigurace, dokud kontrola nevystane _čistá_nebo dokud neprojdou všechny kontroly.  

- Nakonfigurujte pravidelné opakované prohledávání tak, aby se spouštěla jednou týdně, a nakonfigurujte příslušnou osobu tak, aby přijímala souhrnné e-maily. 

- Projděte si souhrn VA po každém týdenním skenování. U všech nalezených chyb zabezpečení vyhodnoťte posun od předchozího výsledku kontroly a zjistěte, zda má být kontrola vyřešena. Zkontrolujte, zda existuje legitimní důvod pro změnu konfigurace.   

- V příslušných případě vyřešte kontroly a aktualizujte směrné plány. Vytvořte položky lístku pro řešení akcí a sledujte je, dokud nebudou vyřešeny. 

**Další zdroje**:

- [Posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Služba SQL Vulnerability Assessment pomáhá identifikovat chyby zabezpečení databáze](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identifikace a označení citlivých dat 

Objevte sloupce, které potenciálně obsahují citlivá data. To, co je považováno za citlivé údaje, do značné míry závisí na zákazníkovi, nařízení o dodržování předpisů atd., a musí být vyhodnoceno uživateli odpovědnými za tyto údaje. Klasifikovat sloupce použít pokročilé citlivosti založené auditování a ochrany scénáře. 

**Jak implementovat**:

- Pomocí [zjišťování a klasifikace dat SQL](sql-database-data-discovery-and-classification.md) můžete zjišťovat, klasifikovat, označovat a chránit citlivá data ve vašich databázích. 
  - Zobrazení doporučení klasifikace, které jsou vytvořeny automatické zjišťování v řídicím panelu zjišťování a klasifikace dat SQL. Přijměte příslušné klasifikace tak, aby vaše citlivá data byla trvale označena popisky klasifikace. 
  - Ručně přidejte klasifikace pro všechna další citlivá datová pole, která nebyla zjištěna automatizovaným mechanismem. 
- Další informace naleznete v tématu [ZJIŠŤOVÁNÍ dat SQL & klasifikace](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Osvědčené postupy**:

- Pravidelně monitorujte řídicí panel klasifikace pro přesné posouzení stavu klasifikace databáze. Sestavu o stavu klasifikace databáze lze exportovat nebo vytisknout pro sdílení pro účely dodržování předpisů a auditování.

- Průběžně sledujte stav doporučených citlivých dat v posouzení ohrožení zabezpečení SQL. Sledujte pravidlo zjišťování citlivých dat a identifikujte případný posun v doporučených sloupcích pro klasifikaci.  

- Používejte klasifikaci způsobem, který je přizpůsoben specifickým potřebám vaší organizace. Přizpůsobte si zásady ochrany informací (popisky citlivosti, typy informací, logiku zjišťování) v zásadách [ochrany informací SQL](../security-center/security-center-info-protection-policy.md) v Centru zabezpečení Azure. 

### <a name="track-access-to-sensitive-data"></a>Sledování přístupu k citlivým datům 
Sledujte, kdo přistupuje k citlivým datům, a zachyťte dotazy na citlivá data v protokolech auditu.

**Jak implementovat**:

- Pomocí sql auditu a klasifikace dat v kombinaci. 
  - V protokolu [auditování databáze SQL](sql-database-auditing.md) můžete sledovat přístup konkrétně k citlivým datům. Můžete také zobrazit informace, jako jsou data, ke kterým byl přístup přístup, a také jeho popisek citlivosti. Další informace naleznete v [tématu Zjišťování dat & klasifikace](sql-database-data-discovery-and-classification.md) a [auditování přístupu k citlivým datům](sql-database-data-discovery-and-classification.md#audit-sensitive-data). 

**Osvědčené postupy**:

- Podívejte se na osvědčené postupy pro části Auditování a klasifikace dat: 
  - [Auditování kritických událostí zabezpečení](#audit-critical-security-events) 
  - [Identifikace a označení citlivých dat](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Vizualizace stavu zabezpečení a dodržování předpisů 

Použijte jednotný systém správy zabezpečení infrastruktury, který posiluje stav zabezpečení datových center (včetně databází SQL). Zobrazení seznamu doporučení týkajících se zabezpečení databází a stavu dodržování předpisů.

**Jak implementovat**:

- Sledujte doporučení zabezpečení související s SQL a aktivní hrozby v [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Běžné bezpečnostní hrozby a potenciální skutečnosti snižující závažnost rizika

Tato část vám pomůže najít bezpečnostní opatření na ochranu před určitými způsoby útoku. Očekává se, že většina skutečnosti snižující závažnost rizika lze dosáhnout pomocí jednoho nebo více pokynů zabezpečení výše.

### <a name="security-threat-data-exfiltration"></a>Bezpečnostní hrozba: Exfiltrace dat

Exfiltrace dat je neoprávněné kopírování, přenos nebo načítání dat z počítače nebo serveru. Viz definice [exfiltrace dat](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipedii.

Připojení k serveru Azure SQL Database přes veřejný koncový bod představuje riziko exfiltrace dat, protože vyžaduje, aby zákazníci otevřeli své brány firewall veřejným IP adresy.  

**Scénář 1**: Aplikace na virtuálním počítači Azure se připojí k databázi na serveru Azure SQL Database. Rogue actor získá přístup k virtuálnímu připojení a kompromituje ji. V tomto scénáři exfiltrace dat znamená, že externí entita pomocí neautorizovaného virtuálního počítače se připojí k databázi, zkopíruje osobní data a uloží je do úložiště objektů blob nebo jiné databáze SQL v jiném předplatném.

**Scénář 2**: Rouge DBA. Tento scénář je často vyvolána zabezpečení citlivých zákazníků z regulovaných odvětví. V tomto scénáři může uživatel s vysokými oprávněními zkopírovat data z azure sql database do jiného předplatného, které není řízeno vlastníkem dat.

**Potenciální skutečnosti snižující závažnost rizika**:

Azure SQL Database dnes nabízí následující techniky pro zmírnění ohrožení exfiltrace dat: 

- Pomocí kombinace povolit a odepřít pravidla na nsg virtuálních počítačích Azure řídit, které oblasti přístup z virtuálního počítače. 
- Pokud používáte databázový server Azure SQL (obsahující jednotonové databáze nebo elastické fondy), nastavte následující možnosti:
  - Povolit služby Azure off.
  - Povolte jenom provoz z podsítě obsahující virtuální počítač Azure nastavením pravidla brány firewall virtuální sítě.
  - Použít [soukromý odkaz](sql-database-private-endpoint-overview.md)
- Pro spravovanou instanci pomocí privátní IP přístup ve výchozím nastavení řeší první obavy o exfiltraci dat neautorizovaný virtuální ho. Zapněte funkci delegování podsítě v podsíti a automaticky nastavte nejrestriktivnější zásady v podsíti spravované instance.
- Rogue DBA obavy je více vystavena spravované instance, protože má větší plochu a požadavky na síť jsou viditelné pro zákazníky. Nejlepší zmírnění pro to je použití všech postupů v této příručce zabezpečení, aby se zabránilo rogue DBA scénář na prvním místě (nejen pro exfiltraci dat). Vždy šifrované je jedna metoda pro ochranu citlivých dat šifrováním a udržováním klíče nepřístupného pro DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Bezpečnostní aspekty kontinuity a dostupnosti provozu

Většina bezpečnostních standardů se zabývá dostupností dat z hlediska provozní kontinuity, které bylo dosaženo implementací možností redundance a převzetí služeb při selhání, aby se zabránilo selhání jednotlivých bodů. Pro scénáře havárie je běžnou praxí zachovat zálohy souborů dat a protokolu.V následující části je podrobný přehled funkcí, které jsou integrované do Azure. Poskytuje také další možnosti, které lze nakonfigurovat tak, aby vyhovovaly specifickým potřebám: 

- Azure nabízí integrovanou vysokou dostupnost: [Vysoká dostupnost a Azure SQL Database](sql-database-high-availability.md) 

- Úroveň Business Critical zahrnuje skupiny s podporou převzetí služeb při selhání, zóny s více dostupností, úplné a rozdílové zálohy protokolů a zálohy obnovení v čase, které jsou ve výchozím nastavení povoleny:  
  - [Vysoká dostupnost a Azure SQL Database – redundantní konfigurace zóny](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatizované zálohy](sql-database-automated-backups.md)
  - [Obnovení databáze Azure SQL pomocí automatického zálohování databáze – obnovení bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore)

- Další funkce kontinuity podnikání, jako jsou skupiny automatického převzetí služeb při selhání v různých geografických geografických službách Azure, lze nakonfigurovat, jak je popsáno zde: [Přehled kontinuity podnikání s databází Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Další kroky

- Podívejte se [na přehled možností zabezpečení Azure SQL Database](sql-database-security-overview.md)
