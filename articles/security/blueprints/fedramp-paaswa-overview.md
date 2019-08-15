---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure – PaaS pro FedRAMP
description: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure – PaaS pro FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: b74373201db26405653584fc4000993b9d6fdf43
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946828"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: Webová aplikace PaaS pro FedRAMP

## <a name="overview"></a>Přehled

[Federal Risk and Authorization Management program (FedRAMP)](https://www.fedramp.gov/) je USA program pro státní správu, který poskytuje standardizovaný přístup k vyhodnocování zabezpečení, autorizaci a nepřetržitému monitorování cloudových produktů a služeb. Tento Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny pro zajištění architektury Microsoft Azure platformy jako služby (PaaS), která pomáhá implementovat podmnožinu vysoce FedRAMPch ovládacích prvků. Toto řešení poskytuje pokyny k nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky. Sestavujte a nakonfigurujte svá vlastní řešení v Azure.

Tato referenční architektura, implementační Příručky k implementaci a modely hrozeb mají sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostatečné pro zcela splnění požadavků vysokého směrného plánu FedRAMP. Je třeba počítat s následujícím:
- Architektura poskytuje směrný plán, který zákazníkům umožňuje nasazovat úlohy do Azure v souladu s FedRAMP.
- Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje referenční architekturu pro webovou aplikaci v PaaS s Azure SQL Database back-end. Webová aplikace je hostovaná v izolovaném Azure App Service Environment, což je privátní vyhrazené prostředí v datacentru Azure. Zatížení prostředí vyrovnává provoz pro webovou aplikaci napříč virtuálními počítači, které spravuje Azure. Tato architektura zahrnuje taky skupiny zabezpečení sítě, Application Gateway, Azure DNS a Load Balancer. Kromě toho Azure Monitor poskytuje analýzy stavu systému v reálném čase. **Azure doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a import dat do podsítě referenční architektury.**

![Diagram referenční architektury PaaS webové aplikace pro FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "Diagram referenční architektury PaaS webové aplikace pro FedRAMP")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Firewall webových aplikací
        - Režim brány firewall: prevence
        - Sada pravidel: OWASP 3,0
        - Naslouchací proces: port 443
- Virtuální síť Azure
- Skupiny zabezpečení sítě
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Webové aplikace Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje zákazníkům pracovat s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci používají šablonu pro nasazení a tato šablona může fungovat v různých prostředích, jako jsou testování, příprava a produkce. Správce prostředků poskytuje funkce zabezpečení, auditování a označování, které zákazníkům pomohou spravovat prostředky po nasazení.

**App Service Environment v2**: [Azure App Service Environment (POmocného mechanismu)](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku.

Služby ASE jsou izolované jenom tak, aby se spouštěly jenom aplikace jednoho zákazníka a nasadily se vždycky do virtuální sítě. Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikací a aplikace můžou vytvořit vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům.

Použití služby ASE pro tuto architekturu je povolené pro následující ovládací prvky/konfigurace:

- Hostitel v rámci zabezpečeného Virtual Network Azure a pravidel zabezpečení sítě
- Pomocného programu s nakonfigurovaným certifikátem interního nástroje podepsaným svým držitelem pro komunikaci HTTPS
- [Režim interního vyrovnávání zatížení](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Zakázat [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Změnit [šifru TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Řízení [příchozího provozu za sekundu porty](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Firewall webových aplikací – omezení dat](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Povolení [Azure SQL Database provozu](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

Část [doprovodné materiály a doporučení](#guidance-and-recommendations) obsahuje další informace o služby ase.

**Webová aplikace Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostovat webové aplikace v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Azure DevOps nebo libovolného úložiště Git.

### <a name="virtual-network"></a>Virtuální sítě
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Následující skupin zabezpečení sítě existuje:
- 1 NSG pro Application Gateway
- 1 NSG pro App Service Environment
- 1 NSG pro Azure SQL Database

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

**Azure DNS**: Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure DNS podporuje i privátní domény DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat své aplikace a vytvářet vysokou dostupnost služeb. Load Balancer podporuje příchozí i odchozí scénáře a poskytuje nízkou latenci, vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datacentry Azure a z nich. Všechny transakce, které se mají Azure Storage prostřednictvím Azure Portal probíhat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   Azure SQL Database je nakonfigurované na použití [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém znění.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Always Encrypted sloupců](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistí, že se citlivá data v databázovém systému nikdy neobjeví jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [Zabezpečení na úrovni řádků](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) umožňuje uživatelům definovat zásady pro omezení přístupu k datům, aby se zpracování nepokračovalo.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) je možné provést po nasazení referenční architektury. Zákazníci budou muset upravit nastavení dynamického maskování dat tak, aby odpovídalo schématu databáze.

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti správy identit v prostředí Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je víceklientské cloudová služba Microsoftu a služba pro správu identit, která je založená na víceklientské architektuře. Všichni uživatelé pro toto řešení se vytvoří v AAD, včetně uživatelů, kteří přistupují k Azure SQL Database.
-   Ověřování pro aplikaci se provádí pomocí AAD. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kromě toho šifrování sloupce databáze používá Azure Active Directory k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Řízení přístupu na základě role v Azure](../../role-based-access-control/role-assignments-portal.md) umožňuje přesné řízení přístupu pro Azure. Přístup k předplatnému je omezený na správce předplatného a přístup k prostředkům se může omezit na základě role uživatele.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím.  Správci můžou pomocí AAD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace, nakonfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které přijmou vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení
**Správa tajných** kódů Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit data a přístup k těmto datům:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení (HSM). Typ klíče je 32bitový klíč RSA s ochranou 2048.
- Všem uživatelům a identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Application Gateway** Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway s firewallem webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](../../application-gateway/create-ssl-portal.md)
- Zakázat [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](../../application-gateway/waf-overview.md)
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s OWASP 3,0 RuleSet
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastní sondy stavu](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také nabízí systém reputace.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Azure Monitor poskytuje rozsáhlé protokolování systémových a uživatelských aktivit a také stav systému. Shromažďuje a analyzuje data vygenerovaná prostředky v Azure a v místních prostředích.
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall.
- **Archivace protokolu**: Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci. Tyto protokoly se připojují k protokolům Azure Monitor pro zpracování, ukládání a vytváření sestav řídicích panelů.

Kromě toho jsou součástí této architektury Tato řešení monitorování:
-   [Vyhodnocení služby Active Directory](../../azure-monitor/insights/ad-assessment.md): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md): Antimalwarové řešení oznamuje stav malwaru, hrozeb a stavu ochrany.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Řešení Azure Automation ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z Application Insights a Azure SQL Database.
-   [Security and Audit](../../security-center/security-center-intro.md): Řídicí panel Security and Audit poskytuje přehled o stavu zabezpečení prostředků tím, že poskytuje metriky pro domény zabezpečení, významné problémy, detekce, analýzu hrozeb a běžné dotazy zabezpečení.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
-   [Update Management](../../automation/automation-update-management.md): Řešení Update Management umožňuje zákazníkům spravovat aktualizace zabezpečení operačního systému, včetně stavu dostupných aktualizací a procesu instalace požadovaných aktualizací.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Protokoly aktivit Azure](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.
-   [Change Tracking](../../automation/change-tracking.md): Řešení Change Tracking zákazníkům umožňuje snadno identifikovat změny v prostředí.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhají uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API v prostředcích Azure pro zákazníky.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/fedrampPaaSWebAppDFD) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![PaaS webová aplikace pro model FedRAMP Threat](images/fedramp-paaswa-threat-model.png?raw=true "PaaS webová aplikace pro model FedRAMP Threat")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
[Tabulka podrobný plán zabezpečení a dodržování předpisů Azurea vysoké zodpovědnosti zákazníka](https://aka.ms/blueprinthighcrm) uvádí všechny ovládací prvky zabezpečení, které vyžadují vysoké základní hodnoty FedRAMP. Tato matice označuje, zda je implementace každého ovládacího prvku zodpovědná za společnost Microsoft, zákazníka nebo sdílenou část mezi nimi.

[Matrice implementace High Control podrobný plán zabezpečení a dodržování předpisů Azure-FedRAMP PaaS WebApp](https://aka.ms/fedrampPaaSWebAppCIM) obsahuje všechny ovládací prvky zabezpečení, které jsou požadovány vysokými základními hodnotami FedRAMP. Tato matice obsahuje informace o tom, které ovládací prvky jsou pokryté architekturou webové aplikace PaaS, včetně podrobného popisu způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je třeba nakonfigurovat tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace PaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit připojení VPN mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
