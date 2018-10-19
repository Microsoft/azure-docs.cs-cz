---
title: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro FedRAMP
description: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro FedRAMP
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
ms.openlocfilehash: f5ba6a001f8933283e0867367ef7bd8d3918c3fd
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405374"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Zabezpečení Azure a dodržování předpisů: IaaS webové aplikace pro FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) je program státní instituce v USA, který přináší standardizovaný přístup k posuzování zabezpečení, autorizaci a nepřetržitému monitorování cloudových produktů a služby. Toto zabezpečení Azure a dodržování předpisů podrobného plánu automatizace obsahuje pokyny pro nasazení do odpovídajícího FedRAMP infrastruktury jako služby (IaaS) prostředí, který je vhodný pro jednoduchou webovou aplikaci přístupem k Internetu. Toto řešení automatizuje nasazení a konfiguraci prostředků Azure pro běžné referenční architekturu, představením způsobů, ve které zákazníci mohou pokrýt konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky, kteří k sestavení a Nakonfigurujte svoje vlastní řešení v Azure. Toto řešení implementuje podmnožinu ovládací prvky ze směrného plánu FedRAMP High, podle SP NIST 800-53. Další informace o požadavcích FedRAMP a toto řešení, najdete v článku [dodržování předpisů dokumentaci](#compliance-documentation).
> [!NOTE]
> Toto řešení nasadí do Azure Government.

Tento zabezpečení Azure a dodržování předpisů podrobného plánu Automation automaticky nasadí IaaS referenční architektura pro webové aplikace s řízení předem nakonfigurované zabezpečení, které pomáhají zákazníkům zajistit soulad s požadavky programu FedRAMP. Řešení se skládá z šablon Azure Resource Manageru a skripty prostředí PowerShell, které provedou nasazení prostředků a konfigurace.

Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez jakýchkoli úprav nestačí zcela podle požadavků FedRAMP High standardních hodnot. Je třeba počítat s následujícím:
- Tato architektura poskytuje standardní hodnoty pomáhá zákazníkům používat Azure způsobem odpovídajícího FedRAMP.
- Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

Získejte rychlý přehled toho, jak toto řešení funguje, podívejte se na to [videa](https://aka.ms/fedrampblueprintvideo) vysvětlí a předvede jeho nasazení.

Klikněte na tlačítko [tady](https://aka.ms/fedrampblueprintrepo) pokyny pro nasazení.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení nasadí referenční architekturu pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datovou vrstvu, služby Active Directory infrastruktury, Application Gateway a nástroje pro vyrovnávání zatížení. Virtuální počítače nasazené na web a data úrovní, jsou nakonfigurované ve skupině dostupnosti a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používá k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému. Hostitel typu bašta zajišťující Správci nasazení přístup k prostředkům zabezpečené připojení. **Azure doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.**

![IaaS webové aplikace pro FedRAMP diagram referenční architektury](images/fedramp-iaaswa-architecture.png?raw=true "IaaS webové aplikace pro FedRAMP diagram referenční architektury")

Toto řešení používá následující služby Azure. Podrobnosti o architektura nasazení jsou umístěné v [architektura nasazení](#deployment-architecture) oddílu.

- Azure Virtual Machines
    - (1) bastion host (Windows Server 2016 Datacenter)
    - (2) řadič domény active Directory (Windows Server 2016 Datacenter)
    - (2) uzel clusteru SQL serveru (SQL Server 2017 na Windows serveru 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Skupiny dostupnosti
    - (1) řadiče domény active Directory
    - (1) uzly clusteru SQL
    - (1) web/IIS
- Azure Virtual Network
    - (((1) /16 virtuálních sítí
    - (5) /24 podsítě
    - Nastavení DNS jsou nastaveny na obou řadičích domény
- Nástroj pro vyrovnávání zatížení Azure
- Azure Application Gateway
    - (1) Aplikační brána WAF povoleno
        - Režim brány firewall: ochrany před únikem informací
        - Sada pravidel, která: OWASP 3.0
        - Naslouchacího procesu: port 443
- Azure Storage
    - (7) účty geograficky redundantní úložiště
- Disk s kopií cloudu Azure
- Trezor služby Recovery Services
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Log Analytics
- Azure Monitor

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje prvky k vývoji a implementaci.

**Bastion host**: hostitel bastionu je jediný bod položku, která poskytuje zabezpečené připojení pro Správci nasazení přístup k prostředkům. Bastionu hostitele NSG umožňuje připojení pouze na portu TCP 3389 pro protokol RDP. Zákazníci můžou nakonfigurovat další bastionu hostitele pro splnění požadavků pro posilování systému organizace.

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředků v architektuře s podsítí samostatné webové, databáze podsítě, podsíť služby Active Directory a podsítě pro správu ve virtuální síti. Podsítě jsou logicky oddělené použitý pro jednotlivé podsítě k omezení provozu mezi podsítěmi na pouze to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Podrobnosti najdete na konfiguraci pro [skupiny zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazeny s tímto řešením. Zákazníci můžou nakonfigurovat skupiny zabezpečení sítě tak, že upravíte soubor novějšímu pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každé z podsítí má skupina zabezpečení vyhrazené sítě (NSG):
- 1 skupiny zabezpečení sítě pro službu Application Gateway (LBNSG)
- 1 skupina NSG pro bastion host (MGTNSG)
- 1 skupina NSG pro primární a záložní řadiče domény (ADNSG)
- 1 skupina NSG pro servery SQL Server (SQLNSG)
- 1 skupina NSG pro webová vrstva (WEBNSG)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupiny zabezpečení sítě.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém stavu pomocí několika měr šifrování.

**Azure Storage**: abyste splnili požadavky na šifrování dat v klidovém stavu, použijte všechny účty úložiště [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server je nakonfigurován pro použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), který provádí v reálném čase šifrování a dešifrování dat a souborů protokolu k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.

Zákazníci mohou také nakonfigurovat následující bezpečnostní opatření systému SQL Server:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Funkce Always Encrypted sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo serverů aplikace s přístupem ke klíčům k datům ve formátu prostého textu.
-   [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasazuje. Zákazníci, bude nutné upravit nastavení dodržovat svoje schéma databáze maskování dynamických dat.

**Azure Disk Encryption**: Azure Disk Encryption se používá šifrování disků virtuálních počítačů IaaS s Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k poskytování šifrování disků s operačním systémem a daty. Toto řešení je integrovaná se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

### <a name="identity-management"></a>Správa identit

Tyto technologie nabízejí identity možnosti správy v prostředí Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management.
- Ověřování zákazníků nasazené webové aplikace je možné provádět pomocí služby Azure AD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure na základě rolí řízení přístupu (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) umožňuje přesněji zaměřené access management pro službu Azure. Přístup k předplatnému je omezená na správce předplatného a přístup k prostředkům lze omezit na základě role uživatele.
- Nasazená instance služby Active Directory IaaS poskytuje správu identit na úrovni operačního systému pro nasazené virtuální počítače IaaS.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Azure Key Vault pomáhá spravovat klíče pro šifrování disku virtuálního počítače IaaS a tajné kódy pro tuto referenční architekturu.

**Správa oprav**: Windows virtuálních počítačů nasazených v tomto zabezpečení Azure a dodržování předpisů podrobného plánu Automation jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení také nasadí řešení Azure Automation, přes který můžete vytvořit nasazení aktualizací pro nasazení opravy na servery Windows, v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Služba Application Gateway**: architektura omezuje riziko ohrožení zabezpečení pomocí služby Application Gateway s firewallem webových aplikací (WAF) a sady pravidel OWASP povolena. Další možnosti patří:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim WAF)
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) pomocí sady pravidel OWASP 3.0

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: alespoň jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA. Toto řešení nasadí všechny webové vrstvy a dat úroveň virtuálních počítačů v [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují distribuci virtuálních počítačů napříč několika izolovanými hardwarovými clustery pro zlepšení dostupnosti. Kromě toho toto řešení nasadí virtuální počítače systému SQL Server ve skupině dostupnosti jako [skupiny dostupnosti AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Poskytuje funkci dostupnosti skupin Always On pro možnosti vysoké dostupnosti a zotavení po havárii.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače umožňuje rychlejší obnovení.

**Cloud s kopií clusteru**: [disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání ve Windows serveru 2016, která využívá Azure jako arbitrážní bod. Disk s kopií cloudu, stejně jako všechny ostatní určující disky kvora, může hlasovat a účastnit se výpočtů kvora, ale používá standardní veřejně dostupné úložiště objektů Blob Azure. Tím se eliminují režii navíc Údržba virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Log Analytics poskytuje rozsáhlou protokolování systémových a uživatelských aktivit a také stav systému. [Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) řešení shromažďuje a analyzuje data vygenerovaná prostředky v Azure a místním prostředí.

- **Protokoly aktivit:**[protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného.   Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly:**[diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou všechny protokoly, protože ho vygeneroval každý prostředek.   Tyto protokoly patří protokoly událostí systému Windows, protokoly služby Azure storage, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall.
- **Archivace protokolu:** všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci. Tyto protokoly se připojit ke službě Azure Log Analytics pro zpracování, ukládání a vytváření sestav na řídicím.

Kromě toho jsou nainstalovány následující řešení monitorování v rámci této architektury. Všimněte si, že se jedná o odpovědnosti zákazníka ke konfiguraci těchto řešení, aby bylo v souladu s ovládacími prvky zabezpečení FedRAMP:
-   [Posouzení AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
-   [Posouzení antimalwaru](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): The Antimalwarovým řešením hlásí stav malwaru, ohrožení a ochrana.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spouští a spravuje sady runbook.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): řídicí panel zabezpečení a Audit poskytuje podrobný pohled na stav zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýza hrozeb a běžné dotazy na zabezpečení.
-   [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): řešení Update Management umožňuje správu zákazníka aktualizace zabezpečení operačního systému, včetně proces instalace požadovaných aktualizací a stav dostupných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Protokoly aktivit Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení Change Tracking umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v prostředcích zákazníků Azure.

## <a name="threat-model"></a>Model hrozeb
Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/fedrampWAdfd) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace IaaS pro model hrozeb FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS Web Express pro model hrozeb FedRAMP")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů

[Azure zabezpečení a dodržování předpisů – FedRAMP vysokou zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP High směrného plánu. Matice označuje, zda provádění každý ovládací prvek zodpovídá za Microsoft, Zákazník, nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – FedRAMP IaaS webové aplikace vysokou ovládací prvek implementace matice](https://aka.ms/blueprintwacim) vypisuje všechny ovládací prvky zabezpečení vyžadované FedRAMP High směrného plánu. Matice obsahuje informace, na které se vztahují ovládacích prvků IaaS architektura pro webové aplikace, včetně podrobného popisu způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="deploy-the-solution"></a>Nasazení řešení

Tento zabezpečení Azure a dodržování předpisů podrobného plánu Automation se skládá z JSON konfigurační soubory a skripty prostředí PowerShell, které jsou zpracovávány službou rozhraní API Azure Resource Manageru k nasazení prostředků v rámci Azure. Podrobné pokyny jsou k dispozici [tady](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Toto řešení nasadí do Azure Government.

#### <a name="quickstart"></a>Rychlý start
1. Klonovat nebo stáhnout [to](https://aka.ms/fedrampblueprintrepo) úložišti GitHub na váš místní pracovní stanici.

2. Spustit skript prostředí PowerShell před nasazením: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klikněte na tlačítko níže, přihlaste se na webu Azure portal, zadejte požadované parametry šablony ARM a klikněte na tlačítko **nákupní**.

    [![Nasazení do Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektuře webová aplikace IaaS. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-Site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

- Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.  
- Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.  
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.  
- Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.  
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
- Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
