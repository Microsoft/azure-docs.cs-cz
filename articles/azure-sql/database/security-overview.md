---
title: Přehled zabezpečení
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Přečtěte si o zabezpečení v Azure SQL Database a spravované instanci Azure SQL, včetně toho, jak se liší od SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 084f9aae16cfbf495f05c90c8244b2b9b71cf624
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812930"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Přehled možností zabezpečení Azure SQL Database a SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Tento článek popisuje základy zabezpečení datové vrstvy aplikace pomocí [Azure SQL Database](sql-database-paas-overview.md), [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)a [Azure synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Strategie zabezpečení popsaná níže v rámci podrobného přístupu s více vrstvami, jak je znázorněno na obrázku níže, a přesouvá se z vnější části:

![Diagram vícevrstvé obrany s hloubkou. Zákaznická data jsou encaseda v úrovních zabezpečení sítě, správy přístupu a ochrany informací.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Zabezpečení sítě

Microsoft Azure SQL Database, spravovaná instance SQL a Azure synapse Analytics poskytují službu relačních databází pro cloudové a podnikové aplikace. Kvůli lepší ochraně zákaznických dat brání brány firewall síťovému přístupu k serveru, dokud nebude přístup explicitně udělen na základě IP adresy nebo původu provozu virtuální sítě Azure.

### <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP

Pravidla brány firewall protokolu IP udělují přístup k databázím na základě zdrojové IP adresy jednotlivých požadavků. Další informace najdete v tématu [přehled Azure SQL Database a pravidla brány firewall služby Azure synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

[Koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md) prodlužují připojení k virtuální síti přes páteřní síť Azure a umožňují Azure SQL Database identifikaci podsítě virtuální sítě, ze které provoz pochází. Aby bylo možné provozovat Azure SQL Database, pomocí [značek služby](../../virtual-network/network-security-groups-overview.md) SQL povolte odchozí přenosy přes skupiny zabezpečení sítě.

[Pravidla virtuální sítě](vnet-service-endpoint-rule-overview.md) umožňují Azure SQL Database přijímat jenom komunikace, které se odesílají z vybraných podsítí v rámci virtuální sítě.

> [!NOTE]
> Řízení přístupu pomocí pravidel brány firewall *se nevztahuje* na **spravovanou instanci SQL**. Další informace o potřebné konfiguraci sítě najdete v tématu [připojení ke spravované instanci](../managed-instance/connect-application-instance.md) .

## <a name="access-management"></a>Správa přístupu

> [!IMPORTANT]
> Správa databází a serverů v Azure je řízena přiřazeními rolí uživatelského účtu portálu. Další informace o tomto článku najdete v tématu [řízení přístupu na základě role Azure v Azure Portal](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Ověřování je proces, který označuje, že uživatel vyžádá. Azure SQL Database a spravovaná instance SQL podporují dva typy ověřování:

- **Ověřování SQL**:

    Ověřování SQL odkazuje na ověření uživatele při připojení k Azure SQL Database nebo spravované instanci Azure SQL pomocí uživatelského jména a hesla. Při vytváření serveru je nutné zadat přihlašovací jméno **Správce serveru** s uživatelským jménem a heslem. Pomocí těchto přihlašovacích údajů se může **Správce serveru** ověřit u jakékoli databáze na daném serveru nebo v instanci jako vlastník databáze. Potom může správce serveru vytvořit další přihlášení a uživatele SQL, která uživatelům umožňují připojit se pomocí uživatelského jména a hesla.

- **Ověřování Azure Active Directory**:

    Ověřování Azure Active Directory je mechanismus připojení [Azure SQL Database](sql-database-paas-overview.md), [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) a [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit v Azure Active Directory (Azure AD). Ověřování Azure AD umožňuje správcům centrálně spravovat identity a oprávnění uživatelů databáze spolu s dalšími službami Azure v jednom centrálním umístění. To zahrnuje minimalizaci úložiště hesel a umožňuje centralizované zásady rotace hesel.

     Aby bylo možné používat ověřování Azure AD s SQL Database, je nutné vytvořit správce serveru s názvem **Správce služby Active Directory** . Další informace najdete v tématu [připojení k SQL Database pomocí ověřování Azure Active Directory](authentication-aad-overview.md). Ověřování Azure AD podporuje spravované i federované účty. Federované účty podporují uživatele a skupiny systému Windows pro doménu zákazníka federované pomocí Azure AD.

    K dispozici jsou další možnosti ověřování Azure AD, které jsou [univerzálním ověřováním služby Active Directory pro SQL Server Management Studio](authentication-mfa-ssms-overview.md) připojení včetně [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) a [podmíněného přístupu](conditional-access-configure.md).

> [!IMPORTANT]
> Správa databází a serverů v Azure je řízena přiřazeními rolí uživatelského účtu portálu. Další informace o tomto článku najdete v tématu [řízení přístupu na základě rolí v Azure v Azure Portal](../../role-based-access-control/overview.md). Řízení přístupu pomocí pravidel brány firewall *se nevztahuje* na **spravovanou instanci SQL**. Další informace o potřebné konfiguraci sítě najdete v následujícím článku o [připojení ke spravované instanci](../managed-instance/connect-application-instance.md) .

## <a name="authorization"></a>Autorizace

Autorizace odkazuje na oprávnění přiřazená uživateli v rámci databáze v Azure SQL Database nebo v Azure SQL Managed instance a určuje, co může uživatel dělat. Oprávnění jsou řízena přidáním uživatelských účtů do [databázových rolí](/sql/relational-databases/security/authentication-access/database-level-roles) a přiřazením oprávnění na úrovni databáze k těmto rolím nebo tím, že uživatel udělí určitá [oprávnění na úrovni objektu](/sql/relational-databases/security/permissions-database-engine). Další informace najdete v tématu [přihlášení a uživatelé](logins-create-manage.md) .

Osvědčeným postupem je v případě potřeby vytvořit vlastní role. Přidejte uživatele do role s nejnižšími oprávněními potřebnými k provedení jejich pracovní funkce. Nepřiřazujte oprávnění přímo uživatelům. Účet správce serveru je členem předdefinované role db_owner, která má rozsáhlá oprávnění a měla by být udělována pouze malým uživatelům s administrativními cly. Pro aplikace použijte příkaz [Spustit jako](/sql/t-sql/statements/execute-as-clause-transact-sql) a určete kontext spuštění volaného modulu nebo použijte [aplikační role](/sql/relational-databases/security/authentication-access/application-roles) s omezenými oprávněními. Tento postup zajišťuje, že aplikace, která se připojuje k databázi, má nejnižší oprávnění, která aplikace potřebuje. Tyto osvědčené postupy také podporují oddělení povinností.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádků

Row-Level Security umožňuje zákazníkům řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz (například členství ve skupině nebo kontext spuštění). Row-Level zabezpečení lze také použít k implementaci vlastních konceptů zabezpečení na základě popisků. Další informace najdete v tématu [zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security).

![Diagram znázorňující, že Row-Level zabezpečení chrání jednotlivé řádky databáze SQL prostřednictvím přístupu uživatelů prostřednictvím klientské aplikace.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrana před hrozbami

SQL Database a SQL spravované instance zabezpečují data zákazníků tím, že poskytují možnosti auditování a detekce hrozeb.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditování SQL v protokolech Azure Monitor a Event Hubs

SQL Database a auditování spravované instance SQL sleduje aktivity databáze a pomáhá udržet dodržování standardů zabezpečení tím, že zaznamenává databázové události do protokolu auditu ve vlastnictví účtu Azure Storage ve vlastnictví zákazníka. Auditování umožňuje uživatelům sledovat probíhající databázové aktivity a analyzovat a prozkoumat historické aktivity a identifikovat potenciální hrozby nebo podezření na zneužití a narušení zabezpečení. Další informace najdete v tématu Začínáme s [auditováním SQL Database](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Rozšířená ochrana před internetovými útoky analyzuje protokoly a detekuje neobvyklé chování a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Výstrahy se vytvářejí pro podezřelé aktivity, jako je například injektáže SQL, potenciální defiltrace dat a útoky hrubou silou nebo pro anomálie ve vzorech přístupu, aby bylo možné zachytit eskalace oprávnění a porušení přihlašovacích údajů. Výstrahy se zobrazují z  [Azure Security Center](https://azure.microsoft.com/services/security-center/), kde jsou uvedeny podrobnosti o podezřelých aktivitách a doporučení pro další šetření, která jsou k dispozici, a s akcemi pro zmírnění hrozby. Rozšířená ochrana před internetovými útoky se dá pro jednotlivé servery povolit za další poplatek. Další informace najdete v tématu [Začínáme s SQL Database rozšířené ochrany před internetovými útoky](threat-detection-configure.md).

![Diagram znázorňující přístup pro monitorování detekce hrozeb SQL k databázi SQL pro webovou aplikaci od externího útočníka a škodlivého programu Insider](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (šifrování při přenosu)

SQL Database, Managed instance SQL a služba Azure synapse Analytics zabezpečují zákaznická data tím, že šifrují data v pohybu pomocí [protokolu TLS (Transport Layer Security)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

SQL Database, Managed instance SQL a Azure synapse Analytics vynutila šifrování (SSL/TLS) pro všechna připojení. Tím se zajistí, že všechna data budou zašifrovaná mezi klientem a serverem, a to bez ohledu na nastavení **šifrování** nebo **TrustServerCertificate** v připojovacím řetězci.

V rámci osvědčeného postupu doporučujeme, abyste v připojovacím řetězci, který používá aplikaci, zadali šifrované _**připojení a**_ nedůvěřujete certifikátu serveru. Tím se aplikace vynutí ověřit certifikát serveru, a tím zabrání v ohrožení vaší aplikace muž při útokech středního typu.

Například při použití ovladače ADO.NET se dosáhnete pomocí  **Encrypt = true** a **TrustServerCertificate = false**. Pokud získáte připojovací řetězec z Azure Portal, bude mít správné nastavení.

> [!IMPORTANT]
> Všimněte si, že některé ovladače od jiných výrobců nemůžou ve výchozím nastavení používat protokol TLS, nebo se musí spoléhat na starší verzi TLS (<1,2), aby fungovala. V takovém případě server stále umožňuje připojení k vaší databázi. Doporučujeme však vyhodnotit rizika zabezpečení, která umožňují, aby se tyto ovladače a aplikace připojovaly k SQL Database, zejména pokud ukládáte citlivá data.
>
> Další informace o TLS a připojení najdete v tématu [informace o TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity) .

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparentní šifrování dat (šifrování v klidovém umístění)

[Transparentní šifrování dat (TDE) pro SQL Database, spravovaná instance SQL a Azure synapse Analytics](transparent-data-encryption-tde-overview.md) přináší úroveň zabezpečení, která vám umožní chránit neaktivní data před neoprávněným přístupem nebo offline přístupem k nezpracovaným souborům nebo zálohám. Mezi běžné scénáře patří krádež datového centra nebo nezabezpečené vyřazení hardwaru nebo médií, jako jsou diskové jednotky a záložní pásky.TDE šifruje celou databázi pomocí šifrovacího algoritmu AES, který nepožaduje, aby vývojáři aplikací provedli změny v existujících aplikacích.

V Azure se všechny nově vytvořené databáze šifrují ve výchozím nastavení a šifrovací klíč databáze je chráněný integrovaným certifikátem serveru.  Údržba a rotace certifikátů spravuje služba a nevyžaduje od uživatele žádné vstupy. Zákazníci, kteří chtějí převzít kontrolu nad šifrovacími klíči, mohou spravovat klíče v [Azure Key Vault](../../key-vault/general/security-features.md).

### <a name="key-management-with-azure-key-vault"></a>Správa klíčů pomocí Azure Key Vault

Podpora [Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) pro [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umožňuje zákazníkům převzít vlastnictví správy klíčů a rotace pomocí [Azure Key Vault](../../key-vault/general/security-features.md), cloudového externího systému správy klíčů Azure. Pokud je přístup k trezoru klíčů odvolaný, databáze nemůže být dešifrována a načtena do paměti. Azure Key Vault poskytuje platformu pro správu centrálních klíčů, využívá důkladně monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které vám pomůžou splnit požadavky na dodržování předpisů zabezpečení.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (šifrování při použití)

![Diagram znázorňující základy funkce Always Encrypted. Databáze SQL s zámkem je k dispozici pouze v aplikaci obsahující klíč.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) je funkce navržená tak, aby chránila citlivá data uložená v konkrétních databázových sloupcích z Accessu (například čísla kreditních karet, národní identifikační čísla nebo data podle _potřeby_ ). Patří sem správci databáze nebo jiní privilegovaní uživatelé, kteří mají oprávnění k přístupu k databázi, aby mohli provádět úlohy správy, ale nemají přístup k určitým datům v zašifrovaných sloupcích bez obchodních potřeb. Data jsou vždy šifrována, což znamená, že šifrovaná data jsou dešifrována pouze pro zpracování klientskými aplikacemi s přístupem k šifrovacímu klíči. Šifrovací klíč se nikdy nezveřejňuje SQL Database nebo spravované instance SQL a může být uložený buď v [úložišti certifikátů Windows](always-encrypted-certificate-store-configure.md) , nebo v [Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

![Diagram znázorňující dynamické maskování dat Obchodní aplikace odesílá data do databáze SQL, která před odesláním zpět do obchodní aplikace data maskuje.](./media/security-overview/azure-database-ddm.png)

Dynamické maskování dat minimalizuje odhalení citlivých dat tím, že je pro uživatele bez oprávnění maskuje. Dynamické maskování dat automaticky zjišťuje potenciálně citlivá data v Azure SQL Database a spravované instanci SQL a poskytuje užitečná doporučení pro maskování těchto polí s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace najdete v tématu [Začínáme s SQL Database a dynamické maskování dat spravované instance SQL](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Správa zabezpečení

### <a name="vulnerability-assessment"></a>Posouzení ohrožení zabezpečení

[Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je jednoduchá konfigurace služby, která může zjišťovat, sledovat a pomáhat s tím, že je možné slabá místa v databázi s cílem proaktivně zlepšit celkové zabezpečení databáze. Posouzení ohrožení zabezpečení (VA) je součástí nabídky Azure Defender pro SQL, což je jednotný balíček pro pokročilé funkce zabezpečení SQL. K posouzení ohrožení zabezpečení se dá využít a spravovat prostřednictvím centrálního portálu Azure Defender pro SQL Portal.

### <a name="data-discovery-and-classification"></a>Zjišťování a klasifikace dat

Zjišťování a klasifikace dat (aktuálně ve verzi Preview) poskytuje pokročilé možnosti integrované do Azure SQL Database a SQL Managed instance pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Vyhledávání a klasifikace důležitých citlivých dat (podniková/finanční, zdravotní péče, osobní údaje atd.) může hrát pivotovou roli ve vaší organizaci stature Information Protection. Může sloužit jako infrastruktura na:

- Různé scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklé přístup k citlivým datům.
- Řízení přístupu a posílení zabezpečení databází obsahujících vysoce citlivá data.
- podporu dodržování předpisů v oblasti ochrany osobních údajů a dalších regulací;

Další informace najdete v tématu [Začínáme se zjišťováním a klasifikací dat](data-discovery-and-classification-overview.md).

### <a name="compliance"></a>Dodržování předpisů

Kromě výše uvedených funkcí a funkcí, které vám pomohou aplikace splňovat různé požadavky na zabezpečení, Azure SQL Database se také účastní pravidelných auditů a bylo certifikováno pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://www.microsoft.com/trust-center/compliance/compliance-overview) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="next-steps"></a>Další kroky

- Diskuzi o používání přihlašovacích údajů, uživatelských účtů, databázových rolí a oprávnění v SQL Database a spravované instanci SQL najdete v tématu [Správa přihlašovacích jmen a uživatelských účtů](logins-create-manage.md).
- Diskuzi o auditování databáze najdete v tématu [auditování](../../azure-sql/database/auditing-overview.md).
- Diskuzi o detekci hrozeb najdete v tématu [detekce hrozeb](threat-detection-configure.md).