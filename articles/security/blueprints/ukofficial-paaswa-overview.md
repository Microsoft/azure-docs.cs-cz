---
title: Podrobný plán zabezpečení a dodržování předpisů Azure hostování webových aplikací PaaS pro Velké Británie a oficiální úlohy
description: Podrobný plán zabezpečení a dodržování předpisů Azure hostování webových aplikací PaaS pro Velké Británie a oficiální úlohy
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 1f6eeea85a348bb8e88a387fa0fc6bed55e41a5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262773"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: PaaS hostování webových aplikací pro Velkou Británii pro oficiální úlohy

## <a name="azure-security-and-compliance-blueprints"></a>Plány zabezpečení a dodržování předpisů v Azure

Plány Azure se skládají z doprovodnéch dokumentů a šablon automatizace, které nasazují cloudové architektury, a nabízejí řešení scénářů, které mají požadavky na akreditaci nebo dodržování předpisů. Plány Azure jsou doprovodné materiály a kolekce šablon automatizace, které umožňují Microsoft Azure zákazníkům zrychlit doručování svých obchodních cílů prostřednictvím zřízení základní architektury, která se dá rozšířit tak, aby splňovala jakékoli další požadavky.

## <a name="overview"></a>Přehled

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny a skripty pro automatizaci pro poskytování architektury webové aplikace Microsoft Azure [platformy jako služby (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) , která je vhodná pro zpracování úloh klasifikovaných jako oficiální v tajnosti. [ ](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Tato klasifikace zabezpečení zahrnuje většinu informací vytvořených nebo zpracovávaných veřejným sektorem. To zahrnuje běžné provozní operace a služby, které jsou v případě ztráty, krádeže nebo publikování na médiu, z nichž některé by mohly mít škodlivé důsledky. Typický profil hrozeb pro oficiální klasifikaci je stejný jako soukromý podnik, který poskytuje cenné informace a služby. OFICIÁLNÍ organizace ve Spojeném království předpokládá nutnost chránit data a služby státní správy proti hrozbám a napadení útočníky s využitím ohraničených schopností a prostředků, jako je (ale nejedná se o omezení na), hactivists tlakové skupiny, vyšetřovací novináři, příslušné individuální hackeři a většina trestných jednotlivců a skupin.

Tento podrobný plán byl přezkoumán národním internetovým centrem zabezpečení (NCSC) a je v souladu se zásadami cloudového zabezpečení NCSC 14.

Architektura používá platformu Azure [jako součásti služby](https://azure.microsoft.com/overview/what-is-paas/) k dodávání prostředí, které zákazníkům umožňuje vyhnout se nákladům a složitosti nákupů softwarových licencí a spravovat základní aplikační infrastrukturu a middleware. vývojové nástroje a další prostředky. Zákazníci spravují aplikace a služby, které vyvíjí, zaměřené na poskytování obchodních hodnot, zatímco Microsoft Azure spravují další prostředky Azure, jako jsou virtuální počítače, úložiště a sítě, a přináší tak další část [ zodpovědnost](../fundamentals/paas-deployments.md) za správu infrastruktury na platformě Azure. [Azure App Services](https://azure.microsoft.com/services/app-service/) nabízí automatické škálování, vysokou dostupnost, podporuje Windows a Linux a umožňuje automatizované nasazení z GitHubu, Azure DevOps nebo libovolného úložiště Git jako výchozí služby. Pomocí App Services se můžou vývojáři soustředit na poskytování obchodních hodnot bez režie správy infrastruktury. Je možné vytvářet bezserverová nové jazyky Java, PHP, Node. js, Python, HTML nebo C# webové aplikace nebo taky migrovat existující cloudové nebo místní webové aplikace do Azure App Services (i když důkladná opatrnost a testování pro potvrzení výkonu). požadováno).

Tento podrobný plán se zaměřuje na zřizování zabezpečené základní [platformy jako](https://azure.microsoft.com/overview/what-is-paas/) webového rozhraní pro veřejné a také uživatele Back-Office. Tento scénář návrhu podrobného plánu bere v úvahu použití webových služeb hostovaných v Azure, kde může veřejný uživatel bezpečně odesílat, zobrazovat a spravovat citlivá data. také, že záložní úřad nebo pracovník pro státní správu může bezpečně zpracovat citlivá data, která odeslal veřejný uživatel. Případy použití pro tento scénář můžou zahrnovat:

- Uživatel, který odesílá vrácení daně, s operátorem vlády, který zpracovává odeslání;
- Uživatel požadující službu prostřednictvím webové aplikace s koncovým uživatelem, který prochází a doručuje službu; ani
- Uživatel, který hledá a zobrazuje informace o veřejné doméně týkající se služby pro státní správu.

Při použití šablon [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a skriptů rozhraní příkazového řádku Azure nasadí podrobný plán prostředí, které se bude zavádět do národního centra zabezpečení internetového centra (NCSC) 14 v oblasti [cloudového zabezpečení](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) a do středu pro Internet. Zabezpečení (CIS) [kritické ovládací prvky zabezpečení](https://www.cisecurity.org/critical-controls.cfm). NCSC doporučuje svým zákazníkům používat zásady zabezpečení cloudu k vyhodnocení vlastností zabezpečení služby a k pochopení rozdělení zodpovědnosti mezi zákazníkem a dodavatelem. Společnost Microsoft poskytuje informace pro každý z těchto principů, aby lépe porozuměly rozdělení odpovědností. Tato architektura a odpovídající šablony Azure Resource Manager podporují Microsoft White Paper, [14 kontroly zabezpečení cloudu pro Velká Británie v cloudu pomocí Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Tato architektura byla přezkoumána NCSC a je v souladu se zásadami cloudového zabezpečení UK NCSC 14, což umožňuje organizacím veřejného sektoru rychle sledovat jejich schopnost plnit povinnosti dodržování předpisů pomocí cloudových služeb globálně a na Velká Británie na Microsoft Azure Cloud. Tato šablona nasadí infrastrukturu pro úlohu. Kód aplikace a podpora softwaru obchodní vrstvy a datové vrstvy musí nainstalovat a nakonfigurovat zákazníci. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/ukofficial-paaswa-repo/).

Tento plán je základem základní architektury. Naši zákazníci mohou tento podrobný plán využít jako základ pro své oficiální webové úlohy klasifikace a rozšiřovat šablony a prostředky s jejich vlastními požadavky. Tento podrobný plán se sestaví na základě zásad [OFFICALch webových aplikací IaaS pro Velké Británie](https://aka.ms/ukofficial-iaaswa) , které nabízí našim zákazníkům možnosti implementace [infrastruktury jako služby (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) a PaaS pro hostování webových úloh.

K nasazení tohoto podrobného plánu se vyžaduje předplatné Azure. Pokud předplatné Azure nemáte, můžete se rychle zaregistrovat a snadno se zdarma: Začněte s Azure. Pokyny k nasazení získáte kliknutím [sem](https://aka.ms/ukofficial-paaswa-repo/) .

## <a name="architecture-and-components"></a>Architektura a součásti

Tento podrobný plán zajišťuje řešení hostování webové aplikace v cloudovém prostředí Azure, které podporuje oficiální úlohy Spojené království. Architektura zajišťuje zabezpečené prostředí, které využívá platformu Azure jako možnosti služby. V rámci prostředí se nasazují dvě App Service webové aplikace (jeden pro veřejné uživatele a jeden pro uživatele s Back-Office) s aplikační vrstvou API pro poskytování podnikových služeb pro webový front-end. Azure SQL Database je nasazen jako spravované relační úložiště dat pro aplikaci. Připojení k těmto součástem mimo platformu a mezi všemi těmito součástmi je pomocí protokolu TLS 1,2 šifrováno, aby se zajistila ochrana osobních údajů při přenosu s přístupem ověřeným pomocí Azure Active Directory.

![Referenční diagram architektury webové aplikace PaaS pro Velkou Británii pro úlohy](images/ukofficial-paaswa-architecture.png?raw=true "Referenční diagram architektury webové aplikace PaaS pro Velkou Británii pro úlohy")

V rámci architektury nasazení, zabezpečení zřizování úložiště, monitorování & protokolování, sjednocené řízení zabezpečení & rozšířené ochrany před internetovými útoky a také nasazení možností správy, aby se zajistilo, že zákazníci budou mít k dispozici všechny nástroje vyžadované pro Zabezpečte a Monitorujte své prostředí pro toto řešení.

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

- Azure Active Directory
- App Service
- Webová aplikace
- Aplikace API
- Azure DNS
- Key Vault
- Azure Monitor (protokoly)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje prvky nasazení a implementace.

### <a name="security"></a>Zabezpečení

#### <a name="identity-and-authentication"></a>Identita a ověřování

Tento podrobný plán zajišťuje, aby byl přístup k prostředkům chráněný prostřednictvím služby Directory and Identity Management Services. Tato architektura umožňuje plné využití [identity jako hraničního obvodu](../fundamentals/paas-deployments.md). 

Následující technologie poskytují možnosti správy identit v prostředí Azure:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je víceklientské cloudové služby Microsoftu a služba pro správu identit založené na víceklientské architektuře. Všichni uživatelé pro toto řešení byli vytvořeni v Azure Active Directory, včetně uživatelů, kteří přistupují k SQL Database.
- Ověřování pro webovou aplikaci s přístupem k operátoru a přístup pro správu prostředků Azure se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Šifrování sloupce databáze používá Azure AD k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu [Always Encrypted: Chraňte citlivá data v](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)SQL Database.
- Webová aplikace pro občany je nakonfigurována pro veřejný přístup. Aby bylo umožněno vytváření a ověřování účtů prostřednictvím služby Active Directory nebo zprostředkovatele identity sociální sítě [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) možné v případě potřeby integrovat.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální slabá místa a rizikové účty a poskytuje doporučení k vylepšení stav zabezpečení identit vaší organizace. konfiguruje automatizované odpovědi na zjištěné podezřelé. akce související s identitami vaší organizace a prošetří podezřelé incidenty a provádí vhodná opatření k jejich vyřešení.
- Správa [Access Control na základě rolí v Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesné řízení přístupu pro Azure. Přístup k předplatnému je omezený na správce předplatného a přístup Azure Key Vault je omezený jenom na uživatele, kteří vyžadují přístup ke správě klíčů.
- Díky využití [Azure Active Directory zákazníci podmíněného přístupu](../../active-directory/active-directory-conditional-access-azure-portal.md) můžou vyhovět dalším kontrolním mechanismům zabezpečení při přístupu k aplikacím nebo uživatelům ve svém prostředí na základě konkrétních podmínek, jako je umístění, zařízení, stav a riziko při přihlašování.
- [Azure DDoS Protection](../fundamentals/paas-deployments.md#security-advantages-of-a-paas-cloud-service-model) v kombinaci s osvědčenými postupy pro návrh aplikace zajišťuje ochranu před útoky DDoS a nepřetržitým monitorováním provozu a zmírnění běžných útoků na úrovni sítě v reálném čase. S architekturou PaaS je ochrana DDoS na úrovni platformy pro zákazníka transparentní a integrovaná do platformy, ale je důležité si uvědomit, že odpovědnost za návrh zabezpečení aplikací je u zákazníka.

#### <a name="data-in-transit"></a>Přenášená data

Data se předávají z vnějšku a mezi komponentami Azure je chráněná pomocí [protokolu TLS/SSL (Transport Layer Security/SSL (Secure Sockets Layer))](https://www.microsoft.com/TrustCenter/Security/Encryption), který využívá symetrickou kryptografii založenou na sdíleném tajném klíči k šifrování komunikace při cestách přes síť. Ve výchozím nastavení se síťový provoz zabezpečuje pomocí TLS 1,2.

#### <a name="security-and-malware-protection"></a>Zabezpečení a ochrana proti malwaru

[Azure Security Center](https://azure.microsoft.com/services/security-center/) poskytuje centralizované zobrazení stavu zabezpečení všech vašich prostředků Azure. Na první pohled můžete ověřit, jestli jsou správně nastavené správné kontrolní mechanismy zabezpečení, a můžete rychle identifikovat všechny prostředky, které vyžadují pozornost.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) je přizpůsobený cloudový poradce, který pomáhá dodržovat osvědčené postupy pro optimalizaci nasazení Azure. Analyzuje konfiguraci vašich prostředků a telemetrii jejich využívání a následně doporučí řešení, která zlepší finanční úspornost, výkon, dostupnost a zabezpečení vašich prostředků Azure.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) je funkce ochrany v reálném čase, která pomáhá identifikovat a odebírat viry, spyware a další škodlivý software. Ve výchozím nastavení se tato služba nainstaluje na podkladovou infrastrukturu virtuálních počítačů s PaaS a je spravovaná prostředky infrastruktury Azure transparentně pro zákazníka.

### <a name="paas-services-in-this-blueprint"></a>PaaS služby v tomto podrobném plánu

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service poskytuje plně spravované prostředí pro hostování webů pro webovou aplikaci vyvinuté v jazycích Java, PHP, Node. js Python, C# HTML a bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje Windows i Linux a umožňuje automatizované nasazení z [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) nebo jakéhokoli úložiště založeného na Gitu.

App Service jsou [kompatibilní s normami ISO, SOC a PCI](https://www.microsoft.com/TrustCenter/) a můžou ověřovat uživatele pomocí [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) nebo s přihlášením přes sociální sítě ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)a [Microsoft Authentication](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)).

Plány Basic, Standard a Premium jsou určené pro produkční úlohy a běží na vyhrazených instancích virtuálních počítačů. Každá instance může podporovat více aplikací a domén. Služba App Services také podporuje [omezení IP adres](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) pro zabezpečení provozu na důvěryhodné IP adresy v případě potřeby a také [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) pro zabezpečené připojení k jiným službám PaaS, jako je například [Key Vault](https://azure.microsoft.com/services/key-vault/) a [Azure SQL. Databáze](https://azure.microsoft.com/services/sql-database/). Pokud je potřeba další zabezpečení, náš izolovaný plán hostuje vaše aplikace v privátním a vyhrazeném prostředí Azure a je ideální pro aplikace, které vyžadují zabezpečená připojení k vaší místní síti, nebo další výkon a škálování.

Tato šablona nasadí následující funkce App Service:

- [Standardní](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) App Service – úroveň plánu
- Více App Servicech [slotů nasazení](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Vývoj, verze Preview, QA, UAT a těžba kurzů (výchozí slot).
- [Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) , které se připojují k [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (můžete také použít k poskytnutí přístupu k [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Integrace s [Azure Application Insights](../../azure-monitor/app/azure-web-apps.md) pro monitorování výkonu
- [Diagnostické protokoly](../../azure-monitor/platform/resource-logs-overview.md) 
- [Výstrahy](../../azure-monitor/app/alerts.md) metriky 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database je spravovaná služba relačních databází pro obecné účely v Microsoft Azure, která podporuje struktury, jako jsou relační data, JSON, prostorová data a XML. SQL Database nabízí spravované samostatné databáze SQL, spravované databáze SQL v [elastickém fondu](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)a [spravované instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) SQL (ve verzi Public Preview). Zajišťuje [dynamicky škálovatelný výkon](../../sql-database/sql-database-purchase-models.md) a nabízí možnosti jako [indexy columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) pro extrémní analytické analýzy a generování sestav nebo [OLTP v paměti](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) pro extrémní zpracování transakcí. Microsoft zajišťuje bezproblémové opravy a aktualizace základního kódu SQL a odděluje veškerou správu základní infrastruktury.

Azure SQL Database v tomto podrobném plánu

Instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Pravidla brány firewall na úrovni serveru a databáze](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)nebo prostřednictvím [koncových bodů služby Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pomocí [pravidel virtuální sítě](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) pomáhá chránit Azure SQL Database a Azure Data Warehouse před hrozbou škodlivých aktivit. Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase bez nutnosti změny aplikace.
- [Ověřování Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění. Centrální správa ID poskytuje jediné místo pro správu uživatelů databáze a zjednodušuje správu oprávnění.
- Použití Azure Active Directory pro správu databáze
- [Protokoly auditu](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) do účtů úložiště
- [Výstrahy](../../azure-monitor/app/alerts.md) metrik pro neúspěšná připojení DB
- [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) je cloudová služba spravovaná Microsoftem, která poskytuje vysoce dostupné, zabezpečené, odolné, škálovatelné a redundantní úložiště. Azure Storage se skládá ze služeb Blob Storage, File Storage a Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Azure Storage v tomto podrobném plánu

Tato šablona používá následující součásti Azure Storage:

- [Šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Povoluje jenom připojení HTTPS.

#### <a name="data-at-rest"></a>Neaktivní uložená data

Prostřednictvím [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) všechna data zapsaná do Azure Storage šifrovaná pomocí 256 šifrování AES, což je jedno z nejúčinnějších šifr, které jsou k dispozici. Můžete použít šifrovací klíče spravované Microsoftem s SSE nebo můžete použít [vlastní šifrovací klíče](../../storage/common/storage-encryption-keys-portal.md).

Účty úložiště je možné zabezpečit prostřednictvím [Virtual Network koncových bodů služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pomocí [pravidel virtuální sítě](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Podrobné informace o zabezpečení Azure Storage najdete v [příručce zabezpečení](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Správa tajných kódů

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) slouží k zabezpečení klíčů a tajných kódů aplikace, aby bylo zajištěno, že nejsou přístupné třetí straně. Služba Key Vault není určená k použití jako úložiště uživatelských hesel. Umožňuje vytvořit více zabezpečených kontejnerů nazývaných trezory. Tyto trezory využívají moduly hardwarového zabezpečení (HSM). Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault v tomto podrobném plánu

- Obsahuje přístupový klíč k úložišti, s přístupem pro čtení uděleným [spravované identitě](https://docs.microsoft.com/azure/app-service/overview-managed-identity) webové aplikace pro zákazníky.
- Obsahuje heslo SQL Server DBA (v samostatném trezoru).
- Protokolování diagnostiky

### <a name="monitoring-logging-and-audit"></a>Monitorování, protokolování a audit

#### <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](https://azure.microsoft.com/services/log-analytics/) jsou služby v Azure, které vám pomůžou shromažďovat a analyzovat data vygenerovaná prostředky ve vašem cloudovém a místním prostředí.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>V tomto podrobném plánu Azure Monitor protokoly.

- Posouzení SQL
- Diagnostika Key Vault
- Application Insights připojení
- Protokol aktivit Azure

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře na různých platformách. Slouží k monitorování živých webových aplikací, které automaticky zjišťují anomálie v výkonu, analyzují výkon, diagnostikují problémy a porozuměli tomu, jak uživatelé pracují s aplikací. Application Insights můžete nasadit na platformy, včetně .NET, Node. js a Java EE, hostované místně nebo v cloudu. Integruje se s vaším procesem DevOps a obsahuje body připojení k celé řadě dalších vývojářských nástrojů.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights v tomto podrobném plánu

Tato šablona používá následující součásti Application Insights:

- Řídicí panel Application Insights na web (operátor, zákazník a rozhraní API)

#### <a name="azure-activity-logs"></a>Protokoly aktivit Azure

Auditování [protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) audity kontrolují události v rámci vašich předplatných. Pomocí protokolu aktivit můžete určit, kdo a kdy použít pro všechny operace zápisu (PUT, POST, DELETE) u prostředků ve vašem předplatném. Můžete také zjištění stavu operace a další relevantní vlastnosti.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) umožňuje základní monitorování služeb Azure tím, že umožňuje shromažďování metrik, protokolů aktivit a diagnostických protokolů. Azure Monitor poskytuje metriky a protokoly základní infrastruktury pro většinu služeb v Microsoft Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/ukofficial-paaswa-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![PaaS hostování webových aplikací pro Velkou Británii pro model hrozeb pro úlohy](images/ukofficial-paaswa-threat-model.png?raw=true "PaaS hostování webových aplikací pro Velkou Británii pro model hrozeb pro úlohy")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Dokumentace k dodržování předpisů pro NCSC Cloud Security

Komerční služba pro koruna (agentura, která funguje ke zlepšení obchodních a podnikových aktivit), obnovila klasifikaci podnikových cloudových služeb Microsoftu do G-cloudu V6, které pokrývají všechny své nabídky na oficiální úrovni. Podrobnosti o Azure a G-cloudu najdete v [souhrnu posouzení zabezpečení cloudu pro Azure Británie G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Tento podrobný plán se zařadí ke 14 zásadám cloudového zabezpečení, které jsou popsány v tématu [Principy NCSC cloudového](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) zabezpečení, které vám pomohou zajistit prostředí, které podporuje úlohy klasifikované jako oficiální v ČR.

[Oficiální zákaznická matice podrobný plán zabezpečení a dodržování předpisů Azure-UK](https://aka.ms/ukofficial-crm) (excelový sešit) obsahuje seznam všech 14 zásad cloudového zabezpečení a označení pro každý princip (neboli princip principu subpart), ať už je to zásada implementace zodpovědnost za společnost Microsoft, zákazníka nebo sdílená mezi těmito dvěma službami.

[Webová aplikace podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro Velkou Británii – matrice implementace](https://aka.ms/ukofficial-paaswa-pim) (excelový sešit) obsahuje seznam všech 14 zásad cloudového zabezpečení a označuje všechny zásady (neboli princip subpart), které jsou určené jako zodpovědnost zákazníka v matici zodpovědnosti zákazníka, 1), pokud podrobný plán implementuje princip a 2) popis způsobu, jakým implementace zarovnává s principem požadavku.  

Cloud Security Alliance (CSA) navíc publikovala matrici řízení cloudu pro podporu zákazníků při hodnocení poskytovatelů cloudu a k identifikaci otázek, které by měly být zodpovězeny před přechodem na cloudové služby. V odpovědi Microsoft Azure odpovědět na dotazník CSA konsensu Assessment Initiative ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), který popisuje, jak Microsoft řeší navržené principy.

## <a name="third-party-assessment"></a>Posouzení třetí strany

Tento podrobný plán byl přezkoumán národním internetovou centrem zabezpečení (NCSC) Spojené království a zarovnává se se zásadami cloudového zabezpečení NCSC 14.

Šablony automatizace byly testovány týmem Spojeného království úspěšnosti Azure Cloud Solution a naším partnerem Microsoftu, [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Nasazení řešení

Tato Podrobný plán zabezpečení a dodržování předpisů Azure Automation se skládá z konfiguračních souborů JSON a skriptů PowerShellu, které služba API pro Azure Resource Manager využívá k nasazení prostředků v rámci Azure. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/ukofficial-paaswa-repo).

Pro nasazení jsou k dispozici tři přístupy. Jednoduché "expresní" rozhraní [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vhodné pro rychlé vytváření testovacího prostředí; parametr rozhraní příkazového [řádku Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) poskytující větší konfiguraci pro prostředí úloh. a Azure Portal nasazení na základě, kde operátor může určit parametry nasazení prostřednictvím Azure Portal. 

1.  Naklonujte nebo stáhněte [Toto](https://aka.ms/ukofficial-paaswa-repo) úložiště GitHub na místní pracovní stanici.
2.  Přečtěte si [metodu 1: Azure CLI 2 (expresní verze)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) a spustit poskytnuté příkazy.
3.  Kontrola [metody 1a: Azure CLI 2 (konfigurace nasazení přes argumenty skriptu)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) a spuštění zadaných příkazů
4.  Kontrola [metody 2: Azure Portal proces](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) nasazení a provést uvedené příkazy

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) můžete použít před rozhraním API App Service k zajištění dalších vrstev zabezpečení, omezování a ovládacích prvků pro vystavení, proxy a ochraně rozhraní API.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) může být implementován jako ovládací prvek, který umožňuje uživatelům zaregistrovat, vytvořit identitu a povolit autorizaci a řízení přístupu pro veřejnou webovou aplikaci.

## <a name="disclaimer"></a>Zřeknutí se práv

- Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
- Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
- Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
- Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
- Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
- Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
