---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure – IaaS pro FedRAMP
description: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure – IaaS pro FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: a0458525eaf985ac6b1ff2afde5726bbac45b4f2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778778"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Webová aplikace IaaS pro FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov) je program pro státní správu USA, který poskytuje standardizovaný přístup k vyhodnocování zabezpečení, autorizaci a nepřetržitému monitorování cloudových produktů a služeb. Tato Podrobný plán zabezpečení a dodržování předpisů Azureá automatizace poskytuje pokyny pro nasazení prostředí IaaS (infrastruktura jako služba) vyhovující FedRAMP, které je vhodné pro jednoduchou internetovou webovou aplikaci. Toto řešení automatizuje nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky k sestavení a Nakonfigurujte vlastní řešení v Azure. Řešení implementuje podmnožinu ovládacích prvků od vysokého směrného plánu FedRAMP založenou na NIST SP 800-53. Další informace o požadavcích na FedRAMP a tomto řešení najdete v [dokumentaci k dodržování předpisů](#compliance-documentation).
> [!NOTE]
> Toto řešení se nasazuje do Azure Government.

Tato Podrobný plán zabezpečení a dodržování předpisů Azureá automatizace automaticky nasadí referenční architekturu webové aplikace v IaaS s předem nakonfigurovanými ovládacími prvky zabezpečení, aby zákazníkům pomohla dosáhnout dodržování požadavků FedRAMP. Toto řešení se skládá z Azure Resource Manager šablon a skriptů PowerShellu, které provedou nasazení a konfiguraci prostředků.

Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostačující pro zcela splnění požadavků vysokého směrného plánu FedRAMP. Je třeba počítat s následujícím:
- Tato architektura poskytuje směrný plán, který zákazníkům umožňuje využívat Azure v souladu s FedRAMPm způsobem.
- Zákazníci zodpovídají za provádění vhodného posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Rychlý přehled toho, jak toto řešení funguje, najdete v tomto [videu](https://aka.ms/fedrampblueprintvideo) , které vysvětluje a demonstruje jeho nasazení.

Pokyny k nasazení získáte kliknutím [sem](https://aka.ms/fedrampblueprintrepo) .

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení nasadí referenční architekturu pro webovou aplikaci v IaaS pomocí back-endu SQL Server. Tato architektura zahrnuje webovou vrstvu, datovou vrstvu, infrastrukturu služby Active Directory, Application Gateway a Load Balancer. Virtuální počítače nasazené na webové a datové vrstvy jsou nakonfigurované ve skupině dostupnosti a instance SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro zajištění vysoké dostupnosti. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory slouží k vymáhání zabezpečení a konfigurací dodržování předpisů na úrovni operačního systému. Bastionu hostitel poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do podsítě referenční architektury.**

![Diagram referenční architektury IaaS webové aplikace pro FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Diagram referenční architektury IaaS webové aplikace pro FedRAMP")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

- Azure Virtual Machines
    - (1) hostitel bastionu (Windows Server 2016 Datacenter)
    - (2) řadič domény služby Active Directory (Windows Server 2016 Datacenter)
    - (2) SQL Server uzel clusteru (SQL Server 2017 v systému Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Sady dostupnosti
    - (1) řadiče domény služby Active Directory
    - (1) uzly clusteru SQL
    - (1) Web/IIS
- Azure Virtual Network
    - (1)/16 virtuálních sítí
    - (5)/24 podsítí
    - Nastavení DNS je nastavené na oba řadiče domény.
- Azure Load Balancer
- Azure Application Gateway
    - (1) Application Gateway WAF povolen
        - režim brány firewall: prevence
        - sada pravidel: OWASP 3,0
        - naslouchací proces: port 443
- Azure Storage
    - (7) geograficky redundantní účty úložiště
- Sdílená složka Azure v cloudu
- Trezor Recovery Services
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (protokoly)

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část popisuje vývojové a implementační prvky.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům. NSG hostitele bastionu umožňuje připojení pouze na portu TCP 3389 pro protokol RDP. Zákazníci mohou dále konfigurovat hostitele bastionu tak, aby splňovali požadavky na posílení zabezpečení systému organizace.

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředky v architektuře pomocí samostatné webové podsítě, podsítě databáze, podsítě služby Active Directory a podsítě pro správu v rámci virtuální sítě. Podsítě jsou logicky oddělené pomocí pravidel skupin zabezpečení sítě použitých pro jednotlivé podsítě, aby se omezil provoz mezi podsítěmi jenom na to, co je potřeba pro funkce systému a správy.

Podívejte se prosím na konfiguraci [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazených v tomto řešení. Zákazníci můžou nakonfigurovat skupiny zabezpečení sítě úpravou výše uvedeného souboru pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako příručky.

Každá z podsítí má vyhrazenou skupinu zabezpečení sítě (NSG):
- 1 NSG pro Application Gateway (LBNSG)
- 1 NSG pro hostitele bastionu (MGTNSG)
- 1 NSG pro primární a záložní řadič domény (ADNSG)
- 1 NSG pro SQL Server (SQLNSG)
- 1 NSG pro webovou vrstvu (WEBNSG)

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém formátu pomocí několika šifrovacích opatření.

**Azure Storage**: Aby se splnily požadavky na šifrování na základě dat, všechny účty úložiště používají [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server je nakonfigurovaná tak, aby používala [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), která provádí šifrování a dešifrování dat a souborů protokolů v reálném čase za účelem ochrany neaktivních informací. TDE poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.

Zákazníci můžou taky nakonfigurovat následující bezpečnostní opatření SQL Server:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Always Encrypted sloupců](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistí, že se citlivá data v databázovém systému nikdy neobjeví jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
-   [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) je možné provést po nasazení referenční architektury. Zákazníci budou muset upravit nastavení dynamického maskování dat tak, aby odpovídalo schématu databáze.

**Azure Disk Encryption**: Azure Disk Encryption slouží k šifrování disků virtuálních počítačů s Windows IaaS. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro disky s operačním systémem a daty. Řešení je integrované s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

### <a name="identity-management"></a>Správa identit

Následující technologie poskytují možnosti správy identit v prostředí Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je víceklientské cloudové služby Microsoftu a služba pro správu identit založené na víceklientské architektuře.
- Ověřování pro webovou aplikaci nasazené zákazníkem se dá provést pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Správa [Access Control na základě rolí v Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesné řízení přístupu pro Azure. Přístup k předplatnému je omezený na správce předplatného a přístup k prostředkům se může omezit na základě role uživatele.
- Nasazená instance služby Active Directory IaaS poskytuje správu identit na úrovni operačního systému nasazených virtuálních počítačů s IaaS.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Azure Key Vault pomáhá spravovat IaaS šifrovací klíče a tajné kódy disků virtuálního počítače pro tuto referenční architekturu.

**Správa oprav**: Virtuální počítače s Windows nasazené tímto Podrobný plán zabezpečení a dodržování předpisů Azure Automation jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace ze služby web Windows Update. Toto řešení také nasadí řešení Azure Automation, pomocí kterého je možné vytvořit nasazení aktualizací pro nasazení oprav na servery Windows v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovatelnými výstrahami v případě, že se známý škodlivý nebo nežádoucí software pokusí Nainstalujte nebo spusťte na chráněných virtuálních počítačích.

**Application Gateway**: Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway s bránou firewall webových aplikací (WAF) a povolená RuleSet OWASP. Mezi další možnosti patří:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Režim WAF)
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s OWASP 3,0 RuleSet

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost:** Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA. Řešení nasadí všechny virtuální počítače webové vrstvy a datové vrstvy do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují, že se virtuální počítače distribuují napříč více izolovanými hardwarovými clustery, aby se zlepšila dostupnost. Toto řešení navíc nasadí virtuální počítače s SQL Server do skupiny dostupnosti jako [skupinu dostupnosti AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Funkce Skupina dostupnosti Always On poskytuje možnosti pro vysokou dostupnost a zotavení po havárii.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace Azure Virtual Machines v této architektuře. Pomocí trezoru Recovery Services můžou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, což umožní rychlejší obnovení.

**Disk s kopií cloudu**: [Disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání ve Windows serveru 2016, který využívá Azure jako arbitrážního bodu. Disk s kopií cloudu, stejně jako jakýkoli jiný určující disk kvora, získá hlas a může se zúčastnit výpočtů kvora, ale používá standardně veřejně dostupný Blob Storage Azure. Tím se eliminuje mimořádná režie při údržbě virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Protokoly Azure Monitor poskytují rozsáhlé protokolování aktivity systému a uživatele a také stav systému. [Azure monitor protokoluje](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a v místních prostředích.

- **Protokoly aktivit:**  [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly:**  [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly služby Azure Storage, protokoly auditu Key Vault a Application Gateway přístup a protokoly brány firewall.
- **Archivace protokolu:**  Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci. Tyto protokoly se připojují k protokolům Azure Monitor pro zpracování, ukládání a vytváření sestav řídicích panelů.

Kromě toho jsou v rámci této architektury nainstalovány následující řešení monitorování. Všimněte si, že zodpovědnost zákazníka při konfiguraci těchto řešení pro zajištění souladu s ovládacími prvky zabezpečení FedRAMP:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Antimalwarové řešení oznamuje stav malwaru, hrozeb a stavu ochrany.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Řešení Azure Automation ukládá, spouští a spravuje Runbooky.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Řídicí panel Security and Audit poskytuje přehled o stavu zabezpečení prostředků tím, že poskytuje metriky pro domény zabezpečení, významné problémy, detekce, analýzu hrozeb a běžné dotazy zabezpečení.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Řešení Update Management umožňuje zákazníkům spravovat aktualizace zabezpečení operačního systému, včetně stavu dostupných aktualizací a procesu instalace požadovaných aktualizací.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Protokoly aktivit Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Change Tracking zákazníkům umožňuje snadno identifikovat změny v prostředí.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhají uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API v prostředcích Azure pro zákazníky.

## <a name="threat-model"></a>Model hrozeb
Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/fedrampWAdfd) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![IaaS webová aplikace pro model FedRAMP Threat](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS webová aplikace pro model FedRAMP Threat")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů

[Tabulka podrobný plán zabezpečení a dodržování předpisů Azurea vysoké zodpovědnosti zákazníka](https://aka.ms/blueprinthighcrm) uvádí všechny ovládací prvky zabezpečení, které vyžadují vysoké základní hodnoty FedRAMP. Tato matice označuje, zda je implementace každého ovládacího prvku zodpovědná za společnost Microsoft, zákazníka nebo sdílenou část mezi nimi.

[Matrice implementace vysokého řízení webové aplikace podrobný plán zabezpečení a dodržování předpisů Azure-FedRAMP](https://aka.ms/blueprintwacim) obsahuje seznam všech ovládacích prvků zabezpečení vyžadovaných vysokým směrným plánem FedRAMP. Tato matice obsahuje informace o tom, které ovládací prvky jsou pokryté architekturou webové aplikace IaaS, včetně podrobného popisu způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="deploy-the-solution"></a>Nasazení řešení

Tato Podrobný plán zabezpečení a dodržování předpisů Azure Automation se skládá z konfiguračních souborů JSON a skriptů PowerShellu, které služba API pro Azure Resource Manager využívá k nasazení prostředků v rámci Azure. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Toto řešení se nasazuje do Azure Government.

#### <a name="quickstart"></a>Rychlý start
1. Naklonujte nebo stáhněte [Toto](https://aka.ms/fedrampblueprintrepo) úložiště GitHub na místní pracovní stanici.

2. Spusťte skript PowerShellu předběžného nasazení: Azure-Blueprint/předdeploy/Orchestration_InitialSetup. ps1.

3. Klikněte na tlačítko níže, přihlaste se k Azure Portal, zadejte požadované parametry šablony ARM a klikněte na **koupit**.

    [![Nasazení do Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je třeba nakonfigurovat tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace IaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="disclaimer"></a>Zřeknutí se práv

- Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.  
- Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.  
- Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.  
- Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.  
- Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
- Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
