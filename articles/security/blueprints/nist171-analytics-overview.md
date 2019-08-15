---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza dat pro NIST SP 800-171
description: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza dat pro NIST SP 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 12119097f08fbb221f644fa2d86bade78b054269
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946737"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Podrobný plán zabezpečení a dodržování předpisů Azure – analýza dat pro NIST SP 800-171

## <a name="overview"></a>Přehled
[NIST Special publikace 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízených neklasifikovaných informací (CUI), které se nacházejí v nefederálních informačních systémech a organizacích. NIST SP 800-171 stanoví 14 rodin požadavků na zabezpečení pro ochranu důvěrnosti CUI.

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny, které zákazníkům pomohou nasadit architekturu analýzy dat v Azure, která implementuje podmnožinu ovládacích prvků NIST SP 800-171. Toto řešení ukazuje, jak můžou zákazníci splnit konkrétní požadavky na zabezpečení a dodržování předpisů. Také slouží jako základ pro zákazníky k vytváření a konfiguraci vlastních řešení pro analýzu dat v Azure.

Tato referenční architektura, přidružená příručka implementace a model hrozeb, mají sloužit jako základ pro zákazníky, kteří přizpůsobují jejich konkrétní požadavky. Neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení této architektury bez úprav není dostatečné pro zcela splnění požadavků nástroje NIST SP 800-171. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u všech vytvořených řešení, která používají tuto architekturu. Požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje analytickou platformu, na které můžou zákazníci vytvářet svoje vlastní analytické nástroje. Referenční architektura popisuje obecný případ použití. Zákazníci je můžou použít k zadávání dat prostřednictvím hromadných importů dat pomocí hromadného importu dat správcem SQL/dat. Můžou je taky použít k zadávání dat prostřednictvím aktualizací provozních dat prostřednictvím provozního uživatele. Oba workstreams zahrnují Azure Functions pro import dat do Azure SQL Database. Azure Functions musí být nakonfigurované zákazníkem prostřednictvím Azure Portal, aby se úlohy importu nastavily jako jedinečné pro požadavky na analýzu zákazníka.

Azure nabízí celou řadu sestav a analytických služeb pro zákazníka. Toto řešení používá Azure Machine Learning Services a SQL Database k rychlému procházení dat a dodávání rychlejších výsledků prostřednictvím inteligentnějšího modelování dat. Machine Learning slouží ke zvýšení rychlosti dotazů tím, že zjišťují nové vztahy mezi datovými sadami. Zpočátku jsou data vyškolená prostřednictvím několika statistických funkcí. Následně je možné synchronizovat až sedm dalších fondů dotazů se stejnými tabelárními modely pro rozprostření úlohy dotazů a omezení doby odezvy. Server pro zákazníky přináší celkový počet fondů dotazů na osm.

Pro lepší analýzy a vytváření sestav je možné SQL Database nakonfigurovat s indexy úložiště sloupců. Machine Learning a SQL Database je možné škálovat nahoru nebo dolů nebo zcela vypnout v reakci na používání zákazníků. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Jako osvědčený postup doporučujeme použít pro rozšířené zabezpečení používání důvěryhodné certifikační autority.

Po nahrání dat do databáze SQL a vyškolená službou Machine Learning je tato služba vyzpůsobená operačním uživatelem a správcem SQL/dat pomocí Power BI. Power BI zobrazuje data intuitivním a přebírá informace napříč více datovými sadami, aby bylo možné vykreslovat lepší přehled. Power BI má vysokou míru přizpůsobivosti a snadno se integruje s SQL Database. Zákazníci je můžou nakonfigurovat tak, aby zpracovávala nejrůznější scénáře, které vyžadují jejich obchodní potřeby.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurují z Azure Portal. Úložiště šifruje všechna data pomocí Šifrování služby Storage a udržuje tak důvěrnost dat v klidovém umístění. Geograficky redundantní úložiště zajišťuje, že neškodná událost v primárním datovém centru zákazníka nevede ke ztrátě dat. Druhá kopie je uložená v samostatném umístění stovky mil.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Pro řízení přístupu k nasazeným prostředkům se používá řízení přístupu na základě role (RBAC) Azure Active Directory (Azure AD). Tyto prostředky zahrnují klíče zákazníka v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci nakonfigurují protokoly služby monitoring Services pro zaznamenávání protokolů. Stav systému se zobrazuje v jednom řídicím panelu, který se snadno používá.

SQL Database se obvykle spravují prostřednictvím SQL Server Management Studio. Spouští se z místního počítače nakonfigurovaného pro přístup k databázi SQL prostřednictvím zabezpečené sítě VPN nebo připojení Azure ExpressRoute. Doporučujeme *, abyste nakonfigurovali připojení VPN nebo ExpressRoute pro správu a import dat do skupiny prostředků*.

![Diagram referenční architektury pro analýzu dat pro NIST SP 800-171](images/nist171-analytics-architecture.png "Diagram referenční architektury pro analýzu dat pro NIST SP 800-171")

Toto řešení používá následující služby Azure. Další informace najdete v části [architektura nasazení](#deployment-architecture) .

- Application Insights
- Azure Active Directory
- Katalog dat Azure
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (protokoly)
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 sítí
    - (2)/24 sítí
    - (2) skupiny zabezpečení sítě
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Azure Event Grid**: Díky [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)můžou zákazníci snadno vytvářet aplikace s architekturami založenými na událostech. Uživatelé vyberou prostředek Azure, ke kterému se chce přihlásit. Pak přidávají obslužné rutině události nebo Webhooku koncovému bodu, do kterého má být odeslána událost. Zákazníci mohou zabezpečit koncové body Webhooku přidáním parametrů dotazu do adresy URL Webhooku při vytvoření odběru události. Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS. Díky Event Grid můžou zákazníci řídit úroveň přístupu pro různé uživatele, aby mohli provádět různé operace správy. Uživatelé můžou vypisovat odběry událostí, vytvářet nové a generovat klíče. Event Grid používá službu Azure RBAC.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) je výpočetní služba bez serveru, která spouští kód na vyžádání. Nemusíte explicitně zřizovat ani spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Služba Azure Machine Learning**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) je technika pro datové vědy, která umožňuje počítačům používat stávající data k předpovědi budoucího chování, výsledků a trendů.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) usnadňuje zjišťování a pochopení zdrojů dat uživateli, kteří data spravují. U běžných zdrojů dat je možné registrovat, připsaná a vyhledávat data. Data zůstanou v jeho stávajícím umístění, ale do Data Catalog se přidá kopie jeho metadat. Součástí je odkaz na umístění zdroje dat. Metadata jsou indexována, aby byl každý zdroj dat snadno vyhledán pomocí vyhledávání. Indexování také usnadňuje uživatelům, kteří je objevují.

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresním prostorem 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Skupin zabezpečení sítě) obsahuje seznam řízení přístupu, který povoluje nebo zakazuje provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Následující skupin zabezpečení sítě existuje:
  - NSG pro službu Active Directory
  - NSG pro úlohu

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datovými centry Azure a z nich. Všechny transakce do úložiště prostřednictvím Azure Portal probíhají prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné splňovat požadavky na zašifrovaná data v klidovém stavu, všechna [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](../../storage/common/storage-service-encryption.md). Tato funkce pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných v NIST SP 800-171.

**Azure Disk Encryption**: [Šifrování disku](../azure-security-disk-encryption-overview.md) používá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase k ochraně informací v klidovém čase. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby, když k nim dojde. Poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjevují jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem k klíčům.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na neprivilegované uživatele nebo aplikace. Může automaticky zjistit potenciálně citlivá data a navrhnout vhodné masky pro použití. Dynamické maskování dat pomáhá snižovat přístup, takže citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. *Zákazníci zodpovídají za úpravu nastavení, aby odpovídaly schématu databáze.*

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je cloudová služba Microsoftu pro víceklientské cloudové služby a správu identit. Všichni uživatelé pro toto řešení se vytvářejí ve službě Azure AD a zahrnují uživatele, kteří přistupují k SQL Database.
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Šifrování sloupců databáze také používá Azure AD k ověření aplikace pro SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Správci služby [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) můžou použít k definování jemně odstupňovaných přístupových oprávnění. Díky tomu můžou udělit jenom množství přístupu, které uživatelé potřebují k provádění svých úloh. Místo udělení všech uživatelů bez omezení oprávnění pro prostředky Azure můžou správci pro přístup k datům dovolit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Zákazníci můžou použít [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) k minimalizaci počtu uživatelů, kteří mají přístup k určitým informacím, jako jsou data. Správci můžou pomocí Azure AD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace. Konfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace. Prošetří také podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Následující možnosti Key Vault můžou zákazníkům chránit data:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typem klíče je hardwarový klíč chráněný modulem zabezpečení 2048.
- Všem uživatelům a identitám se uděluje minimální požadovaná oprávnění pomocí RBAC.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Azure Security Center**: Díky [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Security Center taky přistupuje k existujícím konfiguracím služeb Azure, aby poskytovala doporučení týkající se konfigurace a služeb, která vám pomůžou zdokonalit stav zabezpečení a chránit data.

 Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se spouštějí při výskytu hrozby nebo podezřelé aktivity. Zákazníci můžou pomocí [vlastních pravidel upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení na základě dat, která už jsou shromážděná z jejich prostředí.

 Security Center poskytuje prioritní výstrahy zabezpečení a incidenty. Security Center usnadňuje zákazníkům zjišťovat a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Týmy reakce na incidenty mohou sestavy používat při vyšetřování a nápravě hrozeb.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly úložiště, protokoly Key Vault auditu a protokoly brány firewall Azure Application Gateway a. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uživatelé můžou nakonfigurovat dobu uchování až 730 dní, aby splnily konkrétní požadavky.

**Protokoly Azure monitor**: Protokoly se konsolidují do [protokolů Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění dat se data uspořádají do samostatných tabulek pro každý datový typ v Log Analytics pracovní prostory. Tímto způsobem lze analyzovat všechna data společně bez ohledu na její původní zdroj. Security Center se integruje s protokoly Azure Monitor. Zákazníci můžou pomocí dotazů Kusto přistupovat ke svým datům události zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](../../monitoring/monitoring-solutions.md) Azure:
-   [Vyhodnocení služby Active Directory](../../azure-monitor/insights/ad-assessment.md): Řešení pro kontrolu stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Poskytuje prioritní seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Vyhodnocení SQL](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografická distribuce. Také oznamuje, kolik agentů nereaguje a kolik agentů odesílá provozní data.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z SQL Database. Zákazníci můžou pomocí řešení automatizace [Change Tracking](../../automation/change-tracking.md) snadno identifikovat změny v prostředí.

**Azure Monitor**: [Monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, zachovat zabezpečení a identifikovat trendy. Organizace je můžou použít k auditu, vytváření výstrah a archivaci dat. Můžou taky sledovat volání rozhraní API ve svých prostředcích Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba pro správu výkonu aplikací pro webové vývojáře na různých platformách. Detekuje anomálie výkonu a zahrnuje výkonné analytické nástroje. Nástroje vám pomůžou diagnostikovat problémy a pomáhat zákazníkům pochopit, co uživatelé s aplikací dělají. Je navržený tak, aby pomohla uživatelům neustále zlepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/nist171-analytics-tm) nebo najdete tady. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Analýza dat pro model hrozeb NIST SP 800-171](images/nist171-analytics-threat-model.png "Analýza dat pro model hrozeb NIST SP 800-171")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
[Tabulka zodpovědnosti zákazníka podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171](https://aka.ms/nist171-crm) obsahuje všechny ovládací prvky zabezpečení, které vyžaduje NIST SP 800-171. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[Matrice implementace ovládacího prvku analýzy dat podrobný plán zabezpečení A dodržování předpisů Azure NIST sp 800-171](https://aka.ms/nist171-analytics-cim) poskytuje informace o tom, které ovládací prvky NIST SP 800-171 jsou řešeny architekturou analýzy dat. Obsahuje podrobný popis způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.


## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury pro analýzu dat. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet. Zákazníci můžou připojení použít k bezpečnému propojení informací v rámci šifrovaného propojení mezi vaší sítí a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, nabízí společnost Microsoft ještě ještě bezpečnější možnost připojení. ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Připojení ExpressRoute se připojují přímo k poskytovateli telekomunikačních služeb zákazníka. V důsledku toho se data necestují přes Internet a nejsou vystavená. Tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení. 

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

### <a name="extract-transform-load-process"></a>Extrahovat – transformovat a načíst proces
[Základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) dat může načítat data do SQL Database bez nutnosti samostatného nástroje pro extrakci, transformaci a načtení a import. Základ kódu umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence and Analysis stack a nástroje třetích stran, které jsou kompatibilní s SQL Server, se dají použít s základnu.

### <a name="azure-ad-setup"></a>Nastavení služby Azure AD
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k zaměstnancům, kteří pracují s prostředím. Místní služba Active Directory se dá integrovat do Azure AD ve [čtyřech kliknutích](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci taky můžou nasazovat infrastrukturu služby Active Directory (řadiče domény) do Azure AD. Provedete to tak, že nasadíte infrastrukturu služby Active Directory pro subdoménu doménové struktury Azure AD.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
