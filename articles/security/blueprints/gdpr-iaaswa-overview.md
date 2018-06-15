---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - IaaS webové aplikace pro GDPR
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - IaaS webové aplikace pro GDPR
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162187"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého - IaaS webové aplikace pro GDPR

## <a name="overview"></a>Přehled
Obecné nařízení ochrany dat (GDPR) obsahuje mnoho požadavky o shromažďování, ukládání a pomocí osobní údaje, včetně jak organizace identifikovat a zabezpečit data, osobní zohlednily požadavky průhlednost, zjistit a sestavy narušení osobní data a pracovníky train ochrany osobních údajů a ostatním zaměstnancům. GDPR jednotlivce nabízí větší kontrolu nad jejich osobních údajů a ukládá mnoho nových závazků v organizacích, které shromažďovat, zpracovat nebo analyzovat osobní data. GDPR ukládá nové pravidel na organizace, které nabízejí zboží a služeb lidí z Evropské unie (EU), nebo že shromažďovat a analyzovat data svázané s obyvatele Evropské unie. GDPR platí bez ohledu na to, kde se nachází v organizaci.

Microsoft navrhovat Azure s špičkový bezpečnostní opatření a zásady ochrany osobních údajů k zabezpečení dat v cloudu, včetně osobních údajů, které se identifikovanou pomocí GDPR kategorií. Společnosti Microsoft [smluvních podmínek](http://aka.ms/Online-Services-Terms) potvrdit Microsoft požadavkům procesory.

Tento zabezpečení Azure a dodržování předpisů plán, podle kterého obsahuje pokyny k nasazení infrastruktury jako služby (IaaS) prostředí, který je vhodný pro jednoduchou webovou aplikaci internetové brány. Toto řešení ukazuje způsoby, ve kterém můžete zákazníkům splňují konkrétní požadavky zabezpečení a dodržování předpisů GDPR a slouží jako základ pro zákazníky, vytvořit a nakonfigurovat vlastní řešení IaaS webové aplikace v Azure. Zákazníci mohou využít tuto referenční architekturu a postupujte podle společnosti Microsoft [čtyři kroky](https://aka.ms/gdprebook) v cestě k GDPR dodržování předpisů:
1. Vyhledávání: Určete, které osobní údaje existuje a kde se nachází.
2. Správa: Jak osobní data řídit použití a získat přístup.
3. Chránit: Ovládací prvky zabezpečení k zabránění, zjišťování a reakce na ohrožení zabezpečení a úniky dat vytvořte.
4. Sestava: Uchovávat požadované dokumentaci a spravovat požadavky na data a oznámení v souladu.

Tento referenční architektura, přidružené implementace průvodce a model hrozeb které slouží jako základ pro zákazníky se jejich specifické požadavky přizpůsobit a by se neměla používat jako-je v produkčním prostředí. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem GDPR způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení nasadí referenční architektura pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datové vrstvy, služby Active Directory infrastruktury, aplikační brány a nástroj pro vyrovnávání zatížení. Virtuální počítače nasazené na úrovně web a data jsou nakonfigurované v nastavení dostupnosti, a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používají k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Správa bastionu hostitel poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. **Azure doporučuje konfiguraci připojení VPN nebo ExpressRoute pro správu a data importovat do podsítě architektura odkaz.**

![IaaS Web Express pro diagram architektury odkaz GDPR](images/gdpr-iaaswa-architecture.png?raw=true "IaaS Web Express pro diagram architektury GDPR odkaz")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

- Azure Virtual Machines
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
    - (2) řadič domény active Directory (Windows Server 2016 Datacenter)
    - (2) uzlu clusteru serveru SQL (2017 SQL serveru na Windows Server 2016)
    - (2) web nebo IIS (Windows Server 2016 Datacenter)
- Skupiny dostupnosti
    - (1) řadiče domény active Directory
    - (1) uzly clusteru SQL
    - (1) webového nebo IIS
- Azure Virtual Network
    - ((1) /16 virtuální sítě
    - (5) /24 podsítě
    - Nastavení DNS jsou nastavené pro oba řadiče domény
- Nástroj pro vyrovnávání zatížení Azure
- Azure Application Gateway
    - (1) Aplikační brána firewall webových aplikací povoleno
        - režimu brány firewall: prevence
        - Sady pravidel: OWASP 3.0
        - naslouchací proces: portu 443
- Azure Storage
    - (7) účty geograficky redundantní úložiště
- Cloud s kopií clusteru
- Trezor služby Recovery Services
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Služby Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Architektura nasazení služby
V následující části Podrobné informace o nasazení a implementace elementy.

**U chráněných hostitelů**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazené prostředky jenom povolením vzdáleného přenosy z veřejných IP adres v seznamu bezpečných adres. Zdroj provoz pro povolení vzdálené plochy (RDP) komunikace, musí být definován v této skupině zabezpečení sítě (NSG).

Toto řešení vytvoří virtuální počítač jako hostitele připojené k doméně bastionu s následující konfigurace:
-   [Rozšíření proti malwaru](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault
-   [Vypnutí automatického zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) snížit spotřebu prostředků virtuálního počítače, když není používán
-   [Ochrana přihlašovacích údajů Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby přihlašovací údaje a jiné tajné spustit v prostředí chráněný, který je izolován od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem z 10.200.0.0/16.

**Skupin zabezpečení sítě**: Toto řešení nasazuje prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správu uvnitř virtuální sítě. Podsítě jsou logicky odděleny použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Najdete v části konfigurace [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazení s tímto řešením. Organizace skupiny zabezpečení sítě můžete nakonfigurovat úpravou souboru nad pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každý podsítí má skupina zabezpečení vyhrazené sítě (NSG):
- 1 skupina NSG pro aplikační brána (LBNSG)
- 1 skupina NSG pro hostitele bastionu (MGTNSG)
- 1 skupina NSG pro primární a záložní řadiče domény (ADNSG)
- 1 skupina NSG pro servery SQL a cloudu s kopií clusteru (SQLNSG)
- 1 skupina NSG pro webová vrstva (WEBNSG)

### <a name="data-in-transit"></a>Přenášených dat
Azure šifruje veškerá komunikace do a z datových centrech Azure ve výchozím nastavení. Navíc všechny transakce do služby Azure Storage prostřednictvím portálu Azure neproběhne přes HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu prostřednictvím více měr, včetně šifrování a auditování databáze.

**Úložiště Azure**: ke splnění šifrovaná data na požadavky rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá chránit a ochrany osobních údajů na podporu závazky týkajícími se zabezpečení organizace a požadavky na dodržování předpisů definované GDPR.

**Azure Disk Encryption**: Azure Disk Encryption slouží šifrované disky virtuálních počítačů IaaS ve Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení jsou integrované s Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**SQL Server**: instance systému SQL Server používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci ochrany informací na rest. Šifrování TDE poskytuje záruku, že uložené osobní data nebyla neoprávněnému přístupu k.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby důvěrných osobních údajů se nikdy jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
- [Rozšířené vlastnosti](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) funkce slouží k ukončí zpracování dat tématům, protože umožňuje uživatele, přidání vlastních vlastností do databázové objekty a data označit jako "Nákup ukončen" pro podporu aplikační logiku a zabránit zpracování přidružená osobní data.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [SQL databáze Dynamická Data maskování (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje ohrožení důvěrných osobních údajů ve maskování dat pro uživatele bez oprávnění nebo aplikace. DDM může automaticky zjistit potenciálně citlivých data a navrhněte odpovídající masek má být použita. To pomáhá s identifikací osobních údajů kvalifikující GDPR ochrany a pro omezení přístupu tak, že neexistuje databáze prostřednictvím neoprávněného přístupu. **Poznámka: Zákazníci budou muset upravit nastavení DDM řídit jejich schématu databáze.**

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují funkce, které chcete spravovat přístup k osobním datům v prostředí Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service. Všichni uživatelé v tomto řešení jsou vytvořené v AAD, včetně uživatele, kteří používají systému SQL Server.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá AAD k ověřování aplikace SQL Server. Další informace najdete v tématu Jak [chrání citlivá data v databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správcům definovat podrobných přístupová oprávnění k poskytnout pouze takovou úroveň přístupu, aby uživatelé potřebovat k provádění svých úloh. Namísto udělení každý uživatel neomezený oprávnění pro prostředky Azure, mohou správci povolit jenom určité akce pro přístup k osobním datům. Předplatné přístup je omezen na správce předplatného.
- [Správa privilegovaných identit AAD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určitým prostředkům.  Správci můžou pomocí AAD Privileged Identity Management zjistit, omezte a monitorujte privilegované identity a jejich přístup k prostředkům. Tato funkce slouží také k vynucení na vyžádání, za běhu správy přístupu v případě potřeby.
- [Ochrany identit AAD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.
- Nasazenou instanci služby Active Directory IaaS poskytuje identity management na úrovni operačního systému pro nasazené virtuální počítače IaaS.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce Azure Key Vault pomoci chránit osobní údaje a přístup k těmto údajům zákazníkům:
- Konfigurace zásad rozšířeného přístupu na základě potřeba.
- Minimální požadovaná oprávnění k klíče a tajné klíče jsou definovány zásad přístupu k trezoru klíč.
- Všechny klíče a tajné klíče v Key Vault mít data vypršení platnosti.
- Všechny klíče v trezoru klíče jsou chráněny modulů specializované hardwarového zabezpečení (HSM). Typ klíče je klíč RSA HSM chráněné 2048 bitů.
- Minimální požadovaná oprávnění pomocí RBAC jsou udělena všechny uživatele a identity.
- Diagnostické protokoly Key Vault je povolená s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací pro klíče jsou omezeny na ty, které jsou potřeba.
- Řešení jsou integrované s Azure Key Vault pro správu klíčů šifrování disku virtuálního počítače IaaS a tajných klíčů.

**Opravy správy**: virtuální počítače s Windows, které jsou nasazeny jako součást tuto referenční architekturu jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) služby, pomocí kterého je možné vytvořit aktualizované nasazení opravy virtuálních počítačů v případě potřeby.

**Ochrana proti malwaru**: [Antimalware od Microsoftu](https://docs.microsoft.com/azure/security/azure-security-antimalware) u virtuálních počítačů poskytuje možnost ochrany v reálném čase, pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy Když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.

**Výstrahy zabezpečení**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) umožňuje zákazníkům sledovat provoz, shromažďování protokolů a analyzovat zdroje dat pro hrozeb. Kromě toho Azure Security Center používá existující konfigurace služeb Azure k poskytnutí configuration a služby doporučení pomáhají zlepšit zabezpečení a ochrana osobních údajů. Azure Security Center zahrnuje [hrozby intelligence sestavy](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) pro všechny nalezené hrozeb, které pomáhají reakcí na incidenty týmy prozkoumat ohrožení a oprava.

**Aplikační brána**: architektura snižuje riziko ohrožení zabezpečení služby Application Gateway pomocí brány firewall webových aplikací (firewall webových aplikací) a OWASP ruleset povolené. Další možnosti patří:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brány firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (firewall webových aplikací režim)
- [Prevence režimu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s ruleset OWASP 3.0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Testy vlastní stavu](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) poskytovat další ochranu a oznámení. Azure Security Center nabízí také ztrátou dobré pověsti systému.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: řešení nasadí všechny virtuální počítače ve [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti Ujistěte se, že virtuální počítače distribuované ve více clusterů izolované hardwaru ke zlepšení dostupnosti. Během plánované i neplánované údržby, splňuje podmínku 99.95 % je k dispozici alespoň jeden virtuální počítač Azure SLA.

**Trezor služeb zotavení**: [trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ve uložený zálohovaná data a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služeb zotavení zákazníkům obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače povolením rychlejší obnovení.

**Cloud určující**: [cloudu určující](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání v systému Windows Server 2016, která využívá Azure jako arbitrážního bod. Získá hlas určující cloudu, stejně jako všechny ostatní určující disk kvora a můžete podílet na výpočty kvora, ale používá standardní veřejně dostupné úložiště objektů Blob Azure. Tím se eliminuje režijní náklady navíc údržby hostované ve veřejném cloudu virtuálních počítačů.

### <a name="logging-and-auditing"></a>Protokolování a auditování

OMS poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném. Protokoly aktivity vám mohou pomoci určit operaci iniciátor čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows, protokolů Azure Storage, protokoly Key Vault auditu a protokolů přístup a brány firewall Application Gateway.
- **Archivace protokolu**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci. Uchovávání je uživatelsky konfigurovatelného, až do 730 dní, aby splňoval požadavky organizace specifické uchování. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho jsou zahrnuty jako součást této architektury následující OMS řešení:
-   [Hodnocení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/gdprIaaSdfd) nebo naleznete níže. Tento model může pomoct pochopit body potenciální riziko v infrastruktuře systému při provádění změn zákazníků.

![IaaS Web Express pro model hrozeb GDPR](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS Web Express pro model hrozeb GDPR")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - matice odpovědnosti zákazníka GDPR](https://aka.ms/gdprCRM) uvádí řadiče a procesoru odpovědnosti pro všechny články GDPR. Všimněte si, službách Azure zákazníka je obvykle kontroleru a Microsoft funguje jako procesor.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - GDPR IaaS webové aplikace implementace matice](https://aka.ms/gdprIaaSweb) poskytuje informace, na které GDPR jsou články používala architektuře IaaS webové aplikace, včetně podrobného popisu o způsobu implementace splňuje požadavky na každého zahrnutého článku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí nakonfigurovat tak, aby bezpečně navázat připojení k prostředkům nasazen jako součást tuto referenční architekturu IaaS webové aplikace. Zákazníci správně nastavení sítě VPN nebo ExpressRoute, můžete přidejte vrstvu ochrany dat během přenosu.

Implementací přes zabezpečené tunelové propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální síti Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunel" informací uvnitř šifrované odkaz mezi sítí a Azure zákazníka. Site-to-Site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. [Režimu tunelového připojení IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá v této možnosti jako mechanismus šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě lepšímu zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místní umístění nebo poskytovatele hostingu serveru Exchange. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Kromě toho protože to je přímé připojení poskytovatele telekomunikace zákazníka, data není přenášet přes Internet a proto nebude vystavena k němu.

Osvědčené postupy pro implementaci zabezpečeného hybridní síti, která rozšiřuje do místní sítě do Azure jsou [k dispozici](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

- Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.  
- Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.  
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.  
- Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.  
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
- Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
