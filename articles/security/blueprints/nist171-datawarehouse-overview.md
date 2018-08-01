---
title: Zabezpečení Azure a dodržování předpisů – datový sklad pro SP NIST 800-171
description: Zabezpečení Azure a dodržování předpisů – datový sklad pro SP NIST 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: e2d33624e7742986f11129ff4d719edb944ebab1
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392086"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Zabezpečení Azure a dodržování předpisů – datový sklad pro SP NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny k nasazení architektury datového skladu v Azure, která implementuje podmnožinu SP NIST. zákazníci 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky, kteří k vytvoření a konfigurace vlastních řešení datových skladů v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky a nepoužívali-je v produkčním prostředí. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje referenční architekturu implementující vysoce výkonný a zabezpečený cloudový datový sklad. Existují dvě úrovně oddělení dat v této architektuře: kde se importují data, uložená a připravené v clusterovaném prostředí SQL a jiné pro službu Azure SQL Data Warehouse tam, kde je načtení dat pomocí nástroje extrakce, transformace a načítání (třeba [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) dotazy T-SQL) pro zpracování. Jakmile jsou data uložená ve službě Azure SQL Data Warehouse, analýzy můžete spouštět ve velkém měřítku.

Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services pro rychlé vytvoření sestavy z Azure SQL Data Warehouse. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Azure SQL Data Warehouse ukládá data v relačních tabulkách se sloupcovým úložištěm, formátu, který výrazně snižuje náklady na úložiště dat při současném zvyšování výkonu dotazů. V závislosti na požadavcích využití můžete vertikálně navýšit nebo snížit výpočetní prostředky Azure SQL Data Warehouse nebo vypne zcela Pokud nejsou žádné aktivní procesy vyžadující výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje datový provoz SQL, zajistit vysoký výkon. Všechny virtuální počítače v této referenční architektuře můžete nasadit skupinu dostupnosti pomocí instance systému SQL Server nakonfigurován ve skupině dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii možnosti.

Tato referenční Architektura datového skladu zahrnuje také vrstvu služby Active Directory pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší struktury doménové struktury služby Active Directory umožňuje nepřetržité provádění prostředí i v případě, že přístup k doménové struktuře větší není k dispozici. Všechny virtuální počítače jsou připojené k vrstvě služby Active Directory a použití zásad skupiny služby Active Directory k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci datacentra vybraného zákazníka pro odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že data budou replikovat do sekundárního datacentra stovky mil okamžitě a znovu uložené jako tři kopie v rámci stejné datové centrum, brání což vede ke ztrátě nepříznivých událostí ve primárního datového centra zákazníka data.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory, řízení přístupu na základě role se používá pro řízení přístupu k nasazené prostředky, včetně jejich klíčů ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Virtuální počítač slouží jako hostitel bastionu správy, poskytuje zabezpečené připojení Správci nasazení přístup k prostředkům. Data se načtou do pracovní oblasti přes tento hostitel správy bastionu. **Microsoft doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.**

![Datový sklad pro NIST SP 800-171 diagram referenční architektury](images/nist171-dw-architecture.png "datový sklad pro NIST SP 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další podrobnosti najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Skupiny dostupnosti
    - Řadiče domény služby Active Directory
    - Uzly clusteru SQL a s kopií clusteru
- Azure Active Directory
- Katalog dat Azure
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Nástroj pro vyrovnávání zatížení Azure
- Azure Storage
- Azure Log Analytics
- Azure Virtual Machines
    - (1) bastion Host
    - (2) řadič domény active Directory
    - (2) uzel clusteru serveru SQL
    - (1) SQL serveru s kopií clusteru
- Azure Virtual Network
    - (1) /16 sítě
    - (4) /24 sítě
    - (4) skupiny zabezpečení sítě
- Trezor služby Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je podnikového datového skladu, které využívá připojení k rychlému spouštění složitých dotazů nad petabajty dat. masivně paralelní zpracování. Uživatele můžete použít jednoduchých dotazů PolyBase T-SQL k importu velkých objemů dat do SQL Data Warehouse a využívat sílu výkonným paralelním zpracováním ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav pomocí tabulek, grafů, map, měřidel, maticích a informace pro Azure SQL Data Warehouse.

**Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat snadno objevitelné a srozumitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a vyhledávat data. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat spolu s odkazem na umístění zdroje dat přidá do katalogu Data Catalog. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy, musí být definován ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby pověření a dalších tajných kódů je spouštět v chráněném prostředí, která je oddělená od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro datovou vrstvu (clustery SQL serveru, SQL Server s kopií clusteru a nástroje pro vyrovnávání zatížení SQL)
  - Skupina zabezpečení sítě pro hostitele bastion pro správu
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro SQL Server Reporting Services

Skupiny zabezpečení sítě mají určité porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro každou skupinu zabezpečení sítě:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Log Analytics je připojen k [diagnostiky skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupinu zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu pomocí více měr, včetně šifrování a auditování databáze.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k zajištění šifrování svazku pro operační systém a datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo serverů aplikace s přístupem ke klíčům k datům ve formátu prostého textu.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) je možné přestat zpracování subjekty údajů, protože umožňuje uživatelům přidání vlastních vlastností do databázové objekty a data označit jako "Vyřazeno" pro podporu aplikační logiku a zabránit zpracování související finanční data.
- [Zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá při snížení přístup k citlivým datům tak, že neexistuje databáze prostřednictvím před neoprávněným přístupem. **Pro úpravu nastavení dodržovat svoje schéma databáze maskování dynamických dat zodpovídají zákazníci.**

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří v Azure Active Directory, včetně uživatele, kteří používají SQL Database.
-   Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu Jak [integrovat aplikace s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure Active Directory k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každý uživateli neomezený přístup pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k prostředkům a datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako jsou data.  Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je že modul hardwarového zabezpečení chráněných klíčů RSA 2048 bitů.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro virtual machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

Kromě toho tato referenční architektura využívá [sken posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) ve službě Azure Security Center. Po nakonfigurování agenta partnera (třeba Qualysu) hlásí data o ohrožení zabezpečení na platformu partnera pro správu. Naopak platforma partnera pro správu poskytuje ohrožení zabezpečení a data zpět do služby Azure Security Center, monitorování stavu umožňuje zákazníkům rychle identifikovat ohrožené virtuální počítače.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: Server úlohy jsou seskupené v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů v této architektuře. Pomocí trezoru služby Recovery Services zákazníci můžou obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače umožňuje rychlejší obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly služby Azure Storage, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy v pracovním prostoru Operations Management Suite, což umožňuje společnou analýzu bez ohledu na jejich původní zdroj všechna data. Kromě toho Azure Security Center umožňuje integrací s Log Analytics a umožňuje zákazníkům používat dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby Azure SQL Database. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-dw-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Datový sklad pro NIST SP 800-171 model hrozeb](images/nist171-dw-threat-model.png "datový sklad pro NIST SP 800-171 model hrozeb")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 datového skladu ovládací prvek implementace matice](https://aka.ms/nist171-dw-cim) poskytuje informace, na které aktualizací SP NIST 800-171 ovládací prvky jsou zahrnuté do architektury datového skladu, včetně podrobných Popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektury datového skladu. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces extrakce, transformace a načítání
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete bez nutnosti samostatné extract-transform-load načtení dat do Azure SQL Data Warehouse nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje třetích stran, které jsou kompatibilní s SQL serverem, je možné díky technologii PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat se službou Azure Active Directory v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) stávající Azure Active Directory tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby Azure Active Directory.

### <a name="optional-services"></a>Volitelné služby
Azure nabízí širokou škálu služeb pro účely pomoci s úložištěm a pracovní data naformátovaný a je neformátovaný. Tyto služby můžete přidat do této referenční architektuře v závislosti na požadavcích zákazníků:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná Cloudová služba, která je vytvořená pro projekty integrace komplexní hybridní extrakce, transformace a načítání, extrakce, načítání a transformace a data. Azure Data Factory obsahuje možnosti, které vám pomohou trasování a vyhledejte data, včetně vizualizace a monitorování nástroje k identifikaci při data přijdou a kdy a odkud. Pomocí služby Azure Data Factory můžou zákazníci vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestovat data z různorodých úložišť dat. Tyto kanály umožňují zákazníkům k ingestování dat z interních i externích zdrojů. Zákazníci pak můžete zpracovávat a transformovat data pro výstup do úložišť dat, jako je Azure SQL Data Warehouse.
- Zákazníky můžete připravit, Nestrukturovaná data v [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), což umožňuje sběr dat libovolné velikosti, typu, a rychlost příjmu na jediném místě pro provozní a zjišťovací analýzy.  Azure Data Lake má funkce, které umožňují extrahování a převod data. Azure Data Lake Store je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop a integruje se s dalšími službami Azure, jako je Azure SQL Data Warehouse krásně.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
