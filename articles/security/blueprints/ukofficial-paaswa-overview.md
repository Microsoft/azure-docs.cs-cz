---
title: Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci hostování pro oficiální úlohy Spojené království
description: Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci hostování pro oficiální úlohy Spojené království
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 6fe85d7ac527179ab39e89739f5744f3aa1ef8e2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297551"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Zabezpečení Azure a dodržování předpisů: PaaS webového hostování aplikací pro oficiální úlohy Spojené království

## <a name="azure-security-and-compliance-blueprints"></a>Plány zabezpečení a dodržování předpisů v Azure

Plány Azure se skládají z dokumentů s pokyny k a automatizace šablony, které nasazení cloudové architektury, které nabízejí řešení pro scénáře, které mají akreditace požadavky. Plány Azure jsou pokyny a automatizace kolekce šablony, které umožňují zákazníkům Microsoft Azure zrychlí doručování firemních cílů prostřednictvím zřizování foundation architektura, která je možné rozšířit na žádné další požadavky.

## <a name="overview"></a>Přehled

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny a automatizace skripty poskytovat Microsoft Azure [platforma jako služba (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) hostované architektura pro webové aplikace vhodné pro úlohy, které jsou klasifikovány jako [UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Tato klasifikace zabezpečení zahrnuje většinu informací o vytvořit nebo zpracovat veřejného sektoru. Jedná se o běžné obchodní operace a služby, pokud ke ztrátě, odcizení nebo publikovat na médiu, z nichž některé by mohla mít škodlivé následky. Profil typické před internetovými útoky pro oficiální klasifikaci je skoro stejné jako privátní obchodní, který poskytuje cenné informace a služby. UK-OFFICIAL připraven, potřeba chránit data vlády Spojeného království nebo služby využívající hrozby nebo ohrožení zabezpečení, kterou útočníci se jako omezená funkce a prostředky (ale není omezena pouze na) hactivists, single-issue tlak skupiny vyšetřování novináře příslušné jednotlivé hackery a většina trestního uživatelům a skupinám.

Tento podrobný plán zkontroloval podle Spojené království národní Kybernetických zabezpečení centrum (NCSC) a odpovídá principů zabezpečení cloudu 14 NCSC.

Tato architektura využívá Azure [platforma jako služba](https://azure.microsoft.com/overview/what-is-paas/) součásti poskytovat prostředí, které umožňuje zákazníkům řešit náklady a složitost nákupu softwarových licencí, podpůrné aplikační infrastruktury správy a middlewaru nebo nástrojů pro vývoj a další prostředky. Zákazníci spravovat aplikace a služby, které vytvářejí, zaměřuje se na dodávání přidané hodnoty, zatímco je Microsoft Azure spravuje další prostředky Azure jako jsou virtuální počítače, úložiště a sítě, uvedení více [dělení odpovědnosti](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) pro správu infrastruktury do platformy Azure. [Služba Azure App Services](https://azure.microsoft.com/services/app-service/) nabízí automatické škálování, vysokou dostupnost, podporuje Windows a Linux a umožňuje automatizované nasazení z Githubu, službě Azure DevOps nebo v libovolném adresáři Gitu jako výchozí služby. Pomocí App Services, umožňuje vývojářům soustředit se na dodávání přidané hodnoty bez režie na správu infrastruktury. Je možné k vytvoření nové webové aplikace Java, PHP, Node.js, Python, HTML nebo C# úplně nové, nebo také k migraci existujícího cloudu nebo na místní webové aplikace do služby Azure App Services (i když je důkladné kvůli opatrnost a testování pro potvrzení, výkon se vyžaduje).

Tento podrobný plán se zaměřuje na zřizování bezpečný základ [platforma jako služba](https://azure.microsoft.com/overview/what-is-paas/) webové rozhraní pro uživatele public a také back office. Scénáře návrhu podrobného plánu bere v úvahu, že používání Azure hostované webové služby, kde veřejné uživatelů může bezpečně posílat, zobrazit a spravovat citlivých dat; také, že operátor back office nebo státní správy můžete bezpečně zpracovávat citlivá data, která má veřejný uživatel odeslal. Případy použití pro tento scénář může obsahovat:

- Uživatel odesílá vrácení daně, operátorem government zpracování příspěvkem;
- Uživatel žádající o službu prostřednictvím webovou aplikaci s uživatelem back office ověřování a poskytování služeb; nebo
- Hledání uživatele a zobrazení veřejné domény pomáhají informace týkající se služby státní správy.

Pomocí [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) šablony a skripty rozhraní příkazového řádku Azure, podrobný plán nasadí prostředí, které odpovídá na Spojené království národní Kybernetických zabezpečení centrum (NCSC) 14 [principů zabezpečení cloudu](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) a centru pro zabezpečení Internetu (CIS) [kontrolních mechanismů pro zabezpečení důležitých](https://www.cisecurity.org/critical-controls.cfm). NCSC doporučuje používat jejich principů zabezpečení cloudu zákazníky k vyhodnocení vlastností zabezpečení služby a pro lepší porozumění tomu dělení odpovědnost mezi zákazníkem a dodavateli. Microsoft poskytuje informace pro každý z těchto zásad k lepšímu pochopení dělení zodpovědnosti. Tato architektura a odpovídající šablon Azure Resource Manageru podporují Microsoft – dokument White Paper, [14 ovládací prvky cloudové zabezpečení pro Spojené království cloudu pomocí Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Tato architektura zkontroloval NCSC a v souladu s Spojené království NCSC 14 principů zabezpečení cloudu, což umožní organizací ve veřejném sektoru pro jejich schopnosti plnit povinnosti dodržování předpisů pomocí cloudové služby na globálně a ve Spojeném království rychlého Cloud Microsoft Azure. Tato šablona nasadí infrastrukturu pro pracovní vytížení. Kód aplikace a podpůrných obchodní vrstvy a datové vrstvy softwaru musí být nainstalované a nakonfigurované zákazníky. Podrobné pokyny jsou k dispozici [tady](https://aka.ms/ukofficial-paaswa-repo/).

Tento plán je architektura základu. Naši zákazníci můžete použít tento podrobný plán jako základ pro jejich oficiální klasifikace webové úlohy a rozšiřují šablony a prostředky s dodržováním požadavků. Tento podrobný plán založený na zásadách [podrobný plán UK-úřední třívrstvé IaaS webové aplikace](https://aka.ms/ukofficial-iaaswa) nabídnout našim zákazníkům i [infrastruktura jako služba (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) a možnosti implementace PaaS pro hostování webových úloh.

Chcete-li nasadit Tento podrobný plán, je potřeba předplatné Azure. Pokud nemáte předplatné Azure, můžete se zaregistrovat rychle a snadno bez poplatků: Začínáme s Azure. Klikněte na tlačítko [tady](https://aka.ms/ukofficial-paaswa-repo/) pokyny pro nasazení.

## <a name="architecture-and-components"></a>Architektura a komponenty

Tento podrobný plán poskytuje řešení v prostředí cloudu Azure, které podporuje UK-OFFICIAL úlohy hostování webové aplikace. Tato architektura přináší zabezpečeném prostředí, které využívá platformu Azure jako schopnosti služeb. V rámci prostředí dvě webové aplikace služby App Service jsou nasazené (jeden pro veřejnost) a jednu pro back office, s vrstvou aplikaci API k poskytování služeb obchodních pro webového front-endu. Azure SQL Database se nasadí jako spravované relační datové úložiště pro aplikaci. Připojení těchto komponent z mimo platformu a mezi všechny tyto součásti se šifrují pomocí protokolu TLS 1.2, aby data v přenosu ochrany osobních údajů, s přístupem k ověření službou Azure Active Directory.

![PaaS hostování aplikace webu pro Spojené království oficiální úlohy diagram referenční architektury](images/ukofficial-paaswa-architecture.png?raw=true "PaaS hostování aplikace webu pro Spojené království oficiální úlohy diagram referenční architektury")

Jako součást nasazení architektury, poskytování zabezpečeného úložiště, monitorování & protokolování, jednotnou správu zabezpečení a pokročilou ochranu před hrozbami, a řízení se také nasazují možnosti k zajištění, že zákazníci mají všechny nástroje potřebné k zabezpečení a sledovat jejich prostředí pro toto řešení.

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Azure Active Directory
- Managed Service Identity
- App Service
- Webová aplikace
- Aplikace API
- Azure DNS
- Key Vault
- Azure Monitor
- Application Insights
- Log Analytics
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje nasazení a implementaci prvky.

### <a name="security"></a>Zabezpečení

#### <a name="identity-and-authentication"></a>Identita a ověřování

Tento podrobný plán zajistí, že je ochrana přístupu k prostředkům prostřednictvím adresáři a identitami služby správy. Tato architektura umožňuje plně využívat [identity jako bezpečnostní hraniční sítě](https://docs.microsoft.com/azure/security/security-paas-deployments#identity-as-the-primary-security-perimeter). 

Tyto technologie nabízejí identity možnosti správy v prostředí Azure:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všichni uživatelé pro řešení vytvořené v Azure Active Directory, včetně uživatele, kteří používají SQL Database.
- Používání služby Azure AD se provádí ověřování pro operátor směřující webové aplikace a přístup pro správu prostředků Azure. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Šifrování sloupců databáze používá Azure AD k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu [s funkcí Always Encrypted: Ochrana citlivých dat ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Občany, kterým čelí webové aplikace je nakonfigurovaný pro veřejný přístup. K povolení pro vytváření účtů a ověřování pomocí služby active directory nebo sociální sítě zprostředkovatelé identity [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) je možné integrovat se v případě potřeby.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení a rizikových účty poskytuje doporučení k vylepšení zabezpečení stav identity ve vaší organizaci, nakonfiguruje automatické odpovědi, aby rozpoznal podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty a provede příslušnou akci k jejich řešení.
- [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji zaměřené access management pro službu Azure. Přístup k předplatnému je omezená na správce předplatného a služby Azure Key Vault přístup je omezen pouze pro uživatele, kteří potřebují přístup pro správu klíčů.
- Prostřednictvím využití [Azure Active Directory podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) zákazníci můžou vynutit další zabezpečovací mechanizmy u přístupu do aplikací nebo uživatelům ve svém prostředí na základě určitých podmínek, jako je například umístění, zařízení, stavu a přihlašování riziko.
- [Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) kombinovat s osvědčené postupy pro navrhování aplikací, poskytuje ochranu před útoky DDoS, pomocí monitorování neustále v provozu a v reálném čase ke zmírnění běžných útoků na úrovni sítě. Pomocí architektury PaaS platforma úroveň služby DDoS protection je transparentní pro zákazníka a vestavěné do platformy, ale je důležité si uvědomit, že je na starost návrh zabezpečení aplikace u zákazníka.

#### <a name="data-in-transit"></a>Přenášená data

Data jsou přenosu z mimo a mezi komponentami Azure je chráněný pomocí [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), který používá k šifrování komunikace, jakmile symetrické šifrování založené na sdílený tajný klíč Při přenosu přes síť. Ve výchozím nastavení síťový provoz zabezpečené pomocí protokolu TLS 1.2.

#### <a name="security-and-malware-protection"></a>Zabezpečení a malware ochrany

[Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizovaný pohled stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit, že kontrolních mechanismů zabezpečení jsou na místě a správně nakonfigurovaný, a můžete rychle identifikovat všechny prostředky, které vyžadují pozornost.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) je individuální cloudový konzultant, který pomáhá dodržovat osvědčené postupy a optimalizovat nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) je funkce ochrany v reálném čase, který pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. To ve výchozím nastavení je nainstalovaná na základní infrastrukturu modelu PaaS virtuálních počítačů a spravuje prostředky infrastruktury Azure transparentně s konkrétním zákazníkem, zajištění

### <a name="paas-services-in-this-blueprint"></a>Služby PaaS v tomto podrobném plánu

#### <a name="azure-app-service"></a>Azure App Service

Azure Web Apps poskytuje plně spravovaná webová hostitelské prostředí pro webové aplikace vyvinuté v jazyce Java, PHP, Node.js, Python, HTML a C# bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) nebo libovolného úložiště gitu.

App Service je [ISO, SOC a PCI](https://www.microsoft.com/TrustCenter/) a můžou k ověření uživatelů pomocí [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) nebo přihlášení prostřednictvím sociální sítě ([Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication), [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication), [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication), a [ověřování Microsoft](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication).

Basic, Standard a plány Premium jsou určené pro produkční úlohy a běží na vyhrazených instancích virtuálních počítačů. Každá instance může podporovat více aplikací a domén. Aplikace služby také podporu [omezení podle IP adresy](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) zabezpečit provoz na důvěryhodné IP adresy, pokud je to nutné a také [identita spravované služby](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) pro zabezpečené připojení k jiným službám PaaS, jako je například [ Trezor klíčů](https://azure.microsoft.com/services/key-vault/) a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Je-li zvýšit zabezpečení vyžadováním náš plán Isolated hostuje aplikace v privátním, vyhrazeném prostředí Azure a je ideální pro aplikace, které vyžadují zabezpečená připojení k vaší místní síti nebo dodatečný výkon a škálování.

Tato šablona nasadí následující funkce služby App Service:

- [Standardní](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) úroveň plánu služby App Service
- Více webové aplikace [sloty nasazení](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing): vývoj, ve verzi Preview, dotazů a odpovědí, UAT a samozřejmě produkčního prostředí (výchozí slot).
- [Identita spravované služby](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) pro připojení k [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (to může také použít k poskytnutí přístupu k [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integrace s [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) ke sledování výkonu
- [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- Metrika [výstrahy](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database je spravovaná služba relačních databází pro obecné účely v Microsoft Azure, která podporuje struktury, jako jsou relační data, JSON, prostorová data a XML. SQL Database nabízí spravované izolované databáze SQL, spravované databáze SQL v [elastického fondu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)a SQL [spravovaných instancí](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (ve verzi public preview). Poskytuje [dynamicky škálovatelný výkon])https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) a nabízí možnosti jako [indexy columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) pro extrémní analytické analýzy a generování sestav a [OLTP v paměti](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) pro extreme zpracování transakcí. Microsoft zajišťuje bezproblémové opravy a aktualizace základního kódu SQL a odděluje veškerou správu základní infrastruktury.

Azure SQL Database v tomto podrobném plánu

Instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Pravidla brány firewall na úrovni serveru a databáze](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), nebo prostřednictvím [koncové body služeb virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pomocí [pravidel virtuální sítě](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) pomáhá chránit před hrozbou škodlivých činností Azure SQL Database a Azure Data Warehouse. Provede v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu bez nutnosti změny aplikace.
- [Ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), můžete centrálně spravovat identity uživatelů databáze a další služby Microsoftu v jednom centrálním místě. Centrální správa ID zajišťuje centrální místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.
- Použití služby Azure Active Directory pro správu databáze
- [Protokoly auditu](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) do účtů úložiště
- Metrika [výstrahy](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) pro selhání připojení databáze
- [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [služby Azure Storage](https://azure.microsoft.com/services/storage/) je spravovaná Cloudová služba, která poskytuje vysoce dostupné, zabezpečené, odolné, škálovatelné a redundantní úložiště. Azure Storage se skládá ze služeb Blob Storage, File Storage a Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Služba Azure Storage v tento podrobný plán

Tato šablona používá následující součásti služby Azure Storage:

- [Šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Povolit jenom připojení prostřednictvím protokolu HTTPS

#### <a name="data-at-rest"></a>Neaktivní uložená data

Prostřednictvím [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) všechna data zapsat do služby Azure Storage se šifrují pomocí 256bitového šifrování AES, jeden nejsilnějších dostupných variant blokového šifrování. Spravovaná Microsoftem šifrovací klíče můžete použít s SSE nebo můžete použít [šifrovacích klíčů](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Účty úložiště může být zabezpečené pomocí [koncové body služeb virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pomocí [pravidel virtuální sítě](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Podrobné informace o zabezpečení služby Azure Storage najdete v [Průvodci zabezpečením](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Správa tajných kódů

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) slouží k zabezpečení aplikace klíče a tajné klíče k zajištění, že nejsou přístupné třetími stranami. Služba Key Vault není určená k použití jako úložiště uživatelských hesel. Umožňuje vytvořit několik zabezpečených kontejnerů označovaných jako trezory. Tyto trezory využívají moduly hardwarového zabezpečení (HSM). Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů.

#### <a name="azure-key-vault-in-this-blueprint"></a>Služba Azure Key Vault v tomto podrobném plánu

- Obsahuje přístupový klíč úložiště s přístupem pro čtení udělit [identita spravované služby](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) webové aplikace přístupné zákazníka
- Obsahuje heslo správce databáze serveru SQL (v samostatných trezoru)
- Protokolování diagnostiky

### <a name="monitoring-logging-and-audit"></a>Monitorování, protokolování a auditování

#### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) je služba v Azure, která pomáhá shromažďovat a analyzovat data vygenerovaná prostředky ve vašem cloudovém a místním prostředí.

#### <a name="log-analytics-in-this-blueprint"></a>Log Analytics v tomto podrobném plánu

- Posouzení SQL
- Diagnostika služby Key Vault
- Připojení služby Application Insights
- Protokol aktivit Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba správu výkonu aplikací (APM) pro webové vývojáře ve více platformách. Používá k monitorování živé webové aplikace jej bude automaticky zjišťuje anomálie ve výkonu, analýza výkonu, diagnostikovat problémy a pochopit, jak uživatelé pracují s aplikací. Application Insights se dá nasadit na platformách, včetně .NET, Node.js a J2EE, hostovaný místně nebo v cloudu. Integruje se s vaším procesem DevOps a obsahuje body připojení k celé řadě dalších vývojářských nástrojů.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights v tomto podrobném plánu

Tato šablona používá následující komponenty Application Insights:

- Řídicí panel Application Insights na web (operátor, zákazníků a rozhraní API)

#### <a name="azure-activity-logs"></a>Protokoly aktivit v Azure

[Protokol aktivit v Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) auditování událostí rovina řízení pro vaše předplatná. Použití protokolu aktivit, můžete určit "co, kdo a kdy" veškerých operací (PUT, POST, DELETE) provedených ve prostředků ve vašem předplatném zápisu. Můžete také zjištění stavu operace a další relevantní vlastnosti.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) umožňuje základní monitorování pro služby Azure tím, že kolekce metriky, protokoly aktivit a diagnostické protokoly. Azure Monitor poskytuje metriky a protokoly základní infrastruktury pro většinu služeb v Microsoft Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/ukofficial-paaswa-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![PaaS hostování aplikací Web pro model hrozeb oficiální úlohy Spojené království](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS hostování aplikací Web pro model hrozeb oficiální úlohy Spojené království")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentace ke službě principů zabezpečení cloudu NCSC dodržování předpisů

Komerční služby koruna (subjekt, který lze použít ke zlepšení obchodních a zajišťování aktivity vláda) obnovit klasifikace cloudovým službám Microsoftu enterprise v oboru pro v6 G-Cloud, pokrývá všechny nabídky na úrovni OFFICIAL. Podrobnosti o službě Azure a G-Cloud najdete v [souhrn vyhodnocení zabezpečení Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Tento podrobný plán zarovná zásadám 14 cloudové zabezpečení, které jsou popsané v NCSC [principů zabezpečení cloudu](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) k zajištění prostředí, které podporuje úlohy, které jsou klasifikovány jako UK-OFFICIAL.

[Azure zabezpečení a dodržování předpisů – UK oficiální zákazníka odpovědnost matice](https://aka.ms/ukofficial-crm) (Excelový sešit) obsahuje seznam všech principů zabezpečení cloudu 14 a označuje, pro každou zásadu (nebo její hlavní komponenty), ať už se zásadou implementace zodpovídá za Microsoft, Zákazník, nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – PaaS webové aplikace pro UK oficiální Princip implementace matice](https://aka.ms/ukofficial-paaswa-pim) (Excelový sešit) obsahuje seznam všech principů zabezpečení cloudu 14 a označuje, pro každou zásadu (nebo její hlavní komponenty) odpovědnosti zákazníka v matici zodpovědnosti zákazníků, který je určen 1) Pokud je podrobný plán implementuje princip a (2) popis, jak implementace v souladu s principem požadavků:.  

Kromě toho Cloud Security Alliance (CSA) publikované ovládací prvek matice Cloud podporu pro zákazníky v pořadí vyhodnocování poskytovatelů cloudových služeb a identifikovat dotazy, které by měl být zodpovědět, než přechod ke cloudovým službám. V odpovědi, Microsoft Azure odpovědi iniciativy dotazník pro posouzení CSA Caiq ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), která popisuje, jak Microsoft přistupuje k navrhované zásady.

## <a name="third-party-assessment"></a>Posouzení třetích stran

Tento podrobný plán zkontroloval podle Spojené království národní Kybernetických zabezpečení centrum (NCSC) a odpovídá principů zabezpečení cloudu 14 NCSC

Šablony služby automation bylo otestováno týmem Velká Británie zákazníka úspěch jednotka Azure Cloudový architekt řešení a náš partner společnosti Microsoft [Ampliphae](http://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Nasazení řešení

Tento zabezpečení Azure a dodržování předpisů podrobného plánu Automation se skládá z JSON konfigurační soubory a skripty prostředí PowerShell, které jsou zpracovávány službou rozhraní API Azure Resource Manageru k nasazení prostředků v rámci Azure. Podrobné pokyny jsou k dispozici [tady](https://aka.ms/ukofficial-paaswa-repo).

Tři přístupy k dispozici pro nasazení; Jednoduchý "express" [2 rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vhodný rychle vytvářet testovací prostředí; parametrizované [2 rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) přístup poskytuje větší konfigurace prostředí úloh; a webu Azure portal na základě nasazení, kde operátor, který můžete zadat parametry nasazení na webu Azure portal. 

1.  Klonovat nebo stáhnout [to](https://aka.ms/ukofficial-paaswa-repo) úložišti GitHub na váš místní pracovní stanici.
2.  Kontrola [metoda 1: 2 rozhraní příkazového řádku Azure (verze Express)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) a zadané příkazy.
3.  Kontrola [metoda 1a: 2 rozhraní příkazového řádku Azure (konfigurace nasazení přes argumenty skriptu)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) a zadané příkazy
4.  Kontrola [metoda 2: proces nasazování portálu Azure](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) a spouštět uvedené příkazy

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) může před služby API App Service poskytují další vrstvy zabezpečení, omezení a ovládací prvky pro vystavení proxy a ochrana rozhraní API.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) mohou být implementovány jako ovládací prvek umožňuje uživatelům registrovat, vytvoříte identitu a povolení autorizace a řízení pro veřejné webové aplikace přístupu.

## <a name="disclaimer"></a>Právní omezení

- Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
- Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
- Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
- Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
