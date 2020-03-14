---
title: Přehled zabezpečení
description: Přečtěte si o Azure SQL Database a zabezpečení SQL Server, včetně rozdílů mezi cloudem a SQL Server místním prostředím.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 348b8fc44628437cbbcfbcd39a26d048284aa60e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208855"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Přehled možností zabezpečení Azure SQL Database

Tento článek popisuje základy zabezpečení datové vrstvy aplikace pomocí Azure SQL Database. Strategie zabezpečení popsaná níže v rámci podrobného přístupu s více vrstvami, jak je znázorněno na obrázku níže, a přesouvá se z vnější části:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Zabezpečení sítě

Microsoft Azure SQL Database poskytuje relační databázovou službu pro cloudové a podnikové aplikace. Kvůli lepší ochraně zákaznických dat brání brány firewall přístup k síti na databázovém serveru, dokud nebude přístup explicitně udělen na základě IP adresy nebo původu provozu virtuální sítě Azure.

### <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP

Pravidla brány firewall protokolu IP udělují přístup k databázím na základě zdrojové IP adresy jednotlivých požadavků. Další informace najdete v tématu [přehled Azure SQL Database a SQL Data Warehouse pravidla brány firewall](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

[Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) prodlužují připojení k virtuální síti přes páteřní síť Azure a umožňují Azure SQL Database identifikaci podsítě virtuální sítě, ze které provoz pochází. Aby bylo možné provozovat Azure SQL Database, pomocí [značek služby](../virtual-network/security-overview.md) SQL povolte odchozí přenosy přes skupiny zabezpečení sítě.

[Pravidla virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) umožňují Azure SQL Database přijímat jenom komunikace, které se odesílají z vybraných podsítí v rámci virtuální sítě.

> [!NOTE]
> Řízení přístupu pomocí pravidel brány firewall *se nevztahuje* na **spravovanou instanci**. Další informace o potřebné konfiguraci sítě najdete v tématu [připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

## <a name="access-management"></a>Správa přístupu

> [!IMPORTANT]
> Správa databází a databázových serverů v Azure je řízena přiřazeními rolí uživatelského účtu portálu. Další informace o tomto článku najdete v tématu [řízení přístupu na základě role v Azure Portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Ověřování je proces, který označuje, že uživatel vyžádá. Azure SQL Database podporuje dva typy ověřování:

- **Ověřování SQL**:

    Ověřování SQL Database odkazuje na ověření uživatele při připojení k [Azure SQL Database](sql-database-technical-overview.md) pomocí uživatelského jména a hesla. Při vytváření databázového serveru pro databázi je nutné zadat přihlašovací jméno správce serveru s uživatelským jménem a heslem. Pomocí těchto přihlašovacích údajů se "Správce serveru" může ověřit u jakékoli databáze na tomto databázovém serveru jako vlastník databáze. Potom může správce serveru vytvořit další přihlášení a uživatele SQL, která uživatelům umožňují připojit se pomocí uživatelského jména a hesla.

- **Ověřování Azure Active Directory**:

    Ověřování Azure Active Directory je mechanismus připojení k [Azure SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit v Azure Active Directory Azure AD. Ověřování Azure AD umožňuje správcům centrálně spravovat identity a oprávnění uživatelů databáze spolu s dalšími službami Microsoftu v jednom centrálním umístění. To zahrnuje minimalizaci úložiště hesel a umožňuje centralizované zásady rotace hesel.

     Aby bylo možné používat ověřování Azure AD s SQL Database, je nutné vytvořit správce serveru s názvem **Správce služby Active Directory** . Další informace najdete v tématu [připojení k SQL Database pomocí ověřování Azure Active Directory](sql-database-aad-authentication.md). Ověřování Azure AD podporuje spravované i federované účty. Federované účty podporují uživatele a skupiny systému Windows pro doménu zákazníka federované pomocí Azure AD.

    K dispozici jsou další možnosti ověřování Azure AD, které jsou [univerzálním ověřováním služby Active Directory pro SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) připojení včetně [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) a [podmíněného přístupu](sql-database-conditional-access.md).

> [!IMPORTANT]
> Správa databází a serverů v Azure je řízena přiřazeními rolí uživatelského účtu portálu. Další informace o tomto článku najdete v tématu [řízení přístupu na základě role v Azure Portal](../role-based-access-control/overview.md). Řízení přístupu pomocí pravidel brány firewall *se nevztahuje* na **spravovanou instanci**. Další informace o potřebné konfiguraci sítě najdete v následujícím článku o [připojení ke spravované instanci](sql-database-managed-instance-connect-app.md) .

## <a name="authorization"></a>Autorizace

Autorizace odkazuje na oprávnění přiřazená uživateli v rámci Azure SQL Database a určuje, co může uživatel dělat. Oprávnění jsou řízena přidáním uživatelských účtů do [databázových rolí](/sql/relational-databases/security/authentication-access/database-level-roles) a přiřazením oprávnění na úrovni databáze k těmto rolím nebo tím, že uživatel udělí určitá [oprávnění na úrovni objektu](/sql/relational-databases/security/permissions-database-engine). Další informace najdete v tématu [přihlášení a uživatelé](sql-database-manage-logins.md) .

Osvědčeným postupem je v případě potřeby vytvořit vlastní role. Přidejte uživatele do role s nejnižšími oprávněními potřebnými k provedení jejich pracovní funkce. Nepřiřazujte oprávnění přímo uživatelům. Účet správce serveru je členem předdefinované role db_owner, která má rozsáhlá oprávnění a měla by být udělována pouze malým uživatelům s administrativními cly. U aplikací Azure SQL Database použijte příkaz [Spustit jako](/sql/t-sql/statements/execute-as-clause-transact-sql) a určete kontext spuštění volaného modulu nebo použijte [aplikační role](/sql/relational-databases/security/authentication-access/application-roles) s omezenými oprávněními. Tento postup zajišťuje, že aplikace, která se připojuje k databázi, má nejnižší oprávnění, která aplikace potřebuje. Tyto osvědčené postupy také podporují oddělení povinností.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku

Zabezpečení na úrovni řádků umožňuje zákazníkům řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz (například členství ve skupině nebo kontext spuštění). Zabezpečení na úrovni řádků lze také použít k implementaci vlastních konceptů zabezpečení na základě popisků. Další informace najdete v tématu [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrana před hrozbami

SQL Database zabezpečení zákaznických dat zajišťuje možnosti auditování a detekce hrozeb.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditování SQL v protokolech Azure Monitor a Event Hubs

SQL Database auditování sleduje aktivity databáze a pomáhá udržet dodržování standardů zabezpečení tím, že zaznamenává databázové události do protokolu auditu ve vlastnictví účtu Azure Storage vlastněných zákazníkem. Auditování umožňuje uživatelům sledovat probíhající databázové aktivity a analyzovat a prozkoumat historické aktivity a identifikovat potenciální hrozby nebo podezření na zneužití a narušení zabezpečení. Další informace najdete v tématu Začínáme s [auditováním SQL Database](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

Rozšířená ochrana před internetovými útoky analyzuje protokoly SQL Server pro detekci neobvyklého chování a potenciálně škodlivých pokusů o přístup k databázím nebo jejich zneužití. Výstrahy se vytvářejí pro podezřelé aktivity, jako je například injektáže SQL, potenciální defiltrace dat a útoky hrubou silou nebo pro anomálie ve vzorech přístupu, aby bylo možné zachytit eskalace oprávnění a porušení přihlašovacích údajů. Výstrahy se zobrazují z [Azure Security Center](https://azure.microsoft.com/services/security-center/), kde jsou uvedeny podrobnosti o podezřelých aktivitách a doporučení pro další šetření, která jsou k dispozici, a s akcemi pro zmírnění hrozby. Rozšířená ochrana před internetovými útoky se dá pro jednotlivé servery povolit za další poplatek. Další informace najdete v tématu [Začínáme s SQL Database rozšířené ochrany před internetovými útoky](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (šifrování při přenosu)

SQL Database zabezpečuje data zákazníků tím, že šifruje data při pohybu pomocí [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Server pro všechna připojení vynutil šifrování (SSL/TLS). Tím se zajistí, že všechna data budou zašifrovaná mezi klientem a serverem, a to bez ohledu na nastavení **šifrování** nebo **TrustServerCertificate** v připojovacím řetězci.

V rámci osvědčeného postupu doporučujeme, aby v připojovacím řetězci aplikace zadáte šifrované _**připojení a**_ nedůvěřujete certifikátu serveru. Tím se aplikace vynutí ověřit certifikát serveru, a tím zabrání v ohrožení vaší aplikace muž při útokech středního typu.

Například při použití ovladače ADO.NET se dosáhnete pomocí **Encrypt = true** a **TrustServerCertificate = false**. Pokud získáte připojovací řetězec z Azure Portal, bude mít správné nastavení.

> [!IMPORTANT]
> Upozorňujeme, že některé ovladače od jiných výrobců nemůžou ve výchozím nastavení používat protokol TLS, nebo se musí spoléhat na starší verzi TLS (< 1.2), aby fungovala. V takovém případě SQL Server stále umožňuje připojit se k databázi. Doporučujeme však vyhodnotit rizika zabezpečení, která umožňují, aby se tyto ovladače a aplikace připojovaly k SQL Database, zejména pokud ukládáte citlivá data.
>
> Další informace o TLS a připojení najdete v tématu [informace o TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity) .

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparentní šifrování dat (šifrování v klidovém umístění)

[Transparentní šifrování dat (TDE) pro Azure SQL Database](transparent-data-encryption-azure-sql.md) přidá vrstvu zabezpečení, která bude pomáhat chránit neaktivní data před neautorizovaným nebo offline přístupem k nezpracovaným souborům nebo zálohám. Mezi běžné scénáře patří krádež Datacenter nebo nezabezpečené vyřazení hardwaru nebo médií, jako jsou diskové jednotky a záložní pásky. TDE šifruje celou databázi pomocí šifrovacího algoritmu AES, který nepožaduje, aby vývojáři aplikací provedli změny v existujících aplikacích.

V Azure jsou všechny nově vytvořené databáze SQL ve výchozím nastavení šifrované a šifrovací klíč databáze je chráněný integrovaným certifikátem serveru.  Údržba a rotace certifikátů spravuje služba a nevyžaduje od uživatele žádné vstupy. Zákazníci, kteří chtějí převzít kontrolu nad šifrovacími klíči, mohou spravovat klíče v [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Správa klíčů pomocí Azure Key Vault

Podpora [Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) pro [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umožňuje zákazníkům převzít vlastnictví správy klíčů a rotace pomocí [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), cloudového externího systému správy klíčů Azure. Pokud je přístup k trezoru klíčů odvolaný, databáze nemůže být dešifrována a načtena do paměti. Azure Key Vault poskytuje platformu pro správu centrálních klíčů, využívá důkladně monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které vám pomůžou splnit požadavky na dodržování předpisů zabezpečení.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (šifrování při použití)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) je funkce navržená tak, aby chránila citlivá data uložená v konkrétních databázových sloupcích z Accessu (například čísla kreditních karet, národní identifikační čísla nebo data podle _potřeby_ ). Patří sem správci databáze nebo jiní privilegovaní uživatelé, kteří mají oprávnění k přístupu k databázi, aby mohli provádět úlohy správy, ale nemají přístup k určitým datům v zašifrovaných sloupcích bez obchodních potřeb. Data jsou vždy šifrována, což znamená, že šifrovaná data jsou dešifrována pouze pro zpracování klientskými aplikacemi s přístupem k šifrovacímu klíči.  Šifrovací klíč se nikdy nezveřejňuje pro SQL a může být uložený buď v [úložišti certifikátů Windows](sql-database-always-encrypted.md) , nebo v [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

SQL Database dynamické maskování dat omezuje vystavení citlivých dat jejich maskováním uživatelům bez oprávnění. Dynamické maskování dat automaticky zjišťuje potenciálně citlivá data v Azure SQL Database a poskytuje užitečná doporučení pro maskování těchto polí s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace najdete v tématu [Začínáme s SQL Database dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Správa zabezpečení

### <a name="vulnerability-assessment"></a>Posouzení ohrožení zabezpečení

[Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je jednoduchá konfigurace služby, která může zjišťovat, sledovat a pomáhat s tím, že je možné slabá místa v databázi s cílem proaktivně zlepšit celkové zabezpečení databáze. Posouzení ohrožení zabezpečení (VA) je součástí nabídky služby ADS (Advanced Data Security), což je jednotný balíček pro pokročilé funkce zabezpečení SQL. Posouzení ohrožení zabezpečení je dostupné a spravované prostřednictvím portálu centrálních SQL ADS.

### <a name="data-discovery--classification"></a>Zjišťování a klasifikace dat

Klasifikace & Discovery dat (aktuálně ve verzi Preview) poskytuje pokročilé funkce, které jsou integrované do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Vyhledávání a klasifikace důležitých citlivých dat (podniková/finanční, zdravotní péče, osobní údaje atd.) může hrát pivotovou roli ve vaší organizaci stature Information Protection. Může sloužit jako infrastruktura pro:

- Různé scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklé přístup k citlivým datům.
- Řízení přístupu a posílení zabezpečení databází obsahujících vysoce citlivá data.
- Pomoc se splněním standardů ochrany osobních údajů a požadavků na dodržování legislativních předpisů.

Další informace najdete v tématu [Začínáme s klasifikací data discovery &](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Dodržování předpisů

Kromě výše uvedených funkcí a funkcí, které vám pomohou aplikace splňovat různé požadavky na zabezpečení, Azure SQL Database se také účastní pravidelných auditů a bylo certifikováno pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="next-steps"></a>Další kroky

- Diskuzi o používání přihlašovacích údajů, uživatelských účtů, databázových rolí a oprávnění v SQL Database najdete v tématu [Správa přihlašovacích jmen a uživatelských účtů](sql-database-manage-logins.md).
- Diskuzi o auditování databáze najdete v tématu [SQL Database audit](sql-database-auditing.md).
- Diskuzi o detekci hrozeb najdete v tématu [SQL Database detekce hrozeb](sql-database-threat-detection.md).
