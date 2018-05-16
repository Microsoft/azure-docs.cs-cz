---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Analytics pro GDPR
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Analytics pro GDPR
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 9114dea1f95198b5ff8dc1bf759be5d1179885fd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: Analýza pro GDPR

## <a name="overview"></a>Přehled
Obecné nařízení ochrany dat (GDPR) obsahuje mnoho požadavky o shromažďování, ukládání a pomocí osobní údaje, včetně jak organizace identifikovat a zabezpečit data, osobní zohlednily požadavky průhlednost, zjistit a sestavy narušení osobní data a pracovníky train ochrany osobních údajů a ostatním zaměstnancům. GDPR jednotlivce nabízí větší kontrolu nad jejich osobních údajů a ukládá mnoho nových závazků v organizacích, které shromažďovat, zpracovat nebo analyzovat osobní data. GDPR ukládá nové pravidel na organizace, které nabízejí zboží a služeb lidí z Evropské unie (EU), nebo že shromažďovat a analyzovat data svázané s obyvatele Evropské unie. GDPR platí bez ohledu na to, kde se nachází v organizaci.

Microsoft navrhovat Azure s špičkový bezpečnostní opatření a zásady ochrany osobních údajů k zabezpečení dat v cloudu, včetně osobních údajů, které se identifikovanou pomocí GDPR kategorií. Společnosti Microsoft [smluvních podmínek](http://aka.ms/Online-Services-Terms) potvrdit Microsoft požadavkům procesory.

Tento zabezpečení Azure a dodržování předpisů plán, podle kterého obsahuje pokyny k nasazení architektura analýzy dat v Azure, který pomáhá s požadavky GDPR. Toto řešení ukazuje způsoby, ve kterém můžete zákazníkům splňují konkrétní bezpečnostní a požadavky na dodržování předpisů a slouží jako základ pro zákazníky, vytvořit a nakonfigurovat vlastní řešení pro analýzu dat v Azure. Zákazníci mohou využít tuto referenční architekturu a postupujte podle společnosti Microsoft [čtyři kroky](https://aka.ms/gdprebook) v cestě k GDPR dodržování předpisů:
1. Vyhledávání: Určete, které osobní údaje existuje a kde se nachází.
2. Správa: Jak osobní data řídit použití a získat přístup.
3. Chránit: Ovládací prvky zabezpečení k zabránění, zjišťování a reakce na ohrožení zabezpečení a úniky dat vytvořte.
4. Sestava: Uchovávat požadované dokumentaci a spravovat požadavky na data a oznámení v souladu.

Tento referenční architektura, přidružené implementace průvodce a model hrozeb které slouží jako základ pro zákazníky se jejich specifické požadavky přizpůsobit a by se neměla používat jako-je v produkčním prostředí. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem GDPR způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje platformu analytics němž zákazníci mohou vytvářet své vlastní nástroje pro analýzu. Referenční architektura popisuje případu použití obecné kde Zákazníci vstupní data prostřednictvím importy dat hromadné správcem SQL nebo dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Datové proudy obou pracovní začlenit Azure Functions pro import dat do Azure SQL Database. Azure Functions musí být nakonfigurované zákazník prostřednictvím portálu Azure pro zpracování úlohy importu jedinečné pro každé zákazníka vlastní požadavky analytics.

Azure nabízí celou řadu analýz a generování sestav služby pro zákazníka; Toto řešení však zahrnuje služby Azure Machine Learning ve spojení s databází SQL Azure a rychle procházet přes data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování dat zákazníka. Azure Machine Learning je forma strojové učení, které mají zvýšit rychlost dotaz tak, že zjišťování nové vztahy mezi datové sady. Jakmile byl vyzkoušen data prostřednictvím několika statistických funkcí, až 7 další dotaz fondy (8 celkový, včetně serveru zákazníka) můžete synchronizovat se službou stejné tabulkové modely šíří dotazu úlohy a snížení doby odezvy.

Pro rozšířené analýzy a vytváření sestav můžete nakonfigurovat databází SQL Azure s indexy columnstore. Azure Machine Learning a databází SQL Azure můžete škálovat nahoru nebo dolů nebo vypnuté zcela v reakci na využití zákazníka. Veškerý provoz SQL se šifrují pomocí protokolu SSL prostřednictvím zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup Azure doporučuje použití důvěryhodné certifikační autority pro lepší zabezpečení.

Jakmile data jsou odeslány do databáze SQL Azure a cvičení pomocí Azure Machine Learning, se mineralizuje provozní uživatele a správce SQL nebo dat s Power BI. Power BI zobrazí dat. intuitivně a vrátí informace napříč více datových sad k vykreslení větší přehled. Jeho vysoký stupeň přizpůsobivost a snadnou integraci s Azure SQL Database zajišťuje, že zákazníci mohou nakonfigurovat zpracovávat širokou škálu scénářů podle požadavků jejich obchodních potřeb.

Celé řešení je založena na úložiště Azure, což zákazníkům nakonfigurovat z portálu Azure. Azure Storage šifruje všechna data pomocí šifrování služby úložiště na zachovávejte důvěrnost dat v klidovém stavu. Geografické redundantní úložiště (GRS) zajišťuje, že nebude nežádoucí událost na primární datové centrum zákazníka vést ke ztrátě dat jako druhé kopie bude uložena v umístění samostatné stovky miles rychle.

Pro lepší zabezpečení Tato architektura slouží ke správě prostředků s Azure Active Directory a Azure Key Vault. Stav systému je monitorován pomocí Operations Management Suite (OMS) a monitorování Azure. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokoly a zobrazení stavu systému v řídicím panelu jeden, snadno navigaci.

Databáze SQL Azure je běžně spravované přes SQL Server Management Studio (SSMS), který se spouští z místního počítače nakonfigurovaná pro přístup k databázi SQL Azure přes zabezpečené připojení VPN nebo ExpressRoute. **Azure doporučuje konfiguraci připojení VPN nebo ExpressRoute pro správu a data importovat do skupiny prostředků referenční architektura**.

![Analýza pro GDPR odkazovat diagram architektury](images/gdpr-analytics-architecture.png?raw=true "analýza pro GDPR odkazovat diagram architektury")

Toto řešení používá následující služby Azure. Podrobnosti architektury nasazení najdete v [architektura nasazení služby](#deployment-architecture) části.

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Azure Active Directory
- Azure Key Vault
- Služby Operations Management Suite (OMS)
- Azure Monitor
- Azure Storage
- Řídicí panel Power BI
- Katalog dat Azure
- Azure Security Center
- Application Insights
- Azure Event Grid
- skupiny zabezpečení sítě

## <a name="deployment-architecture"></a>Architektura nasazení služby
V následující části Podrobné informace o nasazení a implementace elementy.

**Azure mřížky událostí**
[mřížky událostí Azure](https://docs.microsoft.com/en-us/azure/event-grid/overview) umožňuje zákazníkům snadno vytvářet aplikace, které se na základě událostí architektury. Uživatelé vybrat Azure prostředek, ke kterému se chcete přihlásit k odběru a poskytněte obslužné rutiny události nebo webhooku koncového bodu na události. Zákazníci můžete zabezpečit koncové body webhooku přidáním parametry dotazu na adresu URL webhooku při vytváření předplatného služby událostí. Azure mřížky událostí podporuje pouze HTTPS webhooku koncové body. Azure mřížky událostí umožňuje zákazníkům řídit úroveň přístupu k různým uživatelům dělat různé operace správy, jako je například seznam událostí odběry, vytvořte nové a generovat klíče. Událost mřížky využívá řízení řízení přístupu (RBAC).

**Azure Functions**
[Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) je bez serveru výpočetní služba, která umožňuje uživatelům spustit kód na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Azure Machine Learning**
[Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/) jde o techniku vědecké účely data, která umožňuje počítačům existující data použít k předpovídat budoucí chování, výsledky a trendy.

**Azure Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) díky zdroje dat snadno zjistitelné a srozumitelnější uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovat, označí a vyhledávat osobní data. Data zůstávají uložena ve stávajícím umístění, ale kopie jeho metadata se přidá do katalogu Data Catalog, spolu s odkazem na umístění zdroje dat. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

### <a name="virtual-network"></a>Virtuální síť
Tuto referenční architekturu definuje privátní virtuální sítě s adresním prostorem 10.0.0.0/16.

**Skupin zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu (ACL), povolí nebo zakážou provoz v rámci virtuální sítě. Skupiny Nsg můžete použít k zabezpečení přenosů dat na podsíť nebo jednotlivé úroveň virtuálního počítače. Následující skupiny Nsg existují:
  - Skupina NSG pro službu Active Directory
  - Skupina NSG pro pracovní vytížení

Každý z skupin Nsg mít určité porty a protokoly otevřete tak, aby řešení může fungovat správně a bezpečně. Kromě toho tyto konfigurace jsou povolené pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložený v účtu úložiště
  - Analýzy protokolů OMS je připojený k [NSG na diagnostiky](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: každé podsítě je přidružen jeho odpovídající skupina NSG.

### <a name="data-in-transit"></a>Přenášených dat
Azure šifruje veškerá komunikace do a z datových centrech Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage prostřednictvím portálu Azure dojít přes HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém stavu prostřednictvím šifrování, databáze, auditování a jiných opatření.

**Úložiště Azure** ke splnění šifrovaná data na požadavky rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá chránit a ochrany osobních údajů na podporu závazky týkajícími se zabezpečení organizace a požadavky na dodržování předpisů definované GDPR.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**Azure SQL Database**: Azure SQL Database instance používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL Azure je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci ochrany informací na rest. Šifrování TDE poskytuje záruku, že uložené osobní data nebyla neoprávněnému přístupu k.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby důvěrných osobních údajů se nikdy jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) slouží k ukončí zpracování dat tématům, protože umožňuje uživatele, přidání vlastních vlastností do databázové objekty a data označit jako "Nákup ukončen" pro podporu aplikační logiku a zabránit zpracování přidružené osobní data.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [SQL Dynamická Data maskování (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje ohrožení důvěrných osobních údajů ve maskování dat pro uživatele bez oprávnění nebo aplikace. DDM může automaticky zjistit potenciálně citlivých data a navrhněte odpovídající masek má být použita. To pomáhá s identifikací osobních údajů kvalifikující GDPR ochrany a pro omezení přístupu tak, že neexistuje databáze prostřednictvím neoprávněného přístupu. **Poznámka: Zákazníci budou muset upravit nastavení DDM řídit jejich schématu databáze.**

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují funkce, které chcete spravovat přístup k osobním datům v prostředí Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service. Všichni uživatelé v tomto řešení jsou vytvořené v AAD, včetně uživatele, kteří používají Azure SQL Database.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá AAD k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu Jak [chrání citlivá data v databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správcům definovat podrobných přístupová oprávnění k poskytnout pouze takovou úroveň přístupu, aby uživatelé potřebovat k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění pro prostředky Azure, mohou správci povolit jenom určité akce pro přístup k osobním datům. Předplatné přístup je omezen na správce předplatného.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako je například osobní data.  Správci můžou pomocí AAD Privileged Identity Management zjistit, omezte a monitorujte privilegované identity a jejich přístup k prostředkům. Tato funkce slouží také k vynucení na vyžádání, za běhu správy přístupu v případě potřeby.
-   [Ochrany identit AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu** toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce Azure Key Vault pomoci chránit osobní údaje a přístup k těmto údajům zákazníkům:
- Konfigurace zásad rozšířeného přístupu na základě potřeba.
- Minimální požadovaná oprávnění k klíče a tajné klíče jsou definovány zásad přístupu k trezoru klíč.
- Všechny klíče a tajné klíče v Key Vault mít data vypršení platnosti.
- Všechny klíče v trezoru klíče jsou chráněny modulů specializované hardwarového zabezpečení (HSM). Typ klíče je klíč RSA HSM chráněné 2048 bitů.
- Minimální požadovaná oprávnění pomocí RBAC jsou udělena všechny uživatele a identity.
- Diagnostické protokoly Key Vault je povolená s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací pro klíče jsou omezeny na ty, které jsou potřeba.

**Výstrahy zabezpečení**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) umožňuje zákazníkům sledovat provoz, shromažďování protokolů a analyzovat zdroje dat pro hrozeb. Kromě toho Azure Security Center používá existující konfigurace služeb Azure k poskytnutí configuration a služby doporučení pomáhají zlepšit zabezpečení a ochrana osobních údajů. Azure Security Center zahrnuje [hrozby intelligence sestavy](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) pro všechny nalezené hrozeb, které pomáhají reakcí na incidenty týmy prozkoumat ohrožení a oprava.

### <a name="logging-and-auditing"></a>Protokolování a auditování

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném. Protokoly aktivity vám mohou pomoci určit operaci iniciátor čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Azure Blob, tabulek a protokoly fronty.
- **Archivace protokolu**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci s dobou uchování definované 2 dní. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho jsou zahrnuty jako součást této architektury následující OMS řešení:
-   [Hodnocení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.
-   [Sledování změn](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure monitorování**
[Azure monitorování](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) pomáhá zákazníkům sledování výkonu a zachování zabezpečení, identifikovat trendy tím, že umožňuje organizacím audit, vytvářet výstrahy a archivaci dat, včetně sledování rozhraní API volá v prostředky Azure zákazníků.

**Application Insights**
[Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/) je rozšiřitelný služba Správa výkonu aplikace (APM) pro vývojáře, kteří ve více platformách. Použijte jej k monitorování provozu webové aplikace. Zjistí anomálie výkonu a zahrnuje výkonné analytics nástrojů pro usnadnění diagnostiky problémů a zjistit, co uživatelé ve skutečnosti dělat s aplikací. Je navržen tak, aby pomohla uživatelům neustále průběžně zlepšují výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/gdprAnalyticsdfd) nebo naleznete níže. Tento model může pomoct pochopit body potenciální riziko v infrastruktuře systému při provádění změn zákazníků.

![Analýza pro GDPR hrozby modelu](images/gdpr-analytics-threat-model.png?raw=true "analýza pro GDPR hrozby modelu")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů
[Zabezpečení Azure a dodržování předpisů plán, podle kterého – matice odpovědnosti zákazníka GDPR](https://aka.ms/gdprCRM) uvádí řadiče a procesoru odpovědnosti pro všechny články GDPR. Všimněte si, službách Azure zákazníka je obvykle kontroleru a Microsoft funguje jako procesor.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - matici implementace analýzy dat GDPR](https://aka.ms/gdprAnalytics) poskytuje informace, na které GDPR architektura analýzy dat, včetně podrobného popisu, jak řešit články implementace splňuje požadavky na každého zahrnutého článku.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí nakonfigurovat tak, aby bezpečně navázat připojení k nasazené jako součást tato data prostředky analytics referenční architektura. Zákazníci správně nastavení sítě VPN nebo ExpressRoute, můžete přidejte vrstvu ochrany dat během přenosu.

Implementací přes zabezpečené tunelové propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síti Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunel" informací uvnitř šifrované odkaz mezi sítí a Azure zákazníka. Site-to-Site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. [Režimu tunelového připojení IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá v této možnosti jako mechanismus šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě lepšímu zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místní umístění nebo poskytovatele hostingu serveru Exchange. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Kromě toho protože to je přímé připojení poskytovatele telekomunikace zákazníka, data není přenášet přes Internet a proto nebude vystavena k němu.

Osvědčené postupy pro implementaci zabezpečeného hybridní síti, která rozšiřuje do místní sítě do Azure jsou [k dispozici](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace-zatížením (ETL)
[PolyBase](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/polybase-guide) můžete načíst data do Azure SQL Database bez nutnosti samostatné ETL nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje jiných výrobců, které jsou kompatibilní se systémem SQL Server, lze pomocí funkce PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) je nezbytné pro správu nasazení a zřizování přístup osobám interakci s prostředím. Existující Windows Server Active Directory se dá integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také dokáže spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující aad tím, že nasazené infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
