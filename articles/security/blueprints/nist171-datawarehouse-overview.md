---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro NIST SP 800-171
description: Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 98eb3834efa6dc6ce5d53990f4bb530351660b8a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946711"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Podrobný plán zabezpečení a dodržování předpisů Azure – datový sklad pro NIST SP 800-171

## <a name="overview"></a>Přehled
[NIST Special publikace 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízených neklasifikovaných informací (CUI), které se nacházejí v nefederálních informačních systémech a organizacích. NIST SP 800-171 stanoví 14 rodin požadavků na zabezpečení pro ochranu důvěrnosti CUI.

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny, které zákazníkům pomohou nasadit architekturu datového skladu v Azure, která implementuje podmnožinu ovládacích prvků NIST SP 800-171. Toto řešení ukazuje, jak můžou zákazníci splnit konkrétní požadavky na zabezpečení a dodržování předpisů. Také slouží jako základ pro zákazníky k vytváření a konfiguraci vlastních řešení datového skladu v Azure.

Tato referenční architektura, přidružená příručka implementace a model hrozeb, mají sloužit jako základ pro zákazníky, kteří přizpůsobují jejich konkrétní požadavky. Neměly by se používat tak, jak jsou v produkčním prostředí. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u všech řešení vytvořených pomocí této architektury. Požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje referenční architekturu, která implementuje vysoce výkonný a zabezpečený cloudový datový sklad. Tato architektura obsahuje dvě samostatné datové vrstvy. Jedna úroveň je místo, kam se data importují, ukládají a připravují v clusterovém prostředí SQL. Další úroveň je pro SQL Data Warehouse. S touto vrstvou se data načítají pomocí nástroje Extract-Transforming-Load (ETL) (například dotazy T- [](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) SQL) ke zpracování. Po uložení dat v SQL Data Warehouse může analýza běžet v obrovských měřítkech.

Azure nabízí celou řadu sestav a analytických služeb pro zákazníka. Toto řešení zahrnuje SQL Server Reporting Services pro rychlé vytváření sestav z SQL Data Warehouse. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Jako osvědčený postup doporučujeme použít pro rozšířené zabezpečení používání důvěryhodné certifikační autority.

Azure SQL Data Warehouse ukládá data v relačních tabulkách s sloupcovým úložištěm. Tento formát významně snižuje náklady na úložiště dat, když zvyšuje výkon dotazů. V závislosti na požadavcích na používání SQL Data Warehouse výpočetní prostředky můžete škálovat nahoru nebo dolů nebo vypnout úplně, pokud žádné aktivní procesy nevyžadují výpočetní prostředky.

SQL Server nástroj pro vyrovnávání zatížení spravuje provoz SQL pro zajištění vysokého výkonu. Všechny virtuální počítače v této referenční architektuře se nasazují v sadě dostupnosti s SQL Server instancemi nakonfigurovanými ve skupině dostupnosti Always On. Tato konfigurace poskytuje možnosti pro vysokou dostupnost a zotavení po havárii.

Tato referenční architektura datového skladu zahrnuje také vrstvu služby Active Directory pro správu prostředků v rámci architektury. Podsíť služby Active Directory umožňuje snadné přijetí v rámci větší struktury doménové struktury služby Active Directory. Prostředí tak může pracovat nepřetržitě, i když přístup k větší doménové struktuře není dostupný. Všechny virtuální počítače jsou připojené k doméně na úrovni služby Active Directory. Používají zásady skupiny služby Active Directory k prosazování zabezpečení a konfigurací dodržování předpisů na úrovni operačního systému.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá tři kopie dat v rámci vybraného datového centra zákazníka o odolnost proti chybám. Geograficky redundantní úložiště zajišťuje, že se data replikují do sekundárního datového centra stovky kilometrů a znovu se ukládají jako tři kopie v rámci tohoto datového centra. Toto ujednání brání v primárním datovém centru zákazníka v důsledku ztráty dat.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Pro řízení přístupu k nasazeným prostředkům se používá řízení přístupu na základě role (RBAC) Azure Active Directory (Azure AD). Tyto prostředky zahrnují klíče zákazníka v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci nakonfigurují protokoly služby monitoring Services pro zaznamenávání protokolů. Stav systému se zobrazuje v jednom řídicím panelu, který se snadno používá.

Virtuální počítač slouží jako hostitel bastionu pro správu. Poskytuje správcům zabezpečené připojení pro přístup k nasazeným prostředkům. Data se načítají do pracovní oblasti prostřednictvím tohoto hostitele bastionu pro správu. *Doporučujeme nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do podsítě referenční architektury.*

![Datový sklad pro diagram referenční architektury NIST SP 800-171](images/nist171-dw-architecture.png "Datový sklad pro diagram referenční architektury NIST SP 800-171")

Toto řešení používá následující služby Azure. Další informace najdete v části [architektura nasazení](#deployment-architecture) .

- Skupiny dostupnosti
    - Řadiče domény služby Active Directory
    - SQL Server uzly clusteru a určující kopie
- Azure Active Directory
- Katalog dat Azure
- Azure Key Vault
- Azure Monitor (protokoly)
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
- Trezor Recovery Services
- SQL Data Warehouse
- Služba SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) je podnikový datový sklad, který využívá výkonné paralelní zpracování k rychlému spouštění složitých dotazů napříč petabajty dat. Uživatelé můžou pomocí jednoduchých základních dotazů T-SQL importovat velké objemy dat do SQL Data Warehouse a využít výkonné paralelní zpracování pro spouštění vysoce výkonných analýz.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) poskytuje rychlé vytváření sestav s tabulkami, grafy, mapami, měřidly, maticemi a dalšími pro SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) usnadňuje zjišťování a pochopení zdrojů dat uživateli, kteří data spravují. U běžných zdrojů dat je možné registrovat, připsaná a vyhledávat data. Data zůstanou v jeho stávajícím umístění, ale do Data Catalog se přidá kopie jeho metadat. Součástí je odkaz na umístění zdroje dat. Metadata jsou indexována, aby byl každý zdroj dat snadno vyhledán pomocí vyhledávání. Indexování také usnadňuje uživatelům, kteří je objevují.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem záznamu, který uživatelé můžou použít pro přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje pouze vzdálený provoz z veřejných IP adres v bezpečném seznamu. Aby bylo možné provoz vzdálené plochy povolit, musí být ve skupině zabezpečení sítě definován zdroj provozu.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Rozšíření Azure Diagnostics](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pomocí Key Vault.
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají.
-   [Ochrana Credential Guard v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému.

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Skupin zabezpečení sítě) obsahuje seznam řízení přístupu, který povoluje nebo zakazuje provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Následující skupin zabezpečení sítě existuje:
  - NSG pro datovou vrstvu (SQL Server clustery, SQL Server určující disk a nástroj pro vyrovnávání zatížení SQL)
  - NSG pro hostitele bastionu pro správu
  - NSG pro službu Active Directory
  - NSG pro SQL Server Reporting Services

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povoleny a uloženy v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím několika měr. Mezi tyto míry patří šifrování a auditování databáze.

**Azure Storage**: Aby bylo možné splňovat požadavky na zašifrovaná data v klidovém stavu, všechna [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](../../storage/common/storage-service-encryption.md). Tato funkce pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů.

**Azure Disk Encryption**: [Šifrování disku](../azure-security-disk-encryption-overview.md) používá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro operační systém a datové disky. Řešení se integruje s Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [SQL Database auditování](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase k ochraně informací v klidovém čase. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby, když k nim dojde. Poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjevují jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem k klíčům.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) lze použít k diszastavenému zpracování subjektů údajů. Uživatelé mohou do databázových objektů přidávat vlastní vlastnosti. Můžou také označit data jako "ukončené", aby podporovaly aplikační logiku, aby nedocházelo ke zpracování přidružených finančních dat.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům, aby se zpracování nepokračovalo.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na neprivilegované uživatele nebo aplikace. Může automaticky zjistit potenciálně citlivá data a navrhnout vhodné masky pro použití. Dynamické maskování dat pomáhá snižovat přístup, takže citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. *Zákazníci zodpovídají za úpravu nastavení, aby odpovídaly schématu databáze.*

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je cloudová služba Microsoftu pro víceklientské cloudové služby a správu identit. Všichni uživatelé pro toto řešení se vytvářejí v Azure AD a zahrnují uživatele, kteří přistupují k databázi SQL.
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Šifrování sloupců databáze také používá Azure AD k ověření aplikace pro SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Správci služby [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) můžou použít k definování jemně odstupňovaných přístupových oprávnění. Díky tomu můžou udělit jenom množství přístupu, které uživatelé potřebují k provádění svých úloh. Místo udělení přístupu každému uživateli bez omezení přístupu k prostředkům Azure můžou správci pro přístup k prostředkům a datům udělit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Zákazníci můžou použít [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) k minimalizaci počtu uživatelů, kteří mají přístup k určitým informacím, jako jsou data. Správci můžou pomocí Azure AD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace. Konfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace. Prošetří také podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Následující možnosti Key Vault můžou zákazníkům chránit data:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typem klíče je hardwarový klíč chráněný modulem zabezpečení 2048.
- Všem uživatelům a identitám se uděluje minimální požadovaná oprávnění pomocí RBAC.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje taky službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které se dají aktualizovat nasazení, aby se virtuální počítače v případě potřeby daly opravovat.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro virtuální počítače poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Zákazníci můžou nakonfigurovat výstrahy, které generují, když se v chráněných virtuálních počítačích pokusí o instalaci nebo spuštění známého škodlivého softwaru.

**Azure Security Center**: Díky [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Security Center taky přistupuje k existujícím konfiguracím služeb Azure, aby poskytovala doporučení týkající se konfigurace a služeb, která vám pomůžou zdokonalit stav zabezpečení a chránit data.

Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se spouštějí při výskytu hrozby nebo podezřelé aktivity. Zákazníci můžou pomocí [vlastních pravidel upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení na základě dat, která už jsou shromážděná z jejich prostředí.

Security Center poskytuje prioritní výstrahy zabezpečení a incidenty. Security Center usnadňuje zákazníkům zjišťovat a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Týmy reakce na incidenty mohou sestavy používat při vyšetřování a nápravě hrozeb.

Tato referenční architektura používá také funkci [posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) v Security Center. Po nakonfigurování partnerský Agent (například Qualys) oznamuje data o ohrožení na platformu pro správu partnera. Platforma partnera pro správu pak poskytuje data o ohrožení zabezpečení a data monitorování stavu zpět do služby Security Center. Zákazníci můžou tyto informace využít k rychlé identifikaci zranitelných virtuálních počítačů.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost:** Úlohy serveru jsou seskupené do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , které vám pomůžou zajistit vysokou dostupnost virtuálních počítačů v Azure. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace virtuálních počítačů v této architektuře. S úložištěm Recovery Services můžou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače. Tento proces zrychluje dobu obnovení.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly úložiště, protokoly Key Vault auditu a protokoly brány firewall Azure Application Gateway a. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uživatelé můžou nakonfigurovat dobu uchování až 730 dní, aby splnily konkrétní požadavky.

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění dat se data uspořádají do samostatných tabulek pro každý datový typ v Log Analytics pracovní prostory. Tímto způsobem lze analyzovat všechna data společně bez ohledu na její původní zdroj. Security Center se integruje s protokoly Azure Monitor. Zákazníci můžou pomocí dotazů Kusto přistupovat ke svým datům události zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](../../monitoring/monitoring-solutions.md) Azure:
-   [Vyhodnocení služby Active Directory](../../azure-monitor/insights/ad-assessment.md): Řešení pro kontrolu stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Poskytuje prioritní seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Vyhodnocení SQL](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografická distribuce. Také oznamuje, kolik agentů neodpovídá a kolik agentů odesílá provozní data.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z SQL Database. Zákazníci můžou pomocí řešení automatizace [Change Tracking](../../automation/change-tracking.md) snadno identifikovat změny v prostředí.

**Azure Monitor**: [Monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, zachovat zabezpečení a identifikovat trendy. Organizace je můžou použít k auditu, vytváření výstrah a archivaci dat. Můžou taky sledovat volání rozhraní API ve svých prostředcích Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/nist171-dw-tm) nebo najdete tady. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Datový sklad pro model hrozeb NIST SP 800-171](images/nist171-dw-threat-model.png "Datový sklad pro model hrozeb NIST SP 800-171")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
[Tabulka zodpovědnosti zákazníka podrobný plán zabezpečení a dodržování předpisů Azure – NIST sp 800-171](https://aka.ms/nist171-crm) obsahuje všechny kontroly zabezpečení, které vyžaduje NIST SP 800-171. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[Podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171 – matice implementace ovládacího prvku datového skladu nástroje Data Warehouse](https://aka.ms/nist171-dw-cim) poskytuje informace o tom, které ovládací prvky NIST SP 800-171 jsou pokryté architekturou datového skladu. Obsahuje podrobný popis způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury datového skladu. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet. Zákazníci můžou toto připojení použít k bezpečnému propojení informací v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, nabízí společnost Microsoft ještě ještě bezpečnější možnost připojení. ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Připojení ExpressRoute se připojují přímo k poskytovateli telekomunikačních služeb zákazníka. V důsledku toho se data necestují přes Internet a nejsou vystavená. Tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

### <a name="extract-transform-load-process"></a>Extrahovat – transformovat a načíst proces
[Základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) dat může načíst data do SQL Data Warehouse bez nutnosti samostatného nástroje ETL nebo importu. Základ kódu umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence and Analysis stack a nástroje třetích stran, které jsou kompatibilní s SQL Server, se dají použít s základnu.

### <a name="azure-ad-setup"></a>Nastavení služby Azure AD
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k personálům, kteří pracují s prostředím. Místní služba Active Directory se dá integrovat do Azure AD ve [čtyřech kliknutích](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci taky můžou nasazovat infrastrukturu služby Active Directory (řadiče domény) do Azure AD. Provedete to tak, že nasadíte infrastrukturu služby Active Directory pro subdoménu doménové struktury Azure AD.

### <a name="optional-services"></a>Volitelné služby
Azure nabízí celou řadu služeb, které vám pomůžou s úložištěm a přípravací formátovaných a neformátovaných dat. Do této referenční architektury se dají přidat tyto služby na základě zákaznických požadavků:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) je spravovaná cloudová služba, která je sestavená pro komplexní hybridní ETL, extrakci, transformaci a projekty pro integraci dat. Data Factory mají funkce, které vám pomůžou sledovat a vyhledávat data. Nástroje pro vizualizaci a monitorování identifikují, kdy data dorazila a odkud pocházejí. Zákazníci můžou vytvářet a plánovat pracovní postupy řízené daty označované jako kanály, které ingestují data z různorodých úložišť dat. Můžou kanály použít k ingestování dat z interních a externích zdrojů. Zákazníci potom můžou data zpracovávat a transformovat do úložišť dat, jako je SQL Data Warehouse.
- Zákazníci můžou ve [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) obměnit nestrukturovaná data a zachytit tak data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy. Azure Data Lake mají možnosti, které umožňují extrakci a převod dat. Data Lake Store je kompatibilní s většinou komponent open source v ekosystému Hadoop. Také se integruje s dalšími službami Azure, jako je SQL Data Warehouse.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
