---
title: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro Austrálii CHRÁNĚNÁ
description: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro Austrálii CHRÁNĚNÁ
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: f25e054f0273d770807dad6788f184a876a00fe7
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319196"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro Austrálii chráněné

## <a name="overview"></a>Přehled
Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny pro nasazení infrastruktury jako služby (IaaS) prostředí, který je vhodný pro shromažďování, ukládání a načítání vlády Austrálie CHRÁNĚNÁ data, která je v souladu s cíli Australské státní správy informace zabezpečení ruční (ISM) vytvořen australské signály ředitelství (ASD). Tento podrobný plán běžné referenční architektura představuje sadu a pomáhá předvést správné zpracování citlivých státní správy dat v prostředí zabezpečené, kompatibilní a úrovně.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří na svých vlastních procesů akreditace plánování a systému, které provádějí pomáhají zákazníkům nasadit úlohy o velikosti do Azure způsobem ASD nedodržující předpisy. Zákazníci můžou rozhodnout pro implementaci Azure VPN Gateway nebo ExpressRoute používat federované služby a integraci místních prostředků s prostředky Azure. Zákazníkům musíte zvážit důsledky zabezpečení pomocí místních prostředků. Další konfigurace je potřeba ke splnění všech požadavků, jak se můžou lišit v závislosti na konkrétních podrobnostech provádění jednotlivých zákazníků.

Dosažení shodě ASD vyžaduje, že posuzováním registrované bezpečnostní informace auditování systému. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení nasadí referenční architekturu pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datovou vrstvu, služby Active Directory infrastruktury, Application Gateway a nástroje pro vyrovnávání zatížení. Virtuální počítače nasazené na web a data úrovní, jsou nakonfigurované ve skupině dostupnosti a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používá k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Hostitel typu bašta správy zajišťující Správci nasazení přístup k prostředkům zabezpečené připojení.

Architektura dokáže poskytovat zabezpečené hybridní prostředí, které rozšiřuje místní síť do Azure, umožňuje webové úlohy bezpečně přistupovat ve firemní uživatelé v organizaci privátní místní síti nebo z Internetu. Pro místní řešení je zákazník agilně a zodpovědná za všechny aspekty zabezpečení, operací a dodržování předpisů.

Prostředky Azure, které jsou zahrnuté v tomto řešení se může připojit k místní síti nebo prostředími datových center společně umístěného zařízení (např. CDC v Canbeře) prostřednictvím protokolu IPSec VPN s použitím Azure VPN Gateway nebo ExpressRoute... Rozhodnutí, aby se začala používat síť VPN se má počítat s klasifikací velikost přenášených dat a síťová cesta v úvahu. Zákazníci, kteří používají ve velkém měřítku, zvažte nejdůležitější úlohy s velkými objemy dat požadavky hybridní síťové architektury pomocí služby ExpressRoute pro privátní síťové připojení ke službám Azure. Najdete pokyny a doporučení v části Další podrobnosti o mechanismů připojení k Azure.

Federace se službou Azure Active Directory by měla sloužit k uživatelům umožnit ověřování pomocí místních přihlašovacích údajů a přístup ke všem prostředkům v cloudu pomocí Active Directory Federation Services na místní infrastrukturu. Active Directory Federation Services může poskytnout identitu zjednodušenou a zabezpečenou federaci a webové jednotné možnosti přihlašování pro toto hybridní prostředí. Přečtěte si část pokyny a doporučení pro další podrobnosti o nastavení služby Azure Active Directory.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci vybrané oblasti zákazníka pro odolnost proti chybám. Oblasti Azure, které jsou nasazené ve spárovaných oblastech odolné, a geografické redundantní úložiště zajišťuje, že se data replikují do druhé oblasti se také tři kopie. To brání nežádoucí událost v umístění primární datové zákazníka, čímž dojde ke ztrátě dat.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Řízení přístupu na základě role Azure Active Directory se používá pro řízení přístupu k prostředkům a nasadit klíče ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet. Azure Application Gateway je nakonfigurovaný jako brána firewall v režimu ochrany před únikem informací a vyžaduje protokol TLS provozu s využitím minimálně verze 1.2.

![IaaS webové aplikace pro chráněné AU referenční architekturu](images/au-protected-iaaswa-architecture.png?raw=true "IaaS webové aplikace pro Diagram chráněné AU referenční architektury") 

Toto řešení používá následující služby Azure. Další podrobnosti najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Skupiny dostupnosti
    - (1) uzly clusteru SQL
    - (1) web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) firewall webových aplikací
        - Režim brány firewall: ochrany před únikem informací
        - Sada pravidel, která: OWASP 3.0
        - Port naslouchacího procesu: 443
- Disk s kopií cloudu Azure
- Azure Key Vault
- Nástroj pro vyrovnávání zatížení Azure
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Log Analytics
- Azure Storage
- Azure Virtual Machines
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
    - (2) uzel clusteru SQL serveru (SQL Server 2017 na Windows serveru 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) skupiny zabezpečení sítě
    - Azure Network Watcher
- Trezor služby Recovery Services

Tento podrobný plán obsahuje služby Azure, které nebyly certifikovány pro použití v chráněném klasifikace australské Kybernetických zabezpečení centrum (ACSC). Všechny služby, zahrnuté v této referenční architektuře byly ověřeny ACSC na úrovni šíření omezení značky (DLM). Společnost Microsoft doporučuje, že zákazníci zkontrolujte publikované zabezpečení a sestav auditování související s těmito službami Azure a použití jejich framework správy rizik k určení, zda je vhodná pro jejich interní akreditace a použití ve službě Azure Chráněné klasifikace.

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy (RDP), musí být definován ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředků v architektuře s podsítí samostatné webové, databáze podsítě, podsíť služby Active Directory a podsítě pro správu ve virtuální síti. Podsítě jsou logicky oddělené použitý pro jednotlivé podsítě k omezení provozu mezi podsítěmi na pouze to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Zobrazit konfiguraci [skupiny zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazeny s tímto řešením. Organizace můžou nakonfigurovat skupiny zabezpečení sítě tak, že upravíte soubor novějšímu pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každé z podsítí má vyhrazené síťové skupiny zabezpečení:
- Skupina zabezpečení sítě 1 pro Application Gateway (LBNSG)
- Skupina zabezpečení sítě 1 pro bastion host (MGTNSG)
- Skupina zabezpečení sítě 1 pro servery SQL Server a cloudu s kopií clusteru (SQLNSG)
- Skupina zabezpečení sítě 1 pro webová vrstva (WEBNSG)

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. 

Pro chráněná data přenášená z sítě vlastněné zákazníkem architektura používá na Internetu nebo ExpressRoute s bránou sítě VPN s protokolem IPSEC nakonfigurované.

Kromě toho všechny transakce do Azure prostřednictvím portálu pro správu Azure dojde k přes HTTPS využívání protokolu TLS 1.2.

### <a name="data-at-rest"></a>Data v klidovém stavu
Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit data podporu závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené ISM australské státní správy.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows, zajišťuje šifrování pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**SQL Server**: instance systému SQL Server používá následující bezpečnostní opatření databáze:
-   [Auditování SQL serveru](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) sleduje události databáze a zapisuje je do protokolů auditu.
-   [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Šifrované sloupce](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá při snížení přístup tak, aby citlivá data neexistuje databáze prostřednictvím před neoprávněným přístupem. **Pro úpravu nastavení dodržovat svoje schéma databáze maskování dynamických dat zodpovídají zákazníci.**

### <a name="identity-management"></a>Správa identit
Zákazníci můžou využívat v místním Active Directory Federated Services pro vytvoření federace s [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), které je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) místních adresářů se integruje s Azure Active Directory. Všichni uživatelé v tomto řešení vyžaduje účtů služby Azure Active Directory. S federací přihlášení uživatelé přihlásit do služby Azure Active Directory a mohli ověřovat prostředky Azure pomocí místních přihlašovacích údajů.

Navíc následující funkce Azure Active Directory pomoct spravovat přístup k datům v prostředí Azure:
- Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace. Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení by to ovlivnilo organizace&#39;s identitami, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s organizace&#39;s identitami a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

**Azure Multi-Factor Authentication**: K ochraně identit, by měla být implementována ověřování službou Multi-Factor Authentication. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) je snadno použitelný, škálovatelné a spolehlivé řešení, které poskytuje druhé metody ověřování ochrana uživatelů. Azure Multi-Factor Authentication využívá výkonný cloud a integruje se s místní službou Active Directory a vlastních aplikací. Tato ochrana je rozšířená velkého objemu, klíčových scénářích.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault pomáhá zákazníkům chránit a přístup k tyto údaje:

- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je že modul hardwarového zabezpečení chráněných klíčů RSA 2048 bitů.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.
- Toto řešení je integrovaná se službou Azure Key Vault ke správě klíčů šifrování disku virtuálního počítače IaaS a tajných kódů.

**Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

Kromě toho tato referenční architektura využívá [sken posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) ve službě Azure Security Center. Po nakonfigurování agenta partnera (třeba Qualysu) hlásí data o ohrožení zabezpečení na platformu partnera pro správu. Naopak platforma partnera pro správu poskytuje ohrožení zabezpečení a data zpět do služby Azure Security Center, monitorování stavu umožňuje zákazníkům rychle identifikovat ohrožené virtuální počítače.

**Azure Application Gateway**: architektura snižuje riziko ohrožení zabezpečení pomocí služby Azure Application Gateway nakonfigurovat firewall webových aplikací a pravidel OWASP povolena. Další možnosti patří:

- [Koncové k ukončení SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim ochrany před únikem informací)
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) pomocí sady pravidel OWASP 3.0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastních testů stavu paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také poskytuje systém pověst.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost**: všechny virtuální počítače v nasazení řešení [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují distribuci virtuálních počítačů napříč několika izolovanými hardwarovými clustery pro zlepšení dostupnosti. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci můžou obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače umožňuje rychlejší obnovení.

**Cloud s kopií clusteru**: [disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání ve Windows serveru 2016, která využívá Azure jako arbitrážní bod. Disk s kopií cloudu, stejně jako všechny ostatní určující disky kvora, může hlasovat a účastnit se výpočtů kvora, ale používá standardní veřejně dostupné úložiště objektů Blob Azure. Tím se eliminují režii navíc Údržba virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly služby Azure Storage, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy, což umožní všechna data k analýze společně bez ohledu na jejich původní zdroj. Kromě toho Azure Security Center umožňuje integrací s Log Analytics a umožňuje zákazníkům používat dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly z Azure SQL serveru. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcher poskytuje nástroje pro monitorování, Diagnostika, zobrazujte metriky, zakázání nebo povolení protokolů pro prostředky ve službě Azure virtual network.  Společenství severních entit by měly implementovat protokoly toku Network Watcher pro skupiny zabezpečení sítě a virtuální počítače. Tyto protokoly by být uloženy v účtu úložiště vyhrazené jenom zabezpečení protokoly se ukládají v, který by měl být zabezpečený přístup k účtu úložiště pomocí řízení přístupu na základě rolí.

## <a name="threat-model"></a>Model hrozeb
Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/au-protected-iaaswa-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![IaaS webové aplikace pro Model AU OCHRANOU před internetovými útoky](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS webové aplikace pro Diagram modelu AU OCHRANOU před internetovými útoky")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
Tato dokumentace dodržování předpisů je vytvořen microsoftem, které jsou založené na platformách a službách nabízených společností Microsoft. Tato dokumentace způsobené celou řadou zákaznická nasazení, přináší zobecněný přístup řešení pouze hostované v prostředí Azure. Zákazníci mohou identifikovat a použít alternativní produktů a služeb na základě nedělají provozní prostředí a obchodních výsledků. Zákazníkům, kteří zvolí používat místní prostředky, musí řešit operace pro tyto místní prostředky a zabezpečení. Dokumentované řešení může přizpůsobit zákazníkům řešit jejich konkrétnímu místnímu a požadavky na zabezpečení.

[Azure zabezpečení a dodržování předpisů – matici zodpovědnosti zákazníků AU-PROTECTED](https://aka.ms/au-protected-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované chráněné AU. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – AU-PROTECTED IaaS webové aplikace implementace matice](https://aka.ms/au-protected-iaaswa-cim) poskytuje informace, na kterém jsou adresovány chráněné AU ovládacích prvků IaaS architektura pro webové aplikace, včetně podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Pro utajených informací zabezpečené tunelové propojení IPSec VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení k prostředky nasazené jako součást této referenční architektuře IaaS webové aplikace. Nastavením odpovídajícím způsobem IPSec VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení IPSec VPN s využitím Azure vytvářet virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení může proběhnout přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. 

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost privátní připojení. Azure ExpressRoute je vyhrazené propojení mezi Azure a v místním umístění nebo poskytovatele hostingu serveru Exchange a je považován za privátní sítě. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost a nižší latenci než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

K ochraně důvěrná data, ať už pomocí Internetu nebo Azure ExpressRoute, musíte zákazníkům konfigurace jejich IPSec VPN s použitím následující nastavení:

• Iniciátoru VPN zákazník musí být nakonfigurovaný pro života nepřesahovala 14400 sekund.
• Iniciátoru VPN zákazníka musíte zakázat agresivní IKEv1 režimu.
• Iniciátoru VPN zákazníka, musíte nakonfigurovat Perfect Forward Secrecy.
• Zákazník iniciátoru VPN musíte nakonfigurovat použití HMAC SHA256 nebo vyšší.

Možnosti konfigurace pro zařízení VPN a protokolu IPSec / IKE parametrů je [k dispozici](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) ke kontrole.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat se službou Azure Active Directory v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Kromě toho [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) umožňuje zákazníkům ke konfiguraci federace s místními [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) a Azure Active Directory. Federace přihlášení zákazníci mohou povolit uživatelům přihlášení ke službám Azure Active Directory pomocí svých místních hesel a bez nutnosti znovu zadejte své heslo v podnikové síti. Pomocí možnosti federační službou Active Directory Federation Services, můžete nasadit nové instalace služby AD FS nebo můžete zadat existující instalace ve farmě systému Windows Server 2012 R2.

Aby se zabránilo důvěrná data z nesynchronizuje do Azure Active Directory, můžete omezit zákazníkům atributy, které se replikují do Azure Active Directory s použitím následující nastavení v Azure Active Directory Connect:
- [Povolit filtrování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Zakázat synchronizaci hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
- [Zakázat zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Zakázat zpětný zápis zařízení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
- Ponechejte výchozí nastavení pro [prevence náhodného odstranění](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) a [automatický upgrade](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)

## <a name="disclaimer"></a>Právní omezení
- Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
- Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
- Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
- Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
