---
title: Zabezpečení Azure a dodržování předpisů – analýza dat pro PCI DSS
description: Zabezpečení Azure a dodržování předpisů – analýza dat pro PCI DSS
services: security
author: meladie
ms.assetid: 6c48cd8e-c548-44e8-a21a-17316c152cbb
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: de272e3a8ca316d46efafc0af637b6f783f9cdd3
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579526"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-pci-dss"></a>Dodržování předpisů a zabezpečení Azure: Analýza pro PCI DSS

## <a name="overview"></a>Přehled

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny pro nasazení architektury analýzy dat v Azure, která pomáhá s požadavky na platební kartu Industry Data Security Standards (PCI DSS 3.2). Představuje běžné referenční architekturu a demonstruje správné zpracování údajů o kreditních kartách (včetně karty číslo, vypršení platnosti a ověřování dat) v prostředí zabezpečené, kompatibilní a úrovně. Tento podrobný plán ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů a slouží jako základ pro zákazníky, kteří k vytvoření a konfigurace svá vlastní řešení pro analýzu dat v Azure.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky pro splnění požadavků PCI DSS 3.2. Toto řešení poskytuje standardní hodnoty pomáhá zákazníkům nasadit úlohy o velikosti do Azure způsobem kompatibilní s PCI DSS 3.2; Toto řešení by neměly ale sloužit jako-je v produkčním prostředí, protože není nutná další konfigurace.

Dosažení PCI DSS dodržování předpisů vyžaduje, že akreditované Assessor pro kvalifikovaný zabezpečení (podmínky) certifikovat řešení produkčním prostředí zákazníka. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty

Toto zabezpečení Azure a dodržování předpisů poskytuje platforma pro analýzy, na kterých zákazníci mohou vytvářet své vlastní analytické nástroje. Referenční architektura obsahuje případu použití obecných kde Zákazníci vstupní data pomocí hromadného importu dat správce SQL/dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Oba datové proudy práce začlenit funkce Azure pro import dat do Azure SQL Database. Služba Azure Functions je nutné nakonfigurovat zákazníků prostřednictvím webu Azure portal pro úlohy importu jedinečný pro každého zákazníka vlastní zpracování požadavků na analýzu.

Azure nabízí širokou škálu služeb generování sestav a analýz pro zákazníky. Toto řešení zahrnuje služby Azure Machine Learning ve spojení s Azure SQL Database a rychle procházet data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování. Azure Machine Learning zvyšuje rychlost dotazů zjištěním nové relace mezi datovými sadami. Jakmile data vyškolila přes několik statistických funkcí, až 7 dalšího dotazu fondy (celková, včetně serveru zákazník 8) můžete synchronizovat se službou stejné tabulkové modely rozložit dotazu úlohy a snížení doby odezvy.

Pro rozšířené analýzy a generování sestav lze nastavit s indexy columnstore Azure SQL Database. Azure Machine Learning a Azure SQL databáze můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Jakmile se data odeslán do databáze SQL Azure a školení technologii Azure Machine Learning, je mineralizuje provozní uživatele a správce SQL/Data s Power BI. Power BI zobrazí data intuitivně a získává informace napříč více datových sad, chcete-li nakreslit lepší přehled o. Jeho vysoký stupeň přizpůsobivost a snadnou integraci s Azure SQL Database zajišťuje, že zákazníci můžou nakonfigurovat zpracovávat širokou škálu scénářů znamének podle svých obchodních potřeb.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci datacentra vybraného zákazníka pro odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že data budou replikovat do sekundárního datacentra stovky mil okamžitě a znovu uložené jako tři kopie v rámci stejné datové centrum, brání což vede ke ztrátě nepříznivých událostí ve primárního datového centra zákazníka data.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory, řízení přístupu na základě role se používá pro řízení přístupu k nasazené prostředky, včetně jejich klíčů ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Azure SQL Database je obecně spravované přes SQL Server Management Studio (SSMS), který se spouští z místního počítače nakonfigurovaná pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute. **Microsoft doporučuje, abyste konfiguraci připojení VPN nebo ExpressRoute pro správu a data importovat do skupiny prostředků odkaz na architekturu**.

![Analýza pro diagram referenční architektury PCI DSS](images/pcidss-analytics-architecture.png "Analytics pro diagram referenční architektury PCI DSS")

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Application Insights
- Azure Active Directory
- Katalog dat Azure
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 sítě
    - (2) /24 sítě
    - (2) skupiny zabezpečení sítě
- Operations Management Suite
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) umožňuje zákazníkům snadno vytvářet aplikace s architekturami založenými na událost. Uživatelé vybrat prostředek Azure, které se chcete přihlásit k odběru a obslužné rutiny události nebo webhooku k odeslání události do koncového bodu. Zákazníkům umožňují zabezpečit koncové body webhooku přidáním parametrů dotazu na adresu URL webhooku, při vytváření odběru událostí. Azure Event Grid se podporuje jenom koncové body HTTPS webhooku. Azure Event Grid umožňuje zákazníkům určit úroveň přístupu k různým uživatelům provádět různé operace správy, jako je například seznam odběrů událostí, vytvářet nové a vygenerujte klíče. Event Grid využívá řízení přístupu na základě rolí Azure.

**Služba Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) je a bez serveru výpočetní služba, která umožňuje uživatelům spouštět kód na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) je technika datové vědy, která umožňuje počítačům s využitím existujících dat předpovídá budoucí chování, výsledky a trendy.

**Azure Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat snadno objevitelné a srozumitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a hledá finanční data. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat spolu s odkazem na umístění zdroje dat přidá do katalogu Data Catalog. Tato metadata jsou také indexována, aby byl každý zdroj dat snadno objevitelný prostřednictvím vyhledávání a aby byl srozumitelný uživatelům, kteří ho objevili.

### <a name="virtual-network"></a>Virtuální síť

Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:

  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro pracovní vytížení

Skupiny zabezpečení sítě mají určité porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro každou skupinu zabezpečení sítě:

- [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
- Log Analytics je připojen k [skupinu zabezpečení sítě&#39;s diagnostické protokoly](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupinu zabezpečení sítě.

### <a name="data-in-transit"></a>Přenášená data

Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).  To pomáhá zabezpečit a chránit data držitelů platebních karet podporu závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené PCI DSS 3.2.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows, zajišťuje šifrování pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
- [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
- Azure SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
- [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
- [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
- [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) je možné přestat zpracování subjekty údajů, protože umožňuje uživatelům přidání vlastních vlastností do databázové objekty a data označit jako "Vyřazeno" pro podporu aplikační logiku a zabránit zpracování související finanční data.
- [Zabezpečení na úrovní řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá identifikovat a omezit přístup k datům tak, že neexistuje databáze prostřednictvím před neoprávněným přístupem. Pro úpravu nastavení dodržovat svoje schéma databáze maskování dynamických dat zodpovídají zákazníci.

### <a name="identity-management"></a>Správa identit

Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) microsoftem&#39;s více tenanty založené na cloudu adresáři a identitami služby management. Všechny uživatele tohoto řešení se vytvoří v Azure Active Directory, včetně uživatele, kteří používají Azure SQL Database.
- Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure Active Directory k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace. Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení by to ovlivnilo organizace&#39;s identitami, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s organizace&#39;s identitami a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení

**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault pomáhá zákazníkům chránit a přístup k tyto údaje:

- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je HSM chráněné 2048bitový klíč RSA.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly služby Azure Storage, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy v pracovním prostoru Operations Management Suite, což umožňuje společnou analýzu bez ohledu na jejich původní zdroj všechna data. Kromě toho Azure Security Center umožňuje integrací s Log Analytics a umožňuje zákazníkům používat dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby Azure SQL Database. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Detekuje anomálie výkonu a obsahuje výkonné analytické nástroje pro usnadnění diagnostiky potíží a pochopit, jak vlastně uživatelé s aplikací. Je navržena tak, aby pomáhal uživatelům průběžně vylepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/PCIAnalyticsTM) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Analýza pro diagram referenční architektury PCI DSS](images/pcidss-analytics-threat-model.png "Analytics pro model hrozeb PCI DSS")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – PCI DSS zákazníka odpovědnost matice](https://aka.ms/pcidss-analytics-tm) uvádí odpovědnost za všechny požadavky PCI DSS 3.2.

[Azure zabezpečení a dodržování předpisů – PCI DSS Data Analytics implementace matice](https://aka.ms/pcidss-analytics-cim) poskytuje informace, na které PCI DSS 3.2 požadavky se tak vyřeší, tak na architekturu analýzy dat, včetně podrobného popisu o způsobu implementace splňuje požadavky na každého zahrnutého ovládacího prvku.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute

Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást tato data analytics referenční architektuře. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces extrakce, transformace a načítání
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) může načtení dat do Azure SQL Database bez potřeby samostatná extrakce, transformace, načíst nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Společnosti Microsoft business intelligence a analýza zásobníku, jakož i nástroje třetích stran, které jsou kompatibilní s SQL serverem, je možné díky technologii PolyBase.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat se službou Azure Active Directory v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) stávající Azure Active Directory tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby Azure Active Directory.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
