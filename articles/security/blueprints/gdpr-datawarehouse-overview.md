---
title: Zabezpečení Azure a dodržování předpisů – datový sklad gdpr
description: Zabezpečení Azure a dodržování předpisů – datový sklad gdpr
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 6a777418c5381f1f52bae31ad4e697248587fc6d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576540"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Zabezpečení Azure a dodržování předpisů: datový sklad gdpr

## <a name="overview"></a>Přehled
Obecné nařízení (GDPR) obsahuje mnoho požadavků o shromažďování, ukládání a používání osobní informace, včetně jak organizacím identifikovat a zabezpečení osobních údajů, plnění požadavků na transparentnosti, zjišťovat a sestavy porušení osobní údaje a o ochraně osobních údajů pracovníci trénování a ostatními zaměstnanci. GDPR poskytuje větší kontrolu nad jejich osobní údaje jednotlivce a ukládá mnoho nových závazků na organizací, které shromažďovat, zpracovávat, analyzovat osobní údaje. GDPR ukládá nová pravidla pro organizace, které nabízejí zboží a služby lidem v Evropské unii (EU) nebo které shromažďovat a analyzovat data svázaná s pobytem v EU. GDPR se vztahuje bez ohledu na to, kde se nachází organizace.

Microsoft proto navrhl Azure se špičkovou bezpečnostní opatření a zásad ochrany osobních údajů zabezpečující data v cloudu, včetně kategorií osobní údaje, které jsou identifikované GDPR. Společnosti Microsoft [smluvní podmínky](http://aka.ms/Online-Services-Terms) garantoval splňování požadavků procesorů Microsoft.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny pro nasazení architektury datového skladu v Azure, která pomáhá zajistit s požadavky GDPR. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky, kteří k vytvoření a konfigurace vlastních řešení datových skladů v Azure. Zákazníci mohou využívat tuto referenční architekturu a postupujte podle společnosti Microsoft [čtyři kroky](https://aka.ms/gdprebook) v cestě k dodržování předpisů GDPR:
1. Vyhledávání: Určete, které osobní údaje existuje a kde se nachází.
2. Správa: Řídí jak osobní data se budou používat a získat přístup.
3. Chránit: Řízení zabezpečení, které hrozbami, detekci a reakce na ohrožení zabezpečení a úniky dat navázat.
4. Sestava: Uchovávat vyžaduje dokumentaci a spravovat požadavky na data a oznámení o porušení zabezpečení.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky a nepoužívali-je v produkčním prostředí. Je třeba počítat s následujícím:
- Tato architektura poskytuje standardní hodnoty pomáhá zákazníkům nasadit úlohy o velikosti do Azure v podobě GDPR nedodržující předpisy.
- Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje referenční architekturu, která implementuje vysoce výkonný a zabezpečený cloudový datový sklad. Existují dvě úrovně oddělení dat v této architektuře: kde se importují data, uložená a připravené v clusterovaném prostředí SQL a jiné pro službu Azure SQL Data Warehouse tam, kde je načtení dat pomocí nástroje ETL (třeba [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Dotazy T-SQL) pro zpracování. Jakmile jsou data uložená ve službě Azure SQL Data Warehouse, analýzy můžete spouštět ve velkém měřítku.

Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlé vytvoření sestavy z Azure SQL Data Warehouse. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Data ve službě Azure SQL Data Warehouse uložená v relačních tabulkách se sloupcovým úložištěm, formátu, který výrazně snižuje náklady na úložiště dat při současném zvyšování výkonu dotazů.  V závislosti na požadavcích využití můžete vertikálně navýšit nebo snížit výpočetní prostředky Azure SQL Data Warehouse nebo vypne zcela Pokud nejsou žádné aktivní procesy vyžadující výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje datový provoz SQL, zajistit vysoký výkon. Všechny virtuální počítače v této referenční architektuře můžete nasadit skupinu dostupnosti pomocí instance systému SQL Server nakonfigurován ve skupině dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii možnosti.

Tato referenční Architektura datového skladu zahrnuje také vrstvu služby Active Directory (AD) pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší struktury doménové struktury AD umožňuje nepřetržité provádění prostředí i v případě, že přístup k doménové struktuře větší není k dispozici. Všechny virtuální počítače jsou připojené k vrstvě služby Active Directory a použití zásad skupiny služby Active Directory k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Virtuální počítač slouží jako hostitel bastionu správy, poskytuje zabezpečené připojení Správci nasazení přístup k prostředkům. Data se načtou do pracovní oblasti přes tento hostitel správy bastionu. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.**

![Datový sklad pro diagram referenční architektury GDPR](images/gdpr-datawarehouse-architecture.png?raw=true "datového skladu pro GDPR diagram referenční architektury")

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.

-   Azure Virtual Machines
    - (1) bastion Host
    -   (2) řadič domény active Directory
    -   (2) uzel clusteru serveru SQL
    -   (1) SQL serveru s kopií clusteru
-   Skupiny dostupnosti
    - Řadiče domény služby Active Directory
    - Uzly clusteru SQL a s kopií clusteru
-   Virtual Network
    - (4) podsítě
    -   (4) skupiny zabezpečení sítě
- SQL Data Warehouse
- SQL Server Reporting Services
- Nástroj pro vyrovnávání zatížení Azure SQL
- Azure Active Directory
- Trezor služby Recovery Services
- Azure Key Vault
- Operations Management Suite (OMS)
- Katalog dat Azure
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je organizace datového skladu (podnikového), který využívá masivně paralelní zpracování (MPP) k rychlému spouštění složitých dotazů nad petabajty dat., uživatelé si tak můžou efektivně Identifikujte osobní údaje. Uživatele můžete použít jednoduchých dotazů PolyBase T-SQL k importu velkých objemů dat do SQL Data Warehouse a využívat sílu architektury MPP ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav pomocí tabulek, grafů, map, měřidel, maticích a informace pro Azure SQL Data Warehouse.

**Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat snadno objevitelné a srozumitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a vyhledávat osobní údaje. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat spolu s odkazem na umístění zdroje dat přidá do katalogu Data Catalog. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy (RDP), musí být definován ve skupině zabezpečení sítě (NSG).

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby pověření a dalších tajných kódů je spouštět v chráněném prostředí, která je oddělená od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť, která adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahovat seznamů řízení přístupu (ACL), která povolují nebo zakazují provoz ve virtuální síti. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro datovou vrstvu (clustery SQL serveru, SQL Server s kopií clusteru a nástroje pro vyrovnávání zatížení SQL)
  - Skupina zabezpečení sítě pro správu hostitele bastionu
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro SQL Server Reporting Services

Skupiny zabezpečení sítě mají konkrétní porty a protokoly, otevřete řešení mohli pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - OMS Log Analytics je připojen k [skupin zabezpečení sítě a Diagnostika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu pomocí více měr, včetně šifrování a auditování databáze.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit osobní údaje na podporu závazky zabezpečení organizace a požadavky na dodržování předpisů GDPR definované.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k poskytování šifrování disků s operačním systémem a daty. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že uložené osobní data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že nikdy zobrazuje citlivé osobní údaje jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo serverů aplikace s přístupem ke klíčům k datům ve formátu prostého textu.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkce je možné přestat zpracování subjekty údajů, protože umožňuje uživatelům přidání vlastních vlastností do databázové objekty a data označit jako "Vyřazeno" pro podporu aplikační logiku a zabránit zpracování přidružené osobních údajů.
- [Zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [SQL Database dynamických dat maskování (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých osobních dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. DDM můžete automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá s identifikací osobních údajů kvalifikaci ochrany podle nařízení GDPR a pro omezení přístupu tak, že neexistuje databáze prostřednictvím před neoprávněným přístupem. **Poznámka: Zákazníci bude nutné upravit nastavení DDM dodržovat svoje schéma databáze.**

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti pro správu přístup k osobním údajům v prostředí Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří ve službě AAD, včetně uživatele, kteří používají SQL Database.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Šifrování sloupců databáze dále používá AAD k ověřování aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k osobním datům. Přístup k předplatnému je omezená na správce předplatného.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako je osobní údaje.  Mohou správci AAD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Ochrana Identity AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou osobních údajů a přístup k tyto údaje:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení (HSM). Typ klíče je HSM chráněné 2048bitový klíč RSA.
- Minimální požadovaná oprávnění pomocí RBAC jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Výstrahy zabezpečení**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) zákazníkům umožňuje monitorování provozu, shromažďovat protokoly a analýza zdroje dat pro hrozby. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a ochrana osobních údajů. Azure Security Center zahrnuje [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) pro každou zjištěnou hrozbou pro pomoc týmům reakce na incidenty vyšetřením a odstraněním hrozeb.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: Server úlohy jsou seskupené v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače umožňuje rychlejší obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) poskytuje rozsáhlou protokolování systémových a uživatelských aktivit a také stav systému. OMS [Log Analytics](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a místním prostředí.
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Blob v Azure, tabulky a fronty protokoly.
- **Archivace protokolu**: všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci. Tyto protokoly se připojit ke službě Azure Log Analytics pro zpracování, ukládání a vytváření sestav na řídicím.

Kromě toho jsou zahrnuty jako součást této architektury následující řešení OMS:
-   [Posouzení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
-   [Posouzení antimalwaru](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The Antimalwarovým řešením hlásí stav malwaru, ohrožení a ochrana.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spouští a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): řídicí panel zabezpečení a Audit poskytuje podrobný pohled na stav zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýza hrozeb a běžné dotazy na zabezpečení.
-   [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): řešení Update Management umožňuje správu zákazníka aktualizace zabezpečení operačního systému, včetně proces instalace požadovaných aktualizací a stav dostupných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Protokoly aktivit Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení Change Tracking umožňuje zákazníkům snadno identifikovat změny v prostředí.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/gdprDWdfd) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Datový sklad pro model hrozeb GDPR](images/gdpr-datawarehouse-threat-model.png?raw=true "datového skladu pro model hrozeb GDPR")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka GDPR](https://aka.ms/gdprCRM) uvádí kontroleru a procesor odpovědnost za všechny články podle nařízení GDPR. Mějte prosím na paměti, že pro služby Azure, je zákazník obvykle kontroleru a vystupuje Microsoft jako zpracovatel.

[Azure zabezpečení a dodržování předpisů – GDPR datového skladu implementace matice](https://aka.ms/gdprDW) poskytuje informace, na které GDPR články jsou vyřešeny architektury datového skladu, včetně podrobného popisu jak implementace splňuje požadavky na každého zahrnutého článku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektury datového skladu. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace a načítání (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete bez nutnosti samostatné ETL načtení dat do Azure SQL Data Warehouse nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje třetích stran, které jsou kompatibilní s SQL serverem, je možné díky technologii PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující AAD tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="optional-services"></a>Volitelné služby
Azure nabízí širokou škálu služeb pro účely pomoci s úložištěm a pracovní data naformátovaný a je neformátovaný. Tyto služby můžete přidat do této referenční architektuře v závislosti na požadavcích zákazníků:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná Cloudová služba, která je vytvořená pro komplexní hybridní extract-transform-load (ETL), extrakce načítání a transformace (ELT) a data projekty integrace. Azure Data Factory obsahuje funkce a trasování a najít osobní údaje, včetně vizualizace a monitorování nástroje k identifikaci při data přijdou a kdy a odkud. Pomocí služby Azure Data Factory můžou zákazníci vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestovat data z různorodých úložišť dat. Tyto kanály umožňují zákazníkům k ingestování dat z interních i externích zdrojů. Zákazníci pak můžete zpracovávat a transformovat data pro výstup do úložišť dat, jako je Azure SQL Data Warehouse.
- Zákazníky můžete připravit, Nestrukturovaná data v [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), což umožňuje sběr dat libovolné velikosti, typu, a rychlost příjmu na jediném místě pro provozní a zjišťovací analýzy.  Azure Data Lake má funkce, které umožňují extrahování a převod data. Azure Data Lake Store je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop a integruje se s dalšími službami Azure, jako je Azure SQL Data Warehouse krásně.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
