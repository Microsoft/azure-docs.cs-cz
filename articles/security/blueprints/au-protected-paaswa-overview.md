---
title: Zabezpečení Azure a dodržování předpisů – chráněné PaaS webovou aplikaci pro Austrálii
description: Zabezpečení Azure a dodržování předpisů – chráněné PaaS webovou aplikaci pro Austrálii
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/16/2018
ms.author: meladie
ms.openlocfilehash: a8d2eca785ad166aa4cff26bce876e41770a3427
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246094"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Zabezpečení Azure a dodržování předpisů – chráněné PaaS webovou aplikaci pro Austrálii

## <a name="overview"></a>Přehled

Toto zabezpečení Azure a dodržování předpisů poskytuje pokyny pro nasazení platforma jako služba (PaaS) prostředí, který je vhodný pro shromažďování, ukládání a načítání vlády Austrálie CHRÁNĚNÁ data, která je v souladu s cíli Australské státní správy informace zabezpečení ruční (ISM) vytvořen australské signály ředitelství (ASD). Tento podrobný plán běžné referenční architektura představuje sadu a pomáhá předvést správné zpracování citlivých státní správy dat v prostředí zabezpečené, kompatibilní a úrovně.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří na svých vlastních procesů akreditace plánování a systému, které provádějí pomáhají zákazníkům nasadit úlohy o velikosti do Azure způsobem ASD nedodržující předpisy. Zákazníci můžou rozhodnout pro implementaci Azure VPN Gateway nebo ExpressRoute používat federované služby a integraci místních prostředků s prostředky Azure. Zákazníkům musíte zvážit důsledky zabezpečení pomocí místních prostředků. Další konfigurace je potřeba ke splnění všech požadavků, jak se můžou lišit v závislosti na konkrétních podrobnostech provádění jednotlivých zákazníků.

Dosažení shodě ASD vyžaduje, že posuzováním registrované bezpečnostní informace auditování systému. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto řešení poskytuje referenční architektura pro webové aplikace PaaS s back-endu Azure SQL Database. Webové aplikace je hostována v izolované Azure App Service Environment, což je privátním a vyhrazeném prostředí v Azure datové centrum. Prostředí rozloží provoz pro webovou aplikaci ve virtuálních počítačích, které spravuje Azure. Všechna webová připojení aplikace vyžadují protokol TLS s minimální verze 1.2. Tato architektura také zahrnuje skupiny zabezpečení sítě, Application Gateway, Azure DNS a nástroje pro vyrovnávání zatížení.

Architektura dokáže poskytovat zabezpečené hybridní prostředí, které rozšiřuje místní síť do Azure, umožňuje webové úlohy bezpečně přistupovat ve firemní uživatelé v organizaci privátní místní síti nebo z Internetu. Pro místní řešení je zákazník agilně a zodpovědná za všechny aspekty zabezpečení, operací a dodržování předpisů.

Prostředky Azure, které jsou součástí tohoto řešení můžete připojit k místní síti nebo prostředími datových center společně umístěného zařízení (např. CDC v Canbeře) prostřednictvím IPSec VPN pomocí brány sítě VPN a ExpressRoute. Rozhodnutí ohledně využití sítě VPN se má počítat s klasifikací velikost přenášených dat a síťová cesta v úvahu. Zákazníci, kteří používají ve velkém měřítku, zvažte nejdůležitější úlohy s velkými objemy dat požadavky hybridní síťové architektury pomocí služby ExpressRoute pro privátní síťové připojení ke službám Azure. Odkazovat [pokyny a doporučení](#guidance-and-recommendations) části Další podrobnosti o mechanismů připojení k Azure.

Federace se službou Azure Active Directory by měla sloužit k uživatelům umožnit ověřování pomocí místních přihlašovacích údajů a přístup ke všem prostředkům v cloudu pomocí Active Directory Federation Services na místní infrastrukturu. Active Directory Federation Services může poskytnout identitu zjednodušenou a zabezpečenou federaci a webové jednotné možnosti přihlašování pro toto hybridní prostředí. Odkazovat [pokyny a doporučení](#guidance-and-recommendations) části Další podrobnosti o nastavení služby Azure Active Directory.

Toto řešení využívá Azure Storage účty, které zákazníci můžou nakonfigurovat důvěrnost dat v klidovém stavu pomocí šifrování služby Storage. Azure uloží tři kopie dat v rámci vybrané oblasti zákazníka pro odolnost proti chybám. Oblasti Azure, které jsou nasazené ve spárovaných oblastech odolné, a geografické redundantní úložiště zajišťuje, že se data replikují do druhé oblasti se také tři kopie. To brání nežádoucí událost v umístění primární datové zákazníka, čímž dojde ke ztrátě dat.

Pro zvýšení zabezpečení všechny prostředky Azure v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Řízení přístupu na základě role Azure Active Directory se používá pro řízení přístupu k prostředkům a nasadit klíče ve službě Azure Key Vault. Stav systému je monitorovaný pomocí Azure Security Center a Azure Monitor. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet. Azure Application Gateway je nakonfigurovaný jako brána firewall v režimu ochrany před únikem informací a zakazuje provoz, který není TLSv1.2 nebo vyšší. Toto řešení využívá Azure aplikace Service Environment v2 k izolaci webové vrstvy v prostředí s více tenanty.

![PaaS webovou aplikaci pro chráněné AU referenční architekturu](images/au-protected-paaswa-architecture.png?raw=true "PaaS webovou aplikaci pro Diagram chráněné AU referenční architektury")

Toto řešení používá následující služby Azure. Další podrobnosti najdete v [architektura nasazení](#deployment-architecture) oddílu.

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
- Azure Virtual Network
    - (1) /16 sítě
    - (4) /24 sítě
    - Skupiny zabezpečení sítě
- Skupiny zabezpečení sítě
- Trezor služby Recovery Services
- Webové aplikace Azure

Tento podrobný plán obsahuje služby Azure, které nebyly certifikovány pro použití v chráněném klasifikace australské Kybernetických zabezpečení centrum (ACSC). Společnost Microsoft doporučuje, že zákazníci zkontrolujte publikované zabezpečení a sestav auditování související s těmito službami Azure a použití jejich framework správy rizik k určení, zda je vhodná pro jejich interní akreditace a použití ve službě Azure Chráněné klasifikace.

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

**App Service Environment v2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce služby App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací služby App Service ve velkém měřítku.

Služby App Service Environment jsou samostatný, aby se spustily jenom aplikace jednoho zákazníka a vždycky jsou nasazené do virtuální sítě. Zákazníci mají podrobnou kontrolu nad síťovými přenosy v obou vstupní a výstupní aplikace a aplikace můžete vytvářet vysokorychlostní zabezpečená připojení prostřednictvím virtuálních sítí a místním podnikovým prostředkům.

Povolit používání služby App Service Environment pro tuto architekturu pro následující ovládací prvky/konfigurace:

- Služby App Service Environment musí být nakonfigurován pro použití plán Isolated
    - Konfigurace různých prostředích App Service pro aplikace na různé klasifikace
- Hostování v zabezpečené virtuální síti Azure a pravidel zabezpečení sítě
- Služby App Service Environment nakonfigurovanou certifikát podepsaný svým držitelem interního nástroje pro vyrovnávání pro komunikaci přes protokol HTTPS. Jako osvědčený postup společnost Microsoft doporučuje použití důvěryhodné certifikační autority nabízí vyšší zabezpečení.
- [Interní režim vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (režim 3)
- Zakázat [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Změna [šifer TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ovládací prvek [příchozí provoz N/W porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall webových aplikací – omezení dat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Povolit [provoz Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Služba Azure Web Apps**: [Azure Web Apps](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostovat webové aplikace v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Visual studio Team Services nebo libovolného úložiště Git.

### <a name="virtual-network"></a>Virtual Network
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě slouží k zabezpečení provozu na úrovni jednotlivých virtuálních počítačů nebo podsítě. Existují následující skupiny zabezpečení sítě:
- Skupina zabezpečení sítě 1 pro službu Application Gateway
- Skupina zabezpečení sítě 1 pro App Service Environment
- Skupina zabezpečení sítě 1 pro službu Azure SQL Database
- Skupina zabezpečení sítě 1 pro bastion host

Skupiny zabezpečení sítě mají určité porty a protokoly otevřete tak, aby řešení můžete pracovat zabezpečeně a správně. Kromě toho jsou povoleny následující konfigurace pro každou skupinu zabezpečení sítě:

  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložená v účtu úložiště
  - Azure Log Analytics je připojen k [diagnostiky skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Podsítě**: Každá podsíť je přidružený k jeho odpovídající skupinu zabezpečení sítě.

**Azure DNS**: Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostitelská služba určená pro domény DNS, která nabízí řešení názvů pomocí infrastruktury Azure. Hostování domény v Azure, uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturačních podmínek jako u ostatních služeb Azure. Azure DNS podporuje také privátní DNS domény.

**Nástroj Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat svoje aplikace a poskytovat vysokou dostupnost služeb. Nástroj pro vyrovnávání zatížení podporuje další scénáře využití příchozí i odchozí a poskytuje nízkou latenci a vysokou propustnost a škálování až na úrovni milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. 

Pro chráněná data přenášená z sítě vlastněné zákazníkem architektura používá Azure Internet nebo ExpressRoute s bránou VPN nakonfigurovaný s protokolem IPSEC.

Kromě toho všechny transakce do Azure prostřednictvím portálu pro správu Azure dojde k přes HTTPS využívání protokolu TLS 1.2.
Neaktivní uložená data

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data při nečinnosti pomocí šifrování, auditování databáze a jiné míry.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit data na závazků organizace na zabezpečení a dodržování předpisů požadavkům definovaným ve Správci služeb sítě Internet australské státní správy.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows, zajišťuje šifrování pro datové disky. Řešení se integruje se službou Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disku.

**Azure SQL Database**: instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování služby Active Directory a povolení](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umožňuje správu identit uživatelů databáze a další služby Microsoftu v jednom centrálním místě.
-   [Auditování služby SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do auditu protokolu v účtu služby Azure storage.
-   Azure SQL Database je nakonfigurován na použití [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), který provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v rozhraní rest. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekce a reakce na potenciální hrozby, jakmile nastanou díky poskytování upozornění zabezpečení pro podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklých databázových přístup vzory. Detekce hrozeb SQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivitě a doporučená akce na tom, jak zkoumat a zmírnit hrozby.
-   [Vždy šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Maskování dynamických dat SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá při snížení přístup tak, aby citlivá data neexistuje databáze prostřednictvím před neoprávněným přístupem. Zákazníci, bude nutné upravit nastavení dodržovat svoje schéma databáze maskování dynamických dat.

### <a name="identity-management"></a>Správa identit
Zákazníci můžou využívat v místním Active Directory Federated Services pro vytvoření federace s [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), které je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) místních adresářů se integruje s Azure Active Directory. Všichni uživatelé v tomto řešení vyžaduje účtů Azure Active Directory, včetně uživatele, kteří používají Azure SQL Database. S federací přihlášení uživatelé přihlásit do služby Azure Active Directory a mohli ověřovat prostředky Azure pomocí místních přihlašovacích údajů.

Navíc následující funkce Azure Active Directory pomoct spravovat přístup k datům v prostředí Azure:
- Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupců databáze používá Azure Active Directory k ověření aplikace ke službě Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data ve službě Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určité informace. Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení by to mělo dopad identity organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

**Azure Multi-Factor Authentication**: K ochraně identit, by měla být implementována ověřování službou Multi-Factor Authentication. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) je snadno použitelný, škálovatelné a spolehlivé řešení, které poskytuje druhé metody ověřování ochrana uživatelů. Azure Multi-Factor Authentication využívá výkonný cloud a integruje se s místní službou Active Directory a vlastních aplikací. Tato ochrana je rozšířená velkého objemu, klíčových scénářích.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je že modul hardwarového zabezpečení chráněných klíčů RSA 2048 bitů.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

**Služba Application Gateway**: architektura snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway firewall webových aplikací a pravidel OWASP povolena. Další možnosti patří:

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

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy, což umožní všechna data k analýze společně bez ohledu na jejich původní zdroj. Kromě toho Azure Security Center umožňuje integrací s Log Analytics a umožňuje zákazníkům používat dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje rizika a stav prostředí serveru v pravidelných intervalech a poskytuje uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru a poskytuje zákazníkům s uspořádaný seznam doporučení, které jsou specifické pro nasazenou serverové infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): řešení Agent Health sestavy, kolik agentů je nasazených a zeměpisné rozšíření, jakož i kolik agentů, které jsou reagovat a počet agentů, které jsou odesílání provozních dat.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze služby Azure SQL Database. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

Azure Network Watcher: [Azure Network Watcher] 9https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview) poskytuje nástroje pro monitorování, Diagnostika, zobrazujte metriky, zakázání nebo povolení protokolů pro prostředky ve službě Azure virtual network.  Společenství severních entit by měly implementovat protokoly toku Network Watcher pro skupiny zabezpečení sítě a virtuální počítače. Tyto protokoly by být uloženy v účtu úložiště vyhrazené jenom zabezpečení protokoly se ukládají v, který by měl být zabezpečený přístup k účtu úložiště pomocí řízení přístupu na základě rolí.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/au-protected-paaswa-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace PaaS pro Model hrozeb AU-PROTECTED](images/au-protected-paaswa-threat-model.png?raw=true "webová aplikace PaaS diagramu modelu AU OCHRANOU před internetovými útoky")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů
Tato dokumentace dodržování předpisů je vytvořen microsoftem, které jsou založené na platformách a službách nabízených společností Microsoft. Tato dokumentace způsobené celou řadou zákaznická nasazení, přináší zobecněný přístup řešení pouze hostované v prostředí Azure. Zákazníci mohou identifikovat a použít alternativní produktů a služeb na základě nedělají provozní prostředí a obchodních výsledků. Zákazníkům, kteří zvolí používat místní prostředky, musí řešit operace pro tyto místní prostředky a zabezpečení. Dokumentované řešení může přizpůsobit zákazníkům řešit jejich konkrétnímu místnímu a požadavky na zabezpečení.

[Azure zabezpečení a dodržování předpisů – matici zodpovědnosti zákazníků AU-PROTECTED](https://aka.ms/au-protected-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované AU Prot. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů – AU-PROTECTED PaaS webové aplikace implementace matice](https://aka.ms/au-protected-paaswa-cim) poskytuje informace, na kterém jsou chráněné AU ovládací prvky řešený architektura webové aplikace PaaS, včetně podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute

Pro utajených informací zabezpečené tunelové propojení IPSec VPN je potřeba nakonfigurovat tak, aby bezpečně navázat připojení k prostředky nasazené jako součást této referenční architektuře IaaS webové aplikace. Nastavením odpovídajícím způsobem IPSec VPN, Zákazníci přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení IPSec VPN s využitím Azure vytvářet virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení může proběhnout přes Internet a umožňuje zákazníkům bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. 

Protože provoz v rámci tunelového připojení sítě VPN přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí možnost privátní připojení. Azure ExpressRoute je vyhrazené propojení mezi Azure a v místním umístění nebo poskytovatele hostingu serveru Exchange a je považován za privátní sítě. Jak připojení ExpressRoute se nepřenášejí prostřednictvím Internetu, tato připojení nabízí další spolehlivost, vyšší rychlost a nižší latenci než Typická připojení přes Internet. Navíc vzhledem k tomu, že toto je přímé připojení poskytovatele telekomunikačních zákazníka, data nejsou prochází přes Internet a proto není vystavený ho.

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

K ochraně důvěrná data, ať už pomocí Internetu nebo Azure ExpressRoute, musíte zákazníkům konfigurace jejich IPSec VPN s použitím následující nastavení:

• Iniciátoru VPN zákazník musí být nakonfigurovaný pro života nepřesahovala 14400 sekund.
• Iniciátoru VPN zákazníka musíte zakázat agresivní IKEv1 režimu.
• Iniciátoru VPN zákazníka, musíte nakonfigurovat Perfect Forward Secrecy.
• Zákazník iniciátoru VPN musíte nakonfigurovat použití HMAC SHA256 nebo vyšší.

Možnosti konfigurace pro zařízení VPN a protokolu IPSec / IKE parametrů je [k dispozici](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) ke kontrole.

### <a name="azure-active-directory-setup"></a>Instalace nástroje Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu pro pracovníky, práci s prostředím. Existující systému Windows Server Active Directory je možné integrovat se službou Azure Active Directory v [čtyři kliknutí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Kromě toho [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) umožňuje zákazníkům ke konfiguraci federace s místními [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) a Azure Active Directory. Federace přihlášení zákazníci mohou povolit uživatelům přihlášení ke službám Azure Active Directory pomocí svých místních hesel a bez nutnosti znovu zadejte své heslo v podnikové síti. Pomocí možnosti federační službou Active Directory Federation Services, můžete nasadit nové instalace služby AD FS nebo můžete zadat existující instalace ve farmě systému Windows Server 2012 R2.

Aby se zabránilo důvěrná data z nesynchronizuje do Azure Active Directory, můžete omezit zákazníkům atributy, které se replikují do Azure Active Directory s použitím následující nastavení v Azure Active Directory Connect:

- [Povolit filtrování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Zakázat synchronizaci hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Zakázat zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Zakázat zpětný zápis zařízení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Ponechejte výchozí nastavení pro [prevence náhodného odstranění](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) a [automatický upgrade](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Právní omezení

 - Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití.
 - Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům.
 - Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely.
 - Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka.
 - Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
 - Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
