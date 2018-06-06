---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - IaaS webové aplikace pro FedRAMP
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - IaaS webové aplikace pro FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 27346d8e2133ea61056817fb48050a4e5c8c3c97
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726478"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: IaaS webové aplikace pro FedRAMP

## <a name="overview"></a>Přehled

[Federal rizika a autorizace správu Program (FedRAMP)](https://www.fedramp.gov) je program government celou USA, který poskytuje standardizovaná přístup k zabezpečení, autorizace a nepřetržité monitorování produktů cloudu a služby. Tento zabezpečení Azure a dodržování předpisů plán, podle kterého automatizace obsahuje pokyny pro nasazení kompatibilní se standardem FedRAMP infrastruktury jako služby (IaaS) prostředí, který je vhodný pro jednoduchou webovou aplikaci internetové brány. Toto řešení umožňuje automatizovat nasazení a konfigurace prostředků Azure pro běžné referenční architektura, ukázka způsoby, ve kterém zákazníkům můžete splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníkům umožňují vytvářet a Nakonfigurujte vlastní řešení v Azure. Řešení implementuje podmnožinu ovládacích prvků ze směrného plánu FedRAMP vysoká, podle SP NIST 800-53. Další informace o požadavcích FedRAMP a řešení najdete v tématu [dodržování předpisů dokumentaci](#compliance-documentation).
> [!NOTE]
> Toto řešení se nasadí do Azure Government.

Tento zabezpečení Azure a dodržování předpisů plán, podle kterého automatizace automaticky nasadí IaaS webové aplikace referenční architektura s ovládacími prvky pro zákazníky, dosažení souladu s požadavky FedRAMP předem nakonfigurovaný zabezpečení. Řešení se skládá z šablon Azure Resource Manageru a skriptů prostředí PowerShell, které průvodce prostředků nasazení a konfigurace.

Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostatečná pro úplně splnění požadavků FedRAMP vysokou standardních hodnot. Je třeba počítat s následujícím:
- Tato architektura poskytuje směrný plán pro zákazníky, používat Azure kompatibilní se standardem FedRAMP způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

Pro rychlý přehled o tom, jak toto řešení funguje, si pusťte toto [video](https://aka.ms/fedrampblueprintvideo) vysvětlením a předvedení jeho nasazení.

Klikněte na tlačítko [sem](https://aka.ms/fedrampblueprintrepo) pokyny pro nasazení.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení nasadí referenční architektura pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datové vrstvy, služby Active Directory infrastruktury, aplikační brány a nástroj pro vyrovnávání zatížení. Virtuální počítače nasazené na úrovně web a data jsou nakonfigurované ve skupině dostupnosti a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používají k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Bastionu hostitel poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. **Azure doporučuje konfiguraci připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě architektura odkaz.**

![IaaS webové aplikace pro diagram architektury odkaz FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "IaaS webové aplikace pro diagram architektury FedRAMP odkaz")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

- Azure Virtual Machines
    - (1) bastionu hostitele (Windows Server 2016 Datacenter)
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
- Určující cloudu Azure
- Trezor služby Recovery Services
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Služby Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>Architektura nasazení služby

V následující části Podrobné informace o vývoji a implementaci elementy.

**U chráněných hostitelů**: hostitel bastionu je jediný bod položku, která poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. Bastionu NSG hostitele umožňuje připojení pouze na TCP port 3389 pro protokol RDP. Zákazníci mohou nakonfigurovat další bastionu hostitele pro splnění požadavků pro posilování systému organizace.

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem z 10.200.0.0/16.

**Skupin zabezpečení sítě**: Toto řešení nasazuje prostředky v architekturu s podsíť samostatný webový, databáze podsíť, podsíť služby Active Directory a podsítě správu uvnitř virtuální sítě. Podsítě jsou logicky odděleny použít na jednotlivé podsítě omezit přenos dat mezi podsítě, které jenom to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Najdete v tématu Konfigurace [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazení s tímto řešením. Zákazníci mohou nakonfigurovat skupiny zabezpečení sítě úpravou souboru nad pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každý podsítí má skupina zabezpečení vyhrazené sítě (NSG):
- 1 skupina NSG pro aplikační brána (LBNSG)
- 1 skupina NSG pro hostitele bastionu (MGTNSG)
- 1 skupina NSG pro primární a záložní řadiče domény (ADNSG)
- 1 skupina NSG pro servery SQL Server (SQLNSG)
- 1 skupina NSG pro webová vrstva (WEBNSG)

**Podsítě**: každé podsítě je přidružen jeho odpovídající skupina NSG.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém stavu pomocí několika míry šifrování.

**Úložiště Azure**: Chcete-li splnit požadavky na šifrování dat na rest, použijte všechny účty úložiště [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), která provede v reálném čase šifrování a dešifrování dat a souborů protokolu ochrany informací v klidovém stavu. Šifrování TDE poskytuje záruku, že data uložena nebyla neoprávněnému přístupu k.

Zákazníci mohou také nakonfigurovat následující bezpečnostní opatření systému SQL Server:
-   [AD ověřování a autorizace](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   [Pravidla brány firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby citlivá data nikdy se jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
-   [Databáze SQL dynamická data maskování](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasadí. Zákazníci bude nutné upravit dynamická data maskování nastavení řídit jejich schématu databáze.

**Azure Disk Encryption**: Azure Disk Encryption slouží šifrované disky virtuálních počítačů IaaS ve Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku operačního systému a dat disků. Řešení jsou integrované s Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

### <a name="identity-management"></a>Správa identit

Tyto technologie nabízejí identity možnosti správy v prostředí Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service.
- Lze provést ověření do zákazníka nasazené webové aplikace pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji správu cílených přístupu k Azure. Předplatné přístup je omezen na správce předplatného, a přístup k prostředkům může být omezen na základě role uživatele.
- Nasazenou instanci služby Active Directory IaaS poskytuje identity management na úrovni operačního systému pro nasazené virtuální počítače IaaS.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Azure Key Vault pomáhá spravovat klíče pro šifrování disku virtuálního počítače IaaS a tajné klíče pro tuto referenční architekturu.

**Opravy správy**: Windows virtuálních počítačů nasazených v tomto zabezpečení Azure a dodržování předpisů plán, podle kterého Automation jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení taky nasadí řešení Azure Automation, pomocí kterého lze vytvořit nasazení aktualizací na servery Windows v případě potřeby nasadit opravy.

**Ochrana proti malwaru**: [Antimalware od Microsoftu](https://docs.microsoft.com/azure/security/azure-security-antimalware) u virtuálních počítačů poskytuje možnost ochrany v reálném čase, pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software, se dají konfigurovat výstrahy Když známé škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit ve chráněných virtuálních počítačů.

**Aplikační brána**: architektura snižuje riziko ohrožení zabezpečení služby Application Gateway pomocí brány firewall webových aplikací (firewall webových aplikací) a OWASP ruleset povolené. Další možnosti patří:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brány firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (firewall webových aplikací režim)
- [Prevence režimu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s ruleset OWASP 3.0

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: během plánované i neplánované údržby, splňuje podmínku 99.95 % je k dispozici alespoň jeden virtuální počítač Azure SLA. Řešení nasadí všechny webovou vrstvu a virtuálních počítačů ve vrstvě dat [sadu dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti Ujistěte se, že virtuální počítače distribuované ve více clusterů izolované hardwaru ke zlepšení dostupnosti. Kromě toho toto řešení nasazuje virtuální počítače systému SQL Server v skupiny dostupnosti jako [skupiny dostupnosti AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Poskytuje funkci dostupnosti skupin Always On pro vysokou dostupnost a zotavení po havárii možnosti.

**Trezor služeb zotavení**: [trezoru služeb zotavení](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) ve uložený zálohovaná data a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služeb zotavení zákazníkům obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače povolením rychlejší obnovení.

**Cloud určující**: [cloudu určující](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání v systému Windows Server 2016, která využívá Azure jako arbitrážního bod. Získá hlas určující cloudu, stejně jako všechny ostatní určující disk kvora a můžete podílet na výpočty kvora, ale používá standardní veřejně dostupné úložiště objektů Blob Azure. Tím se eliminuje režijní náklady navíc údržby hostované ve veřejném cloudu virtuálních počítačů.

### <a name="logging-and-auditing"></a>Protokolování a auditování

OMS poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. OMS [analýzy protokolů](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.

- **Protokoly aktivity:**[protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném. Protokoly aktivity vám mohou pomoci určit operaci iniciátor čas výskytu a stav.
- **Diagnostické protokoly:**[diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly vygenerované každých prostředkem. Tyto protokoly zahrnují protokoly událostí systému Windows, protokoly úložiště Azure, protokoly Key Vault auditu a protokolů Application Gateway přístup a brány firewall.
- **Archivace protokolu:** všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci. Uchovávání je uživatelsky konfigurovatelného, až do 730 dní, aby splňoval požadavky organizace specifické uchování. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho následující řešení OMS jsou nainstalované jako součást této architektury. Všimněte si, že se jedná o odpovědnost zákazníka ke konfiguraci těchto řešení, abyste se přiblížili FedRAMP ovládacích prvků zabezpečení:
-   [Hodnocení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure monitorování**
[Azure monitorování](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu a zachování zabezpečení, identifikovat trendy tím, že umožňuje organizacím audit, vytvářet výstrahy a archivaci dat, včetně sledování volání rozhraní API v Azure prostředky zákazníků.

## <a name="threat-model"></a>Model hrozeb
Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/fedrampWAdfd) nebo naleznete níže. Tento model může pomoct pochopit body potenciální riziko v infrastruktuře systému při provádění změn zákazníků.

![IaaS webové aplikace pro model hrozeb FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS Web Express pro model hrozeb FedRAMP")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP vysoké zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice označuje, zda implementace každý ovládací prvek zodpovídá Microsoft, zákazník nebo sdílené mezi nimi.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP IaaS webové aplikace vysoké řízení implementace matice](https://aka.ms/blueprintwacim) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice poskytuje informace, na kterém se ovládací prvky předmětem architektuře IaaS webové aplikace, včetně podrobného popisu způsobu implementace splňuje požadavky na každého zahrnutého ovládacího prvku.

## <a name="deploy-the-solution"></a>Nasazení řešení

Tento zabezpečení Azure a dodržování předpisů automatizace plán, podle kterého se skládá z JSON konfigurační soubory a skripty prostředí PowerShell, které jsou zpracovávány službou rozhraní API Správce Azure Resource Manager k nasazení prostředků v rámci Azure. Podrobné pokyny jsou k dispozici [zde](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Toto řešení se nasadí do Azure Government.

#### <a name="quickstart"></a>Rychlý start
1. Klonovat nebo stáhnout [to](https://aka.ms/fedrampblueprintrepo) úložiště GitHub do místní pracovní stanici.

2. Spustit skript prostředí PowerShell před nasazením: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klikněte na tlačítko níže, přihlaste se k portálu Azure, zadejte požadované parametry šablony ARM a klikněte na **nákupu**.

    [![Nasazení do Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
