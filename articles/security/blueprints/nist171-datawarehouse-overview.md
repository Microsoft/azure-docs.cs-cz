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
ms.openlocfilehash: ec608964190c65d8d064582920e53545b9ee62a6
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403998"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Zabezpečení Azure a dodržování předpisů – datový sklad pro SP NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny k nasazení architektury datového skladu v Azure, která implementuje podmnožinu SP NIST. zákazníci 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů. Slouží také jako základ pro zákazníky, kteří k vytvoření a konfigurace vlastních řešení datových skladů v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky. Neměl by se používat jako-je v produkčním prostředí. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů vytvořené pomocí této architektury řešení. Požadavky může lišit v závislosti na konkrétní implementaci každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje referenční architekturu implementující vysoce výkonný a zabezpečený cloudový datový sklad. Existují dvě úrovně oddělení dat v této architektuře. Jednu vrstvu je kterého se naimportuje data, uložená a připraveny v clusterovaném prostředí SQL. Další úroveň je určena pro SQL data warehouse. V rámci této vrstvy načtení dat pomocí nástroje extract-transform-load (ETL) (například [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) dotazy T-SQL) pro zpracování. Po data uložená ve službě SQL Data Warehouse, můžete spouštět analýzy ve velkém měřítku.

Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services pro rychlé vytvoření sestavy z datového skladu SQL. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučujeme použít důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Azure SQL Data Warehouse ukládá data v relačních tabulkách se sloupcovým úložištěm. Tento formát výrazně snižuje náklady na úložiště dat při zvyšuje výkon dotazů. SQL Data Warehouse výpočetních prostředků můžete v závislosti na požadavcích využití vertikálně navýšit nebo snížit nebo vypne zcela Pokud žádné aktivní procesy vyžadují výpočetní prostředky.

Nástroj pro vyrovnávání zatížení systému SQL Server spravuje datový provoz SQL kvůli vysokému výkonu. Všechny virtuální počítače (VM) v této referenční architektuře můžete nasadit skupinu dostupnosti pomocí instance systému SQL Server nakonfigurován ve skupině dostupnosti Always On. Tato konfigurace poskytuje funkce pro vysokou dostupnost a zotavení po havárii.

Tato referenční Architektura datového skladu zahrnuje také vrstvu služby Active Directory pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší doménové struktury služby Active Directory. Tímto způsobem prostředí můžete provozovat nepřetržitě, i když je přístup k větší doménová struktura není k dispozici. Všechny virtuální počítače jsou připojený k vrstvě služby Active Directory. Zásady skupiny služby Active Directory používají k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci vybraného datového centra zákazníka pro odolnost proti chybám. Geograficky redundantní úložiště zajišťuje, že data se replikují do sekundárního datového centra stovky mil okamžitě a znovu uložené jako tři kopie v rámci datového centra. Toto uspořádání zabraňuje za následek ztrátu dat nepříznivých událostí ve primárního datového centra zákazníka.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory (Azure AD) řízení přístupu na základě role (RBAC) se používá k řízení přístupu k nasazené prostředky. Tyto prostředky zahrnují klíče zákazníků ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokoly. Zobrazí se stav systému v jednom řídicím panelu, který se snadno používá.

Virtuální počítač slouží jako hostitel typu bašta správy. Správci nasazení přístup k prostředkům nabízí zabezpečené připojení. Data se načtou do pracovní oblasti přes tento hostitel správy bastionu. *Doporučujeme nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.*

![Datový sklad pro NIST SP 800-171 diagram referenční architektury](images/nist171-dw-architecture.png "datový sklad pro NIST SP 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v tématu [architektura nasazení](#deployment-architecture) oddílu.

- Skupiny dostupnosti
    - Řadiče domény služby Active Directory
    - Uzly clusteru SQL serveru a určující sdílené složky
- Azure Active Directory
- Katalog dat Azure
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Nástroj pro vyrovnávání zatížení Azure
- Azure Storage
- Azure Log Analytics
- Azure Virtual Machines
    - (1) bastion host
    - (2) řadič domény active Directory
    - (2) uzel clusteru serveru SQL
    - (1) SQL serveru s kopií clusteru
- Azure Virtual Network
    - (((1) /16 sítě
    - (4) /24 sítě
    - (4) skupiny zabezpečení sítě
- Trezor služby Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je podnikového datového skladu, který využívá k rychlému spouštění složitých dotazů nad petabajty dat. masivně paralelní zpracování. Uživatele můžete použít jednoduchých dotazů PolyBase T-SQL k importu velkého objemu dat do SQL data warehouse a využijte sílu masivně paralelní zpracování ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav pomocí tabulek, grafů, map, měřidel, maticích a více pro SQL Data Warehouse.

**Azure Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat byly snadno vyhledatelné a pochopitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a vyhledávat data. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat se přidá do katalogu Data Catalog. Odkaz na umístění zdroje dat je v ceně. Metadata je indexována, aby byl každý zdroj dat snadno vyhledat přes vyhledávání. Indexování také umožňuje srozumitelné pro uživatele, kteří je zjistili.

**Bastion host**: hostitel bastionu je jediný bod položku, kterou uživatelé mohou používat pro přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Pro povolení provozu vzdálené plochy, musí být definován zdrojový provoz ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Key Vault.
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače, když se nepoužívá.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povoleno spuštění pověření a dalších tajných kódů v chráněném prostředí, která bude izolovaná od operačního systému.

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě (Nsg). Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro datovou vrstvu (clusterů systému SQL Server, SQL serveru s kopií clusteru a nástroje pro vyrovnávání zatížení SQL)
  - Skupina zabezpečení sítě pro správu hostitele bastionu
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro SQL Server Reporting Services

Skupiny zabezpečení sítě obsahují konkrétní porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou uložené v účtu úložiště a povoleny.
  - Log Analytics je připojen k [diagnostiky skupin zabezpečení sítě a](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu pomocí více měr. Tato opatření zahrnovat auditování databáze a šifrování.

**Azure Storage**: pro splnění požadavků pro šifrovaná data v klidovém stavu, všechny [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato funkce pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů.

**Azure Disk Encryption**: [šifrování disku](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) používá funkci BitLocker systému Windows pro zajištění šifrování svazku operačního systému a datové disky. Řešení se integruje se službou Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, když k nim dojde. Nabízí výstrahy zabezpečení podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé databázové vzory přístupu k.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v systému databáze. Po povolení šifrování dat pouze klientských aplikací nebo serverů aplikace s přístupem ke klíčům můžou k datům ve formátu prostého textu.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) slouží k ukončení zpracování subjekty údajů. Uživatele můžete přidat vlastní vlastnosti databázové objekty. Se také označení dat jako "Vyřazeno" pro podporu aplikační logiku a zabránit zpracování přidružená finanční data.
- [Zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat neoprávněným uživatelům nebo aplikacím. Může automaticky zjistit potenciálně citlivých data a navrhnout odpovídající masky uplatňovat. Dynamické maskování dat pomáhá omezit přístup tak, aby citlivá data nejsou ukončení databáze prostřednictvím před neoprávněným přístupem. *Zákazníci odpovídají za nastavení dodržovat svoje schéma databáze.*

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je Microsoft víceklientské cloudové adresáře a identity management service. Všechny uživatele tohoto řešení se vytvoří ve službě Azure AD a zahrnovat uživatele, kteří přistupují k databázi SQL.
-   Pomocí služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu Jak [integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Šifrování sloupců databáze také používá Azure AD k ověření aplikace ke službě SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správci definovat oprávnění velice přesně kontrolovat přístup. S ním že můžete udělit pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každý uživateli neomezený přístup pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k prostředkům a datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) je možné minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako jsou data zákazníků. Správci můžou používat Azure AD Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce také umožňuje vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci. Prozkoumá také podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je hardwaru chráněné modulem zabezpečení 2048bitový klíč RSA.
- Všichni uživatelé a identity jsou udělena minimální požadovaná oprávnění pomocí RBAC.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Správa oprav**: virtuální počítače s Windows nasazené jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit nasazení aktualizované o opravu virtuálních počítačů v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro virtuální počítače poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Zákazníci můžou nakonfigurovat výstrahy, které generují při označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Azure Security Center**: S [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Security Center také přistupuje k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Security Center používá celou řadu možností detekce zákazníkům potenciální útoky výstrahy, které se zaměřují jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se aktivují místo pořízením hrozby nebo podezřelé aktivity. Zákazníci můžou využít [vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Security Center nabízí výstrahy zabezpečení seřazené podle priority a incidentů. Security Center usnadňuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozeb. Reakce na incidenty týmy mohou pomocí sestavy, když vyšetřením a odstraněním hrozeb.

Tato referenční architektura také využívá [sken posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) schopností ve službě Security Center. Po dokončení konfigurace, agent partnera (například Qualysu) hlásí data o ohrožení zabezpečení na platformu partnera pro správu. Platforma partnera pro správu pak poskytuje data o ohrožení zabezpečení a data monitorování stavu zpět do služby Security Center. Zákazníky můžete použít tyto informace rychle identifikovat virtuální počítače jsou zranitelné.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: Server úlohy jsou seskupené v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Alespoň jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, který splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače. Tento proces urychluje dobu obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly úložiště, protokoly auditu služby Key Vault a Azure Application Gateway přístup a brána firewall protokoly. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uživatelé můžou konfigurovat dobu uchování až do 730 dnů podle svých specifických požadavků.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění dat jsou uspořádány do samostatných tabulek pro jednotlivé datové typy v rámci pracovních prostorů Log Analytics. Tímto způsobem lze všechna data pohromadě, analyzovat bez ohledu na jejich původní zdroj. Security Center umožňuje integrací s Log Analytics. Zákazníci mohou pomocí dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Poskytuje seznam seřazený podle priority doporučení specifická pro nasazenou serverové infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Zákazníkům přináší uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): sestavy řešení Agent Health tom, kolik agentů je nasazených a jejich geografické distribuce. Zároveň udává, kolik agentů jsou reagovat a počet agentů, které odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby SQL Database. Zákazníci můžou využít automatizaci [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožní snadno identifikovat změny v prostředí.

**Azure Monitor**: [monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů. Organizace slouží k auditovat, vytvářet upozornění a archivovat data. Také mohou sledovat volání rozhraní API ve svých prostředků Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-dw-tm) nebo najdete tady. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Datový sklad pro NIST SP 800-171 model hrozeb](images/nist171-dw-threat-model.png "datový sklad pro NIST SP 800-171 model hrozeb")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 datového skladu ovládací prvek implementace matice](https://aka.ms/nist171-dw-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky jsou zahrnuté do architektury datového skladu. Obsahuje podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurován pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektury datového skladu. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s využitím Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síť Azure. Toto připojení se provádí přes Internet. Zákazníci mohou používat toto připojení k bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Vzhledem k tomu, že provoz v rámci tunelového připojení sítě VPN prochází přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí další ještě větší zabezpečení možností připojení. ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Připojení ExpressRoute připojují přímo k poskytovatele telekomunikačních zákazníka. Data v důsledku toho nebude procházet přes Internet a není vystavený. Tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces extrakce, transformace a načítání
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete bez nutnosti samostatné ETL načtení dat do SQL Data Warehouse nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence a analýza zásobníku a nástroje třetích stran, které jsou kompatibilní s SQL serverem je možné díky technologii PolyBase.

### <a name="azure-ad-setup"></a>Azure AD – nastavení
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Místní služby Active Directory je možné integrovat s Azure AD [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také můžete navázat nasazenou infrastrukturu služby Active Directory (řadiče domény) do služby Azure AD. K tomuto účelu zkontrolujte nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby Azure AD.

### <a name="optional-services"></a>Volitelné služby
Azure nabízí širokou škálu služeb pro účely pomoci s úložištěm a pracovní data naformátovaný a je neformátovaný. Tyto služby můžete přidat do této referenční architektury založené na požadavcích zákazníků:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná Cloudová služba, která je vytvořená pro komplexní projekty hybridní ETL, extrakce, načítání a transformace, data a data integrace. Data Factory obsahuje funkce a trasování a vyhledat potřebná data. Vizualizace a monitorování nástroje identifikovat, když data přijdou a kdy a odkud. Zákazníci můžou vytvářet a plánovat pracovní postupy řízené daty, označované jako kanály, které ingestovat data z různorodých úložišť dat. Kanály, můžete použít k ingestování dat z interních i externích zdrojů. Zákazníci pak můžete zpracovávat a transformovat data pro výstup do úložišť dat, jako je SQL Data Warehouse.
- Zákazníky můžete připravit, Nestrukturovaná data v [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) zachytit data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a zjišťovací analýzy. Azure Data Lake má funkce, které umožňují extrahování a převod data. Data Lake Store je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop. Výborně se taky integruje s dalšími službami Azure, jako je SQL Data Warehouse.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
