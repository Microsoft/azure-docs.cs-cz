---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro automatizaci FedRAMP
description: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro automatizaci FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 79ed2b6e5d7bb600a79e12d19268035491f3fe08
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946860"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Datový sklad pro automatizaci FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) je USA program pro státní správu, který poskytuje standardizovaný přístup k vyhodnocování zabezpečení, autorizaci a nepřetržitému monitorování cloudových produktů a služeb. Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny k tomu, jak doručovat architekturu Microsoft Azure datového skladu, která pomáhá implementovat podmnožinu vysoce FedRAMPch ovládacích prvků. Toto řešení poskytuje pokyny k nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky. Sestavujte a nakonfigurujte svoje vlastní řešení datového skladu v Azure.

Tato referenční architektura, implementační Příručky k implementaci a modely hrozeb mají sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostatečné pro zcela splnění požadavků vysokého směrného plánu FedRAMP. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán, který zákazníkům umožňuje nasazovat úlohy do Azure v souladu s FedRAMP.
- Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Toto řešení poskytuje referenční architekturu datového skladu, která implementuje vysoce výkonný a zabezpečený cloudový datový sklad. V této architektuře existují dvě samostatné datové vrstvy: jednu, kde se data importují, ukládají a připravují v clusterovém prostředí SQL, a další pro Azure SQL Data Warehouse, kde se data načítají pomocí nástroje ETL (např. [základní](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) dotazy T-SQL). pro zpracování. Po uložení dat v Azure SQL Data Warehouse může analýza běžet v obrovských měřítkech.

Microsoft Azure nabízí celou řadu sestav a analytických služeb pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlé vytváření sestav z Azure SQL Data Warehouse. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Data v Azure SQL Data Warehouse jsou uložená v relačních tabulkách s sloupcovým úložištěm, což je formát, který významně snižuje náklady na úložiště dat při zvyšování výkonu dotazů.  V závislosti na požadavcích na používání Azure SQL Data Warehouse výpočetní prostředky můžete škálovat nahoru nebo dolů nebo vypnout úplně, pokud neexistují žádné aktivní procesy vyžadující výpočetní prostředky.

Nástroj pro vyrovnávání zatížení SQL spravuje provoz SQL, což zajišťuje vysoký výkon. Všechny virtuální počítače v této referenční architektuře se nasazují v sadě dostupnosti s SQL Server instancemi nakonfigurovanými ve skupině dostupnosti AlwaysOn pro možnosti zotavení po havárii s vysokou dostupností.

Tato referenční architektura datového skladu také zahrnuje úroveň služby Active Directory (AD) pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadné přijetí v rámci větší struktury doménové struktury AD a umožňuje tak nepřetržitou práci s prostředím i v případě, že přístup k větší doménové struktuře není dostupný. Všechny virtuální počítače jsou připojené k doméně na úrovni služby Active Directory a používají zásady skupiny služby Active Directory k prosazování zabezpečení a konfigurací dodržování předpisů na úrovni operačního systému.

Virtuální počítač slouží jako hostitel bastionu pro správu, který poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům. Data se načítají do pracovní oblasti prostřednictvím tohoto hostitele bastionu pro správu. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do podsítě referenční architektury.**

![Datový sklad pro diagram referenční architektury FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Datový sklad pro diagram referenční architektury FedRAMP")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

Azure Virtual Machines
-   (1) hostitel bastionu
-   (2) řadič domény služby Active Directory
-   (2) SQL Server uzel clusteru
-   (1) SQL Server určující

Sady dostupnosti
-   (1) řadiče domény služby Active Directory
-   (1) uzly clusteru SQL a určující kopie

Virtuální sítě
-   (4) podsítě
-   (4) skupiny zabezpečení sítě

SQL Data Warehouse

Služba SQL Server Reporting Services

Load Balancer Azure SQL

Azure Active Directory

Recovery Services trezor

Azure Key Vault

Protokoly služby Azure Monitor

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část popisuje vývojové a implementační prvky.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je podnikový datový sklad (podnikového), který využívá výkonné paralelní zpracování (MPP) k rychlému spouštění složitých dotazů napříč petabajty daty. Naimportujte velké objemy dat do SQL Data Warehouse pomocí jednoduchých základních dotazů T-SQL a Využijte sílu MPP ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) umožňuje rychlé vytváření sestav s tabulkami, grafy, mapami, měřidly, maticemi a dalšími pro Azure SQL Data Warehouse.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který umožňuje uživatelům přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje vzdálený provoz z veřejných IP adres v bezpečném seznamu. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný ve skupině zabezpečení sítě (NSG).

Virtuální počítač se vytvořil jako hostitel bastionu připojený k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozšíření protokolů Azure Monitor](../../virtual-machines/windows/extensions-oms.md)
-   [Rozšíření Azure Diagnostics](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pomocí Azure Key Vault (ctí Azure Government, PCI DSS, HIPAA a další požadavky)
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají
-   [Ochrana Credential Guard v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [Skupin zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují seznamy Access Control (ACL), které povolují nebo zakazují provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Následující skupin zabezpečení sítě existuje:
  - NSG pro datovou vrstvu (SQL Server clustery, SQL Server určující disk a SQL Load Balancer)
  - NSG pro hostitele bastionu pro správu
  - NSG pro službu Active Directory
  - NSG pro SQL Server Reporting Services

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage** Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro operační systém a datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database** Instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   SQL Database je nakonfigurovaná tak, aby používala [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provádí šifrování a dešifrování dat a souborů protokolů v reálném čase za účelem ochrany neaktivních informací. TDE poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Always Encrypted sloupců](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistí, že se citlivá data v databázovém systému nikdy neobjeví jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
-   [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) je možné provést po nasazení referenční architektury. Zákazníci budou muset upravit nastavení dynamického maskování dat tak, aby odpovídalo schématu databáze.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost:** Úlohy serveru jsou seskupené do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , které vám pomůžou zajistit vysokou dostupnost virtuálních počítačů v Azure. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace Azure Virtual Machines v této architektuře. Pomocí trezoru Recovery Services můžou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, což umožní rychlejší obnovení.

### <a name="logging-and-audit"></a>Protokolování a audit
[Protokoly Azure monitor](../azure-security-disk-encryption-overview.md) poskytují rozsáhlé protokolování aktivity systému a uživatele a také stav systému. [Azure monitor protokoluje](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a v místních prostředích.
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows a protokoly služby Azure Blob Storage, tabulky a fronty.
- **Protokoly brány firewall**: Application Gateway poskytuje úplné diagnostické protokoly a protokoly přístupu. Protokoly brány firewall jsou k dispozici pro prostředky Application Gateway s podporou WAF.
- **Archivace protokolu**: Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage za účelem archivace s definovanou dobou uchovávání 2 dny. Tyto protokoly se připojují k protokolům Azure Monitor pro zpracování, ukládání a vytváření sestav řídicích panelů.

Kromě toho jsou součástí této architektury Tato řešení monitorování:
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Antimalwarové řešení oznamuje stav malwaru, hrozeb a stavu ochrany.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Řešení Azure Automation ukládá, spouští a spravuje Runbooky.
-   [Security and Audit](../../security-center/security-center-intro.md): Řídicí panel Security and Audit poskytuje přehled o stavu zabezpečení prostředků tím, že poskytuje metriky pro domény zabezpečení, významné problémy, detekce, analýzu hrozeb a běžné dotazy zabezpečení.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Update Management](../../automation/automation-update-management.md): Řešení Update Management umožňuje zákazníkům spravovat aktualizace zabezpečení operačního systému, včetně stavu dostupných aktualizací a procesu instalace požadovaných aktualizací.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Protokoly aktivit Azure](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.
-   [Change Tracking](../../azure-monitor/platform/collect-activity-logs.md): Řešení Change Tracking zákazníkům umožňuje snadno identifikovat změny v prostředí.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti správy identit v prostředí Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) může být víceklientské cloudové služby Microsoftu a služba pro správu identit založené na cloudu. Všichni uživatelé pro toto řešení byli vytvořeni v Azure Active Directory, včetně uživatelů, kteří přistupují k SQL Database.
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kromě toho šifrování sloupce databáze používá Azure AD k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace, nakonfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které přijmou vhodná opatření k jejich vyřešení.
-   Správa [Access Control na základě rolí v Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md) umožňuje správu cílené přístupu pro Azure. Přístup k předplatnému je omezený na správce předplatného.

Další informace o používání funkcí zabezpečení Azure SQL Database najdete v ukázce ukázkové [aplikace Contoso Clinic demo](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovatelnými výstrahami v případě, že se známý škodlivý nebo nežádoucí software pokusí Nainstalujte nebo spusťte na chráněných virtuálních počítačích.

**Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje také službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které lze vytvořit aktualizované nasazení, aby se v případě potřeby daly opravit virtuální počítače.


## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo zabezpečené tunelové připojení VPN musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury datového skladu. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Díky správnému nastavení ExpressRoute nebo VPN můžou zákazníci přidat vrstvu ochrany dat při přenosu.

### <a name="extract-transform-load-etl-process"></a>Extrahování – proces transformace a načítání (ETL)
[Základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) dat může načíst data do Azure SQL Data Warehouse bez nutnosti samostatného nástroje ETL nebo importu. Základ kódu umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence and Analysis stack a nástroje třetích stran, které jsou kompatibilní s SQL Server, se dají použít i pro základnu.

### <a name="azure-active-directory-setup"></a>Instalace Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat do AAD ve [čtyřech kliknutích](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci mohou také nasadit infrastrukturu služby Active Directory (řadiče domény) na existující AAD tím, že nasazená infrastruktura služby Active Directory vytvoří subdoménu doménové struktury AAD.

### <a name="additional-services"></a>Další služby
I když tato architektura datového skladu není určená pro nasazení do komerčního prostředí [Azure](https://azure.microsoft.com/overview/what-is-azure/) , je možné dosáhnout podobných cílů prostřednictvím služeb popsaných v této referenční architektuře, jakož i dalších služeb, které jsou k dispozici. pouze v komerčním prostředí Azure. Upozorňujeme, že Azure Commercial udržuje FedRAMP JAB P-ATO na úrovni středních dopadů a umožňuje státním institucím a partnerům nasazovat střední citlivé informace do cloudu s využitím komerčního prostředí Azure.

Azure Commercial nabízí širokou škálu služeb, které zpracovávají naformátovaná a neformátovaná úložiště dat a fázování, aby je bylo možné používat v datových skladech, včetně těchto:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná cloudová služba, která je sestavená pro komplexní hybridní projekty extrakce, transformace a načítání (ETL), extrakce, načítání a transformace (ELT) a integrace dat. Pomocí Azure Data Factory můžou zákazníci vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestují data z různorodých úložišť dat. Zákazníci potom můžou data zpracovávat a transformovat na výstup do úložišť dat, jako je Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umožňuje zachytit data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy. Azure Data Lake Store je kompatibilní s nejvíce otevřenými zdrojovými komponentami v ekosystému Hadoop a integruje se s dalšími službami Azure, jako je třeba Azure SQL Data Warehouse.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (DFD) pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/blueprintdwthreatmodel) nebo najdete níže:

![Datový sklad pro model FedRAMP Threat](images/fedramp-datawarehouse-threat-model.png?raw=true "Datový sklad pro model FedRAMP Threat")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
V tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – FedRAMP vysoká zodpovědnost zákazníka](https://aka.ms/blueprinthighcrm) jsou uvedeny všechny ovládací prvky zabezpečení, které vyžadují vysoké směrné plány FedRAMP. Podobně v tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – FedRAMP mírná zodpovědnosti zákazníka](https://aka.ms/blueprintcrmmod) jsou uvedeny všechny ovládací prvky zabezpečení, které jsou vyžadovány středním směrným plánem FedRAMP. Oba dokumenty podrobně popisují, zda je implementace každého ovládacího prvku zodpovědná za společnost Microsoft, zákazníka nebo sdílenou mezi těmito dvěma způsoby.

FedRAMP a implementační matrice [High Control](https://aka.ms/blueprintdwcimhigh) a [podrobný plán zabezpečení a dodržování předpisů Azure-FedRAMP pro střední řízení](https://aka.ms/blueprintdwcimmod) poskytují informace o tom, které ovládací prvky jsou pokryté podrobný plán zabezpečení a dodržování předpisů Azure architektura datového skladu pro každou standardní hodnotu FedRAMP, včetně podrobného popisu způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
