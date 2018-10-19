---
title: Zabezpečení Azure a dodržování předpisů - Analytics pro FedRAMP
description: Zabezpečení Azure a dodržování předpisů - Analytics pro FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 0e5beb89f3ea2a5c14fc56af35112710964bdb16
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406564"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Dodržování předpisů a zabezpečení Azure: Analýza pro FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) je program státní instituce USA, který přináší standardizovaný přístup k posuzování zabezpečení, autorizaci a nepřetržitému monitorování pro cloud produkty a služby. Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny, jak doručovat architektura analytics Microsoft Azure, která pomáhá implementovat podmnožinu FedRAMP High ovládací prvky. Toto řešení obsahuje pokyny k nasazení a konfiguraci prostředků Azure běžné referenční architekturu, představením způsobů, ve které zákazníci mohou pokrýt konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky vytvořit a konfigurovat vlastní analytická řešení v Azure.

Tato referenční architektura, příručky k implementaci přidružený ovládací prvek a modely hrozeb mají sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům a nepoužívali-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez jakýchkoli úprav je dostatečná k zcela požadavkům FedRAMP High standardních hodnot. Je třeba počítat s následujícím:
- Tato architektura poskytuje standardní hodnoty pomáhá zákazníkům nasadit úlohy o velikosti do Azure v podobě odpovídajícího FedRAMP.
- Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty

Toto řešení poskytuje platforma pro analýzy, na kterých zákazníci mohou vytvářet své vlastní analytické nástroje. Referenční architektura obsahuje případu použití obecných kde Zákazníci vstupní data pomocí hromadného importu dat správce SQL/dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Oba fungují zahrnovat datové proudy [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) pro import dat do SQL Database. Služba Azure Functions je nutné nakonfigurovat zákazníka prostřednictvím portálu Azure pro úlohy importu jedinečný pro každého zákazníka vlastní zpracování požadavků na analýzu.

Microsoft Azure nabízí širokou škálu služeb generování sestav a analýzy pro zákazníka; Toto řešení však zahrnuje služby Azure Analysis Services ve spojení s Azure SQL Database a rychle procházet data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování dat zákazníků. Služby Azure Analytics je forma machine learningu určené pro zvýšení rychlosti dotazu zjištěním nové relace mezi datovými sadami. Jakmile data vyškolila přes několik statistických funkcí, až 7 dalšího dotazu fondy (celková, včetně serveru zákazník 8) můžete synchronizovat se službou stejné tabulkové modely rozložit zatížení dotazy a snížení doby odezvy.

Pro rozšířené analýzy a generování sestav můžete databáze SQL nakonfigurovat s indexy columnstore. Analytické služby Azure a SQL Database můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Jakmile se data odeslán do databáze SQL Azure a školení služba Azure Analysis Services, je mineralizuje provozní uživatele a správce SQL/Data s Power BI. Power BI zobrazí data intuitivně a získává informace napříč více datových sad, chcete-li nakreslit lepší přehled o. Jeho vysoký stupeň přizpůsobivost a snadnou integraci s Azure SQL Database zajišťuje, že zákazníci můžou nakonfigurovat zpracovávat širokou škálu scénářů znamének podle svých obchodních potřeb.

Celé řešení je postavené na službě Azure Storage, které zákazníci účet nakonfigurovat z portálu Azure Portal. Azure Storage šifruje všechna data pomocí šifrování služby Storage k údržbě důvěrnost dat v klidovém stavu.  Geografické redundantní úložiště (GRS) zajišťuje, že nežádoucí událost ve primárního datového centra zákazníka nebude mít za následek ztrátu dat jako druhá kopie se uloží v samostatném umístění stovky mil okamžitě.

Pro zvýšení zabezpečení Tato architektura slouží ke správě prostředků pomocí Azure Active Directory a Azure Key Vault. Stav systému je monitorovat prostřednictvím Log Analytics a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Azure SQL Database je obecně spravované přes SQL Server Management Studio (SSMS), který se spouští z místního počítače nakonfigurovaná pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do skupiny prostředků odkaz na architekturu.**

![Analýza pro diagram referenční architektury FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics pro FedRAMP diagram referenční architektury")

### <a name="roles"></a>Role
Podrobný plán analytics popisuje scénář se tři typy obecné uživatele: provozní uživatele, správce SQL/dat a inženýr systémy. Azure na základě rolí řízení přístupu (RBAC) umožňuje provádění správy přesné přístup prostřednictvím vestavěné vlastní role. Materiály jsou k dispozici pro konfiguraci [řízení přístupu na základě Role](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) sbalování a implementace [předdefinovaných rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Systémy
Inženýra, který vlastníkem druhého předplatného zákazníka pro Azure a nakonfiguruje nasazení řešení na webu Azure Portal.

#### <a name="sqldata-administrator"></a>Správce SQL/dat
Správce SQL/dat vytvoří funkce importu hromadných dat a provozní data funkce aktualizace pro jeho odeslání do Azure SQL database. Správce SQL/dat není zodpovědná za všechny aktualizace provozních dat v databázi, ale budete moct zobrazit data pomocí Power BI.

#### <a name="operational-user"></a>Provozní uživatele
Provozní uživatel pravidelně aktualizuje data a vlastní generování každodenní data. Provozní uživatele bude také interpretovat výsledky přes Power BI.

### <a name="azure-services"></a>Služby Azure

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení najdete v [architektura nasazení](#deployment-architecture) oddílu.
- Azure Functions
- Azure SQL Database
- Službu Azure Analysis Services
- Azure Active Directory
- Azure Key Vault
- Azure Log Analytics
- Azure Monitor
- Azure Storage
- Síť ExpressRoute nebo VPN Gateway
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky k vývoji a implementaci.

![alternativní text](images/fedramp-analytics-components.png?raw=true "Analytics pro FedRAMP diagram komponent")

**Služba Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) řešení pro spouštění malé části kódu v cloudu prostřednictvím Většina programovacích jazyků. Funkce v tomto řešení integrovat se službou Azure Storage k automaticky o přijetí změn zákaznických dat do cloudu, usnadňuje integraci s dalšími službami Azure. Funkce se snadno škálovatelnou a pouze vám být naúčtovány náklady při jejich spuštění.

**Služba Azure Analysis**: [službu Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) poskytuje modelování dat organizace a integrace se službami datové platformy Azure. Azure Analysis Service zrychluje procházení velkých objemů dat díky kombinaci dat z více zdrojů do jednoho datového modelu.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) poskytuje analýzy a generování sestav funkce pro zákazníky, kteří pokusu pull lepší přehled o mimo svoje data zpracovávat prostřednictvím.

### <a name="networking"></a>Sítě
**Skupiny zabezpečení sítě**: [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jsou nastavené ke správě přenosů dat, zaměřuje na nasazených prostředků a služeb. Skupiny zabezpečení sítě jsou nastaveny na odepřít výchozí schéma a pouze povolit provoz, který je obsažen v předem nakonfigurovaných přístupu ovládacího prvku seznam (ACL).

Skupiny zabezpečení sítě mají konkrétní porty a protokoly, otevřete řešení mohli pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) je připojen k této skupině diagnostické protokoly.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Replikace dat** Azure Government nabízí dvě možnosti pro [replikace dat](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Výchozí nastavení je replikace dat **geograficky redundantní úložiště (GRS)**, který asynchronně ukládá data zákazníků v samostatné datové centrum mimo primární oblasti. Tím se zajistí obnovení dat v případě výpadku primární datové centrum.
 - **Místně redundantní úložiště (LRS)** lze také nastavit prostřednictvím účtu úložiště Azure. LRS se replikuje data v jednotce škálování úložiště, která je hostována ve stejné oblasti, ve kterém zákazník vytvoří jejich účtu. Všechna data se replikují souběžně, zajištění, že dojde ke ztrátě žádná data záloh v selhání primárního úložiště škálovací jednotku.

**Azure Storage** podle šifrovaná data na požadavky rest, všechny služby nasazené v této referenční architektury využijte [služby Azure Storage](https://azure.microsoft.com/services/storage/), která ukládá data s [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k poskytování šifrování disků s operačním systémem a daty. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database** instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   SQL Database je nakonfigurován na použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování dat a souborů protokolu k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Funkce Always Encrypted sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo serverů aplikace s přístupem ke klíčům k datům ve formátu prostého textu.
-   [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasazuje. Zákazníci, bude nutné upravit nastavení dodržovat svoje schéma databáze maskování dynamických dat.

### <a name="logging-and-audit"></a>Protokolování a auditování
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) generuje veškeré zobrazení monitorovaných dat, včetně protokolů aktivit, metrik a diagnostických dat, takže se zákazníci můžou vytvořit ucelený přehled o stavu systému.  
[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlou protokolování systémových a uživatelských aktivit a také stav systému. Shromažďuje a analyzuje data vygenerovaná prostředky v Azure a místním prostředí.
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Blob v Azure, tabulky a fronty protokoly.
- **Brána firewall protokoly**: The Application Gateway poskytuje úplné diagnostiky a přístup k protokolům. Protokoly brány firewall jsou dostupné pro prostředky povoleným waf služby Application Gateway.
- **Archivace protokolu**: všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci pomocí dobu definované uchování o délce 2 dny. Tyto protokoly se připojit ke službě Azure Log Analytics pro zpracování, ukládání a vytváření sestav na řídicím.

Kromě toho jsou součástí této architektury řešení následující monitorování:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spouští a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): řídicí panel zabezpečení a Audit poskytuje podrobný pohled na stav zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýza hrozeb a běžné dotazy na zabezpečení.
-   [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
-   [Protokoly aktivit Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

### <a name="identity-management"></a>Správa identit
-   Používání služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure AD k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) povolí, zaměřuje access management pro službu Azure. Přístup k předplatnému je omezená na správce předplatného.

Další informace o používání funkcí zabezpečení služby Azure SQL Database, najdete v článku [aplikací Contoso Clinic Demo](https://github.com/Microsoft/azure-sql-security-sample) vzorku.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo zabezpečené tunelové propojení sítě VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení na prostředky nasazené jako součást této referenční architektuře data analytics. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Nastavením odpovídajícím způsobem ExpressRoute nebo VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci mohou také spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující AAD tím, že nasazenou infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="additional-services"></a>Další služby
#### <a name="iaas---vm-vonsiderations"></a>IaaS – vonsiderations virtuálního počítače
Toto řešení PaaS nejsou sestavené všechny virtuální počítače Azure IaaS. Zákazník může vytvořit virtuální počítač Azure spustit mnoho z těchto služeb PaaS. V takovém případě konkrétní funkce a služby pro provozní kontinuitu a Log Analytics může využít:

##### <a name="business-continuity"></a>Kontinuita podnikových procesů
- **Vysoká dostupnost**: Server úlohy jsou seskupené v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

- **Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače umožňuje rychlejší obnovení.

##### <a name="monitoring-solutions"></a>Řešení monitorování
-   [Posouzení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
-   [Posouzení antimalwaru](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The Antimalwarovým řešením hlásí stav malwaru, ohrožení a ochrana.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): řešení Update Management umožňuje správu zákazníka aktualizace zabezpečení operačního systému, včetně proces instalace požadovaných aktualizací a stav dostupných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking): řešení Change Tracking umožňuje zákazníkům snadno identifikovat změny v prostředí.

##### <a name="security"></a>Zabezpečení
- **Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.
- **Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.

#### <a name="azure-commercial"></a>Komerční Azure
I když tato data analytics architektura není určen pro nasazení [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) prostředí, podobně jako cíle lze dosáhnout prostřednictvím služeb popsaných v této referenční architektuře, stejně jako Další služby k dispozici pouze v Azure komerčním prostředí. Mějte prosím na paměti, že Azure Commercial udržuje FedRAMP JAB P-ATO na střední úrovni dopad povolení státní správy USA a partnerům, aby nasazení mírně citlivé informace do cloudu, využívat Azure komerčním prostředí.

Komerční Azure nabízí širokou škálu analytické služby pro přesun přehledů z velkého objemu dat:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), komponentu [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), vyvíjí model prediktivní analýzy z jednoho nebo více zdrojů dat. Statistické funkce prostřednictvím několika iterací slouží ke generování účinným modelem, který pak mohou využívat aplikace, jako je Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) povolí zachytávání dat libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a zjišťovací analýzy. Azure Data Lake Store je kompatibilní s nejvíce open source komponenty v ekosystému Hadoop a hezky se integruje s dalšími službami Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/blueprintanalyticsthreatmodel) nebo najdete níže:

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – FedRAMP vysokou zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP High směrného plánu. Podobně [Azure zabezpečení a dodržování předpisů – FedRAMP střední zákazníka odpovědnost matice](https://aka.ms/blueprintanalyticscimmod) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP střední směrného plánu. Jak dokumentů zřejmé, zda provádění každý ovládací prvek zodpovídá Microsoft, Zákazník, nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – FedRAMP vysokou ovládací prvek implementace matice](https://aka.ms/blueprintanalyticscimhigh) a [Azure zabezpečení a dodržování předpisů – FedRAMP střední ovládací prvek implementace matice](https://aka.ms/blueprintanalyticscimmod) poskytují informace, na kterém jsou popsané ovládací prvky podle architektury analytics pro každou standardní hodnotu FedRAMP, včetně podrobného popisu způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
