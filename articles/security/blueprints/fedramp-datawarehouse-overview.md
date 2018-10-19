---
title: Zabezpečení Azure a dodržování předpisů – datový sklad pro službu FedRAMP Automation
description: Zabezpečení Azure a dodržování předpisů – datový sklad pro službu FedRAMP Automation
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 727c76dc62c054baff24f0e3e7a3b677450a4070
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404830"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Zabezpečení Azure a dodržování předpisů: datový sklad pro službu FedRAMP Automation

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) je program státní instituce USA, který přináší standardizovaný přístup k posuzování zabezpečení, autorizaci a nepřetržitému monitorování pro cloud produkty a služby. Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny, jak doručovat architektury datového skladu Microsoft Azure, která pomáhá implementovat podmnožinu FedRAMP High ovládací prvky. Toto řešení obsahuje pokyny k nasazení a konfiguraci prostředků Azure běžné referenční architekturu, představením způsobů, ve které zákazníci mohou pokrýt konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky Vytvoření a konfigurace jejich vlastní řešení datových skladů v Azure.

Tato referenční architektura, příručky k implementaci přidružený ovládací prvek a modely hrozeb mají sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům a nepoužívali-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez jakýchkoli úprav je dostatečná k zcela požadavkům FedRAMP High standardních hodnot. Je třeba počítat s následujícím:
- Tato architektura poskytuje standardní hodnoty pomáhá zákazníkům nasadit úlohy o velikosti do Azure v podobě odpovídajícího FedRAMP.
- Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty

Toto řešení poskytuje data warehouse referenční architekturu implementující vysoce výkonný a zabezpečený cloudový datový sklad. Existují dvě úrovně oddělení dat v této architektuře: kde se importují data, uložená a připravené v clusterovaném prostředí SQL a jiné pro službu Azure SQL Data Warehouse tam, kde je načtení dat pomocí nástroje ETL (třeba [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Dotazy T-SQL) pro zpracování. Jakmile jsou data uložená ve službě Azure SQL Data Warehouse, analýzy můžete spouštět ve velkém měřítku.

Microsoft Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlé vytvoření sestavy z Azure SQL Data Warehouse. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Data ve službě Azure SQL Data Warehouse uložená v relačních tabulkách se sloupcovým úložištěm, formátu, který výrazně snižuje náklady na úložiště dat při současném zvyšování výkonu dotazů.  V závislosti na požadavcích využití můžete vertikálně navýšit nebo snížit výpočetní prostředky Azure SQL Data Warehouse nebo vypne zcela Pokud nejsou žádné aktivní procesy vyžadující výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje datový provoz SQL, zajistit vysoký výkon. Všechny virtuální počítače v této referenční architektuře můžete nasadit skupinu dostupnosti pomocí instance systému SQL Server nakonfigurován ve skupině dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii možnosti.

Tato referenční Architektura datového skladu zahrnuje také vrstvu služby Active Directory (AD) pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší struktury doménové struktury AD umožňuje nepřetržité provádění prostředí i v případě, že přístup k doménové struktuře větší není k dispozici. Všechny virtuální počítače jsou připojené k vrstvě služby Active Directory a použití zásad skupiny služby Active Directory k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Virtuální počítač slouží jako hostitel bastionu správy, poskytuje zabezpečené připojení Správci nasazení přístup k prostředkům. Data se načtou do pracovní oblasti přes tento hostitel správy bastionu. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.**

![Datový sklad pro diagram referenční architektury FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "datového skladu pro FedRAMP diagram referenční architektury")

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.

Azure Virtual Machines
-   (1) bastion Host
-   (2) řadič domény active Directory
-   (2) uzel clusteru serveru SQL
-   (1) SQL serveru s kopií clusteru

Skupiny dostupnosti
-   (1) řadiče domény active Directory
-   (1) uzly clusteru SQL a určující sdílené složky

Virtual Network
-   (4) podsítě
-   (4) skupiny zabezpečení sítě

SQL Data Warehouse

SQL Server Reporting Services

Nástroj pro vyrovnávání zatížení Azure SQL

Azure Active Directory

Trezor služby Recovery Services

Azure Key Vault

Log Analytics

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje prvky k vývoji a implementaci.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je skladu dat organizace (podnikového), který využívá masivně paralelní zpracování (MPP) k rychlému spouštění složitých dotazů nad petabajty dat. Importujte velké objemy dat do SQL Data Warehouse pomocí jednoduchých dotazů PolyBase T-SQL a použijte sílu architektury MPP ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umožňuje rychle vytvářet sestavy pomocí tabulek, grafů, map, měřidel, maticích a informace pro Azure SQL Data Warehouse.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy (RDP), musí být definován ve skupině zabezpečení sítě (NSG).

Vytvoření virtuálního počítače jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření log Analytics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault (respektuje Azure Government, PCI DSS, HIPAA a další požadavky)
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby pověření a dalších tajných kódů je spouštět v chráněném prostředí, která je oddělená od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahovat seznamů řízení přístupu (ACL), která povolují nebo zakazují provoz ve virtuální síti. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro datovou vrstvu (clustery SQL serveru, SQL Server s kopií clusteru a nástroje pro vyrovnávání zatížení SQL)
  - Skupina zabezpečení sítě pro správu hostitele bastionu
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro SQL Server Reporting Services

Skupiny zabezpečení sítě mají konkrétní porty a protokoly, otevřete řešení mohli pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Log Analytics je připojen k [skupin zabezpečení sítě a Diagnostika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage** podle šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k poskytování šifrování disků s operačním systémem a daty. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database** instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování dat a souborů protokolu k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Funkce Always Encrypted sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo serverů aplikace s přístupem ke klíčům k datům ve formátu prostého textu.
-   [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasazuje. Zákazníci, bude nutné upravit nastavení dodržovat svoje schéma databáze maskování dynamických dat.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: Server úlohy jsou seskupené v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače umožňuje rychlejší obnovení.

### <a name="logging-and-audit"></a>Protokolování a auditování
[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlou protokolování systémových a uživatelských aktivit a také stav systému. [Log Analytics](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a místním prostředí.
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Blob v Azure, tabulky a fronty protokoly.
- **Brána firewall protokoly**: The Application Gateway poskytuje úplné diagnostiky a přístup k protokolům. Protokoly brány firewall jsou dostupné pro prostředky povoleným waf služby Application Gateway.
- **Archivace protokolu**: všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci pomocí dobu definované uchování o délce 2 dny. Tyto protokoly se připojit ke službě Azure Log Analytics pro zpracování, ukládání a vytváření sestav na řídicím.

Kromě toho jsou součástí této architektury řešení následující monitorování:
-   [Posouzení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
-   [Posouzení antimalwaru](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The Antimalwarovým řešením hlásí stav malwaru, ohrožení a ochrana.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spouští a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): řídicí panel zabezpečení a Audit poskytuje podrobný pohled na stav zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýza hrozeb a běžné dotazy na zabezpečení.
-   [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): řešení Update Management umožňuje správu zákazníka aktualizace zabezpečení operačního systému, včetně proces instalace požadovaných aktualizací a stav dostupných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Protokoly aktivit Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení Change Tracking umožňuje zákazníkům snadno identifikovat změny v prostředí.

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí identity možnosti správy v prostředí Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) může být od Microsoftu založené na cloudu adresáři a identitami správy službou s více tenanty. Všichni uživatelé pro řešení vytvořené v Azure Active Directory, včetně uživatele, kteří používají SQL Database.
-   Používání služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure AD k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) povolí, zaměřuje access management pro službu Azure. Přístup k předplatnému je omezená na správce předplatného.

Další informace o používání funkcí zabezpečení služby Azure SQL Database, najdete v článku [aplikací Contoso Clinic Demo](https://github.com/Microsoft/azure-sql-security-sample) vzorku.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo zabezpečené tunelové propojení sítě VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení k prostředky nasazené jako součást této referenční architektury datového skladu. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Nastavením odpovídajícím způsobem ExpressRoute nebo VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace a načítání (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete bez nutnosti samostatné ETL načtení dat do Azure SQL Data Warehouse nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje třetích stran, které jsou kompatibilní s SQL serverem, je možné díky technologii PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující AAD tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="additional-services"></a>Další služby
I když tato architektura datového skladu není určen pro nasazení [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) prostředí, podobně jako cíle lze dosáhnout prostřednictvím služeb popsaných v této referenční architektuře, stejně jako Další služby k dispozici pouze v Azure komerčním prostředí. Mějte prosím na paměti, že Azure Commercial udržuje FedRAMP JAB P-ATO na střední úrovni dopad povolení státní správy USA a partnerům, aby nasazení mírně citlivé informace do cloudu, využívat Azure komerčním prostředí.

Azure komerční nabízí širokou škálu služeb tento popisovač ve formátu a úložiště neformátovaná data a Fázování pro použití v datových skladů, včetně:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná Cloudová služba, která je vytvořená pro komplexní hybridní extract-transform-load (ETL), extrakce načítání a transformace (ELT) a data projekty integrace. Pomocí služby Azure Data Factory můžou zákazníci vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestovat data z různorodých úložišť dat. Zákazníci pak můžete zpracovávat a transformovat data pro výstup do úložišť dat, jako je Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) povolí zachytávání dat libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a zjišťovací analýzy. Azure Data Lake Store je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop a integruje se s dalšími službami Azure, jako je Azure SQL Data Warehouse krásně.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/blueprintdwthreatmodel) nebo najdete níže:

![Datový sklad pro model hrozeb FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "datového skladu pro model hrozeb FedRAMP")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – FedRAMP vysokou zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP High směrného plánu. Podobně [Azure zabezpečení a dodržování předpisů – FedRAMP střední zákazníka odpovědnost matice](https://aka.ms/blueprintcrmmod) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP střední směrného plánu. Jak dokumentů zřejmé, zda provádění každý ovládací prvek zodpovídá Microsoft, Zákazník, nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – FedRAMP vysokou ovládací prvek implementace matice](https://aka.ms/blueprintdwcimhigh) a [Azure zabezpečení a dodržování předpisů – FedRAMP střední ovládací prvek implementace matice](https://aka.ms/blueprintdwcimmod) poskytují informace, na kterém jsou popsané ovládací prvky podle architektury datového skladu pro každé standardní hodnoty FedRAMP, včetně podrobného popisu způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
