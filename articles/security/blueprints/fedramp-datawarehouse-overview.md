---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - datového skladu pro FedRAMP automatizace
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - datového skladu pro FedRAMP automatizace
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206820"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: datového skladu pro FedRAMP automatizace

## <a name="overview"></a>Přehled

[Federal rizika a autorizace správu Program (FedRAMP)](https://www.fedramp.gov/) je program government celou Spojených státech amerických, který poskytuje standardizovaná přístup k zabezpečení, autorizace a nepřetržité monitorování pro cloud produkty a služby. Tento zabezpečení Azure a dodržování předpisů plán, podle kterého poskytuje pokyny, jak doručovat Architektura datového skladu Microsoft Azure, který pomáhá implementovat podmnožinu FedRAMP vysokou ovládací prvky. Toto řešení obsahuje pokyny k nasazení a konfigurace prostředků Azure pro běžné referenční architektura, ukázka způsoby, ve kterém můžete zákazníkům splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky vytvořit a nakonfigurovat vlastní řešení datového skladu v Azure.

Tento referenční architektura, pokyny implementace přidruženého ovládacího prvku a modely threat které slouží jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav je zcela nesplňují požadavky na vysokou FedRAMP standardních hodnot. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem FedRAMP způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Toto řešení poskytuje architektura odkaz datového skladu, implementující vysoce výkonné a zabezpečení cloudový datový sklad. Existují dvě samostatné datové vrstvy v této architektuře: jeden kde importu, uloženy a dvoufázové instalace v prostředí clusterů SQL a jiné pro Azure SQL Data Warehouse kde načtení dat pomocí nástroje ETL (například [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Dotazů T-SQL) pro zpracování. Jakmile data jsou uložena v Azure SQL Data Warehouse, analytics můžete spustit v masivním měřítku.

Microsoft Azure nabízí celou řadu analýz a generování sestav služby pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlou tvorbu sestav z datového skladu SQL Azure. Veškerý provoz SQL se šifrují pomocí protokolu SSL prostřednictvím zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup Azure doporučuje použití důvěryhodné certifikační autority pro lepší zabezpečení.

Data v Azure SQL Data Warehouse je uložena v relační tabulky s sloupcovém úložiště, formátu, který významně snižuje náklady na úložiště dat při zlepšení výkonu dotazů.  V závislosti na požadavky na použití můžete škálovat nahoru nebo dolů nebo vypnuté úplně Pokud nejsou žádné aktivní procesy, které vyžadují výpočetní prostředky Azure SQL Data Warehouse výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje přenosy dat SQL, zajistit vysoký výkon. Všechny virtuální počítače v této referenční architektura nasadit v sada dostupnosti s instancí systému SQL Server, které jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii možnosti.

Tento referenční Architektura datového skladu zahrnuje taky vrstvou Active Directory (AD) pro správu prostředků v architektuře. Podsíť služby Active Directory umožňuje snadno přijetí v rámci větší struktury doménové struktury AD, aby vám umožnil průběžné operace prostředí i v případě, že přístup k doménové struktuře větší není k dispozici. Všechny virtuální počítače jsou připojené k doméně k vrstvě služby Active Directory a používat zásady skupiny služby Active Directory k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Virtuální počítač slouží jako hostitel bastionu správy, poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. Data se načte do pracovní oblasti prostřednictvím tohoto hostitele bastionu správy. **Azure doporučuje konfiguraci připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě architektura odkaz.**

![Datový sklad pro diagram architektury odkaz FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "datového skladu pro diagram architektury FedRAMP odkaz")

Toto řešení používá následující služby Azure. Podrobnosti architektury nasazení najdete v [architektura nasazení služby](#deployment-architecture) části.

Azure Virtual Machines
-   (1) bastionu hostitele
-   (2) řadič domény active Directory
-   (2) uzel clusteru serveru SQL
-   (1) SQL serveru s kopií clusteru

Skupiny dostupnosti
-   (1) řadiče domény active Directory
-   (1) uzly clusteru SQL a s kopií clusteru

Virtual Network
-   (4) podsítě
-   (4) skupiny zabezpečení sítě

SQL Data Warehouse

SQL Server Reporting Services

Nástroj pro vyrovnávání zatížení Azure SQL

Azure Active Directory

Trezor služeb zotavení

Azure Key Vault

Služby Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>Architektura nasazení služby

V následující části Podrobné informace o vývoji a implementaci elementy.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je Enterprise datového skladu obsahuje aktualizaci která využívá masivně paralelní zpracování MPP rychle spustit složitých dotazů napříč petabajty dat. Importovat velké objemy dat do SQL Data Warehouse pomocí jednoduchého dotazů PolyBase T-SQL a použít ke spuštění analytics vysoce výkonné možnosti MPP.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umožňuje rychlé vytvoření sestavy s tabulkami, grafů, map, měřidla, maticích a více pro Azure SQL Data Warehouse.

**U chráněných hostitelů**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazené prostředky jenom povolením vzdáleného přenosy z veřejných IP adres v seznamu bezpečných adres. Zdroj provoz pro povolení vzdálené plochy (RDP) komunikace, musí být definován v skupina zabezpečení sítě (NSG).

Virtuální počítač byl vytvořen jako hostitel připojený k doméně bastionu s následující konfigurace:
-   [Rozšíření proti malwaru](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault (respektuje Azure Government, PCI DSS, HIPAA a další požadavky)
-   [Vypnutí automatického zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) snížit spotřebu prostředků virtuálního počítače, když není používán
-   [Ochrana přihlašovacích údajů Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby přihlašovací údaje a jiné tajné spustit v prostředí chráněný, který je izolován od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tuto referenční architekturu definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

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
Architektura chrání data v klidovém stavu prostřednictvím šifrování, databáze, auditování a jiných opatření.

**Úložiště Azure** ke splnění šifrovaná data na požadavky rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**Azure SQL Database** Azure SQL Database instance používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování dat a souborů protokolu ochrany informací v klidovém stavu. Šifrování TDE poskytuje záruku, že data uložena nebyla neoprávněnému přístupu k.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby citlivá data nikdy se jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
-   [Databáze SQL dynamická data maskování](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasadí. Zákazníci bude nutné upravit dynamická data maskování nastavení řídit jejich schématu databáze.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: úlohy serveru jsou seskupené v [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Během plánované i neplánované údržby, splňuje podmínku 99.95 % je k dispozici alespoň jeden virtuální počítač Azure SLA.

**Trezor služeb zotavení**: [trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ve uložený zálohovaná data a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služeb zotavení zákazníkům obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače povolením rychlejší obnovení.

### <a name="logging-and-audit"></a>Protokolování a auditování
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Azure Blob, tabulek a protokoly fronty.
- **Brány firewall protokoly**: Application Gateway poskytuje úplné diagnostiky a přístup k protokolům. Brány firewall protokoly jsou dostupné pro aplikační bránu firewall webových aplikací povolené zdroje.
- **Přihlaste se archivaci**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci s dobou uchování definované 2 dní. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

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

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí identity možnosti správy v prostředí Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) může být společnosti Microsoft víceklientské cloudové adresáře a identity management service. Všechny uživatele pro řešení byly vytvořeny v Azure Active Directory, včetně uživatelů přístup k databázi SQL.
-   Ověřování do aplikace se provádí pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá Azure AD k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu Jak [chrání citlivá data v databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) zaměřuje umožňuje správu přístupu k Azure. Předplatné přístup je omezen na správce předplatného.

Další informace o používání funkcí zabezpečení databáze SQL Azure, najdete v článku [Contoso Klinika ukázkové aplikace](https://github.com/Microsoft/azure-sql-security-sample) ukázka.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

**Ochrana proti malwaru**: [Antimalware od Microsoftu](https://docs.microsoft.com/azure/security/azure-security-antimalware) u virtuálních počítačů poskytuje možnost ochrany v reálném čase, pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy Když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.

**Opravy správy**: virtuální počítače s Windows, které jsou nasazeny jako součást tuto referenční architekturu jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) služby, pomocí kterého je možné vytvořit aktualizované nasazení opravy virtuálních počítačů v případě potřeby.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo přes zabezpečené tunelové propojení VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení k nasazené jako součást tohoto datového skladu referenční architektura prostředky. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Nastavením správně, ExpressRoute nebo VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace-zatížením (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete načíst data do Azure SQL Data Warehouse bez nutnosti samostatné ETL nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje jiných výrobců, které jsou kompatibilní se systémem SQL Server, lze pomocí funkce PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je nezbytné pro správu nasazení a zřizování přístup osobám interakci s prostředím. Existující Windows Server Active Directory se dá integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také dokáže spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující aad tím, že nasazené infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="additional-services"></a>Další služby
I když tato architektura datového skladu není určen pro nasazení [komerční Azure](https://azure.microsoft.com/overview/what-is-azure/) prostředí podobné cíle lze provádět pomocí služeb popsaných v této referenční architektura a také Další služby k dispozici pouze v Azure komerčním prostředí. Upozorňujeme, že Azure komerční udržuje FedRAMP JAB P-ATO na střední úrovně dopadu, což vládních organizací a partnery nasazení mírně citlivé informace do cloudu využitím Azure komerčním prostředí.

Azure komerční nabízí širokou škálu služby tento popisovač formátu a neformátovaná data úložiště a pracovní pro použití v datových skladů, včetně:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravované Cloudová služba, která je sestavená pro komplexní hybridní extrakce transformace načítání (ETL), projekty integrace extrakce transformace zatížení (ELT) a data. Pomocí Azure Data Factory, můžete zákazníkům vytvořit a naplánovat názvem kanály, které ingestují data z úložiště dat různorodých pracovních řízené daty. Zákazníci lze zpracovat a transformaci dat pro výstup do úložiště dat, jako je například Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) povolí zachytávání dat ze všech velikost, typ a rychlosti příjmu do jediného místa pro provozní a zjišťovací analýzy. Azure Data Lake Store je kompatibilní s součástí typu nejvíce open source v ekosystému Hadoop a výborně se integruje s jinými službami Azure, jako je například Azure SQL Data Warehouse.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/blueprintdwthreatmodel) nebo naleznete níže:

![Datový sklad pro model hrozeb FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "datového skladu pro model hrozeb FedRAMP")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů
[Zabezpečení Azure a dodržování předpisů plán, podle kterého – FedRAMP vysoké zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Podobně [zabezpečení Azure a dodržování předpisů plán, podle kterého – FedRAMP střední zákazníka odpovědnost matice](https://aka.ms/blueprintcrmmod) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují FedRAMP mírný směrného plánu. Oba dokumenty zřejmé, zda implementace každý ovládací prvek zodpovídá Microsoft, zákazník nebo sdílené mezi nimi.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP vysoké řízení implementace matice](https://aka.ms/blueprintdwcimhigh) a [zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP střední řízení implementace matice](https://aka.ms/blueprintdwcimmod) zadejte informace, na kterém se ovládací prvky předmětem Architektura datového skladu pro každou standardní hodnotu FedRAMP, včetně podrobného popisu způsobu implementace splňuje požadavky na každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
