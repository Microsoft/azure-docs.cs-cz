---
title: Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci pro SP NIST 800-171
description: Zabezpečení Azure a dodržování předpisů – PaaS webové aplikace podle NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 8411e9d2d4118c4e1f656b5bc6e9dafb311aeb26
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391750"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Zabezpečení Azure a dodržování předpisů – PaaS webovou aplikaci pro speciální publikace NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny, které pomáhá zákazníkům nasadit platformu jako službu (PaaS) webové aplikace v Azure, která implementuje podmnožinu NIST SP 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů a slouží jako základ pro zákazníky, kteří pro sestavení a nakonfigurovat vlastní webovou aplikaci v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky a nepoužívali-je v produkčním prostředí. Nasazení této architektury bez jakýchkoli úprav není dostatečná k SP NIST 800-171 zcela požadavkům. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty

Toto zabezpečení Azure a dodržování předpisů poskytuje referenční architektura pro webové aplikace PaaS s back-endu Azure SQL Database. Webová aplikace je hostován v izolovaném Azure aplikace služby prostředí, což je privátním a vyhrazeném prostředí v datovém centru Azure. Prostředí rozloží provoz pro webovou aplikaci ve virtuálních počítačích, které spravuje Azure. Tato architektura také zahrnuje skupiny zabezpečení sítě, Application Gateway, Azure DNS a nástroje pro vyrovnávání zatížení.

Pro rozšířené analýzy a generování sestav lze nastavit s indexy columnstore Azure SQL Database. Azure SQL Database můžete vertikálně navýšit nebo snížit nebo úplně vypne v reakci na využití ze strany zákazníků. Veškerý provoz SQL je šifrován pomocí protokolu SSL pomocí zahrnutí certifikáty podepsané svým držitelem. Jako osvědčený postup doporučuje Azure používat důvěryhodné certifikační autority nabízí vyšší zabezpečení.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci datacentra vybraného zákazníka pro odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že data budou replikovat do sekundárního datacentra stovky mil okamžitě a znovu uložené jako tři kopie v rámci stejné datové centrum, brání což vede ke ztrátě nepříznivých událostí ve primárního datového centra zákazníka data.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory, řízení přístupu na základě role se používá pro řízení přístupu k nasazené prostředky, včetně jejich klíčů ve službě Azure Key Vault. V nástroji Azure Monitor se monitoruje stav systému. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Azure SQL Database je obecně spravovat pomocí SQL Server Management Studio, který se spouští z místního počítače nakonfigurovaná pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute.

Kromě toho Application Insights poskytuje správu výkonu aplikací reálném čase a analytics prostřednictvím Log Analytics. **Microsoft doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a data importovat do podsítě referenční architektury.**

![Webová aplikace PaaS pro diagram referenční architektury SP NIST 800-171](images/nist171-paaswa-architecture.png "PaaS webovou aplikaci pro SP NIST 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další podrobnosti najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Azure Virtual Machines
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (((1) /16 sítě
    - (4) /24 sítě
    - (4) skupiny zabezpečení sítě
- Application Gateway
    - Brána firewall webových aplikací
        - Režim brány firewall: ochrany před únikem informací
        - Sada pravidel, která: OWASP
        - Port naslouchacího procesu: 443
- Application Insights
- Azure Active Directory
- Aplikace Azure Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Nástroj pro vyrovnávání zatížení Azure
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Webové aplikace Azure

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Azure Resource Manageru**: [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje zákazníkům pracovat s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci pomocí šablony pro nasazení a tato šablona může fungovat v různých prostředích, jako je například testovacím, přípravném nebo produkčním prostředí. Resource Manager poskytuje zabezpečení, auditování a označování příznaky funkcí, které pomáhá zákazníkům spravovat svoje prostředky po nasazení.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy (RDP), musí být definován ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby pověření a dalších tajných kódů je spouštět v chráněném prostředí, která je oddělená od operačního systému

**Služba Azure Web Apps**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostovat webové aplikace v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Visual studio Team Services nebo libovolného úložiště Git.

**Aplikace Service Environment v2**: [prostředí služby Azure Application](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce služby App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací služby App Service na velké škálování.

Prostředí aplikace služby je izolovaná na spustit jenom jednu aplikaci a vždy je nasazený do virtuální sítě. Tato izolace funkce umožňuje referenční architektura mít izolaci kompletní klientů, jeho odebrání z prostředí Azure pro více tenantů. Zákazníci mají podrobnou kontrolu nad síťovými přenosy v obou vstupní a výstupní aplikace a aplikace můžete vytvářet vysokorychlostní zabezpečená připojení prostřednictvím virtuálních sítí a místním podnikovým prostředkům. Zákazníci můžou "škálování" s prostředím služby aplikace na základě zatížení metriky, k dispozici rozpočtu nebo předem definovaného plánu.

Následující ovládací prvky/konfigurace umožňuje použití prostředí služeb aplikace pro tuto architekturu:

- Hostování v zabezpečené virtuální síti Azure a pravidel zabezpečení sítě
- Podepsaný svým držitelem interního nástroje pro vyrovnávání certifikát pro komunikaci přes protokol HTTPS. Jako osvědčený postup společnost Microsoft doporučuje použití důvěryhodné certifikační autority nabízí vyšší zabezpečení.
- [Interní režim vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (režim 3)
- Zakázat [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Změna [šifer TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ovládací prvek [příchozí provoz N/W porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall webových aplikací – omezení dat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Povolit [provoz Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

### <a name="virtual-network"></a>Virtual Network
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
- Skupina zabezpečení sítě 1 pro službu Application Gateway
- Skupina zabezpečení sítě 1 pro aplikaci Service Environment
- Skupina zabezpečení sítě 1 pro službu Azure SQL Database
- Skupina zabezpečení sítě 1 pro bastion host

Skupiny zabezpečení sítě mají určité porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro každou skupinu zabezpečení sítě:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Log Analytics je připojen k [diagnostiky skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupinu zabezpečení sítě.

**Azure DNS**: Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba určená pro domény DNS, která nabízí řešení názvů pomocí infrastruktury Azure. Hostování domény v Azure, uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturačních podmínek jako u ostatních služeb Azure. Azure DNS podporuje také privátní DNS domény.

**Nástroj Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat svoje aplikace a poskytovat vysokou dostupnost služeb. Nástroj pro vyrovnávání zatížení podporuje další scénáře využití příchozí i odchozí a poskytuje nízkou latenci a vysokou propustnost a škálování až na úrovni milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Všechny transakce do služby Azure Storage na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené SP NIST 800-171.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows, zajišťuje šifrování pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   Azure SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá při snížení přístup tak, aby citlivá data neexistuje databáze prostřednictvím před neoprávněným přístupem. **Pro úpravu nastavení dodržovat svoje schéma databáze maskování dynamických dat zodpovídají zákazníci.**

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří v Azure Active Directory, včetně uživatele, kteří používají Azure SQL Database.
-   Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure Active Directory k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každý uživateli neomezený přístup pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k prostředkům a datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace.  Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče není že chráněný 2048bitový klíč RSA modulu hardwarového zabezpečení.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

**Azure Application Gateway**: architektura snižuje riziko ohrožení zabezpečení pomocí služby Azure Application Gateway nakonfigurovat firewall webových aplikací a pravidel OWASP povolena. Další možnosti patří:

- [Koncové k ukončení SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim ochrany před únikem informací)
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) pomocí sady pravidel OWASP 3.0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastních testů stavu paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také poskytuje systém pověst.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly služby Azure Storage, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uchovávání je uživatelem konfigurovatelné, až do 730 dnů pro splnění požadavků na uchovávání specifické pro organizaci.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy v pracovním prostoru Operations Management Suite, což umožňuje společnou analýzu bez ohledu na jejich původní zdroj všechna data. Kromě toho Azure Security Center umožňuje integrací s Log Analytics a umožňuje zákazníkům používat dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby Azure SQL Database. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba správy výkonu aplikací webovým vývojářům na více platformách. Detekuje anomálie výkonu Application Insights a zákazníci můžou použít ho k monitorování živé webové aplikace. Obsahuje výkonné analytické nástroje pomáhá zákazníkům diagnostikovat problémy a pochopit, co uživatelé dělají s jejich aplikací. To&#39;s zákazníkům průběžně vylepšovat výkon a použitelnost.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-paaswa-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace PaaS pro model hrozeb SP NIST 800-171](images/nist171-paaswa-threat-model.png "PaaS webovou aplikaci pro model hrozeb SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 PaaS webové aplikace ovládací prvek implementace matice](https://aka.ms/nist171-paaswa-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky se tak vyřeší, podle architektury webové aplikace PaaS, včetně podrobného popisu způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektury PaaS webové aplikace. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost jiný, ještě větší zabezpečení připojení. Azure ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
