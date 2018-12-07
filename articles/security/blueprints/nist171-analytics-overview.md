---
title: Zabezpečení Azure a dodržování předpisů – analýza dat pro SP NIST 800-171
description: Zabezpečení Azure a dodržování předpisů – analýza dat pro SP NIST 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a4f84d6e61d3a100f952908883e6eb70d81f66b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998805"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Zabezpečení Azure a dodržování předpisů – analýza dat pro SP NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů obsahuje pokyny, které pomáhá zákazníkům nasadit architekturu analytics data v Azure, která implementuje podmnožinu NIST SP 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů. Slouží také jako základ pro zákazníky, kteří k vytvoření a konfigurace svá vlastní řešení pro analýzu dat v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky. Neměl by se používat jako-je v produkčním prostředí. Nasazení této architektury bez jakýchkoli úprav není dostatečná k SP NIST 800-171 zcela požadavkům. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořená, který používá tuto architekturu. Požadavky může lišit v závislosti na konkrétní implementaci každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje platforma pro analýzy, na kterých zákazníci mohou vytvářet své vlastní analytické nástroje. Referenční architektura obsahuje případu obecné použití. Zákazníci slouží k zadání dat pomocí hromadného importu dat správcem SQL/Data. Můžete jej také použít pro vstupní data prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Obě workstreams začlenit funkce Azure pro import dat do Azure SQL Database. Služba Azure Functions je nutné nakonfigurovat zákazníků prostřednictvím webu Azure portal ke zpracování úlohy importu jedinečných požadavků na analýzu zákazníka.

Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka. Toto řešení používá k rychle procházet data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování dat služby Azure Machine Learning a SQL Database. Machine Learning slouží pro zvýšení rychlosti dotazu zjištěním nové relace mezi datovými sadami. Data se na začátku trénuje přes několik statistických funkcí. Později až sedm dalšího dotazu fondů můžete synchronizovat se službou stejné tabulkové modely rozložit zatížení dotazy a snížení doby odezvy. Server zákazníka přináší celkem dotazu, fondy na 8.

Pro rozšířené analýzy a generování sestav můžete nakonfigurovat SQL Database s indexy columnstore. Machine Learning a SQL Database můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučujeme použít důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Poté, co dat je odeslán do databáze SQL a školení strojové učení, se mineralizuje provozní uživatele a správce SQL/dat pomocí Power BI. Power BI zobrazí data intuitivně a získává informace napříč více datových sad, chcete-li nakreslit lepší přehled o. Power BI má vysoký stupeň přizpůsobivost a snadno se integruje se službou SQL Database. Zákazníci můžou nakonfigurovat zpracovávat širokou škálu scénářů, které jsou vyžadované svých obchodních potřeb.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurovat z portálu Azure portal. Storage šifruje všechna data pomocí šifrování služby Storage k údržbě důvěrnost dat v klidovém stavu. Geograficky redundantní úložiště zajišťuje, že nežádoucí událost ve primárního datového centra zákazníka nebude mít za následek ztrátu dat. Druhé kopie je uložena v samostatném umístění stovky mil okamžitě.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory (Azure AD) řízení přístupu na základě role (RBAC) se používá k řízení přístupu k nasazené prostředky. Tyto prostředky zahrnují klíče zákazníků ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokoly. Zobrazí se stav systému v jednom řídicím panelu, který se snadno používá.

Běžně je spravované SQL Database přes SQL Server Management Studio. Spuštění z místního počítače nakonfigurovaná pro přístup k databázi SQL pomocí zabezpečené sítě VPN nebo připojení Azure ExpressRoute. *Doporučujeme nakonfigurovat připojení VPN nebo ExpressRoute pro správu a data importovat do skupiny prostředků*.

![Analýza dat pro diagram referenční architektury SP NIST 800-171](images/nist171-analytics-architecture.png "Data Analytics pro SP NIST 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v tématu [architektura nasazení](#deployment-architecture) oddílu.

- Application Insights
- Azure Active Directory
- Katalog dat Azure
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Log Analytics
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 sítě
    - (2) /24 sítě
    - (2) skupiny zabezpečení sítě
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure Event Grid**: S [služby Event Grid](https://docs.microsoft.com/azure/event-grid/overview), Zákazníci můžete snadno vytvářet aplikace pomocí architektury založené na událostech. Uživatelé vybrat prostředek Azure, které se chcete přihlásit k odběru. Potom nabízejí obslužná rutina události nebo webhooku k odeslání události do koncového bodu. Zákazníkům umožňují zabezpečit koncové body webhooku přidáním parametrů dotazu na adresu URL webhooku, při vytváření odběru událostí. Event Grid podporuje jenom koncové body HTTPS webhooku. Pomocí služby Event Grid můžou zákazníci určují úroveň přístupu k různým uživatelům provádět různé operace správy. Uživatelé mohou seznam odběrů událostí, vytvářet nové značky a generovat klíče. Event Grid používá Azure RBAC.

**Služba Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) je výpočetní služba bez serveru, na kterém běží kód na vyžádání. Není nutné explicitně zřizovat nebo spravovat infrastrukturu. Azure Functions můžete použít ke spuštění skriptu nebo kusu kódu jako reakci na různé události.

**Služba Azure Machine Learning**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) je technika datové vědy, která umožňuje počítačům s využitím existujících dat předpovídá budoucí chování, výsledky a trendy.

**Azure Data Catalog**: [katalogu Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) činí zdroje dat byly snadno vyhledatelné a pochopitelné uživatelé, kteří spravují data. Běžné zdroje dat můžete zaregistrovaný, příznakem a vyhledávat data. Data zůstávají uložena ve stávajícím umístění, ale kopie metadat se přidá do katalogu Data Catalog. Odkaz na umístění zdroje dat je v ceně. Metadata je indexována, aby byl každý zdroj dat snadno vyhledat přes vyhledávání. Indexování také umožňuje srozumitelné pro uživatele, kteří je zjistili.

### <a name="virtual-network"></a>Virtuální síť
Tato referenční architektura definuje privátní virtuální síť s adresní prostor 10.0.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě (Nsg). Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
  - Skupina zabezpečení sítě pro službu Active Directory
  - Skupina zabezpečení sítě pro pracovní vytížení

Skupiny zabezpečení sítě obsahují konkrétní porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Log Analytics je připojen k [skupin zabezpečení sítě a Diagnostika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Všechny transakce úložiště na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: pro splnění požadavků pro šifrovaná data v klidovém stavu, všechny [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato funkce pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené SP NIST 800-171.

**Azure Disk Encryption**: [šifrování disku](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) používá funkci BitLocker systému Windows pro zajištění šifrování svazku pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, když k nim dojde. Nabízí výstrahy zabezpečení podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé databázové vzory přístupu k.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v systému databáze. Po povolení šifrování dat pouze klientských aplikací nebo aplikačních serverů s přístupu ke klíčům můžou k datům ve formátu prostého textu.
- [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat neoprávněným uživatelům nebo aplikacím. Může automaticky zjistit potenciálně citlivých data a navrhnout odpovídající masky uplatňovat. Dynamické maskování dat pomáhá omezit přístup tak, aby citlivá data nejsou ukončení databáze prostřednictvím před neoprávněným přístupem. *Zákazníci odpovídají za nastavení dodržovat svoje schéma databáze.*

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je Microsoft víceklientské cloudové adresáře a identity management service. Všechny uživatele tohoto řešení se vytvoří ve službě Azure AD a zahrnovat uživatele, kteří přistupují k databázi SQL.
-   Pomocí služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu Jak [integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Šifrování sloupců databáze také používá Azure AD k ověření aplikace ke službě SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správci definovat oprávnění velice přesně kontrolovat přístup. S ním že můžete udělit pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) je možné minimalizovat počet uživatelů, kteří mají přístup k určité informace, jako jsou data zákazníků. Správci můžou používat Azure AD Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce také umožňuje vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci. Prozkoumá také podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je hardwaru chráněné modulem zabezpečení 2048bitový klíč RSA.
- Všichni uživatelé a identity jsou udělena minimální požadovaná oprávnění pomocí RBAC.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Azure Security Center**: S [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Security Center také přistupuje k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

 Security Center používá celou řadu možností detekce zákazníkům potenciální útoky výstrahy, které se zaměřují jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se aktivují místo pořízením hrozby nebo podezřelé aktivity. Zákazníci můžou využít [vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

 Security Center nabízí výstrahy zabezpečení seřazené podle priority a incidentů. Security Center usnadňuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozeb. Reakce na incidenty týmy mohou pomocí sestavy, když vyšetřením a odstraněním hrozeb.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly úložiště, protokoly auditu služby Key Vault a Azure Application Gateway přístup a brána firewall protokoly. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uživatelé můžou konfigurovat dobu uchování až do 730 dnů podle svých specifických požadavků.

**Log Analytics**: protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění dat jsou uspořádány do samostatných tabulek pro jednotlivé datové typy v rámci pracovních prostorů Log Analytics. Tímto způsobem lze všechna data pohromadě, analyzovat bez ohledu na jejich původní zdroj. Security Center umožňuje integrací s Log Analytics. Zákazníci mohou pomocí dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Poskytuje seznam seřazený podle priority doporučení specifická pro nasazenou serverové infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Zákazníkům přináší uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): sestavy řešení Agent Health tom, kolik agentů je nasazených a jejich geografické distribuce. Sestavy také na tom, kolik agentů jsou reagovat a počet agentů, které odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby SQL Database. Zákazníci můžou využít automatizaci [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožní snadno identifikovat změny v prostředí.

**Azure Monitor**: [monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů. Organizace slouží k auditovat, vytvářet upozornění a archivovat data. Také mohou sledovat volání rozhraní API ve svých prostředků Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) je rozšiřitelná služba správy výkonu aplikací webovým vývojářům na více platformách. Detekuje anomálie výkonu a obsahuje výkonné analytické nástroje. Nástroje pomoci diagnostikovat problémy a které zákazníkům pomůžou pochopit, co uživatelé dělají s aplikací. Je navržena tak, aby pomáhal uživatelům průběžně vylepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-analytics-tm) nebo najdete tady. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Analýza dat pro model hrozeb SP NIST 800-171](images/nist171-analytics-threat-model.png "analýzy dat pro model hrozeb SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 Data Analytics ovládací prvek implementace matice](https://aka.ms/nist171-analytics-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky se tak vyřeší, podle architektury analytics data. Obsahuje podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.


## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurován pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektuře data analytics. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s využitím Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síť Azure. Toto připojení se provádí přes Internet. Zákazníci mohou používat připojení k bezpečně "tunelového propojení" informací uvnitř šifrované propojení svých sítí a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Vzhledem k tomu, že provoz v rámci tunelového připojení sítě VPN prochází přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí další ještě větší zabezpečení možností připojení. ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Připojení ExpressRoute připojují přímo k poskytovatele telekomunikačních zákazníka. Data v důsledku toho nebude procházet přes Internet a není vystavený. Tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení. 

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Proces extrakce, transformace a načítání
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) můžete načíst data do služby SQL Database bez potřeby samostatné extract-transform-load nebo importovat nástroj. PolyBase umožňuje přístup k datům prostřednictvím dotazů T-SQL. Microsoft business intelligence a analýza zásobníku a nástroje třetích stran, které jsou kompatibilní s SQL serverem je možné díky technologii PolyBase.

### <a name="azure-ad-setup"></a>Azure AD – nastavení
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, kteří pracují s prostředím. Místní služby Active Directory je možné integrovat s Azure AD [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také můžete navázat nasazenou infrastrukturu služby Active Directory (řadiče domény) do služby Azure AD. K tomuto účelu zkontrolujte nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby Azure AD.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
