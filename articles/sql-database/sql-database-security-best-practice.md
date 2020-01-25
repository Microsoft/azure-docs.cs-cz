---
title: Osvědčené postupy zabezpečení PlayBook pro Azure SQL Database | Microsoft Docs
description: Tento článek poskytuje obecné pokyny pro osvědčené postupy zabezpečení v Azure SQL Database.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 12/23/2019
ms.reviewer: ''
ms.openlocfilehash: 82297850bf6d03215963a1f81dda166550f2b0d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715176"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Azure SQL Database osvědčené postupy zabezpečení PlayBook

## <a name="overview"></a>Přehled

Tento dokument poskytuje pokyny, jak řešit běžné požadavky na zabezpečení pro nové nebo existující aplikace pomocí Azure SQL Database. Je uspořádána podle oblastí zabezpečení vysoké úrovně. Informace o konkrétních hrozbách najdete v části [běžné bezpečnostní hrozby a potenciální rizika](#common-security-threats-and-potential-mitigations) . I když jsou při migraci aplikací z místního prostředí do Azure k dispozici některá z uvedených doporučení, scénáře migrace nejsou zaměřeny na tento dokument.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Nabídky nasazení Azure SQL Database zahrnuté v této příručce

- [Databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): izolované [databáze](sql-database-single-database.md) a [elastické fondy](sql-database-elastic-pool.md) na [serverech Azure SQL Database](sql-database-servers.md)
- [Spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Nabídky nasazení SQL nezahrnuté v tomto průvodci

- Azure SQL Data Warehouse
- Virtuální počítače Azure SQL (IaaS)
- Místní SQL Server

### <a name="audience"></a>Cílová skupina

V této příručce jsou zamýšlení zákazníci, kteří čelí dotazům k zabezpečení Azure SQL Database. Role, které mají zájem v tomto článku o osvědčených postupech, zahrnují, ale ne omezení na:

- Architekti zabezpečení
- Správci zabezpečení
- Úředníci dodržování předpisů
- Úředníci ochrany osobních údajů
- Technici zabezpečení

### <a id="using"></a>Pomocí této příručky

Tento dokument je určený jako doprovodný průvodce pro naši stávající dokumentaci k [zabezpečení Azure SQL Database](sql-database-security-overview.md) .

Pokud není uvedeno jinak, doporučujeme, abyste provedli všechny osvědčené postupy uvedené v jednotlivých částech, abyste dosáhli příslušného cíle nebo požadavku. Pro splnění konkrétních standardů dodržování předpisů zabezpečení nebo osvědčených postupů jsou v části požadavky nebo cíle uvedeny důležité kontroly dodržování předpisů, ať už je to možné. Jedná se o standardy zabezpečení a předpisy, na které se odkazuje v tomto dokumentu:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, kryptografie
- [Postupy Microsoft Operational Security Assurance (osa)](https://www.microsoft.com/en-us/securityengineering/osa/practices): postupy #1 – 6 a #9
- [NIST speciální publikace 800-53 řízení zabezpečení](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Váš názor

Chystáme se dál aktualizovat doporučení a osvědčené postupy, které jsou tady uvedené. Zadáním odkazu na **zpětnou vazbu** na konci tohoto článku zadejte nebo opravte tento dokument.

## <a name="authentication"></a>Ověření

Ověřování je proces, který označuje, že uživatel vyžádá. Azure SQL Database podporuje dva typy ověřování:

- Ověřování pomocí SQL
- Ověřování Azure Active Directory

### <a name="central-management-for-identities"></a>Centrální Správa identit

Centrální Správa identit nabízí následující výhody:

- Spravujte účty skupin a řízení uživatelských oprávnění bez duplikování přihlašovacích jmen mezi servery Azure SQL Database a databázemi.
- Zjednodušená a flexibilní Správa oprávnění.
- Správa aplikací ve velkém měřítku.

**Jak implementovat**:

- Použijte ověřování pomocí Azure Active Directory (Azure AD) pro centralizovanou správu identit.

**Osvědčené postupy**:

- Vytvořte tenanta Azure AD a [Vytvořte uživatele, kteří](../active-directory/fundamentals/add-users-azure-active-directory.md) budou představovat lidské uživatele, a vytvořit [instanční objekty](../active-directory/develop/app-objects-and-service-principals.md) , které budou představovat aplikace, služby a nástroje pro automatizaci. Instanční objekty jsou stejné jako účty služeb v systému Windows a Linux. 

- Přiřazení přístupových práv k prostředkům k objektům zabezpečení Azure AD prostřednictvím přiřazení skupiny: Vytvoření skupin Azure AD, udělení přístupu ke skupinám a přidání jednotlivých členů do skupin. V databázi vytvořte uživatele databáze s omezením, kteří mapují vaše skupiny Azure AD. 
  - Projděte si články, [nakonfigurujte a spravujte Azure Active Directory ověřování pomocí SQL](sql-database-aad-authentication-configure.md) a [použijte Azure AD pro ověřování pomocí SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > Ve spravované instanci můžete také vytvořit přihlašovací údaje, které se mapují na objekty zabezpečení Azure AD v hlavní databázi. Viz [Create Login (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Použití skupin Azure AD zjednodušuje správu oprávnění a vlastníka skupiny a vlastník prostředku může přidat nebo odebrat členy do/ze skupiny. 

- Vytvořte samostatnou skupinu pro správce Azure AD pro servery SQL DB.

  - Projděte si článek [zřízení správce Azure Active Directory pro server Azure SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Sledujte změny členství ve skupinách Azure AD pomocí sestav aktivit auditu Azure AD. 

- Pro spravovanou instanci se k vytvoření správce Azure AD vyžaduje samostatný krok. 
  - Projděte si článek [zřízení správce Azure Active Directory pro vaši spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - Ověřování Azure AD se zaznamenává v protokolech auditu Azure SQL, ale ne v protokolech přihlášení služby Azure AD.
> - Oprávnění RBAC udělená v Azure se nevztahují na oprávnění služby Azure SQL DB. Tato oprávnění je nutné vytvořit nebo namapovat ručně v databázi SQL pomocí stávajících oprávnění SQL.
> - Na straně klienta ověřování Azure AD potřebuje přístup k Internetu nebo prostřednictvím uživatelsky definované trasy (UDR) do virtuální sítě.
> - Přístupový token Azure AD je uložený v mezipaměti na straně klienta a jeho životnost závisí na konfiguraci tokenu. Viz článek, [konfigurovatelné životnosti tokenů v Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> [!NOTE]
> Zmíněné v: #2 praxe metody OSA, ISO Access Control (AC)

Služba Azure Multi-Factor Authentication (MFA) pomáhá zvýšit zabezpečení tím, že vyžaduje více než jednu formu ověřování.

**Jak implementovat**:

- [Povolte vícefaktorové](../active-directory/authentication/concept-mfa-howitworks.md) ověřování ve službě Azure AD pomocí podmíněného přístupu a použijte interaktivní ověřování. 

- Alternativou je povolení vícefaktorového ověřování pro celou doménu Azure AD nebo AD.

**Osvědčené postupy**:

- Aktivujte podmíněný přístup ve službě Azure AD (vyžaduje předplatné Premium). 
  - Projděte si článek [podmíněný přístup ve službě Azure AD](../active-directory/conditional-access/overview.md).  

- Vytvořte skupiny Azure AD a povolte zásady vícefaktorového ověřování pro vybrané skupiny pomocí podmíněného přístupu Azure AD. 
  - Projděte si článek [Plánování nasazení podmíněného přístupu](../active-directory/conditional-access/plan-conditional-access.md). 

- Vícefaktorové ověřování se dá povolit pro celou službu Azure AD nebo pro celou službu Active Directory federované s Azure AD. 

- Pro SQL DB, kde se požaduje heslo interaktivně, se používá režim ověřování Azure AD Interactive, následovaný ověřováním MFA:      
  - Použijte univerzální ověřování v SSMS. Projděte si článek [použití vícefaktorového ověřování AAD s Azure SQL Database a Azure SQL Data Warehouse (podpora SSMS pro MFA)](sql-database-ssms-mfa-authentication.md).
  - Použijte interaktivní ověřování podporované v nástroji SQL Server Data Tools (SSDT). Přečtěte si článek [Azure Active Directory podpora SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Použijte další nástroje SQL podporující MFA. 
    - Podpora průvodce SSMS pro export/extrakci/nasazení databáze  
    - [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage): možnost '/UA ' 
    - [Nástroj Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility): možnost-G (interaktivní)
    - [nástroj BCP](https://docs.microsoft.com/sql/tools/bcp-utility): možnost-G (interaktivní) 

- Implementujte své aplikace pro připojení k Azure SQL Database pomocí interaktivního ověřování s podporou VÍCEFAKTOROVÉHO ověřování. 
  - Projděte si článek [připojení k Azure SQL Database s využitím Azure Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Tento režim ověřování vyžaduje identity založené na uživatelích. V případech, kdy se používá důvěryhodný model identity, který obchází individuální ověřování uživatelů Azure AD (např. použití spravované identity pro prostředky Azure), VÍCEFAKTOROVÉ ověřování neplatí.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimalizace použití ověřování založeného na heslech pro uživatele

> [!NOTE]
> Zmíněné v: #4 praxe metody OSA, ISO Access Control (AC)

Metody ověřování založené na heslech představují slabší formu ověřování. Přihlašovací údaje můžou být ohrožené nebo neuvedené omylem.

**Jak implementovat**:

- Použijte integrované ověřování Azure AD, které eliminuje používání hesel.

**Osvědčené postupy**:

- Použijte ověřování pomocí jednotného přihlašování s použitím přihlašovacích údajů Windows. Federovat místní doménu AD pomocí Azure AD a používejte integrované ověřování systému Windows (pro počítače připojené k doméně se službou Azure AD).
  - Přečtěte si článek [SSMS podpora integrovaného ověřování Azure AD](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimalizace použití ověřování založeného na heslech pro aplikace 

> [!NOTE]
> Zmíněné v: #4 praxe metody OSA, ISO Access Control (AC)

**Jak implementovat**:

- Povolte spravovanou identitu Azure. Můžete také použít integrované ověřování nebo ověřování založené na certifikátech. 

**Osvědčené postupy**:

- Používejte [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Spravovaná identita přiřazená systémem](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Spravovaná identita přiřazená uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Použití Azure SQL Database ze služby App Service se spravovanou identitou (bez změn kódu)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Pro aplikaci použijte ověřování na základě certifikátu. 
  - Podívejte se na tuto [ukázku kódu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Pro integrovanou federované doménu a počítač připojený k doméně použijte ověřování Azure AD (viz část výše).
  - Prohlédněte si [ukázkovou aplikaci pro integrované ověřování](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Ochrana hesel a tajných kódů

V případě, že se hesla nedaří, ujistěte se, že jsou zabezpečená.

**Jak implementovat**:

- Pomocí Azure Key Vault můžete ukládat hesla a tajné kódy. Kdykoli je to možné, použijte MFA pro Azure SQL Database s uživateli Azure AD.

**Osvědčené postupy**:

- Pokud se nemůžete vyhnout heslům nebo tajným klíčům, ukládejte si uživatelská hesla a tajné klíče aplikace do Azure Key Vault a spravujte přístup prostřednictvím zásad Key Vault přístupu. 

- Různé architektury pro vývoj aplikací můžou nabízet i mechanizmy specifické pro ochranu tajných kódů v aplikaci. Například: [ASP.NET Core App](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Použití ověřování SQL pro starší verze aplikací 

Ověřování SQL odkazuje na ověření uživatele při připojení k Azure SQL Database pomocí uživatelského jména a hesla. Na každém serveru SQL Database nebo spravované instanci se musí vytvořit přihlašovací jméno a v každé databázi se vytvoří uživatel.

**Jak implementovat**:

- Použijte ověřování SQL.

**Osvědčené postupy**:

- Jako správce serveru vytvořte přihlašovací jména a uživatele. Pokud nepoužíváte uživatelé databáze s omezením hesla, ukládají se všechna hesla do hlavní databáze.
  - Přečtěte si článek, [řízení a udělení přístupu k databázi SQL Database a SQL Data Warehouse](sql-database-manage-logins.md).

- Postupujte podle osvědčených postupů pro správu hesel:
  - Zadejte komplexní heslo, které se skládá z velkých a malých písmen, číslic (0-9) a nealfanumerických znaků (například $,!, # nebo%).
  - Používejte delší hesla místo kratších náhodně vybraných znaků.
  - Vynutili ruční změnu hesla nejméně každých 90 dní.

## <a name="access-management"></a>Správa přístupu

Správa přístupu je proces řízení a správy přístupu autorizovaných uživatelů a oprávnění k Azure SQL Database.

### <a name="implement-principle-of-least-privilege"></a>Implementace principu s nejnižšími oprávněními

> [!NOTE]
> Zmíněné v: FedRamp Controls AC-06, NIST: AC-6, #3 praxe

Princip nejnižších oprávnění, která by uživatelé neměli mít více oprávnění, než je potřeba k dokončení svých úkolů. Další informace najdete v článku [jenom dostatečná Správa](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Jak implementovat**:

Přiřaďte pouze potřebná [oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) k dokončení požadovaných úloh:

- Rovina dat SQL: 
    - Použití podrobných oprávnění a uživatelem definovaných databázových rolí (nebo serverových rolí v MI): 
        1. Vytvoření požadovaných rolí
            - [VYTVOŘIT ROLI](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [VYTVOŘIT ROLI SERVERU](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Vytvoření požadovaných uživatelů
            - [VYTVOŘIT UŽIVATELE](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Přidat uživatele jako členy do rolí 
            - [ZMĚNIT ROLI](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ROLE SERVERU ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Pak přiřaďte oprávnění k rolím. 
            - [UDĚLIT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Nezapomeňte nepřiřazovat uživatele k zbytečným rolím.

- V Azure Resource Manager:
  - Pokud jsou dostupné nebo vlastní role RBAC a přiřazují potřebná oprávnění, použijte předdefinované role.
    - [Předdefinované role pro Azure](../role-based-access-control/built-in-roles.md) 
    - [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)

**Osvědčené postupy**:

Následující osvědčené postupy jsou volitelné, ale výsledkem bude lepší správa a podpora vaší strategie zabezpečení: 

- Pokud je to možné, začněte s nejmenší možnou sadou oprávnění a začněte přidávat oprávnění jeden po druhém, pokud existuje skutečná nutnost (a odůvodnění), a to na rozdíl od opačného přístupu: pokaždé, když se postará o oprávnění, převezme krok za krokem. 

- Upustí od přiřazování oprávnění jednotlivým uživatelům. Používejte místo toho role (databáze nebo role serveru) konzistentně. Role značně pomáhají s vytvářením sestav a s oprávněními k odstraňování potíží. (Azure RBAC podporuje pouze přiřazení oprávnění prostřednictvím rolí.) 

- Předdefinované role použijte, pokud se oprávnění rolí shodují přesně s potřebnými oprávněními pro uživatele. Můžete přiřadit uživatele k více rolím. 

- Vytvoření a použití vlastních rolí, pokud předdefinované role udělují příliš mnoho nebo nedostatečná oprávnění. Typické role používané v praxi: 
  - Nasazení zabezpečení 
  - Správce 
  - Vývojář 
  - Pracovníci podpory 
  - Auditor 
  - Automatizované procesy 
  - koncový uživatel 

- Pamatujte, že oprávnění v nástroji SQL Server Database Engine lze použít pro následující obory. Menší rozsah, menší dopad udělených oprávnění: 
  - Server Azure SQL Database (speciální role v hlavní databázi) 
  - databáze 
  - Schéma (viz také: [schéma – návrh pro SQL Server: doporučení pro návrh schématu s ohledem na zabezpečení](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objekt (tabulka, zobrazení, procedura atd.) 
  > [!NOTE]
  > Nedoporučuje se uplatňovat oprávnění na úrovni objektu, protože tato úroveň přináší nepotřebnou složitost na celkovou implementaci. Pokud se rozhodnete použít oprávnění na úrovni objektu, měli byste je jasně zdokumentovat. Totéž platí pro oprávnění na úrovni sloupců, která se ve stejných důvodech ještě méně doporučují. Standardní pravidla pro [zamítnutí](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) se pro sloupce nevztahují.

- Proveďte pravidelné kontroly pomocí [posouzení ohrožení zabezpečení (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) k otestování příliš velkého počtu oprávnění.

### <a name="implement-separation-of-duties"></a>Implementovat oddělení povinností

> [!NOTE]
> Zmíněné v: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Oddělení povinností, označované také jako oddělení cel, popisuje nutnost rozdělit citlivé úkoly na více úkolů, které jsou přiřazeny různým uživatelům, obvykle, aby se zabránilo narušením dat.

**Jak implementovat**:

- Identifikujte požadovanou úroveň rozdělení povinností. Příklady: 
  - Mezi vývojovým a testovacím a produkčním prostředím 
  - Úkoly na úrovni správy s citlivým zabezpečením (DBA) vs – úlohy pro vývojáře. 
    - Příklady: auditor, vytváření zásad zabezpečení pro zabezpečení na úrovni role (RLS), implementace SQL Database objektů s oprávněním DDL.

- Identifikujte komplexní hierarchii uživatelů (a automatizovaných procesů), které přistupují k systému.

- Vytvořte role podle potřeb uživatelů a přiřaďte k rolím oprávnění. 
  - Pro úlohy na úrovni správy v Azure Portal nebo prostřednictvím PowerShellu – automatizace používá role RBAC. Vyhledejte integrovanou roli, která odpovídá požadavku, nebo vytvořte vlastní roli RBAC pomocí dostupných oprávnění. 
  - Vytvořte role serveru pro úlohy na úrovni serveru (vytváření nových přihlašovacích údajů, databází) ve spravované instanci. 
  - Vytváření databázových rolí pro úlohy na úrovni databáze.

- U určitých citlivých úloh zvažte vytvoření speciálních uložených procedur podepsaných certifikátem k provedení úkolů jménem uživatelů. 
  - Příklad: [kurz: podepisování uložených procedur s certifikátem](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementací transparentní šifrování dat (TDE) pomocí klíčů spravovaných zákazníkem v Azure Key Vault umožní oddělení povinností mezi vlastníkem dat a vlastníkem zabezpečení. 
  - Projděte si článek [konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure Portal](../storage/common/storage-encryption-keys-portal.md). 

- Aby se zajistilo, že správce databáze nemůže zobrazit data, která jsou považována za vysoce citlivá a mohou i nadále provádět úlohy DBA, můžete použít Always Encrypted s oddělením rolí. 
  - Přečtěte si články, [Přehled správy klíčů pro Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [zřizování klíčů s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)a [rotaci hlavního klíče sloupce s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- V případech, kdy to není možné, aniž by to bylo možné, aniž by došlo k zásadním nákladům a úsilím, které by mohly vykreslovat systém v nepoužitelném režimu, může dojít k ohrožení zabezpečení a k omezení využití kompenzačních ovládacích prvků, 
  - Lidské intervence v procesech. 
  - Záznamy auditu – Další informace o auditování najdete v tématu [Auditovat kritické události zabezpečení](#audit-critical-security-events).

**Osvědčené postupy**:

- Zajistěte, aby se pro vývojové a testovací a produkční prostředí používaly různé účty. Různé účty vám pomůžou dodržovat oddělení testovacích & produkčních systémů.

- Upustí od přiřazování oprávnění jednotlivým uživatelům. Používejte místo toho role (databáze nebo role serveru) konzistentně. Role, které mají roli, značně pomáhají při vytváření sestav a řešení potíží.

- Předdefinované role použijte, pokud se oprávnění shodují přesně s potřebnými oprávněními – Pokud sjednocení všech oprávnění z několika předdefinovaných rolí vede k 100%, můžete přiřadit více rolí současně. 

- Vytvoření a použití vlastních rolí, pokud předdefinované role udělují příliš mnoho oprávnění nebo nedostatečná oprávnění. 

- Přiřazení rolí se taky dá dělat dočasně, označované také jako dynamické oddělení povinností (DSD), a to v rámci kroků úloh agenta SQL v T-SQL nebo pomocí Azure PIM pro role RBAC. 

- Ujistěte se, že specializující nemá přístup k šifrovacím klíčům nebo k úložištím klíčů a že správci zabezpečení s přístupem ke klíčům nemají přístup k databázi. 

- Vždy nezapomeňte mít záznam auditu pro akce související se zabezpečením. 

- Definici předdefinovaných rolí RBAC můžete načíst, abyste viděli oprávnění, která se používají, a vytvořit vlastní roli na základě výňatků a kumulace těchto funkcí prostřednictvím PowerShellu. 

- Vzhledem k tomu, že kterýkoli člen role db_owner Database může změnit nastavení zabezpečení jako transparentní šifrování dat (TDE) nebo změnit objekt SLO, mělo by být toto členství uděleno opatrně. Na druhé straně mnoho úkolů, jako je změna nastavení databáze, jako je například změna možností DB, vyžaduje db_owner oprávnění. Auditování hraje klíčovou roli v jakémkoli řešení.

- Není možné udržet db_owner zobrazovat data uživatelů jenom s oprávněními. Pokud je v databázi vysoce citlivá data, Always Encrypted můžete použít k bezpečnému zabránění db_owners nebo jiným DBA v jeho zobrazení.

> [!NOTE]
> Dosažení oddělení povinností (SoD) je náročné na úlohy související se zabezpečením nebo při odstraňování potíží. Jiné oblasti, jako je vývoj a role koncového uživatele, je jednodušší oddělení. Většina ovládacích prvků souvisejících s dodržováním předpisů umožňuje použití alternativních funkcí ovládacího prvku, jako je například auditování, pokud jiná řešení nejsou praktická.

Pro čtenáře, kteří chtějí podrobně hlouběji do SoD, doporučujeme následující zdroje: 

- Pro Azure SQL Database:  
  - [Řízení a udělení přístupu k databázi SQL Database a SQL Data Warehouse](sql-database-manage-logins.md)
  - [Oddělení povinností pro vývojáře aplikací](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Oddělení povinností v SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Podepisování uložených procedur v SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Pro správu prostředků Azure:
  - [Předdefinované role pro Azure](../role-based-access-control/built-in-roles.md) 
  - [Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md)
  - [Použití Azure AD Privileged Identity Management pro zvýšení úrovně přístupu](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Provádět běžné revize kódu

> [!NOTE]
> Zmíněno v: PCI: 6.3.2, SOC: SDL-3 

Oddělení povinností není omezeno pouze na data v databázi, ale zahrnuje kód aplikace. Škodlivý kód může potenciálně obejít ovládací prvky zabezpečení. Před nasazením vlastního kódu do produkčního prostředí je důležité zkontrolovat, co se nasazuje.

**Jak implementovat**:

- Použijte databázový nástroj, jako je Azure Data Studio, který podporuje správu zdrojového kódu. 

- Implementujte proces nasazení odděleného kódu.

- Před potvrzením hlavní větve musí osoba (jiná než Autor samotného kódu) kontrolovat kód pro potenciální zvýšení rizika oprávnění a také úpravy škodlivých dat pro ochranu před podvody a neautorizovaným přístupem. To lze provést pomocí mechanismů správy zdrojového kódu.

**Osvědčené postupy**:

- Standardizace: pomáhá implementovat standardní proceduru, která má být následována všemi aktualizacemi kódu. 

- Posouzení ohrožení zabezpečení obsahuje pravidla, která kontrolují nadměrné oprávnění, použití starých šifrovacích algoritmů a další problémy se zabezpečením v rámci schématu databáze. 

- Další kontroly je možné provést v prostředí pro kontrolu a odpovědi pomocí rozšířené ochrany před internetovými útoky, které vyhledává kód, který je zranitelný proti injektáže SQL.

- Příklady toho, co se má vyhledat: 
  - Vytvoření uživatele nebo změna nastavení zabezpečení v rámci automatizovaného nasazení aktualizace kódu SQL. 
  - Uloženou proceduru, která v závislosti na zadaných parametrech aktualizuje peněžní hodnotu v buňce nevyhovujícím způsobem. 

- Ujistěte se, že osoba provádějící kontrolu je samostatná, než autor kódu, který je v revizi kódu a v zabezpečeném kódování.

- Nezapomeňte znát všechny zdroje změn kódu: kód může být ve skriptech T-SQL. může se jednat o ad hoc příkazy, které se mají spustit nebo nasadit, a to formou zobrazení, funkcí, triggerů a uložených procedur. Může být také součástí definicí úloh agenta SQL (kroky), musí být spouštěna z balíčků SSIS, Azure Data Factory a dalších služeb.

## <a name="data-protection"></a>Ochrana dat

Ochrana dat je sada funkcí pro zabezpečení důležitých informací před ohrožením šifrováním nebo zašifrováním.

> [!NOTE]
> Microsoft potvrzuje, že Azure SQL Database kompatibilní se standardem FIPS 140-2 úrovně 1. To se provádí po ověření striktního použití algoritmů FIPS 140-2 úrovně 1 a ověřených instancí FIPS 140-2 úrovně 1, včetně konzistence s požadovanými délkami klíčů, správou klíčů, generováním klíčů a úložištěm klíčů. Účelem tohoto ověření je, aby naši zákazníci mohli reagovat na potřebu nebo požadavek na použití ověřených instancí FIPS 140-2 úrovně 1 při zpracování dat nebo doručování systémů nebo aplikací. V příkazu výše definujeme podmínky dodržování předpisů FIPS 140-2 Level 1 a Standard FIPS 140-2 úrovně 1, které předvádějí zamýšlenou použitelnost v oblasti USA a Kanada pro použití v různých výrazech "FIPS 140-2 Level 1". 


### <a name="encrypt-data-in-transit"></a>Šifrovat data při přenosu

> [!NOTE]
> V tomto postupu: #6 postupů pro OSA, rodina ovládacích prvků ISO: kryptografie

Chrání data při přesunu dat mezi klientem a serverem. Přečtěte si téma [zabezpečení sítě](#network-security).

### <a name="encrypt-data-at-rest"></a>Šifrování dat v klidovém umístění

> [!NOTE]
> V tomto postupu: #6 postupů pro OSA, rodina ovládacích prvků ISO: kryptografie

Šifrování v klidovém případě je kryptografická ochrana dat, když je trvalá v databázi, protokolu a souborech zálohy.

**Jak implementovat**:

- [Transparentní šifrování databáze (TDE)](transparent-data-encryption-azure-sql.md) se spravovanými klíči služby je ve výchozím nastavení povolené pro všechny databáze vytvořené po 2017 v Azure SQL Database.
- Pokud je ve spravované instanci databáze vytvořená v důsledku operace obnovení z místního serveru, bude dodrženo nastavení TDE původní databáze. Pokud původní databáze nemá povolené TDE, doporučujeme, aby se pro spravovanou instanci TDE ručně aktivovaly.

**Osvědčené postupy**:

- Neukládejte data, která vyžadují šifrování v hlavní databázi. Hlavní databázi nelze zašifrovat pomocí TDE.

- Klíče spravované zákazníkem v Azure Key Vault použijte, pokud potřebujete větší transparentnost a přesnější kontrolu nad ochranou TDE. Azure Key Vault umožňuje kdykoli odvolat oprávnění pro vykreslování databáze jako nedostupné. Ochranu TDE můžete centrálně spravovat společně s ostatními klíči, nebo můžete TDE ochranu pomocí Azure Key Vault sami otočit podle vlastního plánu.

- Pokud používáte klíče spravované zákazníkem v Azure Key Vault, postupujte podle článků, [pokyny pro konfiguraci TDE s Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) a [Postup konfigurace geografického Dr s Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Chránit citlivá data při použití z vysoce privilegovaných nebo neautorizovaných uživatelů

Použitá data jsou data uložená v paměti databázového systému během provádění dotazů SQL. Pokud vaše databáze uchovává citlivá data, může být potřeba, abyste zajistili, že uživatelé s vysokými oprávněními, jako jsou například operátoři Microsoftu nebo specializující ve vaší organizaci, brání extrakci dat z paměti procesu SQL Server a nemůžou zobrazení dat ve formátu prostého textu při dotazování databáze.

**Jak implementovat**:

- Pomocí [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) zajistěte, aby citlivá data nebyla vystavená ve formě prostého textu v Azure SQL Database, a to ani v paměti. Tím se chrání data od správců databází (specializující) a cloudových správců (nebo špatných aktérů, které mohou zosobnit vysoce privilegované uživatele) a poskytují větší kontrolu nad tím, kdo má přístup k vašim datům.

**Osvědčené postupy**:

- Always Encrypted není náhradou za zašifrování dat v klidovém režimu (TDE) nebo v přenosu (SSL/TLS), a proto by se neměla používat pro data, která nejsou citlivá, aby se minimalizoval dopad na výkon a funkčnost. Použití Always Encrypted ve spojení s TDE a TLS se doporučuje pro komplexní ochranu neaktivních dat v klidovém provozu a při přenosu. 

- Pokud používáte Always Encrypted k ochraně dat před škodlivými specializující, spravujte Always Encrypted klíče pomocí oddělování rolí. Při oddělování rolí vytvoří Správce zabezpečení fyzické klíče a správce databáze vytvoří objekty metadat hlavního klíče sloupce a šifrovacího klíče sloupce s popisem fyzických klíčů v databázi. Během tohoto procesu správce zabezpečení nepotřebuje přístup k databázi a DBA nebude potřebovat přístup k fyzickým klíčům ve formátu prostého textu. 
  - Podrobnosti najdete v článku [Správa klíčů pomocí oddělení rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) . 

- Uložte hlavní klíče sloupců do Azure Key Vault pro usnadnění správy. Nepoužívejte úložiště certifikátů systému Windows (a obecně distribuovaná řešení úložiště klíčů, jako u jiných řešení správy klíčů nepoužíváme), která umožňují správu klíčů pevně. 

- Pečlivě si promyslete kompromisy používání více klíčů (hlavní klíč sloupce nebo šifrovací klíče sloupců). Snižte počet klíčů, abyste snížili náklady na správu klíčů. Jeden hlavní klíč sloupce a jeden sloupec šifrovací klíč pro každou databázi většinou postačuje v prostředích s ustáleným stavem (ne uprostřed střídání klíčů), pokud nepoužíváte různé skupiny uživatelů, z nichž každý používá různé klíče a přistupuje k různým datům.  

- Otočí hlavní klíče sloupce podle vašich požadavků na dodržování předpisů. Pokud potřebujete také přetočit šifrovací klíče sloupce, zvažte použití online šifrování k minimalizaci výpadku aplikace. 
  - Informace najdete v článku věnovaném [důležitým informacím o výkonu a dostupnosti](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Pokud je potřeba, aby se výpočty (rovnost) dat podporovaly, použijte deterministické šifrování. V opačném případě používejte náhodné šifrování. Vyhněte se použití deterministického šifrování pro datové sady s nízkou entropií nebo datových sad s veřejně známou distribucí. 

- Pokud máte obavy, že třetí strana přistupuje k vašim datům právně bez vašeho souhlasu, zajistěte, aby všechny aplikace a nástroje, které mají přístup k klíčům a datům v prostém textu, běžely mimo Microsoft Azure Cloud. Bez přístupu k klíčům nebude mít třetí strana možnost dešifrovat data, pokud obcházejí šifrování.

- Always Encrypted neumožňuje snadno udělit dočasný přístup k klíčům (a chráněným datům). Pokud například potřebujete sdílet klíče se službou DBA, aby mohl správce služby provádět některé čisticí operace u citlivých a šifrovaných dat, jediným způsobem, jak spolehlivost zrušit přístup k datům z databáze DBA, je použít k otočení šifrovacích klíčů sloupců i sloupce. MN hlavní klíče chránící data, což je náročná operace. 

- Aby bylo možné získat přístup k hodnotám v podobě prostého textu v šifrovaných sloupcích, musí mít uživatel přístup k CMK, který chrání sloupce, které jsou nakonfigurované v úložišti klíčů, který obsahuje CMK. Kromě toho musí mít uživatel k dispozici definici hlavního klíče sloupce a zobrazení všech oprávnění pro databázi definic ŠIFROVACÍho klíče.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Řízení přístupu uživatelů aplikací k citlivým datům prostřednictvím šifrování

Šifrování lze použít jako způsob, jak zajistit, aby mohli data zobrazit nebo aktualizovat pouze konkrétní uživatelé aplikace, kteří mají přístup k kryptografickým klíčům.

**Jak implementovat**:

- Použijte šifrování na úrovni buňky (CLE). Podrobnosti najdete v článku o [šifrování sloupce dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) . 
- Alternativně zvažte použití Always Encrypted, ale mějte na paměti, že je uvedeno níže.

**Osvědčené postupy**

Při použití CLE:

- Řízení přístupu k klíčům prostřednictvím oprávnění a rolí SQL. 

- Pro šifrování dat použijte AES (doporučeno AES 256). Algoritmy, například ŠIFRy, DES a TripleDES, jsou zastaralé a neměly by se používat v důsledku známých chyb zabezpečení. 

- Chraňte symetrické klíče pomocí asymetrických klíčů/certifikátů (nikoli hesel), abyste se vyhnuli používání algoritmu 3DES. 

- Buďte opatrní při migraci databáze pomocí šifrování na úrovni buňky prostřednictvím exportu/importu (soubory BacPac). 
  - Informace o tom, jak zabránit ztrátě klíčů při migraci dat a další pokyny k osvědčeným postupům, najdete v článku [doporučení pro používání šifrování na úrovni buněk v Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) .

Při použití Always Encrypted Pamatujte na to, že Always Encrypted je primárně navržená tak, aby chránila citlivá data při použití od uživatelů s vysokou úrovní oprávnění Azure SQL Database (cloudové operátory, specializující) – viz [chránit citlivá data při použití z vysoce privilegovaných nebo neautorizovaných uživatelů](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Při použití Always Encrypted k ochraně dat před uživateli aplikace Pamatujte na následující problémy:

- Když uživatelům udělíte přístup k citlivým datům tím, že jim poskytnete oprávnění k přístupu k šifrovacímu klíči sloupce a k hlavnímu klíči sloupce, abyste mohli spolehlivě odvolat tento přístup, který potřebujete k otočení šifrovacího klíče sloupce, což je náročná operace, která vyžaduje nové šifrování všech sloupců chrání šifrovací klíč sloupce. 

- Ve výchozím nastavení všechny ovladače klienta Microsoftu podporující Always Encrypted udržují globální mezipaměť (jedna na každou aplikaci) pro šifrovací klíče sloupce. Když ovladač klienta načte šifrovací klíč sloupce ve formátu prostého textu, kontaktujte úložiště klíčů obsahující hlavní klíč sloupce a šifrovací klíč sloupce ve formátu prostého textu se uloží do mezipaměti, což způsobí, že izolaci dat od uživatelů aplikace pro více uživatelů budou náročné. Pokud se vaše aplikace při interakci s úložištěm klíčů (například Azure Key Vault) zosobňuje koncovým uživatelům, poté, co dotaz uživatele naplní mezipaměť pomocí šifrovacího klíče sloupce, pak následující dotaz, který vyžaduje stejný klíč, ale spustí jiný uživatel, použije klíč uložený v mezipaměti. Ovladač nebude volat úložiště klíčů a nebude kontrolovat, zda má druhý uživatel oprávnění pro přístup k šifrovacímu klíči sloupce. V důsledku toho bude uživatel moci zobrazit šifrovaná data i v případě, že uživatel nemá přístup k klíčům. Aby bylo možné zajistit izolaci uživatelů v rámci aplikace pro více uživatelů, možná budete muset zakázat ukládání šifrovacích klíčů sloupce do mezipaměti, což způsobí další zvýšení výkonu, protože ovladač bude potřebovat kontaktovat úložiště klíčů pro každé šifrování nebo dešifrování dat. NázevOperace.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Ochrana dat před neoprávněným zobrazením uživateli aplikace při zachování formátu dat
Další způsob, jak zabránit neautorizovaným uživatelům v zobrazení dat, je zakrýt nebo maskovat data při zachování datových typů a formátů, abyste zajistili, že uživatelské aplikace budou moci pokračovat v manipulaci a zobrazovat data.

**Jak implementovat**:

- Pomocí [dynamického maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) můžete vymezit sloupce tabulky.

> [!NOTE]
> Always Encrypted nepracuje s dynamickým maskování dat. Nelze zašifrovat a maskovat stejný sloupec, což znamená, že je třeba nastavit prioritu ochrany dat při použití vs. maskování dat pro uživatele aplikace pomocí dynamického maskování dat.

**Osvědčené postupy**:

> [!NOTE]
> Dynamické maskování dat nelze použít k ochraně dat před uživateli s vysokou úrovní oprávnění. Zásady maskování se nevztahují na uživatele s přístupem správce, jako je db_owner.

- Nepovolte uživatelům aplikace spouštění dotazů ad hoc (protože můžou pracovat s maskou dynamických dat).  
  - Podrobnosti najdete v článku [obejití maskování s využitím odvození nebo hrubou silou](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) .  

- Pomocí správných zásad řízení přístupu (prostřednictvím oprávnění SQL, rolí, RLS) můžete omezit uživatelská oprávnění k provádění aktualizací v maskovaných sloupcích. Vytvořením masky u sloupce se nebrání aktualizace tohoto sloupce. Proto i když uživatelé při dotazování maskovaného sloupce obdrží maskovaná data, mohou uživatelé aktualizovat data v případě, že mají oprávnění k zápisu.    

-  Dynamické maskování dat nezachovává statistické vlastnosti maskovaných hodnot, což může mít vliv na výsledky dotazu (například dotazy obsahující predikáty filtrování nebo spojení s maskovanými daty).

## <a name="network-security"></a>Zabezpečení sítě
Zabezpečení sítě odkazuje na řízení přístupu a osvědčené postupy pro zabezpečení vašich dat při přenosu do Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Konfigurace klienta pro zabezpečené připojení k Azure SQL Database 
Zabránit klientským počítačům a aplikacím, aby se připojovaly k Azure SQL Database z známých chyb zabezpečení z důvodu závislosti na starších protokolech a šifrovacích sadách.

**Jak implementovat**:

- Zajistěte, aby se klientské počítače připojující se k Azure SQL Database používaly protokol [TLS (Transport Layer Security)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Osvědčené postupy**:

- Konfigurace všech aplikací a nástrojů pro připojení k databázi SQL s povoleným šifrováním 
  - Encrypt = on, TrustServerCertificate = off (nebo ekvivalent u ovladačů od jiných výrobců než od Microsoftu). 

- Pokud vaše aplikace používá ovladač, který nepodporuje protokol TLS nebo podporuje starší verzi TLS, nahraďte ovladač, pokud je to možné. Pokud není to možné, pečlivě vyhodnoťte bezpečnostní rizika. 

- Zmenšení vektorů útoku prostřednictvím zabezpečení SSL 2,0, SSL 3,0, TLS 1,0 a TLS 1,1 tím, že je zakážete v klientských počítačích, které se připojují k Azure SQL Database [nastavení registru TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10). 

- Kontroluje šifrovací sady, které jsou k dispozici na klientech pro [šifrovací sady v TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) , a konkrétně zakažte 3Des na [konfiguraci pořadí šifrovací sady TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Pokud používáte spravovanou instanci, použijte typ připojení **proxy** (výchozí), protože toto nastavení vynutilo šifrování ze strany serveru. Typ připojení **přesměrování** aktuálně nepodporuje vynucování šifrování a je dostupný jenom u privátních IP připojení. 
  - Další informace najdete v tématu [Architektura připojení Azure SQL – zásady připojení](sql-database-connectivity-architecture.md#connection-policy).
  - Pro proxy a přesměrování typu připojení se vynutilo šifrování. 

### <a name="minimize-attack-surface"></a>Minimalizace prostoru pro útoky
Pomocí implementace ovládacích prvků přístupu k síti pro Azure SQL Database Minimalizujte počet funkcí, které mohou být napadeny uživatelem se zlými úmysly.

> [!NOTE]
> V tomto postupu jsou uvedené v: #5 postupů pro OSA

**Jak implementovat**:

Na serveru Azure SQL Database (obsahující databázi s jedním nebo elastickým fondy):
- Nastavte možnost povolení přístupu ke službám Azure na OFF.

- Použijte koncové body služby virtuální sítě a pravidla brány firewall virtuální sítě.

- Použijte privátní odkaz (Preview).

Ve spravované instanci:
- Postupujte podle pokynů v části [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Osvědčené postupy**:

- Omezení přístupu k Azure SQL Database připojením k privátnímu koncovému bodu (například pomocí cesty privátních dat): 
  - Spravovaná instance může být izolovaná v rámci virtuální sítě, aby se zabránilo externímu přístupu. Aplikace a nástroje, které jsou umístěné ve stejné nebo partnerské virtuální síti ve stejné oblasti, by k ní měli přímý přístup. Aplikace a nástroje, které jsou v jiné oblasti, můžou využít připojení VNet-to-VNet nebo partnerského okruhu ExpressRoute k navázání připojení. Zákazník by měl používat skupiny zabezpečení sítě (NSG) k omezení přístupu přes port 1433 jenom na prostředky, které vyžadují přístup ke spravované instanci. 
  - U SQL Databaseho serveru (obsahujícího jednotlivé databáze nebo elastické fondy) použijte funkci [privátního odkazu](sql-database-private-endpoint-overview.md) , která poskytuje vyhrazenou privátní IP adresu pro SQL Database Server v rámci vaší virtuální sítě. Můžete také použít [koncové body služby virtuální sítě s pravidly brány firewall virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) , abyste omezili přístup k serverům SQL Database.
  - Mobilní uživatelé by měli používat připojení VPN typu Point-to-site pro připojení přes cestu k datům.
  - Uživatelé připojení k místní síti by měli používat připojení VPN typu Site-to-site nebo ExpressRoute k připojení přes cestu k datům.

- K Azure SQL Database můžete přistupovat připojením k veřejnému koncovému bodu (například pomocí cesty veřejných dat). Je třeba vzít v úvahu následující osvědčené postupy: 
  - U SQL Database serveru použijte [pravidla brány firewall protokolu IP](sql-database-firewall-configure.md) pro omezení přístupu pouze k autorizovaným IP adresám.
  - Pro spravovanou instanci použijte skupiny zabezpečení sítě (NSG) k omezení přístupu přes port 3342 jenom na požadované prostředky. Další informace najdete v tématu [bezpečné použití spravované instance Azure SQL Database s veřejnými koncovými body](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Veřejný koncový bod spravované instance není ve výchozím nastavení povolený a musí být explicitně povolený. Pokud zásady společnosti nepovolují použití veřejných koncových bodů, použijte [Azure Policy](../governance/policy/overview.md) , abyste zabránili povolení veřejných koncových bodů na prvním místě.

- Nastavení síťových součástí Azure: 
  - Postupujte podle [osvědčených postupů Azure pro zabezpečení sítě](../security/fundamentals/network-best-practices.md).
  - Naplánujte konfiguraci Virtual Network (VNet) podle osvědčených postupů popsaných v tématu [Azure Virtual Network Nejčastější dotazy](../virtual-network/virtual-networks-faq.md) a plán. 
  - Segmentujte virtuální síť do několika podsítí a přiřaďte prostředky pro podobnou roli ke stejné podsíti (například k prostředkům front-endu vs back-end).
  - Použijte [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../virtual-network/security-overview.md) k řízení provozu mezi podsítěmi uvnitř hranice virtuální sítě Azure.
  - Povolte [službě Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) pro vaše předplatné monitorování příchozího a odchozího síťového provozu.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Konfigurace Power BI pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- U Power BI Desktop používejte cestu k privátním datům, kdykoli to bude možné. 

- Ujistěte se, že se Power BI Desktop připojuje pomocí protokolu TLS 1.2 nastavením klíče registru na klientském počítači podle nastavení registru [TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) . 

- Omezte přístup k datům pro konkrétní uživatele přes [zabezpečení na úrovni řádků (RLS) pomocí Power BI](https://docs.microsoft.com/power-bi/service-admin-rls). 

- U Power BI služby použijte [místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-onprem)a zapamatujte si [omezení a požadavky](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Konfigurace App Service pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- U jednoduchých webových aplikací, které se připojují přes Veřejný koncový bod, je nutné, aby nastavení **umožňovalo službám Azure** zapnuto. 

- [Integrujte svoji aplikaci s Virtual Network Azure](../app-service/web-sites-integrate-with-vnet.md) pro připojení k privátním datům do spravované instance. Volitelně můžete také nasadit webovou aplikaci pomocí [prostředí App Service (POmocného programu)](../app-service/environment/intro.md). 

- Pro webovou aplikaci s integrovanou webovou aplikací pro samoobslužné přihlašování nebo virtuální síť, která se připojuje k databázi na serveru SQL Database, můžete použít [koncové body služby virtuální sítě a pravidla brány firewall virtuální](sql-database-vnet-service-endpoint-rule-overview.md) sítě pro omezení přístupu z konkrétní virtuální sítě a podsítě a pak nastavit možnost **Povolení služeb Azure** na off. Můžete taky připojit pomocného mechanismu ke spravované instanci přes cestu k privátním datům.  

- Ujistěte se, že je vaše webová aplikace nakonfigurovaná na základě článku, [osvědčené postupy pro zabezpečení webových a mobilních aplikací PaaS pomocí Azure App Service](../security/security-paas-applications-using-app-services.md). 

- Instalace [firewallu webových aplikací (WAF)](../application-gateway/waf-overview.md) pro ochranu webové aplikace před běžnými zneužitím a ohrožením zabezpečení.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Konfigurace hostování virtuálních počítačů Azure pro zabezpečená připojení k Azure SQL Database

**Osvědčené postupy**:

- Použijte kombinaci pravidel povolení a odmítnutí na skupin zabezpečení sítě virtuálních počítačů Azure, abyste mohli řídit, které oblasti jsou dostupné z virtuálního počítače.

- Ujistěte se, že váš virtuální počítač je nakonfigurovaný podle článku, což je [osvědčené postupy zabezpečení pro IaaS úlohy v Azure](../security/azure-security-iaas.md).

- Ujistěte se, že jsou všechny virtuální počítače přidružené k určité virtuální síti a podsíti. 

- Vyhodnoťte, jestli v [souvislosti s vynuceným tunelovým propojením](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)potřebujete výchozí trasu 0.0.0.0/Internet podle pokynů. 
  - Pokud ano – například podsíť front-end – potom ponechte výchozí trasu.
  - Pokud ne, například střední úroveň nebo podsíť back-end, povolte vynucené tunelování, aby žádný přenos přes Internet nepřešel do místního prostředí (a. k. a mezi různými místy). 

- Implementujte [Volitelné výchozí trasy](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) , pokud používáte partnerský vztah nebo se připojujete místně. 

- Implementujte [uživatelsky definované trasy](../virtual-network/virtual-networks-udr-overview.md#user-defined) , pokud potřebujete odeslat veškerý provoz ve virtuální síti do síťového virtuálního zařízení pro kontrolu paketů. 

- Pomocí [koncových bodů služby virtuální](sql-database-vnet-service-endpoint-rule-overview.md) sítě získáte zabezpečený přístup k PaaS službám, jako je Azure Storage prostřednictvím páteřní sítě Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Ochrana před distribuovanými útoky s cílem odepření služeb (DDoS)
Útok na distribuovaný útok DoS (DDoS) se snaží uživatel se zlými úmysly odeslat zahlcení síťového provozu, aby se Azure SQL Database s cílem zahlcení infrastruktury Azure a způsobila odmítnutí platných přihlašovacích údajů a úloh.

> [!NOTE]
> V tomto postupu jsou uvedené v: #9 postupů pro OSA

**Jak implementovat**:

DDoS Protection se automaticky povolí jako součást platformy Azure. Zahrnuje nepřetržité monitorování provozu a zmírnění útoků na úrovni sítě u veřejných koncových bodů v reálném čase. 

- Pomocí [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) můžete monitorovat veřejné IP adresy přidružené k prostředkům nasazeným ve virtuálních sítích.

- Použití [rozšířené ochrany před internetovými útoky pro Azure SQL Database](sql-database-threat-detection-overview.md) k detekci útoků DOS (Denial of Service) na databáze.

**Osvědčené postupy**:

- Postupujte podle postupů popsaných v části [minimalizace možností útoku](#minimize-attack-surface) , které pomáhají minimalizovat DDoS hrozby útoku. 

- Výstraha rozšířené **přihlašovací údaje SQL** rozšířené ochrany před hrozbami pomáhá detekovat útoky hrubou silou. V některých případech může výstraha dokonce rozlišovat úlohy testování průniku. 

- Pro hostitelské aplikace virtuálních počítačů Azure, které se připojují k SQL Database: 
  - Postupujte podle doporučení pro omezení přístupu prostřednictvím internetových koncových bodů v Azure Security Center. 
  - Pomocí služby Virtual Machine Scale Sets můžete na virtuálních počítačích Azure spouštět více instancí aplikace. 
  - Zakažte RDP a SSH z Internetu, aby nedocházelo k útokům hrubou silou. 

## <a name="monitoring-logging-and-auditing"></a>Sledování, protokolování a auditování  
V této části se dozvíte o funkcích, které vám pomůžou detekovat neobvyklé aktivity indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Popisuje také osvědčené postupy konfigurace auditování databáze pro sledování a zachycení databázových událostí.

### <a name="protect-databases-against-attacks"></a>Ochrana databází proti útokům 
Rozšířená ochrana před internetovými útoky umožňuje rozpoznávat a reagovat na potenciální hrozby, když k nim dojde, a poskytuje tak výstrahy zabezpečení pro aktivity neobvyklé.

**Jak implementovat**:

- Použití [rozšířené ochrany před internetovými útoky pro SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) ke zjištění neobvyklých a potenciálně škodlivých pokusů o přístup k databázím nebo jejich zneužití:
  - Útok prostřednictvím injektáže SQL.
  - Krádež nebo únik přihlašovacích údajů.
  - Zneužití oprávnění
  - Exfiltrace dat.

**Osvědčené postupy**:

- Konfigurace [pokročilých zabezpečení dat](sql-database-advanced-data-security.md#getting-started-with-ads) pro Azure SQL Database pro konkrétní SQL Database Server nebo spravovanou instanci nebo pro všechny servery SQL Database a spravované instance v rámci předplatného přepnutím na [Azure Security Center úrovně Standard](../security-center/security-center-pricing.md). 

- V případě úplného šetření doporučujeme povolit [SQL Database auditování](sql-database-auditing.md) pro sledování událostí databáze a jejich zápis do protokolu auditu v účtu Azure Storage nebo v pracovním prostoru Azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Auditovat kritické události zabezpečení
Sledování událostí databáze vám pomůže pochopit databázovou činnost a získat přehled o nesrovnalostech a anomáliích, které by mohly označovat obchodní aspekty nebo podezření na narušení zabezpečení. Také umožňuje a usnadňuje dodržování standardů dodržování předpisů. 

**Jak implementovat**:

- Povolte [auditování SQL Database](sql-database-auditing.md) ke sledování událostí databáze a jejich zápis do protokolu auditu ve vašem účtu Azure Storage, Log Analytics pracovní prostor (Preview) nebo Event Hubs (Preview). 

- Protokoly auditu se dají zapisovat do účtu Azure Storage, do Log Analytics pracovního prostoru pro spotřebu Azure Monitor protokoly nebo do centra událostí pro spotřebu pomocí centra událostí. Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu se zapíší do každého z nich. 

**Osvědčené postupy**:

- Konfigurací [SQL Database auditování](sql-database-auditing.md) na databázovém serveru pro auditování událostí se budou auditovat všechny stávající a nově vytvořené databáze na tomto serveru.
- Zásady auditování ve výchozím nastavení zahrnují všechny akce (dotazy, uložené procedury a úspěšná a neúspěšná přihlášení) proti databázím, což může vést k velkému objemu protokolů auditu. Zákazníkům se doporučuje [nakonfigurovat auditování pro různé typy akcí a skupin akcí pomocí PowerShellu](sql-database-auditing.md#subheading-7), abyste mohli řídit počet auditovaných akcí a minimalizovali riziko ztráty událostí. To umožní zákazníkům zachytit jenom skutečně potřebná data auditu.
- Protokoly auditu je možné spotřebovat přímo v [Azure Portal](https://portal.azure.com/)nebo z umístění úložiště, které jste nakonfigurovali. 


> [!NOTE]
> Povolením auditování pro Log Analytics se účtují náklady na základě sazeb ingestování. Pomocí této [Možnosti](https://azure.microsoft.com/pricing/details/monitor/)si pamatujte na související náklady, nebo zvažte uložení protokolů auditu v účtu úložiště Azure. 

**Další zdroje informací**:

- [Auditování SQL Database](sql-database-auditing.md)
- [Auditování SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Protokoly zabezpečeného auditu
Aby bylo možné podporovat oddělení povinností a oddělení DBA od auditorů, je klíč, který přijímá míry pro omezení přístupu k účtu úložiště. 

**Jak implementovat**:

- Když ukládáte protokoly auditu k Azure Storage Ujistěte se, že přístup k účtu úložiště je omezený na minimální principy zabezpečení pomocí řízení přístupu k účtu úložiště.

- Další informace najdete v tématu [autorizace přístupu k Azure Storage](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Osvědčené postupy**:

- Řízení přístupu k cíli auditu je klíčovým konceptem oddělení DBA od auditorů. 

- Při auditování přístupu k citlivým datům zvažte zabezpečení dat pomocí šifrování dat, aby se zabránilo úniku informací auditorovi. Další informace najdete v části [Ochrana citlivých dat při použití z vysoce privilegovaných nebo neautorizovaných uživatelů](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Správa zabezpečení

Tato část popisuje různé aspekty a osvědčené postupy pro správu zabezpečení databází stav. Zahrnuje osvědčené postupy pro zajištění, že vaše databáze jsou nakonfigurované tak, aby splňovaly standardy zabezpečení, pro zjišťování a pro klasifikaci a sledování přístupu k potenciálně citlivým datům ve vašich databázích. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Ujistěte se, že databáze jsou nakonfigurované tak, aby splňovaly osvědčené postupy zabezpečení. 

Proaktivně Vylepšete zabezpečení databáze díky zjišťování a oprava potenciálních ohrožení zabezpečení databáze.

**Jak implementovat**:

- Povolit [posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) k prohledávání databáze kvůli problémům se zabezpečením a automatické spouštění pravidelně v databázích.

**Osvědčené postupy**:

- Nejdřív spusťte ve svých databázích a Iterujte opravami kontrolami, které nedodržují osvědčené postupy zabezpečení, a nastavte základní hodnoty pro přijatelné konfigurace, dokud se kontrola neobjeví v poli vyčistit (všechny kontroly jsou úspěšné).  

- Nakonfigurujte pravidelné opakované kontroly, které se spustí jednou týdně, a nakonfigurujte relevantní osobu pro příjem souhrnných e-mailů. 

- Prohlédněte si souhrn hodnot VA za každou týdenní kontrolu. U všech nalezených ohrožení zabezpečení vyhodnoťte posun od předchozího výsledku kontroly a určete, zda má být kontrola vyřešena nebo zda existuje oprávněný důvod ke změně v konfiguraci.   

- Vyřešte kontroly a směrné plány aktualizací, kde jsou relevantní, nebo vytvořte položky lístků pro řešení akcí a sledujte je, dokud nebudou vyřešeny. 

**Další zdroje informací**:

- [Posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze.](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identifikace a označení citlivých dat 

Seznamte se se sloupci, které potenciálně obsahují citlivá data. Klasifikace sloupců pro využití pokročilých scénářů auditování a ochrany na základě citlivosti 

**Jak implementovat**:

- Pomocí [zjišťování a klasifikace dat SQL](sql-database-data-discovery-and-classification.md) můžete zjišťovat, klasifikovat, označovat a chránit citlivá data ve vašich databázích. 
  - Podívejte se na doporučení klasifikace, která jsou vytvořená automatizovaným zjišťováním na řídicím panelu pro zjišťování a klasifikaci dat SQL, a přijměte relevantní klasifikace, aby se vaše citlivá data trvale označila pomocí klasifikace. popisky. 
  - Ručně přidejte klasifikace pro všechna další citlivá datová pole, která nebyla zjištěna automatizovaným mechanismem. 
- Další informace najdete v tématu [klasifikace & SQL data Discovery](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Osvědčené postupy**:

- Pravidelně Sledujte řídicí panel klasifikace, aby bylo přesné posouzení stavu klasifikace databáze. Sestavu stavu klasifikace databáze lze exportovat nebo vytisknout pro účely dodržování předpisů a auditování.

- Průběžně Monitorujte stav doporučených citlivých dat v posouzení ohrožení zabezpečení SQL, sledováním pravidla pro zjištění citlivých dat a určením případného posunu v doporučených sloupcích pro klasifikaci.  

- Používejte klasifikaci způsobem, který je přizpůsobený konkrétním potřebám vaší organizace. Upravte zásady Information Protection (popisky citlivosti, typy informací, logiku zjišťování) v zásadách [SQL Information Protection](../security-center/security-center-info-protection-policy.md) v Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Sledovat přístup k citlivým datům 
Monitor, který přistupuje k citlivým datům, a zachycuje dotazy na citlivá data v protokolech auditu.

**Jak implementovat**:

- V kombinaci použijte audit SQL a klasifikaci dat. 
  - V protokolu [auditu SQL Database](sql-database-auditing.md) můžete sledovat přístup specificky pro citlivá data a zobrazovat informace, jako jsou data, ke kterým došlo, a také popisek citlivosti (viz [přístup k auditování citlivých dat](sql-database-data-discovery-and-classification.md#subheading-3)). 

**Osvědčené postupy**:

- Viz osvědčené postupy pro oddíly auditování a klasifikace dat: 
  - [Auditovat kritické události zabezpečení](#audit-critical-security-events) 
  - [Identifikace a označení citlivých dat](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Vizualizace stavu zabezpečení a dodržování předpisů 

Využijte jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových Center (včetně databází SQL). Prohlédněte si seznam doporučení týkajících se zabezpečení vašich databází a stavu dodržování předpisů.

**Jak implementovat**:

- Monitorujte doporučení zabezpečení související s SQL a aktivní hrozby v [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Běžné bezpečnostní hrozby a potenciální rizika

Tato část vám pomůže najít bezpečnostní opatření pro ochranu před některými vektory útoku. Je očekáváno, že je možné dosáhnout většiny rizik pomocí jednoho nebo více výše uvedených pokynů zabezpečení.

### <a name="security-threat-data-exfiltration"></a>Bezpečnostní hrozba: data exfiltrace

Exfiltrace dat je neoprávněné kopírování, přenos nebo načítání dat z počítače nebo serveru. Podívejte se na definici pro [data exfiltrace](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipedii.

Připojení k Azure SQL Database serveru prostřednictvím veřejného koncového bodu představuje riziko exfiltrace dat, protože zákazníci musí otevřít své brány firewall na veřejné IP adresy.  

**Scénář 1**: aplikace na virtuálním počítači Azure se připojuje k databázi na serveru Azure SQL Database. Neautorizovaný objekt actor získá přístup k virtuálnímu počítači a dojde k jeho ohrožení. V tomto scénáři data exfiltrace znamená, že se externí entita, která používá neautorizovaný virtuální počítač, připojuje k databázi, kopíruje osobní data a ukládá je do úložiště objektů BLOB nebo jiné SQL Database v jiném předplatném.

**Scénář 2**: Rouge dBA. Tento scénář je často vyvolaný zákazníky citlivými z bezpečnostních důvodů ze regulovaných odvětví. V tomto scénáři může uživatel s vysokými oprávněními kopírovat data z Azure SQL Database do jiného předplatného, které neřídí vlastník dat.

**Potenciální rizika**:

V současné době Azure SQL Database nabízí následující techniky pro zmírnění rizik exfiltrace hrozeb pro data: 

- Použijte kombinaci pravidel povolení a odmítnutí na skupin zabezpečení sítě virtuálních počítačů Azure, abyste mohli řídit, které oblasti jsou dostupné z virtuálního počítače. 
- Pokud používáte server Azure SQL Database (obsahující databáze typu Singleton nebo elastické fondy), nastavte následující:
  - Povolí službu Azure OFF.
  - Pomocí nastavení pravidla brány firewall virtuální sítě Povolte jenom provoz z podsítě, která obsahuje váš virtuální počítač Azure.
  - Použít [privátní odkaz](sql-database-private-endpoint-overview.md)
- V případě spravované instance používá přístup k privátní IP adrese ve výchozím nastavení první exfiltrace týkající se neautorizovaných virtuálních počítačů. Zapněte funkci delegování podsítě v podsíti, která bude službou, aby automaticky nastavila nejvíce omezující zásadu v podsíti spravované instance.
- Obavy z neautorizovaných DBA je lepší vystavit se spravovanou instancí, protože má větší plochu a požadavky na síť jsou viditelné pro zákazníky. Nejlepší zmírnění tohoto postupu je použití všech postupů v tomto Průvodci zabezpečením, aby se zabránilo neoprávněnému scénáři DBA v prvním místě (nejen pro data exfiltrace). Always Encrypted je jedna z metod ochrany citlivých dat tím, že ji šifrujete a udržujete klíč nepřístupný ke službě DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspekty zabezpečení při provozní kontinuitě a dostupnosti

Většina standardů zabezpečení řeší dostupnost dat v souvislosti s provozní kontinuitou, dosaženo implementací redundance a převzetím služeb při selhání, aby se předešlo jednomu bodu selhání. V případě scénářů pro havárie se jedná o běžný postup uchovávání záloh dat a souborů protokolů. V následující části najdete základní informace o funkcích, které jsou integrované v Azure, a také další možnosti, které je možné nakonfigurovat tak, aby splňovaly konkrétní potřeby: 

- Azure nabízí integrovanou vysokou dostupnost: [vysokou dostupnost a Azure SQL Database](sql-database-high-availability.md) 

- Pro důležité obchodní informace vrstva zahrnuje skupiny převzetí služeb při selhání, zóny s vícenásobnou dostupností, úplné a rozdílové zálohy protokolů a zálohy obnovení k bodu v čase, které jsou ve výchozím nastavení povolené:  
  - [Redundantní konfigurace s vysokou dostupností a Azure SQL Database zóny](sql-database-high-availability.md#zone-redundant-configuration)
  - [Automatizované zálohy](sql-database-automated-backups.md)
  - [Obnovení Azure SQL Database pomocí automatických záloh databáze – obnovení k bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore)

- Další funkce pro provozní kontinuitu, jako jsou například skupiny pro automatické převzetí služeb při selhání v různých zeměpisných oblastech Azure, se dají nakonfigurovat tak, jak je popsané tady: [Přehled provozní kontinuity pomocí Azure SQL Database](sql-database-business-continuity.md)

## <a name="next-steps"></a>Další kroky

- Zobrazit [Přehled možností zabezpečení Azure SQL Database](sql-database-security-overview.md)