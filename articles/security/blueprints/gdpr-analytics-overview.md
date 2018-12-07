---
title: Zabezpečení Azure a dodržování předpisů - Analytics gdpr
description: Zabezpečení Azure a dodržování předpisů - Analytics gdpr
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 8e4245de5d6a025fa87c8644678896596b07c49e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001126"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Zabezpečení Azure a dodržování předpisů: Analytics gdpr

## <a name="overview"></a>Přehled
Obecné nařízení (GDPR) obsahuje mnoho požadavků o shromažďování, ukládání a používání osobní informace, včetně jak organizacím identifikovat a zabezpečení osobních údajů, plnění požadavků na transparentnosti, zjišťovat a sestavy porušení osobní údaje a o ochraně osobních údajů pracovníci trénování a ostatními zaměstnanci. GDPR poskytuje větší kontrolu nad jejich osobní údaje jednotlivce a ukládá mnoho nových závazků na organizací, které shromažďovat, zpracovávat, analyzovat osobní údaje. GDPR ukládá nová pravidla pro organizace, které nabízejí zboží a služby lidem v Evropské unii (EU) nebo které shromažďovat a analyzovat data svázaná s pobytem v EU. GDPR se vztahuje bez ohledu na to, kde se nachází organizace.

Microsoft proto navrhl Azure se špičkovou bezpečnostní opatření a zásad ochrany osobních údajů zabezpečující data v cloudu, včetně kategorií osobní údaje, které jsou identifikované GDPR. Společnosti Microsoft [smluvní podmínky](https://aka.ms/Online-Services-Terms) garantoval splňování požadavků procesorů Microsoft.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny pro nasazení architektura analýz dat v Azure, která pomáhá zajistit s požadavky GDPR. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů a slouží jako základ pro zákazníky, kteří k vytvoření a konfigurace svá vlastní řešení pro analýzu dat v Azure. Zákazníci mohou využívat tuto referenční architekturu a postupujte podle společnosti Microsoft [čtyři kroky](https://aka.ms/gdprebook) v cestě k dodržování předpisů GDPR:
1. Vyhledávání: Určete, které osobní údaje existuje a kde se nachází.
2. Správa: Řídí jak osobní data se budou používat a získat přístup.
3. Chránit: Řízení zabezpečení, které hrozbami, detekci a reakce na ohrožení zabezpečení a úniky dat navázat.
4. Sestava: Uchovávat vyžaduje dokumentaci a spravovat požadavky na data a oznámení o porušení zabezpečení.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky a nepoužívali-je v produkčním prostředí. Je třeba počítat s následujícím:
- Tato architektura poskytuje standardní hodnoty pomáhá zákazníkům nasadit úlohy o velikosti do Azure v podobě GDPR nedodržující předpisy.
- Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje platforma pro analýzy, na kterých zákazníci mohou vytvářet své vlastní analytické nástroje. Referenční architektura obsahuje případu použití obecných kde Zákazníci vstupní data pomocí hromadného importu dat správce SQL/dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Oba datové proudy práce začlenit funkce Azure pro import dat do Azure SQL Database. Služba Azure Functions je nutné nakonfigurovat zákazníků prostřednictvím webu Azure portal pro úlohy importu jedinečný pro každého zákazníka vlastní zpracování požadavků na analýzu.

Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka; Toto řešení však zahrnuje služby Azure Machine Learning ve spojení s Azure SQL Database a rychle procházet data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování dat zákazníků. Služba Azure Machine Learning je forma machine learningu určené pro zvýšení rychlosti dotazu zjištěním nové relace mezi datovými sadami. Jakmile data vyškolila přes několik statistických funkcí, až 7 dalšího dotazu fondy (celková, včetně serveru zákazník 8) můžete synchronizovat se službou stejné tabulkové modely rozložit zatížení dotazy a snížení doby odezvy.

Pro rozšířené analýzy a generování sestav lze nastavit s indexy columnstore Azure SQL Database. Azure Machine Learning a Azure SQL databáze můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Jakmile se data odeslán do databáze SQL Azure a školení technologii Azure Machine Learning, je mineralizuje provozní uživatele a správce SQL/Data s Power BI. Power BI zobrazí data intuitivně a získává informace napříč více datových sad, chcete-li nakreslit lepší přehled o. Jeho vysoký stupeň přizpůsobivost a snadnou integraci s Azure SQL Database zajišťuje, že zákazníci můžou nakonfigurovat zpracovávat širokou škálu scénářů znamének podle svých obchodních potřeb.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurovat z portálu Azure portal. Azure Storage šifruje všechna data pomocí šifrování služby Storage k údržbě důvěrnost dat v klidovém stavu. Geografické redundantní úložiště (GRS) zajišťuje, že nežádoucí událost ve primárního datového centra zákazníka nebude mít za následek ztrátu dat jako druhá kopie se uloží v samostatném umístění stovky mil okamžitě.

Pro zvýšení zabezpečení Tato architektura slouží ke správě prostředků pomocí Azure Active Directory a Azure Key Vault. Stav systému je monitorovat prostřednictvím Log Analytics a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Azure SQL Database je obecně spravované přes SQL Server Management Studio (SSMS), který se spouští z místního počítače nakonfigurovaná pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute. **Konfigurace připojení VPN nebo ExpressRoute pro správu a data Azure doporučuje naimportovat do skupiny prostředků odkaz na architekturu**.

![Diagram architektury reference pro analýzy gdpr](images/gdpr-analytics-architecture.png?raw=true "reference pro analýzy gdpr diagram architektury")

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Azure Active Directory
- Azure Key Vault
- Log Analytics
- Azure Monitor
- Azure Storage
- Řídicí panel Power BI
- Katalog dat Azure
- Azure Security Center
- Application Insights
- Azure Event Grid
- skupiny zabezpečení sítě

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure Event Grid**
[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) umožňuje zákazníkům snadno vytvářet aplikace s architekturami založenými na událost. Uživatelé vybrat prostředek Azure, které se chcete přihlásit k odběru a obslužné rutiny události nebo webhooku k odeslání události do koncového bodu. Zákazníkům umožňují zabezpečit koncové body webhooku přidáním parametrů dotazu na adresu URL webhooku, při vytváření odběru událostí. Azure Event Grid se podporuje jenom koncové body HTTPS webhooku. Azure Event Grid umožňuje zákazníkům určit úroveň přístupu k různým uživatelům provádět různé operace správy, jako je například seznam odběrů událostí, vytvářet nové a vygenerujte klíče. Event Grid využívá Azure Role-Based řízení přístupu (RBAC).

**Služba Azure Functions**
[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) je a bez serveru výpočetní služba, která umožňuje uživatelům spouštět kód na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Služba Azure Machine Learning**
[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) je technika datové vědy, která umožňuje počítačům s využitím existujících dat předpovídá budoucí chování, výsledky a trendy.

**Azure Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat snadno objevitelné a srozumitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a vyhledávat osobní údaje. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat spolu s odkazem na umístění zdroje dat přidá do katalogu Data Catalog. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť, která adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahovat seznamů řízení přístupu (ACL), která povolují nebo zakazují provoz ve virtuální síti. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro pracovní vytížení

Skupiny zabezpečení sítě mají konkrétní porty a protokoly, otevřete řešení mohli pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Log Analytics je připojen k [skupin zabezpečení sítě a Diagnostika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage** podle šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit osobní údaje na podporu závazky zabezpečení organizace a požadavky na dodržování předpisů GDPR definované.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows, zajišťuje šifrování pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   Azure SQL Database je nakonfigurován na použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že uložené osobní data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že nikdy zobrazuje citlivé osobní údaje jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) je možné přestat zpracování subjekty údajů, protože umožňuje uživatelům přidání vlastních vlastností do databázové objekty a data označit jako "Vyřazeno" pro podporu aplikační logiku a zabránit zpracování související osobní údaje.
- [Zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [SQL dynamických dat maskování (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých osobních dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. DDM můžete automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá s identifikací osobních údajů kvalifikaci ochrany podle nařízení GDPR a pro omezení přístupu tak, že neexistuje databáze prostřednictvím před neoprávněným přístupem. **Poznámka: Zákazníci bude nutné upravit nastavení DDM dodržovat svoje schéma databáze.**

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti pro správu přístup k osobním údajům v prostředí Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří ve službě AAD, včetně uživatele, kteří používají Azure SQL Database.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Šifrování sloupců databáze dále používá AAD k ověřování aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k osobním datům. Přístup k předplatnému je omezená na správce předplatného.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako je osobní údaje.  Mohou správci AAD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
-   [Ochrana Identity AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů** toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou osobních údajů a přístup k tyto údaje:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení (HSM). Typ klíče je HSM chráněné 2048bitový klíč RSA.
- Minimální požadovaná oprávnění pomocí RBAC jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Výstrahy zabezpečení**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) zákazníkům umožňuje monitorování provozu, shromažďovat protokoly a analýza zdroje dat pro hrozby. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a ochrana osobních údajů. Azure Security Center zahrnuje [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) pro každou zjištěnou hrozbou pro pomoc týmům reakce na incidenty vyšetřením a odstraněním hrozeb.

### <a name="logging-and-auditing"></a>Protokolování a auditování

[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlou protokolování systémových a uživatelských aktivit a také stav systému. [Log Analytics](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a místním prostředí.
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Blob v Azure, tabulky a fronty protokoly.
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
-   [Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking): řešení Change Tracking umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá zákazníkům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně rozhraní API pro sledování volá v prostředcích zákazníků Azure.

**Application Insights**
[Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Použijte ho k monitorování živé webové aplikace. Detekuje anomálie výkonu a obsahuje výkonné analytické nástroje pro usnadnění diagnostiky potíží a pochopit, jak vlastně uživatelé s aplikací. Je navržena tak, aby pomáhal uživatelům průběžně vylepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/gdprAnalyticsdfd) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Model hrozeb Analytics gdpr](images/gdpr-analytics-threat-model.png?raw=true "Analytics gdpr hrozeb modelu")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka GDPR](https://aka.ms/gdprCRM) uvádí kontroleru a procesor odpovědnost za všechny články podle nařízení GDPR. Mějte prosím na paměti, že pro služby Azure, je zákazník obvykle kontroleru a vystupuje Microsoft jako zpracovatel.

[Azure zabezpečení a dodržování předpisů – GDPR Data Analytics implementace matice](https://aka.ms/gdprAnalytics) poskytuje informace, na které GDPR článků se tak vyřeší, tak na architekturu analýzy dat, včetně podrobného popisu jak implementace splňuje požadavky na každého zahrnutého článku.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást tato data analytics referenční architektuře. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Proces extrakce, transformace a načítání (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete načíst data do Azure SQL Database bez nutnosti samostatné ETL nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje třetích stran, které jsou kompatibilní s SQL serverem, je možné díky technologii PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující AAD tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
