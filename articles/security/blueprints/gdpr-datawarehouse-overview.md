---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - datového skladu pro GDPR
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - datového skladu pro GDPR
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: datového skladu pro GDPR

## <a name="overview"></a>Přehled
Obecné nařízení ochrany dat (GDPR) obsahuje mnoho požadavky o shromažďování, ukládání a pomocí osobní údaje, včetně jak organizace identifikovat a zabezpečit data, osobní zohlednily požadavky průhlednost, zjistit a sestavy narušení osobní data a pracovníky train ochrany osobních údajů a ostatním zaměstnancům. GDPR jednotlivce nabízí větší kontrolu nad jejich osobních údajů a ukládá mnoho nových závazků v organizacích, které shromažďovat, zpracovat nebo analyzovat osobní data. GDPR ukládá nové pravidel na organizace, které nabízejí zboží a služeb lidí z Evropské unie (EU), nebo že shromažďovat a analyzovat data svázané s obyvatele Evropské unie. GDPR platí bez ohledu na to, kde se nachází v organizaci.

Microsoft navrhovat Azure s špičkový bezpečnostní opatření a zásady ochrany osobních údajů k zabezpečení dat v cloudu, včetně osobních údajů, které se identifikovanou pomocí GDPR kategorií. Společnosti Microsoft [smluvních podmínek](http://aka.ms/Online-Services-Terms) potvrdit Microsoft požadavkům procesory.

Tento zabezpečení Azure a dodržování předpisů plán, podle kterého obsahuje pokyny k nasazení Architektura datového skladu v Azure, který pomáhá s požadavky GDPR. Toto řešení ukazuje způsoby, ve kterém můžete zákazníkům splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky sestavení a nakonfigurovat vlastní řešení datového skladu v Azure. Zákazníci mohou využít tuto referenční architekturu a postupujte podle společnosti Microsoft [čtyři kroky](https://aka.ms/gdprebook) v cestě k GDPR dodržování předpisů:
1. Vyhledávání: Určete, které osobní údaje existuje a kde se nachází.
2. Správa: Jak osobní data řídit použití a získat přístup.
3. Chránit: Ovládací prvky zabezpečení k zabránění, zjišťování a reakce na ohrožení zabezpečení a úniky dat vytvořte.
4. Sestava: Uchovávat požadované dokumentaci a spravovat požadavky na data a oznámení v souladu.

Tento referenční architektura, přidružené implementace průvodce a model hrozeb které slouží jako základ pro zákazníky se jejich specifické požadavky přizpůsobit a by se neměla používat jako-je v produkčním prostředí. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem GDPR způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Referenční Architektura datového skladu cloudu vysoce výkonné a zabezpečení, které poskytne toto řešení. Existují dvě samostatné datové vrstvy v této architektuře: jeden kde importu, uloženy a dvoufázové instalace v prostředí clusterů SQL a jiné pro Azure SQL Data Warehouse kde načtení dat pomocí nástroje ETL (například [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Dotazů T-SQL) pro zpracování. Jakmile data jsou uložena v Azure SQL Data Warehouse, analytics můžete spustit v masivním měřítku.

Azure nabízí celou řadu analýz a generování sestav služby pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlou tvorbu sestav z datového skladu SQL Azure. Veškerý provoz SQL se šifrují pomocí protokolu SSL prostřednictvím zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup Azure doporučuje použití důvěryhodné certifikační autority pro lepší zabezpečení.

Data v Azure SQL Data Warehouse je uložena v relační tabulky s sloupcovém úložiště, formátu, který významně snižuje náklady na úložiště dat při zlepšení výkonu dotazů.  V závislosti na požadavky na použití můžete škálovat nahoru nebo dolů nebo vypnuté úplně Pokud nejsou žádné aktivní procesy, které vyžadují výpočetní prostředky Azure SQL Data Warehouse výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje přenosy dat SQL, zajistit vysoký výkon. Všechny virtuální počítače v této referenční architektura nasadit v sada dostupnosti s instancí systému SQL Server, které jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii možnosti.

Tento referenční Architektura datového skladu zahrnuje taky vrstvou Active Directory (AD) pro správu prostředků v architektuře. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší struktury doménové struktury AD, aby vám umožnil průběžné operace prostředí i v případě, že přístup k doménové struktuře větší není k dispozici. Všechny virtuální počítače jsou připojené k doméně k vrstvě služby Active Directory a používat zásady skupiny služby Active Directory k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Virtuální počítač slouží jako hostitel bastionu správy, poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. Data se načte do pracovní oblasti prostřednictvím tohoto hostitele bastionu správy. **Azure doporučuje konfiguraci připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě architektura odkaz.**

![Datový sklad pro diagram architektury odkaz GDPR](images/gdpr-datawarehouse-architecture.png?raw=true "datového skladu pro diagram architektury GDPR odkaz")

Toto řešení používá následující služby Azure. Podrobnosti architektury nasazení najdete v [architektura nasazení služby](#deployment-architecture) části.

-   Azure Virtual Machines
    - (1) bastionu hostitele
    -   (2) řadič domény active Directory
    -   (2) uzel clusteru serveru SQL
    -   (1) SQL serveru s kopií clusteru
-   Skupiny dostupnosti
    - Řadiče domény služby Active Directory
    - Uzly clusteru SQL a určující sdílené složky
-   Virtual Network
    - (4) podsítě
    -   (4) skupiny zabezpečení sítě
- SQL Data Warehouse
- SQL Server Reporting Services
- Nástroj pro vyrovnávání zatížení Azure SQL
- Azure Active Directory
- Trezor služeb zotavení
- Azure Key Vault
- Služby Operations Management Suite (OMS)
- Katalog dat Azure
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura nasazení služby
V následující části Podrobné informace o nasazení a implementace elementy.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je Enterprise Data skladu obsahuje aktualizaci která využívá masivně paralelní zpracování MPP rychle spustit složitých dotazů napříč petabajty dat, což umožňuje uživatelům efektivní Identifikujte osobní data. Uživatelé mohou pomocí jednoduchých dotazů PolyBase T-SQL importovat velké objemy dat do SQL Data Warehouse a využívat power MPP spustit vysoce výkonné analytics.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav s tabulkami, grafů, map, měřidla, maticích a více pro Azure SQL Data Warehouse.

**Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) díky zdroje dat snadno zjistitelné a srozumitelnější uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovat, označí a vyhledávat osobní data. Data zůstávají uložena ve stávajícím umístění, ale kopie jeho metadata se přidá do katalogu Data Catalog, spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

**U chráněných hostitelů**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazené prostředky jenom povolením vzdáleného přenosy z veřejných IP adres v seznamu bezpečných adres. Zdroj provoz pro povolení vzdálené plochy (RDP) komunikace, musí být definován v skupina zabezpečení sítě (NSG).

Toto řešení vytvoří virtuální počítač jako hostitele připojené k doméně bastionu s následující konfigurace:
-   [Rozšíření proti malwaru](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault
-   [Vypnutí automatického zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) snížit spotřebu prostředků virtuálního počítače, když není používán
-   [Ochrana přihlašovacích údajů Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby přihlašovací údaje a jiné tajné spustit v prostředí chráněný, který je izolován od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tuto referenční architekturu definuje privátní virtuální sítě s adresním prostorem 10.0.0.0/16.

**Skupin zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu (ACL), povolí nebo zakážou provoz v rámci virtuální sítě. Skupiny Nsg můžete použít k zabezpečení přenosů dat na podsíť nebo jednotlivé úroveň virtuálního počítače. Následující skupiny Nsg existují:
  - Skupina NSG pro datovou vrstvu (clustery SQL Server, SQL Server s kopií clusteru a nástroj pro vyrovnávání zatížení SQL)
  - Skupina NSG pro správu hostitele bastionu
  - Skupina NSG pro službu Active Directory
  - Skupina NSG pro SQL Server Reporting Services

Každý z skupin Nsg mít určité porty a protokoly otevřete tak, aby řešení může fungovat správně a bezpečně. Kromě toho tyto konfigurace jsou povolené pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložený v účtu úložiště
  - Analýzy protokolů OMS je připojený k [NSG na diagnostiky](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: každé podsítě je přidružen jeho odpovídající skupina NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu prostřednictvím více měr, včetně šifrování a auditování databáze.

**Úložiště Azure**: ke splnění šifrovaná data na požadavky rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá chránit a ochrany osobních údajů na podporu závazky týkajícími se zabezpečení organizace a požadavky na dodržování předpisů definované GDPR.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**Azure SQL Database**: Azure SQL Database instance používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci ochrany informací na rest. Šifrování TDE poskytuje záruku, že uložené osobní data nebyla neoprávněnému přístupu k.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby důvěrných osobních údajů se nikdy jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkce slouží k ukončí zpracování dat tématům, protože umožňuje uživatele, přidání vlastních vlastností do databázové objekty a data označit jako "Nákup ukončen" pro podporu aplikační logiku a zabránit zpracování přidružená osobní data.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [SQL databáze Dynamická Data maskování (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje ohrožení důvěrných osobních údajů ve maskování dat pro uživatele bez oprávnění nebo aplikace. DDM může automaticky zjistit potenciálně citlivých data a navrhněte odpovídající masek má být použita. To pomáhá s identifikací osobních údajů kvalifikující GDPR ochrany a pro omezení přístupu tak, že neexistuje databáze prostřednictvím neoprávněného přístupu. **Poznámka: Zákazníci budou muset upravit nastavení DDM řídit jejich schématu databáze.**

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují funkce, které chcete spravovat přístup k osobním datům v prostředí Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service. Všichni uživatelé v tomto řešení jsou vytvořené v AAD, včetně uživatelů přístup k databázi SQL.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá AAD k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu Jak [chrání citlivá data v databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správcům definovat podrobných přístupová oprávnění k poskytnout pouze takovou úroveň přístupu, aby uživatelé potřebovat k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění pro prostředky Azure, mohou správci povolit jenom určité akce pro přístup k osobním datům. Předplatné přístup je omezen na správce předplatného.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako je například osobní data.  Správci můžou pomocí AAD Privileged Identity Management zjistit, omezte a monitorujte privilegované identity a jejich přístup k prostředkům. Tato funkce slouží také k vynucení na vyžádání, za běhu správy přístupu v případě potřeby.
- [Ochrany identit AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce Azure Key Vault pomoci chránit osobní údaje a přístup k těmto údajům zákazníkům:
- Konfigurace zásad rozšířeného přístupu na základě potřeba.
- Minimální požadovaná oprávnění k klíče a tajné klíče jsou definovány zásad přístupu k trezoru klíč.
- Všechny klíče a tajné klíče v Key Vault mít data vypršení platnosti.
- Všechny klíče v trezoru klíče jsou chráněny modulů specializované hardwarového zabezpečení (HSM). Typ klíče je klíč RSA HSM chráněné 2048 bitů.
- Minimální požadovaná oprávnění pomocí RBAC jsou udělena všechny uživatele a identity.
- Diagnostické protokoly Key Vault je povolená s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací pro klíče jsou omezeny na ty, které jsou potřeba.

**Opravy správy**: virtuální počítače s Windows, které jsou nasazeny jako součást tuto referenční architekturu jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) služby, pomocí kterého je možné vytvořit aktualizované nasazení opravy virtuálních počítačů v případě potřeby.

**Ochrana proti malwaru**: [Antimalware od Microsoftu](https://docs.microsoft.com/azure/security/azure-security-antimalware) u virtuálních počítačů poskytuje možnost ochrany v reálném čase, pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy Když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.

**Výstrahy zabezpečení**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) umožňuje zákazníkům sledovat provoz, shromažďování protokolů a analyzovat zdroje dat pro hrozeb. Kromě toho Azure Security Center používá existující konfigurace služeb Azure k poskytnutí configuration a služby doporučení pomáhají zlepšit zabezpečení a ochrana osobních údajů. Azure Security Center zahrnuje [hrozby intelligence sestavy](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) pro všechny nalezené hrozeb, které pomáhají reakcí na incidenty týmy prozkoumat ohrožení a oprava.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: úlohy serveru jsou seskupené v [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Během plánované i neplánované údržby, splňuje podmínku 99.95 % je k dispozici alespoň jeden virtuální počítač Azure SLA.

**Trezor služeb zotavení**: [trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ve uložený zálohovaná data a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služeb zotavení zákazníkům obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače povolením rychlejší obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném. Protokoly aktivity vám mohou pomoci určit operaci iniciátor čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Azure Blob, tabulek a protokoly fronty.
- **Přihlaste se archivaci**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci. Uchovávání je uživatelsky konfigurovatelného, až do 730 dní, aby splňoval požadavky organizace specifické uchování. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho jsou zahrnuty jako součást této architektury následující OMS řešení:
-   [Hodnocení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/gdprDWdfd) nebo naleznete níže. Tento model může pomoct pochopit body potenciální riziko v infrastruktuře systému při provádění změn zákazníků.

![Datový sklad pro model hrozeb GDPR](images/gdpr-datawarehouse-threat-model.png?raw=true "datového skladu pro model hrozeb GDPR")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů
[Zabezpečení Azure a dodržování předpisů plán, podle kterého – matice odpovědnosti zákazníka GDPR](https://aka.ms/gdprCRM) uvádí řadiče a procesoru odpovědnosti pro všechny články GDPR. Všimněte si, službách Azure zákazníka je obvykle kontroleru a Microsoft funguje jako procesor.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - GDPR datového skladu implementace matice](https://aka.ms/gdprDW) poskytuje informace, na které GDPR Architektura datového skladu, včetně podrobného popisu, jak řešit články implementace splňuje požadavky na každého zahrnutého článku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí nakonfigurovat tak, aby bezpečně navázat připojení k nasazené jako součást tohoto datového skladu referenční architektura prostředky. Zákazníci správně nastavení sítě VPN nebo ExpressRoute, můžete přidejte vrstvu ochrany dat během přenosu.

Implementací přes zabezpečené tunelové propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síti Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunel" informací uvnitř šifrované odkaz mezi sítí a Azure zákazníka. Site-to-Site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. [Režimu tunelového připojení IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá v této možnosti jako mechanismus šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě lepšímu zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místní umístění nebo poskytovatele hostingu serveru Exchange. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Kromě toho protože to je přímé připojení poskytovatele telekomunikace zákazníka, data není přenášet přes Internet a proto nebude vystavena k němu.

Osvědčené postupy pro implementaci zabezpečeného hybridní síti, která rozšiřuje do místní sítě do Azure jsou [k dispozici](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace-zatížením (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete načíst data do Azure SQL Data Warehouse bez nutnosti samostatné ETL nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje jiných výrobců, které jsou kompatibilní se systémem SQL Server, lze pomocí funkce PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je nezbytné pro správu nasazení a zřizování přístup osobám interakci s prostředím. Existující Windows Server Active Directory se dá integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také dokáže spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující aad tím, že nasazené infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="optional-services"></a>Volitelné služby
Azure nabízí celou řadu služeb jako pomoc s úložiště a pracovní formátovaný a neformátovaná data. Pro tuto referenční architekturu v závislosti na požadavcích zákazníka mohou být přidány následující služby:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravované Cloudová služba, která je sestavená pro komplexní hybridní extrakce transformace načítání (ETL), projekty integrace extrakce transformace zatížení (ELT) a data. Azure Data Factory obsahuje možnosti, které vám pomůžou trasování a vyhledejte osobní data, včetně vizualizace a nástroje k identifikovat, kdy byly přijaty dat a kde pochází z monitorování. Pomocí Azure Data Factory, můžete zákazníkům vytvořit a naplánovat názvem kanály, které ingestují data z úložiště dat různorodých pracovních řízené daty. Tyto kanály poskytují zákazníkům pro načítání dat z interních i externích zdrojů. Zákazníci lze zpracovat a transformaci dat pro výstup do úložiště dat, jako je například Azure SQL Data Warehouse.
- Zákazníci mohou Příprava nestrukturovaných dat v [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), což umožňuje sběru dat v libovolnou velikost, typ a rychlost přijímání na jednom místě pro provozní a zjišťovací analýzy.  Azure Data Lake má možnosti, které umožňují extrakci a převod dat. Azure Data Lake Store je kompatibilní s součástí typu nejvíce open source v ekosystému Hadoop a výborně se integruje s jinými službami Azure, jako je například Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
