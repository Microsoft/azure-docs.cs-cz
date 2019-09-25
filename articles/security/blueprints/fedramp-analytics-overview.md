---
title: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza pro FedRAMP
description: Podrobný plán zabezpečení a dodržování předpisů Azure – analýza pro FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 9850c5f064815315db6f85a931e7e175d605dcc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257595"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Analýzy pro FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) je USA program pro státní správu, který poskytuje standardizovaný přístup k vyhodnocování zabezpečení, autorizaci a nepřetržitému monitorování cloudových produktů a služeb. Tento Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny k tomu, jak doručovat architekturu Microsoft Azure Analytics, která pomáhá implementovat podmnožinu vysoce FedRAMPch ovládacích prvků. Toto řešení poskytuje pokyny k nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky. Sestavujte a nakonfigurujte svá vlastní Analytická řešení v Azure.

Tato referenční architektura, implementační Příručky k implementaci a modely hrozeb mají sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostatečné pro zcela splnění požadavků vysokého směrného plánu FedRAMP. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán, který zákazníkům umožňuje nasazovat úlohy do Azure v souladu s FedRAMP.
- Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Toto řešení poskytuje analytickou platformu, na které můžou zákazníci vytvářet svoje vlastní analytické nástroje. Referenční architektura popisuje obecný případ použití, kdy zákazník zadá data prostřednictvím hromadného importu dat prostřednictvím správce SQL/dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozního uživatele. Oba pracovní proudy zahrnují [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) pro import dat do SQL Database. Azure Functions musí být zákazníkem nakonfigurovaným prostřednictvím webu Azure Portal, aby bylo možné zpracovávat úkoly importu jedinečné pro vlastní analytické požadavky jednotlivých zákazníků.

Microsoft Azure nabízí celou řadu sestav a analytických služeb pro zákazníka; Toto řešení však zahrnuje Azure Analysis Services ve spojení s Azure SQL Database pro rychlé procházení dat a poskytování rychlejších výsledků prostřednictvím inteligentnějšího modelování zákaznických dat. Služba Azure Analytics Services je forma strojového učení určená ke zvýšení rychlosti dotazů díky zjišťování nových vztahů mezi datovými sadami. Jakmile se data vyškole prostřednictvím několika statistických funkcí, až 7 dalších fondů dotazů (celkem 8 včetně zákaznického serveru), můžete synchronizovat se stejnými tabelárními modely pro rozprostření úlohy dotazů a zkrátit dobu odezvy.

Pro lepší analýzy a vytváření sestav je možné konfigurovat databáze SQL pomocí indexů columnstore. Služby Azure Analytics a databáze SQL je možné škálovat nahoru nebo dolů nebo zcela vypnout v reakci na používání zákazníků. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Jakmile se data nahrají do Azure SQL Database a vyškolená Azure Analysis Services, bude je vynásobeni operačním uživatelem a správcem SQL/dat pomocí Power BI. Power BI zobrazuje data intuitivním a přebírá informace napříč více datovými sadami, aby bylo možné vykreslovat lepší přehled. Jejich vysoký stupeň přizpůsobivosti a snadné integrace s Azure SQL Database zajišťuje, že je zákazníci můžou nakonfigurovat tak, aby zpracovávala nejrůznější scénáře podle potřeb jejich obchodních potřeb.

Celé řešení je postavené na Azure Storage, který si zákazníci nakonfigurují z webu Azure Portal. Azure Storage šifruje všechna data pomocí Šifrování služby Storage a udržuje tak důvěrnost dat v klidovém znění.  Geografické redundantní úložiště (GRS) zajišťuje, že neškodná událost v primárním datovém centru zákazníka nebude mít za následek ztrátu dat, protože druhá kopie bude uložena v samostatném umístění stovky mil.

Pro zvýšení zabezpečení tato architektura spravuje prostředky pomocí Azure Active Directory a Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu.

Azure SQL Database je běžně spravované prostřednictvím SQL Server Management Studio (SSMS), která se spouští z místního počítače nakonfigurovaného pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do skupiny prostředků referenční architektury.**

![Analýza diagramu referenční architektury pro FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Analýza diagramu referenční architektury pro FedRAMP")

### <a name="roles"></a>Role
Analytický plán popisuje scénář se třemi obecnými uživatelskými typy: provozní uživatel, správce SQL/dat a inženýr systému. Access Control na základě rolí v Azure (RBAC) umožňuje implementaci přesné správy přístupu prostřednictvím integrovaných vlastních rolí. Prostředky jsou k dispozici pro konfiguraci [Access Control na základě rolí](../../role-based-access-control/role-assignments-portal.md) a sbalení a implementace [předem definovaných rolí](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>Systems inženýr
System inženýr vlastní předplatné zákazníka pro Azure a nakonfiguruje nasazení řešení prostřednictvím webu Azure Portal.

#### <a name="sqldata-administrator"></a>SQL/správce dat
Správce SQL/data zřídí funkci hromadného importu dat a funkci aktualizace provozních dat pro nahrávání do Azure SQL Database. Správce SQL/data není zodpovědný za žádné aktualizace provozních dat v databázi, ale bude moci zobrazit data prostřednictvím Power BI.

#### <a name="operational-user"></a>Operační uživatel
Operační uživatel pravidelně aktualizuje data a vlastní každodenní generování dat. Provozní uživatel také bude interpretovat výsledky prostřednictvím Power BI.

### <a name="azure-services"></a>Služby Azure

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .
- Azure Functions
- Azure SQL Database
- Služba Azure Analysis
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (protokoly)
- Azure Storage
- ExpressRoute/VPN Gateway
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část popisuje vývojové a implementační prvky.

![alternativní text](images/fedramp-analytics-components.png?raw=true "Diagram analýz pro součásti FedRAMP")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) jsou řešení pro spouštění malých částí kódu v cloudu prostřednictvím většiny programovacích jazyků. Funkce v tomto řešení se integrují s Azure Storage a automaticky vyžádají zákaznická data do cloudu, což usnadňuje integraci s dalšími službami Azure. Funkce jsou snadno škálovatelné a při jejich spuštění se účtují jenom náklady.

**Služba Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) poskytuje modelování a integraci podnikových dat pomocí služeb Azure Data Platform. Služba Azure Analysis Service zrychluje procházení obrovských objemů dat kombinováním dat z více zdrojů do jednoho datového modelu.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) poskytuje funkce pro analýzu a vytváření sestav pro zákazníky, kteří se snaží získat větší přehled o svých aktivitách zpracování dat.

### <a name="networking"></a>Sítě
**Skupiny zabezpečení sítě**: [Skupin zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) se nastavují pro správu provozu směrovaného na nasazených zdrojích a službách. Skupiny zabezpečení sítě jsou nastavené na výchozí nastavení Odepřít a povoluje jenom provoz, který je obsažený v předem nakonfigurovaném seznamu Access Control (ACL).

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
  - [Protokoly Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md) jsou připojené k diagnostickým protokolům NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Replikace dat** Azure Government má dvě možnosti pro [replikaci dat](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Výchozím nastavením pro replikaci dat je **geograficky redundantní úložiště (GRS)** , které asynchronně ukládá data zákazníků do samostatného datového centra mimo primární oblast. Tím se zajistí obnovení dat v celkové události ztráty pro primární datové centrum.
 - **Místně redundantní úložiště (LRS)** se dá nakonfigurovat taky prostřednictvím účtu Azure Storage. LRS replikuje data v rámci jednotky škálování úložiště, která je hostována ve stejné oblasti, ve které zákazník vytvoří svůj účet. Všechna data se replikují souběžně, což zajistí, že nedojde ke ztrátě zálohovaných dat v primárním selhání jednotky škálování úložiště.

**Azure Storage** Všechny služby nasazené v této referenční architektuře využívají [Azure Storage](https://azure.microsoft.com/services/storage/), které ukládají data pomocí [šifrování služby Storage](../../storage/common/storage-service-encryption.md), aby splnily požadavky na uchovávání šifrovaných dat.

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro operační systém a datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database** Instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   SQL Database je nakonfigurovaná tak, aby používala [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provádí šifrování a dešifrování dat a souborů protokolů v reálném čase za účelem ochrany neaktivních informací. TDE poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Always Encrypted sloupců](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistí, že se citlivá data v databázovém systému nikdy neobjeví jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
-   [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) je možné provést po nasazení referenční architektury. Zákazníci budou muset upravit nastavení dynamického maskování dat tak, aby odpovídalo schématu databáze.

### <a name="logging-and-audit"></a>Protokolování a audit
[Azure monitor](../../azure-monitor/overview.md) generuje všechna zobrazení dat monitorování, včetně protokolů aktivit, metrik a diagnostických dat, a umožňuje zákazníkům vytvořit úplný přehled o stavu systému.  
[Protokoly Azure monitor](../azure-security-disk-encryption-overview.md) poskytují rozsáhlé protokolování aktivity systému a uživatele a také stav systému. Shromažďuje a analyzuje data vygenerovaná prostředky v Azure a v místních prostředích.
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/resource-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows a protokoly služby Azure Blob Storage, tabulky a fronty.
- **Protokoly brány firewall**: Application Gateway poskytuje úplné diagnostické protokoly a protokoly přístupu. Protokoly brány firewall jsou k dispozici pro prostředky Application Gateway s podporou WAF.
- **Archivace protokolu**: Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage za účelem archivace s definovanou dobou uchovávání 2 dny. Tyto protokoly se připojují k protokolům Azure Monitor pro zpracování, ukládání a vytváření sestav řídicích panelů.

Kromě toho jsou součástí této architektury Tato řešení monitorování:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Řešení Azure Automation ukládá, spouští a spravuje Runbooky.
-   [Security and Audit](../../security-center/security-center-intro.md): Řídicí panel Security and Audit poskytuje přehled o stavu zabezpečení prostředků tím, že poskytuje metriky pro domény zabezpečení, významné problémy, detekce, analýzu hrozeb a běžné dotazy zabezpečení.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Protokoly aktivit Azure](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

### <a name="identity-management"></a>Správa identit
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kromě toho šifrování sloupce databáze používá Azure AD k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace, nakonfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které přijmou vhodná opatření k jejich vyřešení.
-   Správa [Access Control na základě rolí v Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md) umožňuje správu cílené přístupu pro Azure. Přístup k předplatnému je omezený na správce předplatného.

Další informace o používání funkcí zabezpečení Azure SQL Database najdete v ukázce ukázkové [aplikace Contoso Clinic demo](https://github.com/Microsoft/azure-sql-security-sample) .

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo zabezpečené tunelové připojení VPN musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást této referenční architektury pro analýzu dat. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Díky správnému nastavení ExpressRoute nebo VPN můžou zákazníci přidat vrstvu ochrany dat při přenosu.

### <a name="azure-active-directory-setup"></a>Instalace Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat do AAD ve [čtyřech kliknutích](../../active-directory/hybrid/how-to-connect-install-express.md). Zákazníci mohou také nasadit infrastrukturu služby Active Directory (řadiče domény) na existující AAD tím, že nasazená infrastruktura služby Active Directory vytvoří subdoménu doménové struktury AAD.

### <a name="additional-services"></a>Další služby
#### <a name="iaas---vm-considerations"></a>IaaS – požadavky na virtuální počítač
Toto řešení PaaS nezahrnuje žádné virtuální počítače Azure IaaS. Zákazník může vytvořit virtuální počítač Azure, který bude spouštět mnoho z těchto služeb PaaS Services. V takovém případě by se mohly využít konkrétní funkce a služby pro provozní kontinuitu a protokoly Azure Monitor:

##### <a name="business-continuity"></a>Kontinuita podnikových procesů
- **Vysoká dostupnost:** Úlohy serveru jsou seskupené do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , které vám pomůžou zajistit vysokou dostupnost virtuálních počítačů v Azure. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

- **Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace Azure Virtual Machines v této architektuře. Pomocí trezoru Recovery Services můžou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, což umožní rychlejší obnovení.

##### <a name="monitoring-solutions"></a>Řešení monitorování
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Antimalwarové řešení oznamuje stav malwaru, hrozeb a stavu ochrany.
-   [Update Management](../../automation/automation-update-management.md): Řešení Update Management umožňuje zákazníkům spravovat aktualizace zabezpečení operačního systému, včetně stavu dostupných aktualizací a procesu instalace požadovaných aktualizací.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Change Tracking](../../automation/change-tracking.md): Řešení Change Tracking zákazníkům umožňuje snadno identifikovat změny v prostředí.

##### <a name="security"></a>Zabezpečení
- **Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovatelnými výstrahami v případě, že se známý škodlivý nebo nežádoucí software pokusí Nainstalujte nebo spusťte na chráněných virtuálních počítačích.
- **Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje také službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které lze vytvořit aktualizované nasazení, aby se v případě potřeby daly opravit virtuální počítače.

#### <a name="azure-commercial"></a>Komerční Azure
I když tato architektura analýzy dat není určená pro nasazení do [komerčního prostředí Azure](https://azure.microsoft.com/overview/what-is-azure/) , podobné cíle je možné dosáhnout prostřednictvím služeb popsaných v této referenční architektuře a dalších dostupných služeb. pouze v komerčním prostředí Azure. Upozorňujeme, že Azure Commercial udržuje FedRAMP JAB P-ATO na úrovni středních dopadů a umožňuje státním institucím a partnerům nasazovat střední citlivé informace do cloudu s využitím komerčního prostředí Azure.

Azure Commercial nabízí širokou škálu analytických služeb, které umožňují získávat přehledy z velkých objemů dat:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), součást [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), vyvíjí model prediktivní analýzy z jednoho nebo více zdrojů dat. Statistické funkce se používají v několika iteracích ke generování efektivního modelu, který mohou využívat aplikace jako Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) umožňuje zachytit data libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy. Azure Data Lake Store je kompatibilní s nejvíce otevřenými zdrojovými komponentami v ekosystému Hadoop a integruje se s dalšími službami Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (DFD) pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/blueprintanalyticsthreatmodel) nebo najdete níže:

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
V tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – FedRAMP vysoká zodpovědnost zákazníka](https://aka.ms/blueprinthighcrm) jsou uvedeny všechny ovládací prvky zabezpečení, které vyžadují vysoké směrné plány FedRAMP. Podobně v tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – FedRAMP mírná zodpovědnosti zákazníka](https://aka.ms/blueprintanalyticscimmod) jsou uvedeny všechny ovládací prvky zabezpečení, které jsou vyžadovány středním směrným plánem FedRAMP. Oba dokumenty podrobně popisují, zda je implementace každého ovládacího prvku zodpovědná za společnost Microsoft, zákazníka nebo sdílenou mezi těmito dvěma způsoby.

FedRAMP a implementační matrice [High Control](https://aka.ms/blueprintanalyticscimhigh) a [podrobný plán zabezpečení a dodržování předpisů Azure-FedRAMP pro střední řízení](https://aka.ms/blueprintanalyticscimmod) poskytují informace o tom, které ovládací prvky jsou pokryté podrobný plán zabezpečení a dodržování předpisů Azure Architektura analýzy pro jednotlivé standardní hodnoty FedRAMP, včetně podrobného popisu způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
