---
title: PlayBook pro adresování běžných požadavků na zabezpečení
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Tento článek poskytuje běžné požadavky na zabezpečení a osvědčené postupy pro Azure SQL Database a Azure SQL Managed instance.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: e720a95007ff06a954affe03f43f386be3bed39f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442101"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>PlayBook pro adresování běžných požadavků na zabezpečení pomocí Azure SQL Database a spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tento článek poskytuje osvědčené postupy, jak řešit běžné požadavky na zabezpečení. Ne všechny požadavky platí pro všechna prostředí a měli byste se obrátit na databázi a tým zabezpečení, na kterých se funkce implementují.

## <a name="solving-common-security-requirements"></a>Řešení běžných požadavků na zabezpečení

Tento dokument poskytuje pokyny, jak řešit běžné požadavky na zabezpečení pro nové nebo existující aplikace pomocí Azure SQL Database a spravované instance Azure SQL. Je uspořádána podle oblastí zabezpečení vysoké úrovně. Informace o konkrétních hrozbách najdete v části [běžné bezpečnostní hrozby a potenciální rizika](#common-security-threats-and-potential-mitigations) . I když jsou při migraci aplikací z místního prostředí do Azure k dispozici některá z uvedených doporučení, scénáře migrace nejsou zaměřeny na tento dokument.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Nabídky nasazení Azure SQL Database zahrnuté v této příručce

- [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): [samostatné databáze](single-database-overview.md) a [elastické fondy](elastic-pool-overview.md) na [serverech](logical-servers.md)
- [Spravovaná instance Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Nabídky nasazení, které nejsou zahrnuté v tomto průvodci

- Azure Synapse Analytics (dříve SQL Data Warehouse)
- Virtuální počítače Azure SQL (IaaS)
- SQL Server

### <a name="audience"></a>Cílová skupina

V této příručce jsou zamýšlení zákazníci, kteří čelí dotazům k zabezpečení Azure SQL Database. Role, které mají zájem v tomto článku o osvědčených postupech, zahrnují, ale ne omezení na:

- Architekti zabezpečení
- Správci zabezpečení
- Úředníci dodržování předpisů
- Úředníci ochrany osobních údajů
- Technici zabezpečení

### <a name="using-this-guide"></a><a id="using"></a> Pomocí této příručky

Tento dokument je určený jako doprovodný průvodce pro naši stávající dokumentaci k [zabezpečení Azure SQL Database](security-overview.md) .

Pokud není uvedeno jinak, doporučujeme, abyste provedli všechny osvědčené postupy uvedené v jednotlivých částech, abyste dosáhli příslušného cíle nebo požadavku. Pro splnění konkrétních standardů dodržování předpisů zabezpečení nebo osvědčených postupů jsou v části požadavky nebo cíle uvedeny důležité kontroly dodržování předpisů, ať už je to možné. Jedná se o standardy zabezpečení a předpisy, na které se odkazuje v tomto dokumentu:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Access Control, kryptografie
- [Postupy Microsoft Operational Security Assurance (osa)](https://www.microsoft.com/securityengineering/osa/practices): postupy #1 – 6 a #9
- [NIST speciální publikace 800-53 řízení zabezpečení](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

Chystáme se dál aktualizovat doporučení a osvědčené postupy, které jsou tady uvedené. Zadáním odkazu na **zpětnou vazbu** na konci tohoto článku zadejte nebo opravte tento dokument.

## <a name="authentication"></a>Authentication

Ověřování je proces, který označuje, že uživatel vyžádá. Azure SQL Database a spravovaná instance SQL podporují dva typy ověřování:

- Ověřování SQL
- Ověřování služby Azure Active Directory

> [!NOTE]
> Pro všechny nástroje a aplikace třetích stran nemusí být ověřování Azure Active Directory podporováno.

### <a name="central-management-for-identities"></a>Centrální Správa identit

Centrální Správa identit nabízí následující výhody:

- Spravujte účty skupin a řízení uživatelských oprávnění bez duplikování přihlašovacích údajů mezi servery, databázemi a spravovanými instancemi.
- Zjednodušená a flexibilní Správa oprávnění.
- Správa aplikací ve velkém měřítku.

**Jak implementovat**:

- Použijte ověřování pomocí Azure Active Directory (Azure AD) pro centralizovanou správu identit.

**Osvědčené postupy**:

- Vytvořte tenanta Azure AD a [Vytvořte uživatele, kteří](../../active-directory/fundamentals/add-users-azure-active-directory.md) budou představovat lidské uživatele, a vytvořit [instanční objekty](../../active-directory/develop/app-objects-and-service-principals.md) , které budou představovat aplikace, služby a nástroje pro automatizaci. Instanční objekty jsou stejné jako účty služeb v systému Windows a Linux.

- Přiřazení přístupových práv k prostředkům k objektům zabezpečení Azure AD prostřednictvím přiřazení skupiny: Vytvoření skupin Azure AD, udělení přístupu ke skupinám a přidání jednotlivých členů do skupin. V databázi vytvořte uživatele databáze s omezením, kteří mapují vaše skupiny Azure AD. Pokud chcete přiřadit oprávnění v rámci databáze, uveďte uživatele přidružené ke skupinám Azure AD v databázových rolích s příslušnými oprávněními.
  - Projděte si články, [nakonfigurujte a spravujte Azure Active Directory ověřování pomocí SQL](authentication-aad-configure.md) a [použijte Azure AD pro ověřování pomocí SQL](authentication-aad-overview.md).
  > [!NOTE]
  > Ve spravované instanci SQL můžete také vytvořit přihlašovací údaje, které se mapují na objekty zabezpečení Azure AD v hlavní databázi. Viz [Create Login (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- Použití skupin Azure AD zjednodušuje správu oprávnění a vlastníka skupiny a vlastník prostředku může přidat nebo odebrat členy do/ze skupiny.

- Pro všechny servery nebo spravované instance Vytvořte samostatnou skupinu pro správce Azure AD.

  - Projděte si článek [zřízení správce Azure Active Directory pro váš server](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

- Sledujte změny členství ve skupinách Azure AD pomocí sestav aktivit auditu Azure AD.

- Pro spravovanou instanci se k vytvoření správce Azure AD vyžaduje samostatný krok.
  - Projděte si článek [zřízení správce Azure Active Directory pro vaši spravovanou instanci](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

> [!NOTE]
>
> - Ověřování Azure AD se zaznamenává v protokolech auditu Azure SQL, ale ne v protokolech přihlášení služby Azure AD.
> - Oprávnění RBAC udělená v Azure se nevztahují na Azure SQL Database nebo oprávnění ke spravovaným instancím SQL. Taková oprávnění je nutné vytvořit nebo namapovat ručně pomocí stávajících oprávnění SQL.
> - Na straně klienta potřebuje ověřování Azure AD přístup k Internetu nebo prostřednictvím uživatelsky definované trasy (UDR) do virtuální sítě.
> - Přístupový token Azure AD je uložený v mezipaměti na straně klienta a jeho životnost závisí na konfiguraci tokenu. Viz článek, [konfigurovatelné životnosti tokenů v Azure Active Directory](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Pokyny k řešení potíží s ověřováním Azure AD najdete na následujícím blogu: [řešení potíží s Azure AD](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

> Zmíněné v: #2 praxe metody OSA, ISO Access Control (AC)

Azure Multi-Factor Authentication pomáhá zvýšit zabezpečení tím, že vyžaduje více než jednu formu ověřování.

**Jak implementovat**:

- [Povolte Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) ve službě Azure AD pomocí podmíněného přístupu a použijte interaktivní ověřování.

- Alternativou je povolení Multi-Factor Authentication pro celou doménu služby Azure AD nebo AD.

**Osvědčené postupy**:

- Aktivujte podmíněný přístup ve službě Azure AD (vyžaduje předplatné Premium).
  - Projděte si článek [podmíněný přístup ve službě Azure AD](../../active-directory/conditional-access/overview.md).  

- Vytvořte skupiny Azure AD a povolte zásady Multi-Factor Authentication pro vybrané skupiny pomocí podmíněného přístupu Azure AD.
  - Projděte si článek [Plánování nasazení podmíněného přístupu](../../active-directory/conditional-access/plan-conditional-access.md).

- Multi-Factor Authentication můžete povolit pro celou službu Azure AD nebo pro celou službu Active Directory, která je pro Azure AD federované.

- Použijte režim interaktivního ověřování Azure AD pro Azure SQL Database a Azure SQL Managed instance, kde je vyžadováno interaktivní zadání hesla a pak Multi-Factor Authentication:
  - Použijte univerzální ověřování v SSMS. Přečtěte si článek [použití vícefaktorového ověřování Azure AD s Azure SQL Database, spravovanou instancí SQL, Azure synapse (podpora SSMS pro Multi-Factor Authentication)](authentication-mfa-ssms-overview.md).
  - Použijte interaktivní ověřování podporované v nástroji SQL Server Data Tools (SSDT). Přečtěte si článek [Azure Active Directory podpora SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Použijte další nástroje SQL podporující Multi-Factor Authentication.
    - Podpora průvodce SSMS pro export/extrakci/nasazení databáze  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): možnost '/UA '
    - [Nástroj Sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility): možnost-G (interaktivní)
    - [nástroj BCP](https://docs.microsoft.com/sql/tools/bcp-utility): možnost-G (interaktivní)

- Implementujte své aplikace pro připojení k Azure SQL Database nebo spravované instanci Azure SQL pomocí interaktivního ověřování s podporou Multi-Factor Authentication.
  - Projděte si článek [připojení k Azure SQL Database s využitím Azure Multi-Factor Authentication](active-directory-interactive-connect-azure-sql-db.md).
  > [!NOTE]
  > Tento režim ověřování vyžaduje identity založené na uživatelích. V případech, kdy se používá důvěryhodný model identity, který obchází individuální ověřování uživatelů Azure AD (třeba pomocí spravované identity pro prostředky Azure) Multi-Factor Authentication neplatí.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimalizace použití ověřování založeného na heslech pro uživatele

> Zmíněné v: #4 praxe metody OSA, ISO Access Control (AC)

Metody ověřování založené na heslech představují slabší formu ověřování. Přihlašovací údaje můžou být ohrožené nebo neuvedené omylem.

**Jak implementovat**:

- Použijte integrované ověřování Azure AD, které eliminuje používání hesel.

**Osvědčené postupy**:

- Použijte ověřování pomocí jednotného přihlašování s použitím přihlašovacích údajů Windows. Federovat místní doménu AD pomocí Azure AD a používejte integrované ověřování systému Windows (pro počítače připojené k doméně se službou Azure AD).
  - Přečtěte si článek [SSMS podpora integrovaného ověřování Azure AD](authentication-aad-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimalizace použití ověřování založeného na heslech pro aplikace

> Zmíněné v: #4 praxe metody OSA, ISO Access Control (AC)

**Jak implementovat**:

- Povolte spravovanou identitu Azure. Můžete také použít integrované ověřování nebo ověřování založené na certifikátech.

**Osvědčené postupy**:

- Používejte [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).
  - [Spravovaná identita přiřazená systémem](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Spravovaná identita přiřazená uživatelem](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Použití Azure SQL Database ze Azure App Service se spravovanou identitou (bez změny kódu)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Pro aplikaci použijte ověřování na základě certifikátu.
  - Podívejte se na tuto [ukázku kódu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token).

- Pro integrovanou federované doménu a počítač připojený k doméně použijte ověřování Azure AD (viz část výše).
  - Prohlédněte si [ukázkovou aplikaci pro integrované ověřování](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Ochrana hesel a tajných kódů

V případě, že se hesla nedaří, ujistěte se, že jsou zabezpečená.

**Jak implementovat**:

- Pomocí Azure Key Vault můžete ukládat hesla a tajné kódy. Kdykoli je to možné, použijte Multi-Factor Authentication pro Azure SQL Database s uživateli Azure AD.

**Osvědčené postupy**:

- Pokud se nemůžete vyhnout heslům nebo tajným klíčům, ukládejte si uživatelská hesla a tajné klíče aplikace do Azure Key Vault a spravujte přístup prostřednictvím zásad Key Vault přístupu.

- Různé architektury pro vývoj aplikací můžou nabízet i mechanizmy specifické pro ochranu tajných kódů v aplikaci. Například: [ASP.NET Core App](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Použití ověřování SQL pro starší verze aplikací

Ověřování SQL odkazuje na ověření uživatele při připojení k Azure SQL Database nebo spravované instanci SQL pomocí uživatelského jména a hesla. Přihlašování se bude muset vytvořit na každém serveru nebo ve spravované instanci a uživatel vytvořený v každé databázi.

**Jak implementovat**:

- Použijte ověřování SQL.

**Osvědčené postupy**:

- Jako správce serveru nebo instance Vytvořte přihlašovací jména a uživatele. Pokud nepoužíváte uživatelé databáze s omezením hesla, ukládají se všechna hesla do hlavní databáze.
  - Přečtěte si článek, [řízení a udělení přístupu k databázi SQL Database, spravované instanci SQL a Azure synapse Analytics](logins-create-manage.md).

## <a name="access-management"></a>Správa přístupu

Správa přístupu (označovaná taky jako autorizace) je proces řízení a správy přístupu autorizovaných uživatelů a oprávnění k Azure SQL Database nebo spravované instanci SQL.

### <a name="implement-principle-of-least-privilege"></a>Implementace principu s nejnižšími oprávněními

> Zmíněné v: FedRamp Controls AC-06, NIST: AC-6, #3 praxe

Princip nejnižších oprávnění, která by uživatelé neměli mít více oprávnění, než je potřeba k dokončení svých úkolů. Další informace najdete v článku [jenom dostatečná Správa](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Jak implementovat**:

Přiřaďte pouze potřebná [oprávnění](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) k dokončení požadovaných úloh:

- V databázích SQL:
  - Použijte přesnější oprávnění a uživatelsky definované databázové role (nebo role serveru ve spravované instanci):
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
  - Pokud jsou k dispozici nebo vlastní role Azure a přiřadíte potřebná oprávnění, použijte předdefinované role.
    - [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md)
    - [Vlastní role Azure](../../role-based-access-control/custom-roles.md)

**Osvědčené postupy**:

Následující osvědčené postupy jsou volitelné, ale výsledkem bude lepší správa a podpora vaší strategie zabezpečení:

- Pokud je to možné, začněte s nejmenší možnou sadou oprávnění a začněte přidávat oprávnění jeden po druhém, pokud existuje skutečná nutnost (a odůvodnění), a to na rozdíl od opačného přístupu: pokaždé, když se postará o oprávnění, převezme krok za krokem.

- Upustí od přiřazování oprávnění jednotlivým uživatelům. Používejte místo toho role (databáze nebo role serveru) konzistentně. Role značně pomáhají s vytvářením sestav a s oprávněními k odstraňování potíží. (Azure RBAC podporuje pouze přiřazení oprávnění prostřednictvím rolí.)

- Vytvořte a používejte vlastní role s přesnými oprávněními. Typické role používané v praxi:
  - Nasazení zabezpečení
  - Správce
  - Vývojář
  - Pracovníci podpory
  - Auditor
  - Automatizované procesy
  - koncový uživatel
  
- Předdefinované role používejte pouze v případě, že oprávnění role odpovídají přesně potřebným oprávněním pro uživatele. Můžete přiřadit uživatele k více rolím.

- Mějte na paměti, že oprávnění v databázovém stroji se dají použít v následujících oborech (menším rozsahem je menší dopad udělených oprávnění):
  - Server (speciální role v hlavní databázi) v Azure
  - Database
  - Schéma
    - Osvědčeným postupem je použití schémat k udělení oprávnění v rámci databáze. (viz také: [schéma – návrh: doporučení pro návrh schématu s ohledem na zabezpečení](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objekt (tabulka, zobrazení, procedura atd.)

  > [!NOTE]
  > Nedoporučuje se uplatňovat oprávnění na úrovni objektu, protože tato úroveň přináší nepotřebnou složitost na celkovou implementaci. Pokud se rozhodnete použít oprávnění na úrovni objektu, měli byste je jasně zdokumentovat. Totéž platí pro oprávnění na úrovni sloupců, která se ve stejných důvodech ještě méně doporučují. Upozorňujeme také, že ve výchozím nastavení [odmítnutí](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) na úrovni tabulky přepíše udělení na úrovni sloupce. To by vyžadovalo aktivaci [Konfigurace serveru dodržování společných kritérií](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) .

- Proveďte pravidelné kontroly pomocí [posouzení ohrožení zabezpečení (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) k otestování příliš velkého počtu oprávnění.

### <a name="implement-separation-of-duties"></a>Implementovat oddělení povinností

> Zmíněné v: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

Oddělení povinností, označované také jako oddělení cel, popisuje nutnost rozdělit citlivé úkoly na více úkolů, které jsou přiřazeny různým uživatelům. Oddělení povinností pomáhá zabránit narušením dat.

**Jak implementovat**:

- Identifikujte požadovanou úroveň rozdělení povinností. Příklady:
  - Mezi vývojovým a testovacím a produkčním prostředím
  - Úkoly na úrovni správy s citlivým zabezpečením (DBA) vs – úlohy pro vývojáře.
    - Příklady: auditor, vytváření zásad zabezpečení pro zabezpečení na úrovni role (RLS), implementace SQL Database objektů s oprávněním DDL.

- Identifikujte komplexní hierarchii uživatelů (a automatizovaných procesů), které přistupují k systému.

- Vytvořte role podle potřeb uživatelů a přiřaďte k rolím oprávnění.
  - Pro úlohy na úrovni správy v Azure Portal nebo prostřednictvím PowerShellu – automatizace používá role Azure. Najít integrovanou roli, která odpovídá požadavku, nebo vytvořit vlastní roli Azure pomocí dostupných oprávnění
  - Vytvořte role serveru pro úlohy na úrovni serveru (vytváření nových přihlašovacích údajů, databází) ve spravované instanci.
  - Vytváření databázových rolí pro úlohy na úrovni databáze.

- U určitých citlivých úloh zvažte vytvoření zvláštních uložených procedur podepsaných certifikátem, který spustí úlohy jménem uživatelů. Jednou z důležitých výhod digitálně podepsaných uložených procedur je to, že pokud se postup změní, oprávnění udělená předchozí verzi postupu se okamžitě odeberou.
  - Příklad: [kurz: podepisování uložených procedur s certifikátem](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Implementací transparentní šifrování dat (TDE) pomocí klíčů spravovaných zákazníkem v Azure Key Vault umožní oddělení povinností mezi vlastníkem dat a vlastníkem zabezpečení.
  - Projděte si článek [konfigurace klíčů spravovaných zákazníkem pro Azure Storage šifrování z Azure Portal](../../storage/common/storage-encryption-keys-portal.md).

- Aby se zajistilo, že správce databáze nemůže zobrazit data, která jsou považována za vysoce citlivá a mohou i nadále provádět úlohy DBA, můžete použít Always Encrypted s oddělením rolí.
  - Přečtěte si články, [Přehled správy klíčů pro Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [zřizování klíčů s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)a [rotaci hlavního klíče sloupce s oddělením rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation).

- V případech, kdy použití Always Encrypted není proveditelné, nebo alespoň bez podstatných nákladů a úsilí, které může dokonce vykreslovat systém v nepoužitém režimu, může dojít k ohrožení zabezpečení a k omezení využití kompenzačních ovládacích prvků, jako jsou:
  - Lidské intervence v procesech.
  - Záznamy auditu – Další informace o auditování najdete v tématu [Auditovat kritické události zabezpečení](#audit-critical-security-events).

**Osvědčené postupy**:

- Zajistěte, aby se pro vývojové a testovací a produkční prostředí používaly různé účty. Různé účty vám pomůžou dodržovat oddělení testovacích a produkčních systémů.

- Upustí od přiřazování oprávnění jednotlivým uživatelům. Používejte místo toho role (databáze nebo role serveru) konzistentně. Role, které mají roli, značně pomáhají při vytváření sestav a řešení potíží.

- Předdefinované role použijte, pokud se oprávnění shodují přesně s potřebnými oprávněními – Pokud sjednocení všech oprávnění z několika předdefinovaných rolí vede k 100%, můžete přiřadit více rolí současně.

- Vytvoření a použití uživatelsky definovaných rolí, pokud předdefinované role udělují příliš mnoho oprávnění nebo nedostatečná oprávnění.

- Přiřazení rolí se taky dá dělat dočasně, označované také jako dynamické oddělení povinností (DSD), a to v rámci kroků úloh agenta SQL v T-SQL nebo pomocí Azure PIM pro role Azure.

- Ujistěte se, že specializující nemá přístup k šifrovacím klíčům nebo k úložištím klíčů a že správci zabezpečení s přístupem ke klíčům nemají přístup k databázi. Použití [nástroje EKM (Extensible Key Management)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm) může usnadnit dosažení tohoto oddělení. K implementaci EKM se dá použít [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) .

- Vždy nezapomeňte mít záznam auditu pro akce související se zabezpečením.

- Můžete načíst definici předdefinovaných rolí Azure a zobrazit použitá oprávnění a vytvořit vlastní roli založenou na výňatkech a jejich kumulacích přes PowerShell.

- Vzhledem k tomu, že kterýkoli člen role databáze db_owner může změnit nastavení zabezpečení, jako je například transparentní šifrování dat (TDE), nebo změnit objekt SLO, mělo by být toto členství uděleno opatrně. Existuje však mnoho úloh, které vyžadují db_owner oprávnění. Úkol jako změna nastavení databáze, jako je například změna možností databáze. Auditování hraje klíčovou roli v jakémkoli řešení.

- Není možné omezit oprávnění db_owner a zabráníte tak účtu správce v zobrazení uživatelských dat. Pokud je v databázi vysoce citlivá data, Always Encrypted můžete použít k bezpečnému zabránění db_owners nebo jiným DBA v jeho zobrazení.

> [!NOTE]
> Dosažení oddělení povinností (SoD) je náročné na úlohy související se zabezpečením nebo při odstraňování potíží. Jiné oblasti, jako je vývoj a role koncového uživatele, je jednodušší oddělení. Většina ovládacích prvků souvisejících s dodržováním předpisů umožňuje použití alternativních funkcí ovládacího prvku, jako je například auditování, pokud jiná řešení nejsou praktická.

Pro čtenáře, kteří chtějí podrobně hlouběji do SoD, doporučujeme následující zdroje:

- Pro Azure SQL Database a SQL Managed instance:  
  - [Řízení a udělování přístupu k databázi](logins-create-manage.md)
  - [Oddělení povinností pro vývojáře aplikací](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [Oddělení povinností](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Podepisování uložených procedur](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Pro správu prostředků Azure:
  - [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md)
  - [Vlastní role Azure](../../role-based-access-control/custom-roles.md)
  - [Použití Azure AD Privileged Identity Management pro zvýšení úrovně přístupu](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Provádět běžné revize kódu

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

- Nezapomeňte znát všechny zdroje změn kódu. Kód může být ve skriptech T-SQL. Může se jednat o ad hoc příkazy, které se mají spustit nebo nasadit ve formě zobrazení, funkcí, triggerů a uložených procedur. Může být součástí definic úloh agenta SQL (kroky). Dá se taky spouštět v rámci balíčků SSIS, Azure Data Factory a dalších služeb.

## <a name="data-protection"></a>Ochrana dat

Ochrana dat je sada funkcí pro zabezpečení důležitých informací před ohrožením šifrováním nebo zašifrováním.

> [!NOTE]
> Microsoft potvrzuje Azure SQL Database a SQL Managed instance jako kompatibilní se standardem FIPS 140-2 úrovně 1. To se provádí po ověření striktního použití algoritmů FIPS 140-2 úrovně 1 a ověřených instancí FIPS 140-2 úrovně 1, včetně konzistence s požadovanými délkami klíčů, správou klíčů, generováním klíčů a úložištěm klíčů. Účelem tohoto ověření je, aby naši zákazníci mohli reagovat na potřebu nebo požadavek na použití ověřených instancí FIPS 140-2 úrovně 1 při zpracování dat nebo doručování systémů nebo aplikací. V příkazu výše definujeme podmínky dodržování předpisů FIPS 140-2 Level 1 a Standard FIPS 140-2 úrovně 1, které předvádějí zamýšlenou použitelnost v oblasti USA a Kanada pro použití v různých výrazech "FIPS 140-2 Level 1".

### <a name="encrypt-data-in-transit"></a>Šifrovat data při přenosu

> V tomto postupu: #6 postupů pro OSA, rodina ovládacích prvků ISO: kryptografie

Chrání data při přesunu dat mezi klientem a serverem. Přečtěte si téma [zabezpečení sítě](#network-security).

### <a name="encrypt-data-at-rest"></a>Šifrování neaktivních uložených dat

> V tomto postupu: #6 postupů pro OSA, rodina ovládacích prvků ISO: kryptografie

Šifrování v klidovém případě je kryptografická ochrana dat, když je trvalá v databázi, protokolu a souborech zálohy.

**Jak implementovat**:

- [Transparentní šifrování databáze (TDE)](transparent-data-encryption-tde-overview.md) se spravovanými klíči služby je ve výchozím nastavení povolené pro všechny databáze vytvořené po 2017 Azure SQL Database a SQL Managed instance.
- Pokud je ve spravované instanci databáze vytvořená z operace obnovení pomocí místního serveru, bude dodrženo nastavení TDE původní databáze. Pokud původní databáze nemá povolené TDE, doporučujeme, aby se pro spravovanou instanci TDE ručně aktivovaly.

**Osvědčené postupy**:

- Neukládejte data, která vyžadují šifrování v hlavní databázi. Hlavní databázi nelze zašifrovat pomocí TDE.

- Klíče spravované zákazníkem v Azure Key Vault použijte, pokud potřebujete větší transparentnost a přesnější kontrolu nad ochranou TDE. Azure Key Vault umožňuje kdykoli odvolat oprávnění pro vykreslování databáze jako nedostupné. Ochranu TDE můžete centrálně spravovat společně s ostatními klíči, nebo můžete TDE ochranu pomocí Azure Key Vault sami otočit podle vlastního plánu.

- Pokud používáte klíče spravované zákazníkem v Azure Key Vault, postupujte podle článků, [pokyny pro konfiguraci TDE s Azure Key Vault](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) a [Postup konfigurace geografického Dr s Azure Key Vault](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Chránit citlivá data při použití z vysoce privilegovaných nebo neautorizovaných uživatelů

Použitá data jsou data uložená v paměti databázového systému během provádění dotazů SQL. Pokud vaše databáze uchovává citlivá data, může být vaše organizace nutná k tomu, aby se uživatelům s vysokým oprávněním zabránilo v prohlížení citlivých dat ve vaší databázi. Uživatelé s vysokou úrovní oprávnění, jako jsou například operátoři Microsoftu nebo specializující ve vaší organizaci, by měli mít přístup k databázi, ale nemusejí zobrazovat a potenciálně staly všudypřítomnými citlivá data z paměti procesu SQL nebo pomocí dotazování databáze.

Zásady, které určují, která data jsou citlivá a zda musí být citlivá data šifrována v paměti a nejsou přístupná správcům ve formátu prostého textu, jsou specifická pro vaši organizaci a předpisy dodržování předpisů, které je třeba dodržovat. Podívejte se prosím na související požadavky: [identifikace a označení citlivých dat](#identify-and-tag-sensitive-data).

**Jak implementovat**:

- Pomocí [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) zajistěte, aby citlivá data nebyla vystavena ve formátu prostého textu v Azure SQL Database nebo spravované instanci SQL, a to ani v paměti. Always Encrypted chrání data od správců databází (specializující) a cloudových správců (nebo špatných aktérů, které mohou zosobnit vysoce privilegovaných uživatelů) a poskytují větší kontrolu nad tím, kdo má přístup k vašim datům.

**Osvědčené postupy**:

- Always Encrypted není náhradou za zašifrování dat v klidovém režimu (TDE) nebo při přenosu (SSL/TLS). Always Encrypted by se neměla používat pro data, která nejsou citlivá, aby se minimalizoval dopad na výkon a funkčnost. Použití Always Encrypted ve spojení s TDE a protokolem TLS (Transport Layer Security) se doporučuje pro komplexní ochranu neaktivních dat, přenosů a používání.

- Než nasadíte Always Encrypted do provozní databáze, posuďte dopad šifrování identifikovaných sloupců s citlivými daty. Obecně Always Encrypted snižuje funkčnost dotazů v šifrovaných sloupcích a má další omezení uvedená v [podrobnostech o Always Encryptedch funkcích](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Proto může být nutné změnit architekt vaší aplikace, aby znovu implementovala funkčnost, dotaz nepodporuje, na straně klienta nebo v refaktorování schématu databáze, včetně definic uložených procedur, funkcí, zobrazení a triggerů. Pokud nedodržují omezení a omezení Always Encrypted, existující aplikace nemusí fungovat s šifrovanými sloupci. I když ekosystém nástrojů, produktů a služeb Microsoftu, které podporují Always Encrypted, roste, mnoho z nich nefunguje s šifrovanými sloupci. Šifrování sloupce může také ovlivnit výkon dotazů v závislosti na charakteristikách vaší úlohy.

- Pokud používáte Always Encrypted k ochraně dat před škodlivými specializující, spravujte Always Encrypted klíče pomocí oddělování rolí. Při oddělování rolí vytvoří Správce zabezpečení fyzické klíče. Správce databáze vytvoří objekty metadat hlavního klíče sloupce a šifrovacího klíče sloupce, které popisují fyzické klíče v databázi. Během tohoto procesu správce zabezpečení nepotřebuje přístup k databázi a DBA nebude potřebovat přístup k fyzickým klíčům ve formátu prostého textu.
  - Podrobnosti najdete v článku [Správa klíčů pomocí oddělení rolí](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) .

- Uložte hlavní klíče sloupců do Azure Key Vault pro usnadnění správy. Nepoužívejte úložiště certifikátů systému Windows (a obecně distribuovaná řešení úložiště klíčů, jako u jiných řešení správy klíčů nepoužíváme), která umožňují správu klíčů pevně.

- Pečlivě si promyslete kompromisy používání více klíčů (hlavní klíč sloupce nebo šifrovací klíče sloupců). Snižte počet klíčů, abyste snížili náklady na správu klíčů. Jeden hlavní klíč sloupce a jeden sloupec šifrovací klíč na databázi je obvykle dostačující v prostředích s ustáleným stavem (ne uprostřed střídání klíčů). Pokud máte různé skupiny uživatelů, můžete potřebovat další klíče, přičemž každý z nich používá různé klíče a přistupuje k různým datům.  

- Otočí hlavní klíče sloupce podle vašich požadavků na dodržování předpisů. Pokud potřebujete také přetočit šifrovací klíče sloupce, zvažte použití online šifrování k minimalizaci výpadku aplikace.
  - Informace najdete v článku věnovaném [důležitým informacím o výkonu a dostupnosti](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations).

- Pokud je potřeba, aby se výpočty (rovnost) dat podporovaly, použijte deterministické šifrování. V opačném případě používejte náhodné šifrování. Vyhněte se použití deterministického šifrování pro datové sady s nízkou entropií nebo datových sad s veřejně známou distribucí.

- Pokud máte obavy, že třetí strany přistupují k vašim datům právně bez vašeho souhlasu, zajistěte, aby všechny aplikace a nástroje, které mají přístup k klíčům a datům v prostém textu, běžely mimo Microsoft Azure Cloud. Bez přístupu k klíčům nebude mít třetí strana možnost dešifrovat data, pokud obcházejí šifrování.

- Always Encrypted neumožňuje snadno udělit dočasný přístup k klíčům (a chráněným datům). Pokud například potřebujete sdílet klíče se službou DBA a povolit tak nástroji DBA některé operace čištění citlivých a šifrovaných dat. Jediným způsobem, jak spolehlivost odvolat přístup k datům z databáze DBA, je použít k otočení šifrovacích klíčů sloupce a hlavních klíčů sloupce pro ochranu dat, což je náročná operace.

- Aby bylo možné získat přístup k hodnotám v podobě prostého textu v šifrovaných sloupcích, musí mít uživatel přístup k hlavnímu klíči sloupce (CMK), který chrání sloupce, který je nakonfigurovaný v úložišti klíčů obsahujícím CMK. Uživatel musí také mít k dispozici **definici hlavního klíče sloupce** a zobrazení všech oprávnění pro databázi **definic šifrovacího klíče sloupce** .

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Řízení přístupu uživatelů aplikací k citlivým datům prostřednictvím šifrování

Šifrování lze použít jako způsob, jak zajistit, aby data mohla zobrazit nebo aktualizovat pouze konkrétní uživatelé aplikace, kteří mají přístup k kryptografickým klíčům.

**Jak implementovat**:

- Použijte šifrování na úrovni buňky (CLE). Podrobnosti najdete v článku o [šifrování sloupce dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) .
- Používejte Always Encrypted, ale mějte na paměti omezení. Omezení jsou uvedena níže.

**Osvědčené postupy**

Při použití CLE:

- Řízení přístupu k klíčům prostřednictvím oprávnění a rolí SQL.

- Pro šifrování dat použijte AES (doporučeno AES 256). Algoritmy, jako ŠIFRy, DES a TripleDES, jsou zastaralé a neměly by se používat kvůli známým chybám zabezpečení.

- Chraňte symetrické klíče pomocí asymetrických klíčů/certifikátů (nikoli hesel), abyste se vyhnuli používání algoritmu 3DES.

- Buďte opatrní při migraci databáze pomocí šifrování na úrovni buňky prostřednictvím exportu/importu (soubory BacPac).
  - Informace o tom, jak zabránit ztrátě klíčů při migraci dat a další pokyny k osvědčeným postupům, najdete v článku [doporučení pro používání šifrování na úrovni buněk v Azure SQL Database](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) .

Mějte na paměti, že Always Encrypted je primárně navržená tak, aby chránila citlivá data při použití od uživatelů s vysokou úrovní oprávnění Azure SQL Database (specializující) – viz [chránit citlivá data při použití z vysoce privilegovaných nebo neautorizovaných uživatelů](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Při použití Always Encrypted k ochraně dat před uživateli aplikace Pamatujte na následující problémy:

- Ve výchozím nastavení všechny ovladače klienta Microsoftu podporující Always Encrypted udržují globální mezipaměť (jedna na každou aplikaci) pro šifrovací klíče sloupce. Když ovladač klienta získá šifrovací klíč sloupce se prostým textem, kontaktujte úložiště klíčů obsahující hlavní klíč sloupce. šifrovací klíč sloupce ve formátu prostého textu se uloží do mezipaměti. Díky tomu jsou izolována data uživatelů aplikace s více uživateli náročné. Pokud se vaše aplikace při interakci s úložištěm klíčů (například Azure Key Vault) zosobňuje koncovým uživatelům, poté, co dotaz uživatele naplní mezipaměť pomocí šifrovacího klíče sloupce, bude následující dotaz, který vyžaduje stejný klíč, spuštěný jiným uživatelem, použít klíč uložený v mezipaměti. Ovladač nebude volat úložiště klíčů a nebude kontrolovat, zda má druhý uživatel oprávnění pro přístup k šifrovacímu klíči sloupce. V důsledku toho uživatel uvidí šifrovaná data i v případě, že uživatel nemá přístup k klíčům. Pro zajištění izolace uživatelů v rámci aplikace pro více uživatelů můžete zakázat ukládání šifrovacích klíčů sloupce do mezipaměti. Zakázání ukládání do mezipaměti způsobí zvýšení výkonu, protože ovladač bude potřebovat pro každou operaci šifrování nebo dešifrování dat kontaktovat úložiště klíčů.

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

- Pomocí správných zásad řízení přístupu (prostřednictvím oprávnění SQL, rolí, RLS) můžete omezit uživatelská oprávnění k provádění aktualizací v maskovaných sloupcích. Vytvořením masky u sloupce se nebrání aktualizace tohoto sloupce. Uživatelé, kteří přijímají maskovaná data při dotazování maskovaného sloupce, mohou data aktualizovat, pokud mají oprávnění k zápisu.

- Dynamické maskování dat nezachovává statistické vlastnosti maskovaných hodnot. To může mít vliv na výsledky dotazu (například dotazy obsahující predikáty filtrování nebo spojení s maskovánými daty).

## <a name="network-security"></a>Zabezpečení sítě

Zabezpečení sítě odkazuje na řízení přístupu a osvědčené postupy pro zabezpečení vašich dat při přenosu do Azure SQL Database.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>Konfigurace klienta pro zabezpečené připojení k SQL Database/spravované instanci SQL

Osvědčené postupy, jak zabránit klientským počítačům a aplikacím s dobře známými chybami zabezpečení (například pomocí starších protokolů TLS a šifrovacích sad) z připojení k Azure SQL Database a spravované instanci SQL.

**Jak implementovat**:

- Zajistěte, aby se klientské počítače připojující se Azure SQL Database a SQL Managed instance používaly protokol  [TLS (Transport Layer Security)](security-overview.md#transport-layer-security-encryption-in-transit).

**Osvědčené postupy**:

- Konfigurace všech aplikací a nástrojů pro připojení k SQL Database se zapnutým šifrováním
  - Encrypt = on, TrustServerCertificate = off (nebo ekvivalent u ovladačů od jiných výrobců než od Microsoftu).

- Pokud vaše aplikace používá ovladač, který nepodporuje protokol TLS nebo podporuje starší verzi TLS, nahraďte ovladač, pokud je to možné. Pokud není to možné, pečlivě vyhodnoťte bezpečnostní rizika.

- Zmenšení vektorů útoku prostřednictvím zabezpečení SSL 2,0, SSL 3,0, TLS 1,0 a TLS 1,1 tím, že je zakážete v klientských počítačích, které se připojují k Azure SQL Database [nastavení registru TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10).

- Podívejte se na šifrovací sady, které jsou k dispozici na klientovi: [šifrovací sady v TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Konkrétně zakažte 3DES na [konfiguraci pořadí šifrovací sady TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order).

- Pro Azure SQL Database a spravované instance SQL se vynutilo šifrování pro typy připojení proxy a přesměrování. U spravované instance Azure SQL použijte typ připojení **proxy** (výchozí), protože to vynutilo šifrování ze strany serveru. Typ připojení **přesměrování** aktuálně nepodporuje vynucování šifrování a je dostupný jenom u privátních IP připojení.

- Další informace najdete v tématu [architektura Azure SQL Database připojení – zásady připojení](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>Minimalizace prostoru pro útoky

Minimalizujte počet funkcí, které mohou být napadeny uživatelem se zlými úmysly. Implementujte ovládací prvky přístupu k síti pro Azure SQL Database.

> V tomto postupu jsou uvedené v: #5 postupů pro OSA

**Jak implementovat**:

V SQL Database:

- Nastavení povolení přístupu ke službám Azure na úrovni serveru
- Používejte koncové body služby virtuální sítě a pravidla brány firewall virtuální sítě.
- Použijte privátní odkaz (Preview).

Ve spravované instanci SQL:

- Postupujte podle pokynů v části [požadavky na síť](../managed-instance/connectivity-architecture-overview.md#network-requirements).

**Osvědčené postupy**:

- Omezení přístupu k Azure SQL Database a spravované instanci SQL připojením k privátnímu koncovému bodu (například pomocí cesty privátních dat):
  - Spravovaná instance může být izolovaná v rámci virtuální sítě, aby se zabránilo externímu přístupu. Aplikace a nástroje, které jsou ve stejné oblasti nebo v partnerské virtuální síti, mají k ní přímý přístup. Aplikace a nástroje, které jsou v jiné oblasti, můžou k navázání připojení použít připojení Virtual-Network-to-Virtual-Network nebo peering okruhu ExpressRoute. Zákazník by měl používat skupiny zabezpečení sítě (NSG) k omezení přístupu přes port 1433 jenom na prostředky, které vyžadují přístup ke spravované instanci.
  - V případě SQL Database použijte funkci [privátního odkazu](../../private-link/private-endpoint-overview.md) , která poskytuje vyhrazenou soukromou IP adresu pro server v rámci vaší virtuální sítě. Můžete také použít [koncové body služby virtuální sítě s pravidly brány firewall virtuální sítě](vnet-service-endpoint-rule-overview.md) a omezit tak přístup k vašim serverům.
  - Mobilní uživatelé by měli použít připojení VPN typu Point-to-site pro připojení přes cestu k datům.
  - Uživatelé připojení k místní síti by měli použít připojení VPN typu Site-to-site nebo ExpressRoute k připojení přes cestu k datům.

- Přístup k Azure SQL Database a spravované instanci SQL získáte připojením k veřejnému koncovému bodu (například pomocí cesty k veřejnému data). Je třeba vzít v úvahu následující osvědčené postupy:
  - Pro server v SQL Database použijte [pravidla brány firewall protokolu IP](firewall-configure.md) pro omezení přístupu pouze k autorizovaným IP adresám.
  - Pro spravovanou instanci SQL použijte skupiny zabezpečení sítě (NSG) k omezení přístupu přes port 3342 jenom na požadované prostředky. Další informace najdete v tématu [bezpečné použití spravované instance s veřejnými koncovými body](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> Veřejný koncový bod spravované instance SQL není ve výchozím nastavení povolený a musí být explicitně povolen. Pokud zásady společnosti nepovolují použití veřejných koncových bodů, použijte [Azure Policy](../../governance/policy/overview.md) , abyste zabránili povolení veřejných koncových bodů na prvním místě.

- Nastavení síťových součástí Azure:
  - Postupujte podle [osvědčených postupů Azure pro zabezpečení sítě](../../security/fundamentals/network-best-practices.md).
  - Naplánujte Virtual Network konfiguraci podle osvědčených postupů popsaných v tématu [Azure Virtual Network Nejčastější dotazy](../../virtual-network/virtual-networks-faq.md) a plán.
  - Segmentujte virtuální síť do několika podsítí a přiřaďte prostředky pro podobnou roli stejné podsíti (například front-end vs-endové prostředky).
  - Použijte [skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/security-overview.md) k řízení provozu mezi podsítěmi uvnitř hranice virtuální sítě Azure.
  - Povolte [službě Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) pro vaše předplatné monitorování příchozího a odchozího síťového provozu.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurace Power BI pro zabezpečená připojení k spravované instanci služby SQL Database/SQL

**Osvědčené postupy**:

- U Power BI Desktop používejte cestu k privátním datům, kdykoli to bude možné.

- Ujistěte se, že se Power BI Desktop připojuje pomocí protokolu TLS 1.2 nastavením klíče registru na klientském počítači podle nastavení registru [TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) .

- Omezte přístup k datům pro konkrétní uživatele přes [zabezpečení na úrovni řádků (RLS) pomocí Power BI](https://docs.microsoft.com/power-bi/service-admin-rls).

- U Power BI služby použijte [místní bránu dat](https://docs.microsoft.com/power-bi/service-gateway-onprem)a zapamatujte si [omezení a požadavky](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurace App Service pro zabezpečená připojení k spravované instanci služby SQL Database/SQL

**Osvědčené postupy**:

- U jednoduchých webových aplikací, které se připojují přes Veřejný koncový bod, je nutné, aby nastavení **umožňovalo službám Azure** zapnuto.

- [Integrujte svoji aplikaci s Virtual Network Azure](../../app-service/web-sites-integrate-with-vnet.md) pro připojení k privátním datům do spravované instance. Volitelně můžete také nasadit webovou aplikaci pomocí [prostředí App Service (POmocného programu)](../../app-service/environment/intro.md).

- U webové aplikace s přihlašováním nebo integrovanou webovou aplikací virtuální sítě, která se připojuje k databázi v SQL Database, můžete použít [koncové body služby virtuální sítě a pravidla brány firewall virtuální sítě](vnet-service-endpoint-rule-overview.md) pro omezení přístupu z konkrétní virtuální sítě a podsítě. Pak nastavte možnost **zapnout službu Azure** na off. Můžete taky připojit pomocného mechanismu ke spravované instanci ve spravované instanci SQL přes cestu k privátním datům.  

- Ujistěte se, že je vaše webová aplikace nakonfigurovaná podle článku, [osvědčené postupy pro zabezpečení webových a mobilních aplikací PaaS (Platform as a Service) pomocí Azure App Service](../../security/fundamentals/paas-applications-using-app-services.md).

- Instalace [firewallu webových aplikací (WAF)](../../web-application-firewall/ag/ag-overview.md) pro ochranu webové aplikace před běžnými zneužitím a ohrožením zabezpečení.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>Konfigurace hostování virtuálních počítačů Azure pro zabezpečená připojení k spravované instanci SQL Database/SQL

**Osvědčené postupy**:

- Použijte kombinaci pravidel povolení a odmítnutí na skupin zabezpečení sítě virtuálních počítačů Azure, abyste mohli řídit, které oblasti jsou dostupné z virtuálního počítače.

- Ujistěte se, že váš virtuální počítač je nakonfigurovaný podle článku, což je [osvědčené postupy zabezpečení pro IaaS úlohy v Azure](../../security/fundamentals/iaas.md).

- Ujistěte se, že jsou všechny virtuální počítače přidružené k určité virtuální síti a podsíti.

- Vyhodnoťte, jestli v [souvislosti s vynuceným tunelovým propojením](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling)potřebujete výchozí trasu 0.0.0.0/Internet podle pokynů.
  - Pokud ano – například podsíť front-end – potom ponechte výchozí trasu.
  - Pokud ne, například střední úroveň nebo podsíť back-end, povolte vynucené tunelování, aby žádný přenos přes Internet nepřešel do místního prostředí (a. k. a mezi různými místy).

- Implementujte [Volitelné výchozí trasy](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) , pokud používáte partnerský vztah nebo se připojujete místně.

- Implementujte [uživatelsky definované trasy](../../virtual-network/virtual-networks-udr-overview.md#user-defined) , pokud potřebujete odeslat veškerý provoz ve virtuální síti do síťového virtuálního zařízení pro kontrolu paketů.

- Použijte [koncové body služby virtuální sítě](vnet-service-endpoint-rule-overview.md) pro zabezpečený přístup ke službám PaaS, jako je Azure Storage prostřednictvím páteřní sítě Azure.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Ochrana před distribuovanými útoky s cílem odepření služeb (DDoS)

Útok na distribuovaný útok DoS (DDoS) se snaží uživatel se zlými úmysly odeslat zahlcení síťového provozu, aby se Azure SQL Database s cílem zahlcení infrastruktury Azure a způsobila odmítnutí platných přihlašovacích údajů a úloh.

> V tomto postupu jsou uvedené v: #9 postupů pro OSA

**Jak implementovat**:

DDoS Protection se automaticky povolí jako součást platformy Azure. Zahrnuje nepřetržité monitorování provozu a zmírnění útoků na úrovni sítě u veřejných koncových bodů v reálném čase.

- Pomocí [Azure DDoS Protection](../../virtual-network/ddos-protection-overview.md) můžete monitorovat veřejné IP adresy přidružené k prostředkům nasazeným ve virtuálních sítích.

- Použití [rozšířené ochrany před internetovými útoky pro Azure SQL Database](threat-detection-overview.md) k detekci útoků DOS (Denial of Service) na databáze.

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

- Použití [rozšířené ochrany před internetovými útoky pro SQL](threat-detection-overview.md#alerts) ke zjištění neobvyklých a potenciálně škodlivých pokusů o přístup k databázím nebo jejich zneužití:
  - Útok prostřednictvím injektáže SQL.
  - Krádež nebo únik přihlašovacích údajů.
  - Zneužití oprávnění
  - Exfiltrace dat.

**Osvědčené postupy**:

- Konfigurace [rozšířeného zabezpečení dat (ADS)](advanced-data-security.md#getting-started-with-ads)   pro konkrétní server nebo spravovanou instanci. Můžete taky nakonfigurovat reklamu pro všechny servery a spravované instance v rámci předplatného tak, že přepnete na [úroveň Azure Security Center úrovně Standard](../../security-center/security-center-pricing.md).

- Pro úplné prostředí šetření doporučujeme povolit [SQL Database auditování](../../azure-sql/database/auditing-overview.md). Pomocí auditování můžete sledovat události databáze a zapisovat je do protokolu auditu v účtu Azure Storage nebo v pracovním prostoru Azure Log Analytics.

### <a name="audit-critical-security-events"></a>Auditovat kritické události zabezpečení

Sledování událostí databáze pomáhá pochopit databázovou činnost. Můžete získat přehled o nesrovnalostech a anomáliích, které by mohly poukazovat na obavy z podnikání nebo na podezření na narušení zabezpečení. Také umožňuje a usnadňuje dodržování standardů dodržování předpisů.

**Jak implementovat**:

- Povolte auditování [SQL Database](../../azure-sql/database/auditing-overview.md) nebo [auditování spravované instance](../managed-instance/auditing-configure.md) ke sledování událostí databáze a jejich zápis do protokolu auditu ve vašem účtu Azure Storage, Log Analytics pracovní prostor (Preview) nebo Event Hubs (Preview).

- Protokoly auditu se dají zapisovat do účtu Azure Storage, do Log Analytics pracovního prostoru pro spotřebu Azure Monitor protokoly nebo do centra událostí pro spotřebu pomocí centra událostí. Můžete nakonfigurovat libovolnou kombinaci těchto možností a protokoly auditu se zapíší do každého z nich.

**Osvědčené postupy**:

- Konfigurací [SQL Database auditování](../../azure-sql/database/auditing-overview.md) na serveru nebo [auditování spravované instance](../managed-instance/auditing-configure.md) pro auditované události budou auditovány všechny stávající a nově vytvořené databáze na tomto serveru.
- Zásady auditování ve výchozím nastavení zahrnují všechny akce (dotazy, uložené procedury a úspěšná a neúspěšná přihlášení) proti databázím, což může vést k velkému objemu protokolů auditu. Zákazníkům se doporučuje [nakonfigurovat auditování pro různé typy akcí a skupin akcí pomocí PowerShellu](../../sql-database/sql-database-auditing.md#manage-auditing). Tato konfigurace vám pomůže řídit počet auditovaných akcí a minimalizuje riziko ztráty událostí. Vlastní konfigurace auditu umožňují zákazníkům zachytit jenom data auditu, která jsou potřeba.
- Protokoly auditu je možné spotřebovat přímo v [Azure Portal](https://portal.azure.com/)nebo z umístění úložiště, které jste nakonfigurovali.

> [!NOTE]
> Povolením auditování pro Log Analytics se účtují náklady na základě sazeb ingestování. Pomocí této [Možnosti](https://azure.microsoft.com/pricing/details/monitor/)si pamatujte na související náklady, nebo zvažte uložení protokolů auditu v účtu úložiště Azure.

**Další zdroje informací**:

- [Auditování SQL Database](../../azure-sql/database/auditing-overview.md)
- [Auditování SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Protokoly zabezpečeného auditu

Omezte přístup k účtu úložiště za účelem podpory oddělení povinností a oddělení DBA od auditorů.

**Jak implementovat**:

- Když ukládáte protokoly auditu do Azure Storage, ujistěte se, že je přístup k účtu úložiště omezený na minimální principy zabezpečení. Řízení, kdo má přístup k účtu úložiště.
- Další informace najdete v tématu [autorizace přístupu k Azure Storage](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Osvědčené postupy**:

- Řízení přístupu k cíli auditu je klíčovým konceptem oddělení DBA od auditorů.

- Při auditování přístupu k citlivým datům zvažte zabezpečení dat pomocí šifrování dat, aby se zabránilo úniku informací auditorovi. Další informace najdete v části [Ochrana citlivých dat při použití z vysoce privilegovaných nebo neautorizovaných uživatelů](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Správa zabezpečení

Tato část popisuje různé aspekty a osvědčené postupy pro správu zabezpečení databází stav. Zahrnuje osvědčené postupy pro zajištění, že vaše databáze jsou nakonfigurované tak, aby splňovaly standardy zabezpečení, pro zjišťování a pro klasifikaci a sledování přístupu k potenciálně citlivým datům ve vašich databázích.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Ujistěte se, že jsou databáze nakonfigurované tak, aby splňovaly osvědčené postupy zabezpečení.

Proaktivně Vylepšete zabezpečení databáze díky zjišťování a oprava potenciálních ohrožení zabezpečení databáze.

**Jak implementovat**:

- Povolit [posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) k prohledávání databáze kvůli problémům se zabezpečením a automatické spouštění pravidelně v databázích.

**Osvědčené postupy**:

- Nejdřív spusťte ve svých databázích a Iterujte opravami kontrolami, které nedodržují osvědčené postupy zabezpečení. Nastavte standardní hodnoty pro přijatelné _konfigurace, dokud se kontrola_neobjeví, nebo všechny kontroly byly úspěšné.  

- Nakonfigurujte pravidelné opakované kontroly, které se spustí jednou týdně, a nakonfigurujte relevantní osobu pro příjem souhrnných e-mailů.

- Prohlédněte si souhrn hodnot VA za každou týdenní kontrolu. U všech nalezených ohrožení zabezpečení vyhodnoťte posun od předchozího výsledku kontroly a určete, zda má být kontrola vyřešena. Zkontrolujte, jestli existuje oprávněný důvod pro změnu konfigurace.

- Vyřešte kontroly a směrné plány aktualizací tam, kde je to relevantní. Vytvořte položky lístků pro řešení akcí a sledujte je, dokud nebudou vyřešeny.

**Další zdroje informací**:

- [Posouzení ohrožení zabezpečení SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment)
- [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze.](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identifikace a označení citlivých dat

Seznamte se se sloupci, které potenciálně obsahují citlivá data. To, co se považuje za citlivé údaje, je silně závislé na zákazníkovi, nařízení dodržování předpisů atd. a musí být vyhodnoceno uživateli na základě těchto dat. Klasifikace sloupců pro použití pokročilých scénářů auditování a ochrany na základě citlivosti.

**Jak implementovat**:

- Pomocí [zjišťování a klasifikace dat SQL](data-discovery-and-classification-overview.md) můžete zjišťovat, klasifikovat, označovat a chránit citlivá data ve vašich databázích.
  - Prohlédněte si doporučení klasifikace vytvořená automatizovaným zjišťováním na řídicím panelu pro zjišťování a klasifikaci dat SQL. Přijměte příslušné klasifikace, aby byly citlivé údaje trvale označené popisky klasifikace.
  - Ručně přidejte klasifikace pro všechna další citlivá datová pole, která nebyla zjištěna automatizovaným mechanismem.
- Další informace najdete v tématu věnovaném [zjišťování a klasifikaci dat SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Osvědčené postupy**:

- Pravidelně Sledujte řídicí panel klasifikace, aby bylo přesné posouzení stavu klasifikace databáze. Sestavu stavu klasifikace databáze lze exportovat nebo vytisknout pro účely dodržování předpisů a auditování.

- Průběžně monitorovat stav doporučených citlivých dat v posouzení ohrožení zabezpečení SQL. Sledujte pravidlo pro zjištění citlivých dat a určete případný posun v doporučených sloupcích pro klasifikaci.  

- Používejte klasifikaci způsobem, který je přizpůsobený konkrétním potřebám vaší organizace. Upravte zásady Information Protection (popisky citlivosti, typy informací, logiku zjišťování) v zásadách [SQL Information Protection](../../security-center/security-center-info-protection-policy.md) v Azure Security Center.

### <a name="track-access-to-sensitive-data"></a>Sledovat přístup k citlivým datům

Monitor, který přistupuje k citlivým datům, a zachycuje dotazy na citlivá data v protokolech auditu.

**Jak implementovat**:

- Použijte kombinaci SQL Auditu a Klasifikace dat.
  - V protokolu [auditu SQL Database](../../azure-sql/database/auditing-overview.md) můžete sledovat přístup specificky pro citlivá data. Můžete také zobrazit informace, jako jsou data, ke kterým došlo, a popisek citlivosti. Další informace najdete v tématech [zjišťování a klasifikace dat](data-discovery-and-classification-overview.md) a [auditování přístupu k citlivým datům](data-discovery-and-classification-overview.md#audit-sensitive-data).

**Osvědčené postupy**:

- Viz osvědčené postupy pro oddíly auditování a klasifikace dat:
  - [Auditovat kritické události zabezpečení](#audit-critical-security-events)
  - [Identifikace a označení citlivých dat](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Vizualizace stavu zabezpečení a dodržování předpisů

Využijte jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových Center (včetně databází v SQL Database). Prohlédněte si seznam doporučení týkajících se zabezpečení vašich databází a stavu dodržování předpisů.

**Jak implementovat**:

- Monitorujte doporučení zabezpečení související s SQL a aktivní hrozby v [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Běžné bezpečnostní hrozby a potenciální rizika

Tato část vám pomůže najít bezpečnostní opatření pro ochranu před některými vektory útoku. Je očekáváno, že je možné dosáhnout většiny rizik pomocí jednoho nebo více výše uvedených pokynů zabezpečení.

### <a name="security-threat-data-exfiltration"></a>Bezpečnostní hrozba: data exfiltrace

Exfiltrace dat je neoprávněné kopírování, přenos nebo načítání dat z počítače nebo serveru. Podívejte se na definici pro [data exfiltrace](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipedii.

Připojení k serveru prostřednictvím veřejného koncového bodu představuje riziko exfiltrace dat, protože zákazníci si můžou své brány firewall otevřít na veřejné IP adresy.  

**Scénář 1**: aplikace na virtuálním počítači Azure se připojuje k databázi v Azure SQL Database. Neautorizovaný objekt actor získá přístup k virtuálnímu počítači a dojde k jeho ohrožení. V tomto scénáři data exfiltrace znamená, že se externí entita, která používá neautorizovaný virtuální počítač, připojuje k databázi, kopíruje osobní data a ukládá je do úložiště objektů BLOB nebo jiné SQL Database v jiném předplatném.

**Scénář 2**: Rouge dBA. Tento scénář je často vyvolaný zákazníky citlivými z bezpečnostních důvodů ze regulovaných odvětví. V tomto scénáři může uživatel s vysokými oprávněními kopírovat data z Azure SQL Database do jiného předplatného, které neřídí vlastník dat.

**Potenciální rizika**:

V dnešní době se Azure SQL Database a SQL Managed instance nabízí následující techniky pro zmírnění hrozeb exfiltracech dat:

- Použijte kombinaci pravidel povolení a odmítnutí na skupin zabezpečení sítě virtuálních počítačů Azure, abyste mohli řídit, které oblasti jsou dostupné z virtuálního počítače.
- Pokud používáte server v SQL Database, nastavte následující možnosti:
  - Povolí službu Azure OFF.
  - Pomocí nastavení pravidla brány firewall virtuální sítě Povolte jenom provoz z podsítě, která obsahuje váš virtuální počítač Azure.
  - Použít [privátní odkaz](../../private-link/private-endpoint-overview.md)
- V případě spravované instance SQL používá přístup k privátní IP adrese ve výchozím nastavení první exfiltrace týkající se neautorizovaných virtuálních počítačů. Zapněte funkci delegování podsítě v podsíti, aby se automaticky nastavila nejvíce omezující zásada v podsíti spravované instance SQL.
- Obavy z neautorizovaných DBA se zveřejňuje ve službě SQL Managed instance, protože má větší plochu a požadavky na síť jsou viditelné pro zákazníky. Nejlepší zmírnění tohoto postupu je použití všech postupů v tomto Průvodci zabezpečením, aby se zabránilo neoprávněnému scénáři DBA v prvním místě (nejen pro data exfiltrace). Always Encrypted je jedna z metod ochrany citlivých dat tím, že ji šifrujete a udržujete klíč nepřístupný ke službě DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspekty zabezpečení při provozní kontinuitě a dostupnosti

Většina standardů zabezpečení řeší dostupnost dat v souvislosti s provozní kontinuitou, dosaženo implementací redundance a převzetím služeb při selhání, aby se předešlo jednomu bodu selhání. V případě scénářů pro havárie se jedná o běžný postup uchovávání záloh dat a souborů protokolů.V následující části najdete základní informace o funkcích, které jsou integrované v Azure. Poskytuje taky další možnosti, které je možné nakonfigurovat tak, aby splňovaly konkrétní potřeby:

- Azure nabízí integrovanou vysokou dostupnost [s vysokou dostupností s využitím SQL Database a SQL Managed instance](high-availability-sla.md) .

- Pro důležité obchodní informace vrstva zahrnuje skupiny převzetí služeb při selhání, zóny s vícenásobnou dostupností, úplné a rozdílové zálohy protokolů a zálohy obnovení k bodu v čase, které jsou ve výchozím nastavení povolené:  
  - [Redundantní konfigurace s vysokou dostupností – zóna](high-availability-sla.md#zone-redundant-configuration)
  - [Automatizované zálohy](automated-backups-overview.md)
  - [Obnovení databáze pomocí automatických záloh databáze – obnovení k určitému bodu v čase](recovery-using-backups.md#point-in-time-restore)

- Další funkce pro provozní kontinuitu, jako jsou například skupiny automatického převzetí služeb při selhání v různých zeměpisných oblastech Azure, se dají nakonfigurovat podle popisu v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) .

## <a name="next-steps"></a>Další kroky

- Zobrazit [Přehled možností zabezpečení Azure SQL Database](security-overview.md)
