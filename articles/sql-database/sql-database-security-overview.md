---
title: Přehled zabezpečení služby Azure SQL Database | Dokumentace Microsoftu
description: Další informace o zabezpečení Azure SQL Database a SQL Server, jaký je rozdíl mezi cloudem a místním SQL serverem.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a3f47726b1776b260ff8cc5eac766c23053d4fd0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728398"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Přehled možností zabezpečení Azure SQL Database

Tento článek popisuje základní informace o zabezpečení datové vrstvy aplikace využívající Azure SQL Database. Strategie zabezpečení popsané následuje vrstveného přístupu v obrany, jak je znázorněno na obrázku níže a přesune z vnějšku v:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Zabezpečení sítě

Microsoft Azure SQL Database poskytuje relační databázová služba pro cloudové a podnikové aplikace. Pomáhá chránit zákaznická data, nastavení brání brány firewall síťový přístup k databázovému serveru, dokud se explicitně udělí přístup k oprávnění na základě IP adresy nebo Azure Virtual network provoz původu.

### <a name="ip-firewall-rules"></a>Pravidla firewallu protokolu IP

Pravidla firewallu protokolu IP udělit přístup k databázím v závislosti na zdrojovou IP adresu každého požadavku. Další informace najdete v tématu [přehled Azure SQL Database a SQL Data Warehouse pravidla brány firewall](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě

[Koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) rozšířit připojení k virtuální síti přes páteřní síť Azure a povolení – Azure SQL Database k identifikaci provoz pocházející z podsítě virtuální sítě. Chcete-li chcete povolit příjem provozu Azure SQL Database, použijte SQL [značky služeb](../virtual-network/security-overview.md) umožňující odchozí provoz pomocí skupiny zabezpečení sítě.

[Pravidla virtuální sítě](sql-database-vnet-service-endpoint-rule-overview.md) povolit Azure SQL Database a přijímat pouze komunikaci, kterou jsou odesílány z vybrané podsítě ve virtuální síti.

> [!NOTE]
> Řízení přístupu pomocí pravidel brány firewall nemá *není* platí pro **spravovanou instanci**. Další informace o konfiguraci sítě je nepotřebujete, naleznete v části [připojení k managed instance](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Správa přístupu

> [!IMPORTANT]
> Správa databází a databázových serverů v rámci Azure se řídí rolemi uživatelský účet na portálu společnosti. Další informace v tomto článku najdete v tématu [řízení přístupu na základě rolí na webu Azure portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

Ověřování je proces kontroly uživatel koho se. Azure SQL Database podporuje dva typy ověřování:

- **Ověřování SQL**:

    Ověřování databáze SQL odkazuje na ověření uživatele při připojování k [Azure SQL Database](sql-database-technical-overview.md) pomocí uživatelského jména a hesla. Při vytváření serveru databáze pro databázi je třeba zadat přihlašovací údaje "Server admin" pomocí uživatelského jména a hesla. Pomocí těchto přihlašovacích údajů, "server admin" může ověřit k jakékoli databázi na tomto databázovém serveru jako vlastník databáze. Dalších přihlášeních SQL a uživatelé mohou vytvořit správce serveru, které uživatelům umožňují připojovat pomocí uživatelského jména a hesla.

- **Ověřování pomocí Azure Active Directory**:

    Ověřování pomocí Azure Active Directory je mechanismus pro připojení k [Azure SQL Database](sql-database-technical-overview.md) a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pomocí identit v Azure Active Directory (Azure AD). Ověřování Azure AD umožňuje správcům centrálně správu identit a oprávnění uživatele databáze s společně s dalšími službami Microsoftu v jednom centrálním místě. To zahrnuje minimalizaci ukládání hesel a zásady pro otočení centralizované hesla.

     Volá se správce serveru **správce Active Directory** musí být vytvořený službou SQL Database pomocí ověřování Azure AD. Další informace najdete v tématu [připojení k SQL Database pomocí pomocí ověřování Azure Active Directory](sql-database-aad-authentication.md). Ověřování Azure AD podporuje účty federované i spravované. Federované účty podporují Windows uživatele a skupiny pro federované se službou Azure AD doménu zákazníka.

    Další možnosti ověřování Azure AD, které jsou k dispozici jsou [univerzálního ověřování Active Directory pro SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) připojení včetně [ověřování službou Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) a [ Podmíněný přístup](sql-database-conditional-access.md).

> [!IMPORTANT]
> Správa databází a serverů v rámci Azure se řídí rolemi uživatelský účet na portálu společnosti. Další informace v tomto článku najdete v tématu [řízení přístupu na základě rolí na webu Azure portal](../role-based-access-control/overview.md). Řízení přístupu pomocí pravidel brány firewall nemá *není* platí pro **spravovanou instanci**. Podrobnosti najdete v následujícím článku na [připojení k managed instance](sql-database-managed-instance-connect-app.md) Další informace o konfiguraci sítě potřeba.

Povolení odkazuje na oprávnění přiřazená uživateli ve službě Azure SQL Database a určuje, co uživatel může udělat. Oprávnění se řídí přidání uživatelských účtů do [databázové role](/sql/relational-databases/security/authentication-access/database-level-roles) , které definují oprávnění na úrovni databáze nebo udělení uživatele určité [oprávnění na úrovni objektu](/sql/relational-databases/security/permissions-database-engine). Další informace najdete v tématu [přihlašovacích údajů a uživatelů](sql-database-manage-logins.md)

Jako osvědčený postup přidání uživatelů do role s nejnižšími oprávněními, které jsou potřebné pro jejich pracovní funkci. Účet správce serveru je členem role db_owner, která má rozsáhlé oprávnění a mají udělit uživatelům s rozvahou. Při používání aplikací s Azure SQL Database, použijte [aplikační role](/sql/relational-databases/security/authentication-access/application-roles) s omezenými oprávněními. Tím se zajistí, že má aplikace s připojením k databázi nejnižšími oprávněními, aplikací.

### <a name="row-level-security"></a>Zabezpečení na úrovni řádku

Zabezpečení na úrovni řádku umožňuje řízení přístupu k řádkům v databázové tabulce na základě charakteristiky uživatele spouštějícího dotaz (například skupiny členství nebo kontext spuštění). Další informace najdete v tématu [Zabezpečení na úrovni řádku](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Tuto metodu ověřování pomocí uživatelského jména a hesla. 

Přehled oprávnění ve službě Azure SQL Database najdete v tématu [přihlašovacích údajů a uživatelů](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Ochrana před hrozbami

SQL Database chrání vaše zákaznická data tím, že poskytuje možnosti detekce hrozeb a auditování.

### <a name="sql-auditing-in-log-analytics-and-event-hubs"></a>Auditování SQL v Log Analytics a Event Hubs

Auditování služby SQL Database sleduje databázové aktivity a pomáhá udržovat dodržování standardů zabezpečení díky zaznamenávání databázových událostí k auditu protokolu v účtu úložiště Azure vlastněné zákazníkem. Auditování umožňuje uživatelům sledovat probíhající databázové aktivity a pomocí analýzy a zkoumání historické aktivity identifikovat potenciální hrozby nebo možné zneužití a narušení zabezpečení. Další informace najdete v článku s [auditování služby SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detekce hrozeb

Detekce vylepšuje auditování prostřednictvím analýzy protokolů auditu pro neobvyklé chování a potenciálně škodlivých hrozeb se pokusí o přístup k databázím nebo jejich zneužití. Vytvoří se výstrahy pro podezřelé aktivity nebo vzory přístupu k neobvyklé například útoky prostřednictvím injektáže SQL, potenciální data po infiltraci a útoky na hesla hrubou silou. Výstrahy detekce hrozeb se z pohledu [Azure Security Center](https://azure.microsoft.com/services/security-center/), kde jsou uvedeny podrobnosti o podezřelých aktivitách a doporučení pro další šetření uveden spolu s akce ke zmírnění hrozby. Detekce hrozeb stojí 15 USD a server za měsíc. Prvních 60 dní je zdarma. Další informace najdete v tématu [Začínáme s detekcí hrozeb SQL Database](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Information protection a šifrování

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Zabezpečení transportní vrstvy protokolu TLS (šifrování během přenosu)

SQL Database chrání vaše zákaznická data tím, že šifruje data přenášená data pomocí [Transport Layer Security](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).

> [!IMPORTANT]
> Azure SQL Database vynucuje šifrování (SSL/TLS) na všechny časy pro všechna připojení, které zajišťuje, že všechna data jsou zašifrovaná "při přenosu" mezi databází a klienta. K tomu dochází bez ohledu na nastavení **šifrovat** nebo **TrustServerCertificate** v připojovacím řetězci.
>
> V připojovacím řetězci vaší aplikace, ujistěte se, že jste určili šifrované připojení a _není_ důvěřovat certifikátu serveru (to je ovladač pro technologii ADO.NET **Encrypt = True** a  **TrustServerCertificate = False**). To pomáhá zabránit aplikaci v muže v prostředním útoku vynucením aplikace k ověření serveru a vynucení šifrování. Připojovací řetězec můžete získat z webu Azure portal, bude mít správné nastavení.
>
> Informace o připojení a TLS najdete v tématu [požadavky TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparentní šifrování dat (šifrování neaktivních)

[Transparentní šifrování dat (TDE) pro službu Azure SQL Database](transparent-data-encryption-azure-sql.md) přidává další vrstvu zabezpečení, které pomáhají chránit data v klidovém stavu offline nebo neoprávněným přístupem a nezpracovaných souborů nebo zálohy. Mezi obvyklé scénáře patří datacentra, krádeže nebo nezabezpečené vyřazení hardwaru nebo médií, jako jsou diskové jednotky a záložní pásky. Transparentní šifrování dat zašifruje pomocí šifrovacího algoritmu standardu AES, který nevyžaduje žádné změny existujících aplikací vývojářům aplikací celé databáze.

V Azure všechny nově vytvořené databáze SQL se šifrují ve výchozím nastavení a šifrovací klíč databáze je chráněno certifikátem integrovaného serveru.  Certifikát údržby a otočení služba spravuje a nevyžaduje vstup od uživatele. Zákazníci, kteří dávají přednost převzít kontrolu nad šifrovací klíče mohou spravovat klíče v [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Správa klíčů s Azure Key Vault

[Funkce přineste si vlastní klíč](transparent-data-encryption-byok-azure-sql.md) podpory (BYOK) pro [transparentního šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) umožňuje zákazníkům převzít vlastnictví správy klíčů a otočení pomocí [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), Azure v cloudu systém externí správu klíčů. Pokud odvolání přístupu k trezoru klíčů databázi, databázi nelze dešifrovat a načíst do paměti. Služba Azure Key Vault poskytuje platformu centrální správu klíčů, využívá modulů úzce monitorovaných hardwarového zabezpečení (HSM) a umožňuje oddělení povinností mezi správou klíčů a dat, které vám pomohou splnit požadavky na dodržování předpisů zabezpečení.

### <a name="always-encrypted-encryption-in-use"></a>Funkce Always Encrypted (šifrování v použití)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[S funkcí Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) je funkce, navržená k ochraně citlivých dat uložené ve sloupcích konkrétní databázi z aplikace access (třeba čísla kreditních karet, národní identifikační čísla nebo data na _znát_ základ). To zahrnuje správce databáze nebo další privilegovaní uživatelé, kteří mají oprávnění pro přístup k databázi provádět úlohy správy, ale žádné nutně potřebují přístup k datům konkrétního v šifrované sloupce. Data jsou vždy šifrována, což znamená, že šifrovaná data se dešifrují jenom pro zpracování u klientských aplikací s přístupem k šifrovacímu klíči.  Šifrovací klíč se nikdy vystavena SQL a může být uložená buď v [Windows Store certifikát](sql-database-always-encrypted.md) nebo v [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Maskování

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Dynamické maskování dat

Maskování dynamických dat SQL Database omezuje riziko ohrožení citlivých dat pomocí jejich maskování pro neoprávněné uživatele. Automaticky maskování dynamických dat zjišťuje potenciálně citlivá data ve službě Azure SQL Database a poskytuje užitečná doporučení pro maskování těchto polí s minimálním dopadem na aplikační vrstvu. Funguje tak, že maskuje citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění. Další informace najdete v tématu [Začínáme s SQL Database dynamické maskování dat](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Statická data maskování

[Statické maskování dat](/sql/relational-databases/security/static-data-masking) je nástroj na straně klienta k dispozici v [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18,0 ve verzi preview 5 a vyšší.  Statické maskování dat umožňuje uživatelům vytvoření kopie databáze, ve kterém byl trvale maskované data ve vybraných sloupcích. K dispozici funkce maskování patří NULL maskování, maskování jednu hodnotu, náhodně a maskování shuffle skupiny a řetězec složené maskování. Maskované kopii databáze budou moct oddělení produkční a testovací prostředí sdílením maskované kopírování organizace. Dostatečně ochranu citlivých dat a všechny ostatní vlastnosti databáze pravděpodobně nebyla zachována. Maskování databáze se doporučuje, ve kterém jsou vyžadována třetích stran přístup k databázím.

## <a name="security-management"></a>Správa zabezpečení

### <a name="vulnerability-assessment"></a>Posouzení ohrožení zabezpečení

[Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovat službu, můžete zjistit, sledovat a pomáhají náprava potenciálních ohrožení zabezpečení databáze s cílem proaktivně zvýšit celkové zabezpečení databáze. Posouzení ohrožení zabezpečení (VA) je součástí nabídky zabezpečení (reklamy) pokročilé data, která je jednotný balíček pro pokročilé funkce zabezpečení SQL. Posouzení ohrožení zabezpečení je možné získat přístup a spravovat prostřednictvím portálu pro centrální SQL reklamy.

### <a name="data-discovery--classification"></a>Zjišťování a klasifikace dat

Data zjišťování a klasifikace (aktuálně ve verzi preview) nabízí rozšířené možnosti, které jsou integrované do Azure SQL Database pro zjišťování, klasifikace, označování popisky a ochranu citlivých dat ve vašich databázích. Zjišťování a klasifikace nanejvýš citlivá data (obchodní/finanční, zdravotní péče, osobní údaje, atd.) můžete přehrát velmi důležitou roli v vaší organizace zásadní roli. informace o ochranu. Může sloužit jako infrastruktura pro:

- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozorňuje na neobvyklé přístup k citlivým datům.
- Řízení přístupu k a posílení zabezpečení, databáze obsahující vysoce citlivá data.
- Pomáhá splnit požadavky na dodržování legislativních předpisů a data standardy ochrany osobních údajů.

Další informace najdete v tématu [začít pracovat s zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Dodržování předpisů

Kromě výše uvedených funkcí a funkci, která může pomoci vaší aplikace vyhovět různým nárokům na zabezpečení, Azure SQL Database také účastní pravidelných auditů a byla certifikována pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/), kde také najdete nejnovější seznam [certifikátů služby SQL Database v oblasti dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Další postup

- Diskuzi o používání funkcí řízení přístupu ve službě SQL Database najdete v tématu [Řízení přístupu](sql-database-control-access.md).
- Informace o auditování databáze, najdete v článku [auditování služby SQL Database](sql-database-auditing.md).
- Diskuzi o detekci hrozeb, naleznete v tématu [detekce hrozeb služby SQL Database](sql-database-threat-detection.md).
