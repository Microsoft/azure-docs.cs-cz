---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - PaaS webové aplikace pro FedRAMP
description: Zabezpečení Azure a dodržování předpisů plán, podle kterého - PaaS webové aplikace pro FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 20aa842fb8168bc28a388c817f4e4eedbdd63ebd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727652"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Zabezpečení Azure a dodržování předpisů plán, podle kterého: PaaS webové aplikace pro FedRAMP

## <a name="overview"></a>Přehled

[Federal rizika a autorizace správu Program (FedRAMP)](https://www.fedramp.gov/) je program government celou Spojených státech amerických, který poskytuje standardizovaná přístup k zabezpečení, autorizace a nepřetržité monitorování pro cloud produkty a služby. Tento zabezpečení Azure a dodržování předpisů plán, podle kterého poskytuje pokyny, jak doručovat na platformě Microsoft Azure jako služba (PaaS) architekturu, která pomáhá implementovat podmnožinu FedRAMP vysokou ovládací prvky. Toto řešení obsahuje pokyny k nasazení a konfigurace prostředků Azure pro běžné referenční architektura, ukázka způsoby, ve kterém můžete zákazníkům splňovat určité požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky vytvořit a nakonfigurovat vlastní řešení v Azure.

Tento referenční architektura, pokyny implementace přidruženého ovládacího prvku a modely threat které slouží jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav je zcela nesplňují požadavky na vysokou FedRAMP standardních hodnot. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán pro pomoc zákazníkům nasazení úloh do Azure kompatibilní se standardem FedRAMP způsobem.
- Zákazníci jsou zodpovědní za provedení příslušné zabezpečení a hodnocení dodržování předpisů řešení vytvořená s využitím této architektury, protože požadavky se mohou lišit podle specifických vlastností implementace každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje referenční architektura pro webové aplikace PaaS s back-end Azure SQL Database. Webová aplikace je hostován v izolované Azure App Service Environment, což je privátní, vyhrazené prostředí v datovém centru Azure. Zatížení prostředí vyrovnává napříč virtuálními počítači Azure spravuje přenosy dat pro webové aplikace. Tato architektura obsahuje také skupiny zabezpečení sítě, Application Gateway, Azure DNS a nástroj pro vyrovnávání zatížení. Kromě toho poskytuje služby Operations Management Suite analýzu v reálném čase stavu systému a zabezpečení. **Azure doporučuje konfiguraci připojení VPN nebo ExpressRoute pro správu a data importovat do podsítě architektura odkaz.**

![PaaS webové aplikace pro diagram architektury FedRAMP odkaz](images/fedramp-paaswa-architecture.png?raw=true) "PaaS webové aplikace pro diagram architektury odkaz FedRAMP"

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení jsou umístěné v [architektura nasazení služby](#deployment-architecture) části.

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) brány Firewall webových aplikací
        - režimu brány firewall: prevence
        - Sady pravidel: OWASP 3.0
        - naslouchací proces: portu 443
- Virtuální síť Azure
- Skupiny zabezpečení sítě
- Azure DNS
- Azure Storage
- Operations Management Suite
- Azure Monitor
- V2 prostředí služby App Service
- Nástroj pro vyrovnávání zatížení Azure
- Webové aplikace Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architektura nasazení služby
V následující části Podrobné informace o nasazení a implementace elementy.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje zákazníkům pracovat s prostředky v řešení jako se skupinou. Zákazníci můžou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci pomocí šablony pro nasazení a tato šablona může fungovat v různých prostředích, jako je například testování, pracovní a provozní. Resource Manager poskytuje zabezpečení, auditování a označování funkce, které pomáhají zákazníkům po nasazení spravovat jejich prostředky.

**App Service Environment v2**: [Azure App Service prostředí řízení,](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce služby App Service, která poskytuje plně izolovaném a vyhrazeném prostředí pro zabezpečené spouštění aplikace služby App Service ve velkém rozsahu.

ASEs jsou izolované běžela jenom jednoho zákazníka aplikace a jsou vždy nasazené do virtuální sítě. Zákazníci mají jemně odstupňovanou kontrolu nad obě aplikace příchozí a odchozí síťový provoz a aplikace může vytvořit vysokorychlostní zabezpečené připojení přes virtuální sítě k firemním prostředkům místně.

Použití ASEs pro této architektury jsou povoleny pro následující ovládací prvky nebo konfigurace:

- Hostování v zabezpečené virtuální síť Azure a pravidel zabezpečení sítě
- App Service Environment nakonfigurovaný s ILB certifikát podepsaný svým držitelem pro komunikaci pomocí protokolu HTTPS
- [Interní režimu Vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Zakázat [protokol TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Změna [šifer TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ovládací prvek [příchozí provoz N/W porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web Application Firewall – omezit Data](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Povolit [provozu databáze SQL Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

[Pokyny a doporučení](#guidance-and-recommendations) část obsahuje další informace o ASEs.

**Webové aplikace Azure**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostování webových aplikací v programovací jazyk, na které si sami vyberou bez Správa infrastruktury. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Visual studio Team Services nebo libovolného úložiště Git.

### <a name="virtual-network"></a>Virtual Network
Architektura definuje privátní virtuální síť s adresním prostorem z 10.200.0.0/16.

**Skupin zabezpečení sítě**: [skupin zabezpečení (Nsg) sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolí nebo zakážou provoz v rámci virtuální sítě. Skupiny Nsg můžete použít k zabezpečení přenosů dat na podsíť nebo jednotlivé úroveň virtuálního počítače. Následující skupiny Nsg existují:
- 1 skupina NSG pro aplikační bránu
- 1 skupina NSG pro služby App Service Environment
- 1 skupina NSG pro databázi Azure SQL

Každý z skupin Nsg mít určité porty a protokoly otevřete tak, aby řešení může fungovat správně a bezpečně. Kromě toho tyto konfigurace jsou povolené pro jednotlivé skupiny NSG:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložený v účtu úložiště
  - Analýzy protokolů OMS je připojený k [NSG na diagnostiky](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: každé podsítě je přidružen jeho odpovídající skupina NSG.

**Azure DNS**: systému názvů domény nebo DNS, zodpovídá za překladu (nebo vyřešení) název webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostování služba domén DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Hostováním domén v Azure, můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jinými službami Azure. Azure DNS podporuje i privátní domén DNS.

**Azure nástroj pro vyrovnávání zatížení**: [nástroj pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat své aplikace a vytvořte vysokou dostupnost pro služby. Nástroj pro vyrovnávání zatížení podporuje scénáře příchozí i odchozí a poskytuje nízkou latencí a vysokou propustnost a škáluje až miliony toků pro všechny aplikace, TCP a UDP.

### <a name="data-in-transit"></a>Přenášených dat
Azure šifruje veškerá komunikace do a z datových centrech Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage prostřednictvím portálu Azure dojít přes HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu prostřednictvím šifrování, databáze, auditování a jiných opatření.

**Úložiště Azure**: ke splnění šifrovaná data na požadavky rest, všechny [Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkce nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**Azure SQL Database**: Azure SQL Database instance používá následující bezpečnostní opatření databáze:
-   [AD ověřování a autorizace](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování databáze SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu přihlášení účtu úložiště Azure.
-   Databáze SQL Azure je nakonfigurovaný na použití [transparentní šifrování šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), která provede v reálném čase šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakci ochrany informací na rest.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabránit veškerý přístup do databázové servery, dokud správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, kdy k nim dojde prostřednictvím výstrah zabezpečení databáze podezřelé aktivity, potenciální ohrožení zabezpečení, prostřednictvím injektáže SQL a nezvyklé databázové přístup vzory.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistit, aby citlivá data nikdy se jako prostý text v databázi systému. Po povolení šifrování dat, data ve formátu prostého textu přístup jenom klientské aplikace nebo aplikační servery s přístupu ke klíčům.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům a ukončí zpracování.
- [Databáze SQL dynamická data maskování](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) lze provést po referenční architektura nasadí. Zákazníci bude nutné upravit dynamická data maskování nastavení řídit jejich schématu databáze.

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí identity možnosti správy v prostředí Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft víceklientské cloudové adresáře a identity management service. Všichni uživatelé v tomto řešení jsou vytvořené v AAD, včetně uživatele, kteří používají databáze SQL Azure.
-   Do aplikace ověřování pomocí AAD. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho sloupce šifrování databáze používá Azure Active Directory k ověřování aplikace do Azure SQL Database. Další informace najdete v tématu Jak [chrání citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Řízení přístupu Azure na základě rolí](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje přesněji správu cílených přístupu k Azure. Předplatné přístup je omezen na správce předplatného, a přístup k prostředkům může být omezen na základě role uživatele.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace.  Správci můžou pomocí AAD Privileged Identity Management zjistit, omezte a monitorujte privilegované identity a jejich přístup k prostředkům. Tato funkce slouží také k vynucení na vyžádání, za běhu správy přístupu v případě potřeby.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akcí souvisejících s identity organizace a prověří podezřelé incidenty uživateli proveďte příslušné akce jejich řešení.

### <a name="security"></a>Zabezpečení
**Tajné klíče správu** toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných klíčů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce Azure Key Vault pomoc zákazníkům ochrana dat a přístupu na tyto údaje:
- Konfigurace zásad rozšířeného přístupu na základě potřeba.
- Minimální požadovaná oprávnění k klíče a tajné klíče jsou definovány zásad přístupu k trezoru klíč.
- Všechny klíče a tajné klíče v Key Vault mít data vypršení platnosti.
- Všechny klíče v trezoru klíče jsou chráněny modulů specializované hardwarového zabezpečení (HSM). Typ klíče je klíč RSA HSM chráněné 2048 bitů.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Diagnostické protokoly Key Vault je povolená s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací pro klíče jsou omezeny na ty, které jsou potřeba.

**Aplikační brána** architekturu snižuje riziko ohrožení zabezpečení služby Application Gateway pomocí brány Firewall webových aplikací a ruleset OWASP povolena. Další možnosti patří:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Brány Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Prevence režimu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s ruleset OWASP 3.0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Testy vlastní stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytovat další ochranu a oznámení. Azure Security Center nabízí také ztrátou dobré pověsti systému.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Služby Operations Management suite poskytuje rozsáhlé protokolování systému a uživatelské aktivity a také stav systému. Služby Operations Management suite [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) řešení shromažďuje a analyzuje data generována prostředky ve službě Azure a místní prostředí.
- **Protokoly aktivity**: [protokoly aktivity](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) získat přehled o operace provedené na prostředky v předplatném. Protokoly aktivity vám mohou pomoci určit operaci iniciátor čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnují všechny protokoly vygenerované každých prostředkem. Tyto protokoly patří protokoly událostí systému Windows, protokolů Azure Storage, protokoly Key Vault auditu a protokolů přístup a brány firewall Application Gateway.
- **Archivace protokolu**: všechny diagnostické protokoly zápisu na účet úložiště Azure centralizovaný a šifrované pro archivaci. Uchovávání je uživatelsky konfigurovatelného, až do 730 dní, aby splňoval požadavky organizace specifické uchování. Tyto protokoly se připojit k analýze protokolů Azure pro zpracování, ukládání a vytváření sestav řídicího panelu.

Kromě toho jsou zahrnuty jako součást této architektury následující řešení Operations Management suite:
-   [Služby Active directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Služba Active Directory kontroly stavu řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje seznam doporučení, které jsou specifické pro infrastrukturu nasazené serveru seřazený podle priority.
-   [Hodnocení antimalwarových](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): antimalwarových řešení hlásí stav malwaru, hrozby a ochrany.
-   [Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): řešení Azure Automation ukládá, spustí a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby Application Insights a Azure SQL Database.
-   [Zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): zabezpečení a Audit řídicí panel poskytuje podrobný přehled o stavu zabezpečení prostředků tím, že poskytuje metriky na zabezpečení domény, významné problémy, detekce, analýzou hrozeb a běžné dotazy zabezpečení.
-   [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL stavu zkontrolujte řešení vyhodnocuje stav serveru prostředí a riziko v pravidelných intervalech a poskytuje zákazníkům doporučení, které jsou specifické pro infrastrukturu nasazené serveru, jejichž seznam je seřazený podle priority.
-   [Správa aktualizací](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Správa aktualizace řešení umožňuje správu zákazníka aktualizací zabezpečení operačního systému, včetně stavu k dispozici aktualizace a proces instalace požadovaných aktualizací.
-   [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): agenta stavu řešení sestavy jak mnoho agentů, kteří jsou nasazené a jejich zeměpisném rozložení, a také kolik agenty, kteří jsou reagovat a počtu agentů, které jsou odesílání provozních dat.
-   [Azure protokoly aktivity](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): řešení analýzy protokolů aktivity pomáhá analýzy protokolů Azure aktivity ve všech předplatných Azure pro zákazníka.
-   [Sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking): Změna sledování řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure monitorování**
[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu a zachování zabezpečení, identifikovat trendy tím, že umožňuje organizacím audit, vytvářet výstrahy a archivaci dat, včetně sledování volání rozhraní API v Azure prostředky zákazníků.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/fedrampPaaSWebAppDFD) nebo naleznete níže. Tento model může pomoct pochopit body potenciální riziko v infrastruktuře systému při provádění změn zákazníků.

![PaaS webové aplikace pro model hrozeb FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "PaaS Web Express pro model hrozeb FedRAMP")

## <a name="compliance-documentation"></a>Dokumentace dodržování předpisů
[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP vysoké zákazníka odpovědnost matice](https://aka.ms/blueprinthighcrm) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice označuje, zda implementace každý ovládací prvek zodpovídá Microsoft, zákazník nebo sdílené mezi nimi.

[Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP PaaS WebApp vysoké řízení implementace matice](https://aka.ms/fedrampPaaSWebAppCIM) uvádí všechny kontrolní mechanismy zabezpečení, které vyžadují vysokou FedRAMP směrného plánu. Matice poskytuje informace, na kterém se ovládací prvky předmětem architektuře PaaS webové aplikace, včetně podrobného popisu způsobu implementace splňuje požadavky na každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí nakonfigurovat tak, aby bezpečně navázat připojení k prostředkům nasazen jako součást tuto referenční architekturu PaaS webové aplikace. Zákazníci správně nastavení sítě VPN nebo ExpressRoute, můžete přidejte vrstvu ochrany dat během přenosu.

Implementací přes zabezpečené tunelové propojení sítě VPN s Azure, lze vytvořit připojení VPN mezi místní sítí a virtuální síti Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunel" informací uvnitř šifrované odkaz mezi sítí a Azure zákazníka. Site-to-Site VPN je zabezpečený, Vyspělá technologie, která byla nasazena podniky všech velikostí pro dekád. [Režimu tunelového připojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá v této možnosti jako mechanismus šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě lepšímu zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místní umístění nebo poskytovatele hostingu serveru Exchange. Jako připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než Typická připojení přes Internet. Kromě toho protože to je přímé připojení poskytovatele telekomunikace zákazníka, data není přenášet přes Internet a proto nebude vystavena k němu.

Osvědčené postupy pro implementaci zabezpečeného hybridní síti, která rozšiřuje do místní sítě do Azure jsou [k dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument je pouze informativní charakter. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ INFORMACE V TOMTO DOKUMENTU. Tento dokument je poskytován "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a dalších odkazů na internetové weby mohou změnit bez předchozího upozornění. Zákazníci čtení tohoto dokumentu na sebe rizika spojená s jejím používáním.
 - Tento dokument neposkytuje žádná zákonná práva týkající se všech produktů společnosti Microsoft nebo řešení duševního vlastnictví zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může mít za následek vyšší data, síťové nebo využití výpočetních prostředků v Azure a může zvýšit náklady Azure licence nebo předplatné zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit na jejich specifické požadavky a by se neměla používat jako-je v produkčním prostředí.
 - Tento dokument vyvinutý jako odkaz a nesmí se použít k definování všechny prostředky, které může zákazník splňovat požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci se měli obrátit právní podpory z jejich organizace na implementace schválené zákazníka.
