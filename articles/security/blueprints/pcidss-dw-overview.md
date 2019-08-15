---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro PCI DSS
description: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro PCI DSS
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 0e7eadcb511bc55bbafcf20f44a28ffcf2f9be90
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946662"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Datový sklad pro PCI DSS

## <a name="overview"></a>Přehled

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny pro nasazení architektury datového skladu v Azure, které pomáhají s požadavky na standardy zabezpečení dat v odvětví platebních karet (PCI DSS 3,2). Prezentuje společnou referenční architekturu a předvádí správné zpracování dat kreditních karet (včetně čísla karty, vypršení platnosti a ověřovacích dat) v zabezpečeném a kompatibilním prostředí s více vrstvami. Tento podrobný plán znázorňuje způsoby, kterými můžou zákazníci splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky k vytváření a konfiguraci vlastních řešení datového skladu v Azure.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří splní požadavky na PCI DSS 3,2. Toto řešení poskytuje základní údaje, které zákazníkům pomůžou nasazovat úlohy do Azure ve vyhovujícím způsobem PCI DSS 3,2. Toto řešení se ale v produkčním prostředí nepoužívá, protože je potřeba další konfigurace.

Splnění PCI DSS – dodržování předpisů vyžaduje, aby akreditační schvalovací posuzovatel (QUALIFIED Security ASSESSOR) certifikuje řešení pro produkční zákazníky. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Toto řešení poskytuje referenční architekturu, která implementuje vysoce výkonný a zabezpečený cloudový datový sklad. V této architektuře existují dvě samostatné datové vrstvy: jednu z nich, kam se data importují, ukládají a připravují v clusterovém prostředí SQL, a další pro Azure SQL Data Warehouse, kde se data načítají pomocí nástroje pro extrakci, transformaci a načítání (např. základem). [ ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)Dotazy T-SQL) pro zpracování. Po uložení dat v Azure SQL Data Warehouse může analýza běžet v obrovských měřítkech.

Azure nabízí celou řadu sestav a analytických služeb pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services (SSRS) pro rychlé vytváření sestav z Azure SQL Data Warehouse. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Data v Azure SQL Data Warehouse jsou uložená v relačních tabulkách s sloupcovým úložištěm, což je formát, který významně snižuje náklady na úložiště dat při zvyšování výkonu dotazů. V závislosti na požadavcích na používání Azure SQL Data Warehouse výpočetní prostředky můžete škálovat nahoru nebo dolů nebo vypnout úplně, pokud neexistují žádné aktivní procesy vyžadující výpočetní prostředky.

SQL Load Balancer spravuje provoz SQL, což zajišťuje vysoký výkon. Všechny virtuální počítače v této referenční architektuře se nasazují v sadě dostupnosti s SQL Server instancemi nakonfigurovanými ve skupině dostupnosti Always On pro vysokou dostupnost a zotavení po havárii.

Tato referenční architektura datového skladu zahrnuje také vrstvu služby Active Directory pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadné přijetí v rámci větší struktury doménové struktury služby Active Directory, která umožňuje nepřetržitou práci s prostředím i v případě, že přístup k větší doménové struktuře není dostupný. Všechny virtuální počítače jsou připojené k doméně na úrovni služby Active Directory a používají zásady skupiny služby Active Directory k prosazování zabezpečení a konfigurací dodržování předpisů na úrovni operačního systému.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá tři kopie dat v rámci vybraného datacentra zákazníka a zajišťuje odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že se data budou replikovat do sekundárního datacentra stovky kilometrů a znovu se ukládají jako tři kopie v rámci tohoto datového centra, což brání nepříznivé události v primárním datovém centru zákazníka v důsledku ztráty údajů.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Azure Active Directory řízení přístupu na základě rolí se používá pro řízení přístupu k nasazeným prostředkům, včetně jejich klíčů v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu.

Virtuální počítač slouží jako hostitel bastionu pro správu, který poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům. Data se načítají do pracovní oblasti prostřednictvím tohoto hostitele bastionu pro správu. **Microsoft doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do podsítě referenční architektury.**

![Datový sklad pro diagram referenční architektury PCI DSS](images/pcidss-dw-architecture.png "Datový sklad pro diagram referenční architektury PCI DSS")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

- Sady dostupnosti
    - Řadiče Doména služby Active Directory
    - Uzly clusteru SQL a určující kopie
- Azure Active Directory
- Katalog dat Azure
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) hostitel bastionu
    - (2) řadič domény služby Active Directory
    - (2) SQL Server uzel clusteru
    - (1) SQL Server určující
- Azure Virtual Network
    - (1)/16 sítí
    - (4)/24 sítí
    - (4) skupiny zabezpečení sítě
- Recovery Services trezor
- SQL Data Warehouse
- Služba SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje prvky nasazení a implementace.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je podnikový datový sklad (podnikového), který využívá výkonné paralelní zpracování (MPP) k rychlému spouštění složitých dotazů napříč petabajty daty a umožňuje uživatelům efektivně identifikovat finanční data. Uživatelé můžou pomocí jednoduchých základních dotazů T-SQL importovat velké objemy dat do SQL Data Warehouse a využívat sílu MPP ke spouštění vysoce výkonných analýz.

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav s tabulkami, grafy, mapami, měřidly, maticemi a dalšími pro Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) zpřístupňuje zdroje dat uživatelům, kteří data spravují, snadno zjistitelné a srozumitelnější. U běžných zdrojů dat se dají zaregistrovat, označit a vyhledat finanční data. Data zůstanou v jeho stávajícím umístění, ale do Data Catalog se přidá kopie jeho metadat spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který umožňuje uživatelům přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje vzdálený provoz z veřejných IP adres v bezpečném seznamu. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozšíření Azure Diagnostics](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pomocí Azure Key Vault
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají
-   [Ochrana přihlašovacích údajů v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému.

### <a name="virtual-network"></a>Virtuální síť

Tato referenční architektura definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě se dají použít k zabezpečení provozu na úrovni podsítě nebo jednotlivých virtuálních počítačů. Existují následující skupiny zabezpečení sítě:

  - Skupina zabezpečení sítě pro datovou vrstvu (SQL Server clusterů, SQL Server určující disk a SQL Load Balancer)
  - Skupina zabezpečení sítě pro hostitele bastionu pro správu
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro SQL Server Reporting Services

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povolené tyto konfigurace:

- [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
- Protokoly Azure Monitor jsou připojené k [diagnostickým protokolům&#39;skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) .

**Podsítě**: Každá podsíť je přidružená ke své odpovídající skupině zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém měřítku prostřednictvím několika měr, včetně šifrování a auditování databáze.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](../../storage/common/storage-service-encryption.md). To pomáhá chránit a chránit data držitele v podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro disky s operačním systémem a daty. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
- [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
- Azure SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém znění. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
- [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
- [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
- [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjeví v podobě prostého textu. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) lze použít k diszastavenému zpracování subjektů údajů, protože umožňuje uživatelům přidat vlastní vlastnosti do databázových objektů a označit data jako "ukončené", aby podporovaly aplikační logiku, aby nedocházelo ke zpracování přidružených finančních služeb. údajů.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům, aby se zpracování nepokračovalo.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na uživatele nebo aplikace bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky pro použití. Pomůže vám identifikovat a snížit přístup k datům tak, že neukončí databázi prostřednictvím neoprávněného přístupu. Zákazníci zodpovídají za úpravu nastavení dynamického maskování dat, aby odpovídaly schématu databáze.

### <a name="identity-management"></a>Správa identit

Následující technologie poskytují možnosti pro správu přístupu k datům v prostředí Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je víceklientské&#39;cloudové služby a služba pro správu identit od Microsoftu. Všichni uživatelé tohoto řešení jsou vytvořeni v Azure Active Directory, včetně uživatelů, kteří přistupují k Azure SQL Database.
- Ověřování pro aplikaci se provádí pomocí Azure Active Directory. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kromě toho šifrování sloupce databáze používá Azure Active Directory k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Řízení přístupu na základě role v Azure](../../role-based-access-control/role-assignments-portal.md) umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení pro prostředky Azure můžou správci pro přístup k datům dovolit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení ovlivňující identity&#39;organizace a nakonfiguruje automatizované odezvy na zjištěné podezřelé akce související s&#39;identitami organizace s. zkoumá podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení

**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit a přistupovat k těmto datům:

- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je 32bitový klíč RSA s ochranou 2048.
- Všem uživatelům a identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje také službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které lze vytvořit aktualizované nasazení, aby se v případě potřeby daly opravit virtuální počítače.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro virtuální počítače poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovatelnými výstrahami v případě, že se známý škodlivý nebo nežádoucí software pokusí Nainstalujte nebo spusťte na chráněných virtuálních počítačích.

**Azure Security Center**: Díky [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují při výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla výstrah](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) , která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost:** Úlohy serveru jsou seskupené do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , které vám pomůžou zajistit vysokou dostupnost virtuálních počítačů v Azure. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru Recovery Services mohou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, což umožní rychlejší obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci.

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění jsou data uspořádaná do samostatných tabulek pro každý datový typ v rámci Log Analyticsch pracovních prostorů, což umožňuje analyzovat všechna data bez ohledu na její původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](../../monitoring/monitoring-solutions.md) Azure:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z Azure SQL Database. Řešení automatizace [Change Tracking](../../automation/change-tracking.md) zákazníkům umožňuje snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/pcidss-dw-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Datový sklad pro diagram referenční architektury PCI DSS](images/pcidss-dw-threat-model.png "PaaS webová aplikace pro model hrozeb PCI DSS")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů

V tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – PCI DSS zodpovědnosti zákazníka](https://aka.ms/pcidss-crm) najdete seznam povinností pro všechny požadavky PCI DSS 3,2.

[Implementační tabulka podrobný plán zabezpečení a dodržování předpisů Azure-PCI DSS Data Warehouse](https://aka.ms/pcidss-dw-cim) poskytuje informace o tom, které požadavky na PCI DSS 3,2 jsou řešeny architekturou datového skladu, včetně podrobných popisů implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute

Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury datového skladu. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

### <a name="extract-transform-load-process"></a>Extrahovat – transformovat a načíst proces

[Základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) dat může načíst data do Azure SQL Data Warehouse bez nutnosti samostatného nástroje pro extrakci, transformaci, načítání a import. Základ kódu umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence and Analysis stack a nástroje třetích stran, které jsou kompatibilní s SQL Server, se dají použít i pro základnu.

### <a name="azure-active-directory-setup"></a>Instalace Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat s Azure Active Directory [čtyřmi kliknutími](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci mohou také nasadit infrastrukturu služby Active Directory (řadiče domény) na existující Azure Active Directory tím, že nasazená infrastruktura služby Active Directory vytvoří subdoménu Azure Active Directory doménové struktury.

### <a name="optional-services"></a>Volitelné služby

Azure nabízí celou řadu služeb, které vám pomůžou s úložištěm a přípravací formátovaných a neformátovaných dat. Do této referenční architektury je možné přidat následující služby v závislosti na požadavcích zákazníka:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná cloudová služba, která je sestavená pro komplexní hybridní projekty extrakce, transformace a načítání dat. Azure Data Factory mají funkce, které vám pomůžou sledovat a vyhledávat data držitelů, včetně nástrojů pro vizualizaci a monitorování, které identifikují data, která dorazila a odkud pocházejí. Pomocí Azure Data Factory můžou zákazníci vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestují data z různorodých úložišť dat. Tyto kanály umožňují zákazníkům ingestovat data z interních i externích zdrojů. Zákazníci potom můžou data zpracovávat a transformovat na výstup do úložišť dat, jako je Azure SQL Data Warehouse.
- Zákazníci mohou ve [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)obměnit nestrukturovaná data, což umožňuje zachytit data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy.  Azure Data Lake mají možnosti, které umožňují extrakci a převod dat. Azure Data Lake Store je kompatibilní s nejvíce otevřenými zdrojovými komponentami v ekosystému Hadoop a integruje se s dalšími službami Azure, jako je třeba Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
