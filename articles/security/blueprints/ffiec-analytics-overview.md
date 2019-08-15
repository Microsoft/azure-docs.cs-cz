---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza dat pro FFIEC
description: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza dat pro FFIEC
services: security
author: meladie
ms.assetid: 31b70690-682c-4c38-9bbb-14dce162867a
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 474ab3ddd6c931b17b2ece1e22e1e1d4b62f0cdb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946792"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-ffiec-financial-services"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Analýzy pro finanční služby FFIEC

## <a name="overview"></a>Přehled

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny pro nasazení architektury analýzy dat v Azure, která je vhodná pro shromažďování, ukládání a načítání finančních dat, která jsou regulována Radou pro účely federální finanční instituce. (FFIEC).

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří splní požadavky FFIEC. Toto řešení poskytuje základní údaje, které zákazníkům pomůžou nasazovat úlohy do Azure v souladu s FFIECm způsobem. Toto řešení se ale v produkčním prostředí nepoužívá, protože je potřeba další konfigurace.

Splnění FFIEC vyžaduje, aby kvalifikovaní Auditori osvědčují řešení pro produkční zákazníky. Průzkumy procházejí prostřednictvím členských vládních agentur FFIEC, včetně Rady guvernérů systému federálních rezerv (FRB), federální vkladové společnosti (FDIC), národní správy úvěrového sjednocení (NCUA), kanceláře Comptroller Měny (OCC) a úřad pro finanční ochranu spotřebitele (CFPB). Tyto průzkumy potvrzují, že audity jsou dokončeny hodnotiteli, kteří udržují nezávislost z auditované instituce. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje analytickou platformu, na které můžou zákazníci vytvářet svoje vlastní analytické nástroje. Referenční architektura popisuje obecný případ použití, kdy zákazník zadá data prostřednictvím hromadného importu dat prostřednictvím správce SQL/dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozního uživatele. Oba pracovní proudy zahrnují Azure Functions pro import dat do Azure SQL Database. Azure Functions musí být nakonfigurované zákazníkem prostřednictvím Azure Portal, aby se úlohy importu vypracovaly jako jedinečné pro požadavky na analýzu jednotlivých zákazníků.

Azure nabízí řadu zpravodajských a analytických služeb pro zákazníky. Toto řešení zahrnuje Azure Machine Learning služby ve spojení s Azure SQL Database pro rychlé procházení dat a poskytování rychlejších výsledků prostřednictvím inteligentnějšího modelování. Azure Machine Learning zvyšuje rychlost dotazů tím, že zjišťuje nové vztahy mezi datovými sadami. Jakmile se data vyškole prostřednictvím několika statistických funkcí, až 7 dalších fondů dotazů (celkem 8 včetně zákaznického serveru), můžete synchronizovat se stejnými tabelárními modely pro rozprostření úloh dotazů a omezení doby odezvy.

Pro lepší analýzy a vytváření sestav je možné konfigurovat databáze Azure SQL pomocí indexů columnstore. Azure Machine Learning i databáze SQL Azure je možné škálovat nahoru nebo dolů nebo zcela vypnout v reakci na používání zákazníků. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Jakmile se data nahrají do Azure SQL Database a vyškolená Azure Machine Learning, bude je vynásobeni operačním uživatelem a správcem SQL/dat pomocí Power BI. Power BI zobrazuje data intuitivním a přebírá informace napříč více datovými sadami, aby bylo možné vykreslovat lepší přehled. Jejich vysoký stupeň přizpůsobivosti a snadné integrace s Azure SQL Database zajišťuje, že je zákazníci můžou nakonfigurovat tak, aby zpracovávala nejrůznější scénáře podle potřeb jejich obchodních potřeb.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá tři kopie dat v rámci vybraného datacentra zákazníka a zajišťuje odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že se data budou replikovat do sekundárního datacentra stovky kilometrů a znovu se ukládají jako tři kopie v rámci tohoto datového centra, což brání nepříznivé události v primárním datovém centru zákazníka v důsledku ztráty údajů.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Azure Active Directory řízení přístupu na základě rolí se používá pro řízení přístupu k nasazeným prostředkům, včetně jejich klíčů v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu.

Azure SQL Database je běžně spravované prostřednictvím SQL Server Management Studio (SSMS), která se spouští z místního počítače nakonfigurovaného pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute. **Microsoft doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a import dat do skupiny prostředků referenční architektury**.

![Analýza diagramu referenční architektury pro FFIEC](images/ffiec-analytics-architecture.png "Analýza diagramu referenční architektury pro FFIEC")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

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

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) zákazníkům umožňuje snadno sestavovat aplikace s architekturami založenými na událostech. Uživatelé vyberou prostředek Azure, ke kterému se chce přihlásit a poskytnout obslužné rutině události nebo Webhooku koncovému bodu, do kterého má být odeslána událost. Zákazníci mohou zabezpečit koncové body Webhooku přidáním parametrů dotazu do adresy URL Webhooku při vytváření odběru události. Azure Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS. Azure Event Grid umožňuje zákazníkům řídit úroveň přístupu daným různým uživatelům a provádět různé operace správy, jako jsou odběry událostí seznamu, vytváření nových a generování klíčů. Event Grid využívá řízení přístupu na základě role v Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) je výpočetní služba bez serveru, která umožňuje uživatelům spouštět kód na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Služba Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) je technika pro datové vědy, která umožňuje počítačům používat stávající data k předpovědi budoucího chování, výsledků a trendů.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) zpřístupňuje zdroje dat uživatelům, kteří data spravují, snadno zjistitelné a srozumitelnější. U běžných zdrojů dat se dají zaregistrovat, označit a vyhledat finanční data. Data zůstanou v jeho stávajícím umístění, ale do Data Catalog se přidá kopie jeho metadat spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

### <a name="virtual-network"></a>Virtuální síť

Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě se dají použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Existují následující skupiny zabezpečení sítě:

  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro úlohu

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povolené tyto konfigurace:

- [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
- Protokoly Azure Monitor jsou připojené k [diagnostickým protokolům&#39;skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) .

**Podsítě**: Každá podsíť je přidružená ke své odpovídající skupině zabezpečení sítě.

### <a name="data-in-transit"></a>Přenášená data

Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datacentry Azure a z nich. Všechny transakce, které se mají Azure Storage prostřednictvím Azure Portal probíhat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](../../storage/common/storage-service-encryption.md). To pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných nástrojem FFIEC.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

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

**Azure Security Center**: Díky [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují při výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla výstrah](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) , která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

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

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře na různých platformách. Detekuje anomálie ve výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, co uživatelé s aplikací skutečně dělají. Je navržený tak, aby pomohla uživatelům neustále zlepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/ffiec-analytics-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Analýza pro model FFIEC Threat](images/ffiec-analytics-threat-model.png "Analýza pro model FFIEC Threat")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů

V tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – FFIEC zodpovědnost zákazníka](https://aka.ms/ffiec-crm) se zobrazí seznam všech cílů požadovaných nástrojem FFIEC. Tato matice podrobně popisuje, zda je implementace každého cíle zodpovědností od společnosti Microsoft, zákazníka nebo sdílené mezi nimi.

[Tabulka podrobný plán zabezpečení a dodržování předpisů Azure – FFIEC data Analytics Implementation](https://aka.ms/ffiec-analytics-cim) obsahuje informace o tom, které cíle FFIEC jsou řešeny architekturou analýzy dat, včetně podrobného popisu implementace splňuje požadavky každého zahrnutého cíle.


## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute

Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury pro analýzu dat. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně &quot;&quot; propojit informace v rámci šifrovaného propojení mezi zákaznickou&#39;sítí a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení&#39;poskytovatele telekomunikačních služeb pro zákazníky, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

### <a name="extract-transform-load-process"></a>Extrahovat – transformovat a načíst proces

[Základ](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) dat může načíst data do Azure SQL Database bez nutnosti samostatného nástroje pro extrakci, transformaci, načítání a import. Základ kódu umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence and Analysis stack a nástroje třetích stran, které jsou kompatibilní s SQL Server, se dají použít i pro základnu.

### <a name="azure-active-directory-setup"></a>Instalace Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat s Azure Active Directory [čtyřmi kliknutími](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci mohou také nasadit infrastrukturu služby Active Directory (řadiče domény) na existující Azure Active Directory tím, že nasazená infrastruktura služby Active Directory vytvoří subdoménu Azure Active Directory doménové struktury.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
