---
title: Přehled zabezpečení
description: Přečtěte si o azure SQL database a sql server zabezpečení, včetně rozdílů mezi clouda a SQL Server místně.
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
ms.openlocfilehash: 1d08770d81ade2d976142b2ce1fd94dae044cf32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461391"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Přehled možností zabezpečení Azure SQL Database

Tento článek popisuje základy zabezpečení datové vrstvy aplikace pomocí Azure SQL Database. Popsaná strategie zabezpečení se řídí vrstvenou obranou do hloubky, jak je znázorněno na obrázku níže, a pohybuje se zvenčí v:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Zabezpečení sítě

Microsoft Azure SQL Database poskytuje relační databázovou službu pro cloudové a podnikové aplikace. Z důvodu ochrany zákaznických dat brání brány firewall přístupu k databázovému serveru, dokud není přístup explicitně udělen na základě ip adresy nebo původu provozu virtuální sítě Azure.

### <a name="ip-firewall-rules"></a>Pravidla brány firewall IP

Pravidla brány firewall IP udělují přístup k databázím na základě původní IP adresy každého požadavku. Další informace najdete [v tématu Přehled pravidel brány firewall Azure SQL Database a BRÁNY SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

[Koncové body virtuální síťové služby](../virtual-network/virtual-network-service-endpoints-overview.md) rozšiřují připojení virtuální sítě přes páteřní síť Azure a umožňují Azure SQL Database identifikovat podsíť virtuální sítě, ze které provoz pochází. Chcete-li povolit provoz k dosažení Azure SQL Database, použijte [značky služby](../virtual-network/security-overview.md) SQL k povolení odchozího provozu prostřednictvím skupin zabezpečení sítě.

[Pravidla virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) umožňují Azure SQL Database přijímat jenom komunikaci, která se odesílá z vybraných podsítí uvnitř virtuální sítě.

> [!NOTE]
> Řízení přístupu pomocí pravidel brány firewall se *nevztahuje* na **spravovanou instanci**. Další informace o potřebné konfiguraci sítě naleznete v [tématu připojení ke spravované instanci](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Správa přístupu

> [!IMPORTANT]
> Správa databází a databázových serverů v rámci Azure je řízena přiřazeními rolí vašeho uživatelského účtu portálu. Další informace o tomto článku najdete [v tématu řízení přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Ověřování je proces dokazování, že uživatel je tím, za koho se považuje. Azure SQL Database podporuje dva typy ověřování:

- **Ověřování SQL**:

    Ověřování v databázi SQL odkazuje na ověřování uživatele při připojování k [Azure SQL Database](sql-database-technical-overview.md) pomocí uživatelského jména a hesla. Při vytváření databázového serveru pro databázi musí být zadáno přihlášení správce serveru s uživatelským jménem a heslem. Pomocí těchto pověření může "správce serveru" ověřit libovolnou databázi na tomto databázovém serveru jako vlastník databáze. Poté mohou být další přihlášení SQL a uživatelé vytvořeni správcem serveru, které umožňují uživatelům připojit se pomocí uživatelského jména a hesla.

- **Ověřování služby Azure Active Directory**:

    Azure Active Directory ověřování je mechanismus připojení k [Azure SQL Database](sql-database-technical-overview.md) a SQL Data [Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit ve službě Azure Active Directory (Azure AD). Ověřování Azure AD umožňuje správcům centrálně spravovat identity a oprávnění uživatelů databáze spolu s dalšími službami Microsoftu v jednom centrálním umístění. To zahrnuje minimalizaci úložiště hesel a umožňuje centralizované zásady střídání hesel.

     Správce serveru s názvem **Správce služby Active Directory** musí být vytvořen, aby bylo možné používat ověřování Azure AD s databází SQL. Další informace naleznete [v tématu Připojení k databázi SQL pomocí ověřování služby Azure Active Directory](sql-database-aad-authentication.md). Ověřování Azure AD podporuje spravované i federované účty. Federované účty podporují uživatele systému Windows a skupiny pro doménu zákazníka federované pomocí Azure AD.

    Další dostupné možnosti ověřování služby Azure AD jsou připojení [služby AD Universal Authentication for SQL Server Management Studio,](sql-database-ssms-mfa-authentication.md) včetně [vícefaktorového ověřování](../active-directory/authentication/concept-mfa-howitworks.md) a [podmíněného přístupu](sql-database-conditional-access.md).

> [!IMPORTANT]
> Správa databází a serverů v rámci Azure je řízena přiřazeními rolí vašeho uživatelského účtu portálu. Další informace o tomto článku najdete [v tématu řízení přístupu na základě rolí na webu Azure Portal](../role-based-access-control/overview.md). Řízení přístupu pomocí pravidel brány firewall se *nevztahuje* na **spravovanou instanci**. Další informace o potřebné konfiguraci sítě naleznete v následujícím článku o [připojení ke spravované instanci.](sql-database-managed-instance-connect-app.md)

## <a name="authorization"></a>Autorizace

Autorizace odkazuje na oprávnění přiřazená uživateli v rámci databáze Azure SQL database a určuje, co uživatel může dělat. Oprávnění jsou řízena přidáním uživatelských účtů do [databázových rolí](/sql/relational-databases/security/authentication-access/database-level-roles) a přiřazením oprávnění na úrovni databáze k těmto rolím nebo udělením [určitých oprávnění na úrovni objektu uživateli](/sql/relational-databases/security/permissions-database-engine). Další informace naleznete v [tématu Přihlášení a uživatelé](sql-database-manage-logins.md)

Jako osvědčený postup vytvořte vlastní role v případě potřeby. Přidejte uživatele do role s nejmenšími oprávněními potřebnými k provedení jejich funkce práce. Nepřiřazujte oprávnění přímo uživatelům. Účet správce serveru je členem předdefinované role db_owner, která má rozsáhlá oprávnění a měla by být udělena pouze několika uživatelům s administrativními povinnostmi. Pro aplikace Azure SQL Database použijte [příkaz EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) k určení kontextu spuštění volaného modulu nebo použijte role [aplikací](/sql/relational-databases/security/authentication-access/application-roles) s omezenými oprávněními. Tento postup zajišťuje, že aplikace, která se připojuje k databázi, má nejmenší oprávnění potřebná aplikací. Dodržování těchto osvědčených postupů rovněž podporuje oddělení povinností.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku

Zabezpečení na úrovni řádků umožňuje zákazníkům řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele provádějícího dotaz (například členství ve skupině nebo kontext spuštění). Zabezpečení na úrovni řádků lze také použít k implementaci vlastních konceptů zabezpečení založených na popisku. Další informace najdete v tématu [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Ochrana před hrozbami

SQL Database zabezpečuje zákaznická data tím, že poskytuje možnosti auditování a detekce hrozeb.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditování SQL v protokolech Azure Monitoru a v centru událostí

Auditování databáze SQL sleduje databázové aktivity a pomáhá udržovat dodržování standardů zabezpečení zaznamenáváním událostí databáze do protokolu auditu v účtu úložiště Azure vlastněném zákazníkem. Auditování umožňuje uživatelům sledovat probíhající databázové aktivity, stejně jako analyzovat a zkoumat historické aktivity k identifikaci potenciálních hrozeb nebo podezření na zneužití a narušení zabezpečení. Další informace naleznete v tématu Začínáme s [nástroji Auditování databáze SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Rozšířená ochrana před internetovými útoky

Rozšířená ochrana před internetovými systémy analyzuje protokoly serveru SQL Server za účelem zjištění neobvyklého chování a potenciálně škodlivých pokusů o přístup k databázím nebo jejich zneužití. Výstrahy jsou vytvářeny pro podezřelé aktivity, jako je vkládání SQL, potenciální infiltrace dat a útoky hrubou silou nebo pro anomálie ve vzorcích přístupu k zachycení eskalace oprávnění a použití porušení pověření. Výstrahy jsou zobrazeny z [Azure Security Center](https://azure.microsoft.com/services/security-center/), kde jsou k dispozici podrobnosti o podezřelé aktivity a doporučení pro další šetření uvedené spolu s akcemi ke zmírnění hrozby. Rozšířenou ochranu před internetovými hrozbami lze povolit na server za příplatek. Další informace naleznete [v tématu Začínáme s rozšířenou ochranou před internetovými hrozbami databáze SQL Database](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS zabezpečení transportní vrstvy (šifrování při přenosu)

SQL Database zabezpečuje zákaznická data šifrováním dat v pohybu pomocí [zabezpečení transportní vrstvy](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Sql Server vynucuje šifrování (SSL/TLS) za všech okolností pro všechna připojení. Tím je zajištěno, že všechna data jsou šifrována "na cestě" mezi klientem a serverem bez ohledu na nastavení **Encrypt** nebo **TrustServerCertificate** v připojovacím řetězci.

Jako osvědčený postup doporučujeme, abyste v připojovacím řetězci aplikace zadali šifrované připojení a _**nedůvěřovali**_ certifikátu serveru. To vynutí, aby aplikace ověřit certifikát serveru a tím zabrání aplikace jsou citlivé na man v útoku středního typu.

Například při použití ovladače ADO.NET to je provedeno prostřednictvím **Encrypt=True** a **TrustServerCertificate=False**. Pokud získáte připojovací řetězec z webu Azure Portal, bude mít správné nastavení.

> [!IMPORTANT]
> Všimněte si, že některé ovladače jiných výrobců nemusí používat tls ve výchozím nastavení nebo spoléhat na starší verzi TLS (<1.2), aby fungovaly. V tomto případě SQL Server stále umožňuje připojení k databázi. Doporučujeme však vyhodnotit bezpečnostní rizika povolení těchto ovladačů a aplikací pro připojení k databázi SQL, zejména pokud ukládáte citlivá data.
>
> Další informace o tls a připojení naleznete v [tématu TLS úvahy](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparentní šifrování dat (šifrování v klidovém stavu)

[Transparentní šifrování dat (TDE) pro Azure SQL Database](transparent-data-encryption-azure-sql.md) přidává vrstvu zabezpečení, která pomáhá chránit data v klidovém stavu před neoprávněným nebo offline přístupem k nezpracovaným souborům nebo zálohám. Mezi běžné scénáře patří krádež datového centra nebo nezabezpečená likvidace hardwaru nebo médií, jako jsou diskové jednotky a záložní pásky.TDE šifruje celou databázi pomocí šifrovacího algoritmu AES, který nevyžaduje, aby vývojáři aplikací provázli jakékoli změny v existujících aplikacích.

V Azure jsou všechny nově vytvořené databáze SQL ve výchozím nastavení zašifrovány a šifrovací klíč databáze je chráněn integrovaným certifikátem serveru.  Údržba a střídání certifikátů jsou spravovány službou a nevyžaduje žádný vstup od uživatele. Zákazníci, kteří dávají přednost převzetí kontroly nad šifrovacími klíči, mohou spravovat klíče v [úložišti klíčů Azure](../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Správa klíčů pomocí azure key vaultu

[Přineste si vlastní klíč](transparent-data-encryption-byok-azure-sql.md) (BYOK) podpora [pro transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umožňuje zákazníkům převzít vlastnictví správy klíčů a rotace pomocí Azure [Key Vault](../key-vault/general/secure-your-key-vault.md), Cloud-based Azure externí klíč management systému. Pokud je přístup databáze k trezoru klíčů odvolán, nelze databázi dešifrovat a číst do paměti. Azure Key Vault poskytuje centrální platformu pro správu klíčů, využívá úzce monitorované moduly hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které pomáhají splnit požadavky na dodržování předpisů zabezpečení.

### <a name="always-encrypted-encryption-in-use"></a>Vždy šifrované (šifrování v provozu)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Vždy šifrované](/sql/relational-databases/security/encryption/always-encrypted-database-engine) je funkce určená k ochraně citlivých dat uložených v konkrétních databázových sloupcích před přístupem (například čísla kreditních karet, národní identifikační čísla nebo data na základě _potřeby vědět)._ To zahrnuje správce databáze nebo jiné privilegované uživatele, kteří jsou oprávněni přistupovat k databázi k provádění úloh správy, ale nemají obchodní potřebu přístupu k určitým datům v šifrovaných sloupcích. Data jsou vždy šifrována, což znamená, že šifrovaná data jsou dešifrována pouze pro zpracování klientskými aplikacemi s přístupem k šifrovacímu klíči.  Šifrovací klíč není nikdy vystaven sql a může být uložen buď v [úložišti certifikátů Windows](sql-database-always-encrypted.md) nebo v [trezoru klíčů Azure](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Dynamické maskování dat

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

Maskování dynamických dat sql database omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům. Dynamické maskování dat automaticky zjišťuje potenciálně citlivá data v Azure SQL Database a poskytuje užitečné doporučení maskovat tato pole, s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace naleznete [v tématu Začínáme s maskováním dynamických dat databáze SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Správa zabezpečení

### <a name="vulnerability-assessment"></a>Posouzení ohrožení zabezpečení

[Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovatelná služba, která dokáže zjistit, sledovat a pomoci napravit potenciální chyby zabezpečení databáze s cílem proaktivně zlepšit celkové zabezpečení databáze. Posouzení zranitelnosti (VA) je součástí nabídky advanced data security (ADS), což je jednotný balíček pro pokročilé možnosti zabezpečení SQL. K posouzení ohrožení zabezpečení lze přistupovat a spravovat jej prostřednictvím centrálního portálu SQL ADS.

### <a name="data-discovery--classification"></a>Zjišťování a klasifikace dat

zjišťování dat & klasifikace (aktuálně ve verzi preview) poskytuje pokročilé funkce integrované do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Zjišťování a klasifikace vašich nejcitlivějších údajů (obchodní/finanční, zdravotní péče, osobní údaje atd.) může hrát klíčovou roli ve vaší organizační ochraně informací. Může sloužit jako infrastruktura na:

- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozornění na neobvyklý přístup k citlivým datům.
- Řízení přístupu k databázím obsahujícím vysoce citlivá data a posílení zabezpečení těchto databází.
- podporu dodržování předpisů v oblasti ochrany osobních údajů a dalších regulací;

Další informace naleznete [v tématu Začínáme s & klasifikace zjišťování dat](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Dodržování předpisů

Kromě výše uvedených funkcí a funkcí, které mohou vaší aplikaci pomoci splnit různé požadavky na zabezpečení, se Azure SQL Database také účastní pravidelných auditů a byla certifikována podle řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

## <a name="next-steps"></a>Další kroky

- Diskuse o použití přihlášení, uživatelských účtů, databázových rolí a oprávnění v databázi SQL naleznete v tématu [Správa přihlášení a uživatelských účtů](sql-database-manage-logins.md).
- Diskuse o auditování databáze naleznete v tématu [auditování databáze SQL](sql-database-auditing.md).
- Diskuse o detekci hrozeb naleznete v [tématu zjišťování hrozeb databáze SQL](sql-database-threat-detection.md).
