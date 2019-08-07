---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro PCI DSS
description: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro PCI DSS
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 231e3f8a43f2c1fc9e798acebbe24d6635d9f6d8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781007"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Podrobný plán zabezpečení a dodržování předpisů Azure: PaaS webová aplikace pro PCI DSS

## <a name="overview"></a>Přehled

Tato Podrobný plán zabezpečení a dodržování předpisů Azureá automatizace poskytuje pokyny pro nasazení prostředí PaaS 3,2 PCI DSS (Platform as a Service) vyhovující standardům zabezpečení dat v odvětví platební karty, které je vhodné pro shromažďování, ukládání a načítání. data držitele. Toto řešení automatizuje nasazení a konfiguraci prostředků Azure pro společnou referenční architekturu, která demonstruje způsoby, kterými zákazníci můžou splnit konkrétní požadavky na zabezpečení a dodržování předpisů a slouží jako základ pro zákazníky k sestavení a Nakonfigurujte vlastní řešení v Azure. Řešení implementuje podmnožinu požadavků z PCI DSS 3,2. Další informace o požadavcích PCI DSS 3,2 a tomto řešení najdete v části [dokumentace dodržování předpisů](#compliance-documentation) .

Tato Podrobný plán zabezpečení a dodržování předpisů Azureá automatizace automaticky nasadí referenční architekturu webové aplikace v PaaS s předem nakonfigurovanými ovládacími prvky zabezpečení, které zákazníkům pomůžou dosáhnout dodržování požadavků PCI DSS 3,2. Toto řešení se skládá z Azure Resource Manager šablon a skriptů PowerShellu, které provedou nasazení a konfiguraci prostředků.

Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí. Nasazení aplikace do tohoto prostředí bez úprav není dostačující k úplnému splnění požadavků PCI DSS 3,2. Je třeba počítat s následujícím:
- Tato architektura poskytuje směrný plán, který zákazníkům umožňuje využívat Azure ve vyhovujícím způsobem PCI DSS 3,2.
- Zákazníci zodpovídají za provádění vhodného posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Splnění PCI DSS – dodržování předpisů vyžaduje, aby akreditační schvalovací posuzovatel (QUALIFIED Security ASSESSOR) certifikuje řešení pro produkční zákazníky. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Pokyny k nasazení získáte kliknutím [sem](https://aka.ms/pcidss-paaswa-repo) .

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Tato Podrobný plán zabezpečení a dodržování předpisů Azure automatizace nasadí referenční architekturu pro webovou aplikaci v PaaS s Azure SQL Database back-endu. Webová aplikace je hostovaná v izolovaném Azure App Service Environment, což je privátní vyhrazené prostředí v datacentru Azure. Zatížení prostředí vyrovnává provoz pro webovou aplikaci napříč virtuálními počítači, které spravuje Azure. Tato architektura zahrnuje taky skupiny zabezpečení sítě, Application Gateway, Azure DNS a Load Balancer.

Pro lepší analýzy a vytváření sestav je možné konfigurovat databáze Azure SQL pomocí indexů columnstore. Databáze SQL Azure je možné škálovat nahoru nebo dolů nebo zcela vypnout v reakci na využití od zákazníků. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá tři kopie dat v rámci vybraného datacentra zákazníka a zajišťuje odolnost proti chybám. Geografické redundantní úložiště zajišťuje, že se data budou replikovat do sekundárního datacentra stovky kilometrů a znovu se ukládají jako tři kopie v rámci tohoto datového centra, což brání nepříznivé události v primárním datovém centru zákazníka v důsledku ztráty údajů.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Azure Active Directory řízení přístupu na základě rolí se používá pro řízení přístupu k nasazeným prostředkům, včetně jejich klíčů v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu.

Azure SQL Database se běžně spravují prostřednictvím SQL Server Management Studio, která se spouští z místního počítače nakonfigurovaného pro přístup k Azure SQL Database prostřednictvím zabezpečeného připojení VPN nebo ExpressRoute.

Kromě toho Application Insights poskytuje správu výkonu aplikací v reálném čase a analýzy prostřednictvím protokolů Azure Monitor. **Microsoft doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a import dat do podsítě referenční architektury.**

![PaaS webová aplikace pro diagram referenční architektury PCI DSS](images/pcidss-paaswa-architecture.png "PaaS webová aplikace pro diagram referenční architektury PCI DSS")

Toto řešení používá následující služby Azure. Podrobnosti o architektuře nasazení najdete v části [architektura nasazení](#deployment-architecture) .

- App Service Environment v2
- Application Gateway
  - (1) Firewall webových aplikací
    - Režim brány firewall: prevence
    - Sada pravidel: OWASP 3,0
    - Port naslouchacího procesu: 443
- Application Insights
- Azure Active Directory
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 sítí
    - (4)/24 sítí
    - (4) skupiny zabezpečení sítě
- Webové aplikace Azure

## <a name="deployment-architecture"></a>Architektura nasazení

Následující část podrobně popisuje prvky nasazení a implementace.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje zákazníkům pracovat s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci používají šablonu pro nasazení a tato šablona může fungovat v různých prostředích, jako jsou testování, příprava a produkce. Správce prostředků poskytuje funkce zabezpečení, auditování a označování, které zákazníkům pomohou spravovat prostředky po nasazení.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který umožňuje uživatelům přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje vzdálený provoz z veřejných IP adres v bezpečném seznamu. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají
-   [Ochrana Credential Guard v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému

**App Service Environment v2**: Azure App Service Environment je funkce App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku. Tato funkce izolace je nutná pro splnění požadavků na dodržování předpisů PCI.

Prostředí App Service jsou izolovaná jenom tak, aby se spouštěly jenom aplikace jednoho zákazníka a nasadily se vždycky do virtuální sítě. Tato funkce izolace umožňuje, aby referenční architektura vytvořila úplnou izolaci tenanta a odebrala ji z Azure víceklientské prostředí, které klientům brání v vytváření výčtu nasazených App Service Environmentch prostředků. Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikací a aplikace můžou vytvořit vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům. Zákazníci mohou pomocí App Service Environment automaticky škálovat na základě metrik zatížení, dostupného rozpočtu nebo definovaného plánu.

Využijte App Service prostředí pro následující ovládací prvky/konfigurace:

- Hostitel v rámci zabezpečeného Virtual Network Azure a pravidel zabezpečení sítě
- Certifikát interního nástroje podepsaný svým držitelem pro komunikaci pomocí protokolu HTTPS
- [Režim interního vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Zakázat [TLS 1,0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Změnit [šifru TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Řízení [příchozího provozu za sekundu porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall webových aplikací – omezení dat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Povolení [Azure SQL Database provozu](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Webová aplikace Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostovat webové aplikace v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Azure DevOps nebo libovolného úložiště Git.

### <a name="virtual-network"></a>Virtuální sítě

Architektura definuje privátní Virtual Network s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznam Access Control (seznamy ACL), které povolují nebo zakazují provoz v rámci Virtual Network. Skupiny zabezpečení sítě se dají použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Existují následující skupiny zabezpečení sítě:

- 1 skupina zabezpečení sítě pro Application Gateway
- 1 skupina zabezpečení sítě pro App Service Environment
- 1 skupina zabezpečení sítě pro Azure SQL Database
- 1 skupina zabezpečení sítě pro hostitele bastionu

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povolené tyto konfigurace:

- [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
- Protokoly Azure Monitor jsou připojené k [diagnostice&#39;skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) .

**Podsítě**: Každá podsíť je přidružená ke své odpovídající skupině zabezpečení sítě.

**Azure DNS**: Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure DNS podporuje i privátní domény DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat své aplikace a vytvářet vysokou dostupnost služeb. Load Balancer podporuje příchozí i odchozí scénáře a poskytuje nízkou latenci, vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data

Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datacentry Azure a z nich. Všechny transakce, které se mají Azure Storage prostřednictvím Azure Portal probíhat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá chránit a chránit data držitele v podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných PCI DSS 3,2.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance Azure SQL Database používá následující bezpečnostní opatření databáze:

- [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
- [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
- Azure SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém znění. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
- [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
- [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
- [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjeví v podobě prostého textu. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na uživatele nebo aplikace bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky pro použití. Pomůže vám identifikovat a snížit přístup k datům tak, že neukončí databázi prostřednictvím neoprávněného přístupu. Zákazníci zodpovídají za úpravu nastavení dynamického maskování dat, aby odpovídaly schématu databáze.

### <a name="identity-management"></a>Správa identit

Následující technologie poskytují možnosti pro správu přístupu k datům držitele v prostředí Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je víceklientské&#39;cloudové služby a služba pro správu identit od Microsoftu. Všichni uživatelé tohoto řešení jsou vytvořeni v Azure Active Directory, včetně uživatelů, kteří přistupují k Azure SQL Database.
- Ověřování pro aplikaci se provádí pomocí Azure Active Directory. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupce databáze používá Azure Active Directory k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Řízení přístupu na základě role v Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení přístupu k prostředkům Azure můžou správci pro přístup k datům na držiteli aplikace použít jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím, jako jsou například data o držiteli. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detekuje potenciální ohrožení zabezpečení ovlivňující identity&#39;organizace a nakonfiguruje automatizované odezvy na zjištěné podezřelé akce související s&#39;identitami organizace s. zkoumá podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení

**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit a přistupovat k těmto datům:

- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je 32bitový klíč RSA s ochranou 2048.
- Všem uživatelům a identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Azure Security Center**: Díky [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují při výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla výstrah](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) , která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

**Application Gateway Azure**: Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway Azure s nakonfigurovanou bránou firewall webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim prevence)
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s OWASP 3,0 RuleSet
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastní sondy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také nabízí systém reputace.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci.

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění jsou data uspořádaná do samostatných tabulek pro každý datový typ v rámci Log Analyticsch pracovních prostorů, což umožňuje analyzovat všechna data bez ohledu na její původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z Azure SQL Database. Řešení automatizace [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) zákazníkům umožňuje snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací pro webové vývojáře na různých platformách. Application Insights detekuje anomálie výkonu a zákazníci je můžou použít k monitorování živé webové aplikace. Obsahuje výkonné analytické nástroje, které zákazníkům pomohou diagnostikovat problémy a porozumět tomu, co uživatelé skutečně dělají s aplikací. Je&#39;navržená tak, aby pomáhala zákazníkům s nepřetržitým zlepšením výkonu a použitelnosti.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/pcidss-paaswa-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![PaaS webová aplikace pro model hrozeb PCI DSS](images/pcidss-paaswa-threat-model.png "PaaS webová aplikace pro model hrozeb PCI DSS")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů

V tabulce [podrobný plán zabezpečení a dodržování předpisů Azure – PCI DSS zodpovědnosti zákazníka](https://aka.ms/pcidss-crm) jsou uvedeny zodpovědnosti řadiče a procesoru pro všechny požadavky PCI DSS 3,2.

[Podrobný plán zabezpečení a dodržování předpisů Azure – PCI DSS PaaS implementace webové aplikace](https://aka.ms/pcidss-paaswa-cim) poskytuje informace o tom, které požadavky PCI DSS 3,2 jsou řešeny architekturou webové aplikace PaaS, včetně podrobných popisů toho, jak implementace splňuje požadavky každého zahrnutého článku.

## <a name="deploy-this-solution"></a>Nasadit toto řešení
Tato Podrobný plán zabezpečení a dodržování předpisů Azure Automation se skládá z konfiguračních souborů JSON a skriptů PowerShellu, které služba API pro Azure Resource Manager využívá k nasazení prostředků v rámci Azure. Podrobné pokyny k nasazení jsou k dispozici [zde](https://aka.ms/pcidss-paaswa-repo).

#### <a name="quickstart"></a>Rychlý start
1. Naklonujte nebo stáhněte [Toto](https://aka.ms/pcidss-paaswa-repo) úložiště GitHub na místní pracovní stanici.

2. Zkontrolujte 0-Setup-AdministrativeAccountAndPermission.md a spusťte poskytnuté příkazy.

3. Nasaďte testovací řešení s ukázkovými daty společnosti Contoso nebo pilotem počáteční provozní prostředí.
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - Tento skript nasadí prostředky Azure pro ukázku webstoru pomocí ukázkových dat contoso.
   - 1-DeployAndConfigureAzureResources.ps1
     - Tento skript nasadí prostředky Azure potřebné k podpoře provozního prostředí pro webovou aplikaci ve vlastnictví zákazníka. Toto prostředí by mělo být dále přizpůsobeno zákazníkem na základě organizačních požadavků.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení

### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute

Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) je třeba nakonfigurovat tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace PaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně &quot;&quot; propojit informace v rámci šifrovaného propojení mezi zákaznickou&#39;sítí a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí jinou, ještě bezpečnější možnost připojení. Azure ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení&#39;poskytovatele telekomunikačních služeb pro zákazníky, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="disclaimer"></a>Zřeknutí se práv

- Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument je k &quot;dispozici tak, jak je.&quot; Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
- Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
- Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
- Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetního využití prostředků v Azure a můžou zvýšit náklady na&#39;licence Azure pro zákazníky nebo předplatné.
- Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
- Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
