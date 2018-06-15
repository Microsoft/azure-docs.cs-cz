---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Analytics pro FedRAMP
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - Analytics pro FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206823"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: Analýza pro FedRAMP

## <a name="overview"></a>Přehled

[Federal rizika a autorizace správu Program (FedRAMP)](https://www.fedramp.gov/) je program government celou Spojených státech amerických, který poskytuje standardizovaná přístup k zabezpečení, autorizace a nepřetržité monitorování pro cloud produkty a služby. Tento zabezpečení Azure a dodržování předpisů plán, podle kterého poskytuje pokyny, jak doručovat architektura analytics Microsoft Azure, která pomáhá implementovat podmnožinu FedRAMP vysokou ovládací prvky. Toto řešení obsahuje pokyny k nasazení a konfigurace prostředků Azure pro běžné referenční architektura, ukázka způsoby, ve kterém můžete zákazníkům splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky vytvořit a konfigurovat vlastní řešení pro analýzu v Azure.

Tento referenční architektura, pokyny implementace přidruženého ovládacího prvku a modely threat které slouží jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav je zcela nesplňují požadavky na vysokou FedRAMP standardních hodnot. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem FedRAMP způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Toto řešení poskytuje platformu analytics němž zákazníci mohou vytvářet své vlastní nástroje pro analýzu. Referenční architektura popisuje případu použití obecné kde Zákazníci vstupní data prostřednictvím importy dat hromadné správcem SQL nebo dat nebo prostřednictvím aktualizace provozních dat prostřednictvím provozní uživatele. Obě fungovat datové proudy začlenění [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) pro import dat do databáze SQL. Azure Functions musí být nakonfigurované zákazník prostřednictvím portálu Azure pro zpracování úlohy importu jedinečné pro každé zákazníka vlastní požadavky analytics.

Microsoft Azure nabízí celou řadu analýz a generování sestav služby pro zákazníka; Toto řešení však zahrnuje Azure Analysis Services ve spojení s databází SQL Azure a rychle procházet přes data a poskytovat rychlejší výsledky prostřednictvím chytřejší modelování dat zákazníka. Azure Analytics Services je forma strojové učení, které mají zvýšit rychlost dotaz tak, že zjišťování nové vztahy mezi datové sady. Jakmile byl vyzkoušen data prostřednictvím několika statistických funkcí, až 7 další dotaz fondy (8 celkový, včetně serveru zákazníka) můžete synchronizovat se službou stejné tabulkové modely šíří dotazu úlohy a snížení doby odezvy.

Pro rozšířené analýzy a vytváření sestav můžete nakonfigurovat databází SQL s indexy columnstore. Analytické služby Azure a databází SQL můžete škálovat nahoru nebo dolů nebo vypnuté zcela v reakci na využití zákazníka. Veškerý provoz SQL se šifrují pomocí protokolu SSL prostřednictvím zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup Azure doporučuje použití důvěryhodné certifikační autority pro lepší zabezpečení.

Jakmile data jsou odeslány do databáze SQL Azure a cvičení službou Azure Analysis Services, se mineralizuje provozní uživatele a správce SQL nebo dat s Power BI. Power BI zobrazí dat. intuitivně a vrátí informace napříč více datových sad k vykreslení větší přehled. Jeho vysoký stupeň přizpůsobivost a snadnou integraci s Azure SQL Database zajišťuje, že zákazníci mohou nakonfigurovat zpracovávat širokou škálu scénářů podle požadavků jejich obchodních potřeb.

Celé řešení je založena na Azure Storage, který účet zákazníci nakonfigurovat z portálu Azure. Azure Storage šifruje všechna data pomocí šifrování služby úložiště na zachovávejte důvěrnost dat v klidovém stavu.  Geografické redundantní úložiště (GRS) zajišťuje, že nebude nežádoucí událost na primární datové centrum zákazníka vést ke ztrátě dat jako druhé kopie bude uložena v umístění samostatné stovky miles rychle.

Pro lepší zabezpečení Tato architektura slouží ke správě prostředků s Azure Active Directory a Azure Key Vault. Stav systému je monitorován pomocí Operations Management Suite (OMS) a monitorování Azure. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokoly a zobrazení stavu systému v řídicím panelu jeden, snadno navigaci.

Databáze SQL Azure je běžně spravované přes SQL Server Management Studio (SSMS), který se spouští z místního počítače nakonfigurovaná pro přístup k databázi SQL Azure přes zabezpečené připojení VPN nebo ExpressRoute. **Azure doporučuje konfiguraci připojení VPN nebo Azure ExpressRoute pro správu a data importovat do skupiny prostředků odkaz na architekturu.**

![Analýza pro diagram architektury odkaz FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "analýza pro diagram architektury FedRAMP odkaz")

### <a name="roles"></a>Role
Plán, podle kterého analytics popisuje scénář se tři typy obecných uživatelských: provozní uživatelů a správce SQL nebo dat a pracovníka systémy. Azure na základě rolí řízení přístupu (RBAC) umožňuje provádění správu přesné přístupu prostřednictvím vestavěné role, které vlastní. Prostředky jsou k dispozici pro konfiguraci [řízení přístupu na základě Role](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) osnovy a implementace [předdefinované role](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Systémový technik
Systémový technik vlastní zákaznické předplatné Azure a nakonfiguruje nasazení řešení prostřednictvím portálu Azure.

#### <a name="sqldata-administrator"></a>Správce SQL nebo dat
Správce SQL nebo dat vytváří funkce importu dat hromadné a funkce aktualizace provozních dat nahrát do Azure SQL database. Správce dat SQL nebo není zodpovědná za všechny aktualizace provozních dat v databázi, ale bude moct zobrazit data pomocí Power BI.

#### <a name="operational-user"></a>Provozní uživatele
Provozní uživatel pravidelně aktualizuje data a vlastní generování každodenní data. Provozní uživatele bude také interpretovat výsledky pomocí Power BI.

### <a name="azure-services"></a>Služby Azure

Toto řešení používá následující služby Azure. Podrobnosti architektury nasazení najdete v [architektura nasazení služby](#deployment-architecture) části.
- Azure Functions
- Azure SQL Database
- Služba Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- OMS
- Azure Monitor
- Azure Storage
- Bránu ExpressRoute nebo VPN
- Řídicí panel Power BI

## <a name="deployment-architecture"></a>Architektura nasazení služby
V následující části Podrobné informace o vývoji a implementaci elementy.

![alternativní text](images/fedramp-analytics-components.png?raw=true "analýza pro diagram FedRAMP součásti")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) řešení pro malé kód běží v cloudu přes většinu programovacích jazyků. Funkce v tomto řešení integraci s Azure Storage automaticky načítat data zákazníků do cloudu, usnadnění integrace s jinými službami Azure. Funkce jsou snadno škálovatelné a pouze zpoplatněná s náklady, když běží.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) poskytuje modelování podnikových dat a integrace se službami platformy Azure data. Azure Analysis Service urychluje projdete masivní objemy dat kombinováním dat z více zdrojů do jednoho datového modelu.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) poskytuje analýzy a vytváření sestav funkce pro zákazníky pokusu větší přehled mimo jejich úsilí zpracování dat pro vyžádání obsahu.

### <a name="networking"></a>Sítě
**Skupin zabezpečení sítě**: [skupiny Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jsou nastavení ke správě provozu směrované na služby a nasazené prostředky. Skupiny zabezpečení sítě jsou nastaveny na odepřít výchozí schéma a pouze povolit provoz, který je obsažen v předem nakonfigurovaná řízení přístupu seznamu (ACL).

Každý z skupin Nsg mít určité porty a protokoly otevřete tak, aby řešení může fungovat správně a bezpečně. Kromě toho tyto konfigurace jsou povolené pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložený v účtu úložiště
  - OMS [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) je připojen k této skupině diagnostické protokoly.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu prostřednictvím šifrování, databáze, auditování a jiných opatření.

**Replikace dat** Azure Government má dvě možnosti pro [replikaci dat](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Výchozí nastavení pro replikaci dat je **geograficky redundantní úložiště (GRS)**, která asynchronně ukládá data zákazníků v samostatných datovém centru mimo primární oblasti. Tím se zajistí obnovení dat v celkový ztráty pro primární datové centrum.
 - **Místně redundantní úložiště (LRS)** případně je možné nakonfigurovat přes účet úložiště Azure. LRS se replikuje data do jednotky škálování úložiště, který je hostován ve stejné oblasti, ve kterém zákazník vytvoří svého účtu. Všechna data se replikují souběžně, zajistíte, že žádná data záloh je ztrátě při selhání primárního úložiště škálovací jednotky.

**Úložiště Azure** splnit šifrovaná data na požadavky rest, všechny služby nasazené v této referenční architektura využívají [Azure Storage](https://azure.microsoft.com/services/storage/), která ukládá data pomocí [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**Azure SQL Database** Azure SQL Database instance používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování dat a souborů protokolu ochrany informací v klidovém stavu. Šifrování TDE poskytuje záruku, že data uložena nebyla neoprávněnému přístupu k.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby citlivá data nikdy se jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
-   [Databáze SQL dynamická data maskování](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasadí. Zákazníci bude nutné upravit dynamická data maskování nastavení řídit jejich schématu databáze.

### <a name="logging-and-audit"></a>Protokolování a auditování
[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) generuje zobrazení všech up monitorování dat včetně aktivity protokoly, metriky a diagnostických dat, povolení zákazníkům vytvářet úplný přehled o stavu systému.  
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows a úložiště objektů Azure Blob, tabulek a protokoly fronty.
- **Brány firewall protokoly**: Application Gateway poskytuje úplné diagnostiky a přístup k protokolům. Brány firewall protokoly jsou dostupné pro aplikační bránu firewall webových aplikací povolené zdroje.
- **Přihlaste se archivaci**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci s dobou uchování definované 2 dní. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho jsou zahrnuty jako součást této architektury následující OMS řešení:
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.

### <a name="identity-management"></a>Správa identit
-   Ověřování do aplikace se provádí pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá Azure AD k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu Jak [chrání citlivá data v databázi SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.
-   [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) zaměřuje umožňuje správu přístupu k Azure. Předplatné přístup je omezen na správce předplatného.

Další informace o používání funkcí zabezpečení databáze SQL Azure, najdete v článku [Contoso Klinika ukázkové aplikace](https://github.com/Microsoft/azure-sql-security-sample) ukázka.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení

### <a name="expressroute-and-vpn"></a>ExpressRoute a VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo přes zabezpečené tunelové propojení VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení k nasazené jako součást tuto referenční architekturu analýzy dat prostředky. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Nastavením správně, ExpressRoute nebo VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je nezbytné pro správu nasazení a zřizování přístup osobám interakci s prostředím. Existující Windows Server Active Directory se dá integrovat s AAD v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Zákazníci také dokáže spojit nasazenou infrastrukturu služby Active Directory (řadiče domény) existující aad tím, že nasazené infrastrukturu služby Active Directory subdoména doménové struktury služby AAD.

### <a name="additional-services"></a>Další služby
#### <a name="iaas---vm-vonsiderations"></a>IaaS - vonsiderations virtuálních počítačů
Toto řešení PaaS nebere v úvahu všechny virtuální počítače Azure IaaS. Zákazník může vytvořit virtuální počítač Azure spustit mnoho z těchto služeb PaaS. V takovém případě může být konkrétní funkce a služby pro kontinuitu podnikových procesů a OMS využít:

##### <a name="business-continuity"></a>Kontinuita podnikových procesů
- **Vysoká dostupnost**: úlohy serveru jsou seskupené v [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) k zajištění vysoké dostupnosti virtuálních počítačů v Azure. Během plánované i neplánované údržby, splňuje podmínku 99.95 % je k dispozici alespoň jeden virtuální počítač Azure SLA.

- **Trezor služeb zotavení**: [trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ve uložený zálohovaná data a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služeb zotavení zákazníkům obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače povolením rychlejší obnovení.

##### <a name="oms"></a>OMS
-   [Hodnocení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

##### <a name="security"></a>Zabezpečení
- **Ochrana proti malwaru**: [Antimalware od Microsoftu](https://docs.microsoft.com/azure/security/azure-security-antimalware) u virtuálních počítačů poskytuje možnost ochrany v reálném čase, pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy Když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.
- **Opravy správy**: virtuální počítače s Windows, které jsou nasazeny jako součást tuto referenční architekturu jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také OMS [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) služby, pomocí kterého je možné vytvořit aktualizované nasazení opravy virtuálních počítačů v případě potřeby.

#### <a name="azure-commercial"></a>Azure komerční
I když tato architektura analýzy dat není určen pro nasazení [komerční Azure](https://azure.microsoft.com/overview/what-is-azure/) prostředí podobné cíle lze provádět pomocí služeb popsaných v této referenční architektura a také Další služby k dispozici pouze v Azure komerčním prostředí. Upozorňujeme, že Azure komerční udržuje FedRAMP JAB P-ATO na střední úrovně dopadu, což vládních organizací a partnery nasazení mírně citlivé informace do cloudu využitím Azure komerčním prostředí.

Komerční Azure nabízí širokou škálu analytické služby pro stahování Statistika mimo velkých objemů dat:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), komponentu [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), model prediktivní analýzy z jednoho nebo více zdrojů dat sama vyvinula. Statistické funkce se přes několik iterací používají ke generování efektivní model, který pak může využít aplikací, jako jsou Power BI.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) povolí zachytávání dat ze všech velikost, typ a rychlosti příjmu do jediného místa pro provozní a zjišťovací analýzy. Azure Data Lake Store je kompatibilní s součástí typu nejvíce open source v ekosystému Hadoop a výborně se integruje s jinými službami Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat (diagramu toku dat) pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/blueprintanalyticsthreatmodel) nebo naleznete níže:

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů
[Zabezpečení Azure a dodržování předpisů plán, podle kterého – FedRAMP vysoké zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Podobně [zabezpečení Azure a dodržování předpisů plán, podle kterého – FedRAMP střední zákazníka odpovědnost matice](https://aka.ms/blueprintanalyticscimmod) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují FedRAMP mírný směrného plánu. Oba dokumenty zřejmé, zda implementace každý ovládací prvek zodpovídá Microsoft, zákazník nebo sdílené mezi nimi.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP vysoké řízení implementace matice](https://aka.ms/blueprintanalyticscimhigh) a [zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP střední řízení implementace matice](https://aka.ms/blueprintanalyticscimmod) zadejte informace, na kterém se ovládací prvky předmětem analytics architekturu pro každou standardní hodnotu FedRAMP, včetně podrobného popisu způsobu implementace splňuje požadavky na každého zahrnutého ovládacího prvku.

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
