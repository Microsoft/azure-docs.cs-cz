---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro NIST SP 800-171
description: Podrobný plán zabezpečení a dodržování předpisů Azure PaaS webová aplikace NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 4d635d04c5207b3668bd179cec4f21e26dd59452
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946682"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>PaaS webová aplikace pro NIST Special publikace 800-171 Podrobný plán zabezpečení a dodržování předpisů Azure

## <a name="overview"></a>Přehled
[NIST Special publikace 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízených neklasifikovaných informací (CUI), které se nacházejí v nefederálních informačních systémech a organizacích. NIST SP 800-171 stanoví 14 rodin požadavků na zabezpečení pro ochranu důvěrnosti CUI.

Tento Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny, které zákazníkům pomůžou nasadit webovou aplikaci PaaS (Platform as a Service) v Azure, která implementuje podmnožinu NIST SP 800-171 Controls. Toto řešení ukazuje, jak můžou zákazníci splnit konkrétní požadavky na zabezpečení a dodržování předpisů. Také slouží jako základ pro zákazníky, kteří si můžou vytvořit a nakonfigurovat vlastní webovou aplikaci v Azure.

Tato referenční architektura, přidružená příručka implementace a model hrozeb, mají sloužit jako základ pro zákazníky, kteří přizpůsobují jejich konkrétní požadavky. Neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení této architektury bez úprav není dostatečné pro zcela splnění požadavků nástroje NIST SP 800-171. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u všech řešení vytvořených pomocí této architektury. Požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje referenční architekturu pro webovou aplikaci v PaaS s Azure SQL Database back-endu. Webová aplikace je hostována v izolovaném App Servicem prostředí, což je privátní vyhrazené prostředí v datovém centru Azure. Zatížení prostředí vyrovnává provoz pro webovou aplikaci napříč virtuálními počítači, které spravuje Azure. Tato architektura zahrnuje taky skupiny zabezpečení sítě (skupin zabezpečení sítě), službu Azure Application Gateway, Azure DNS a Azure Load Balancer.

Pro lepší analýzy a vytváření sestav je možné konfigurovat databáze Azure SQL pomocí indexů columnstore. Databáze SQL Azure je možné škálovat nahoru nebo dolů nebo zcela vypnout v reakci na využití od zákazníků. Veškerý provoz SQL je zašifrovaný pomocí SSL prostřednictvím zahrnutí certifikátů podepsaných svým držitelem. Osvědčeným postupem je, že Azure doporučuje používání důvěryhodné certifikační autority pro rozšířené zabezpečení.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá tři kopie dat v rámci vybraného datového centra zákazníka o odolnost proti chybám. Geograficky redundantní úložiště zajišťuje, že se data replikují do sekundárního datového centra stovky kilometrů a ukládají se znovu jako tři kopie v rámci tohoto datového centra. Toto ujednání brání v primárním datovém centru zákazníka v důsledku ztráty dat.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Pro řízení přístupu k nasazeným prostředkům se používá řízení přístupu na základě role (RBAC) Azure Active Directory (Azure AD). Tyto prostředky zahrnují klíče zákazníka v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Monitor. Zákazníci nakonfigurují tuto monitorovací službu tak, aby zachytí protokoly. Stav systému se zobrazuje v jednom řídicím panelu, který se snadno používá.

SQL Database se obvykle spravují prostřednictvím SQL Server Management Studio. Spouští se z místního počítače, který je nakonfigurovaný pro přístup k databázi SQL prostřednictvím zabezpečeného připojení VPN nebo Azure ExpressRoute.

Application Insights poskytuje v reálném čase aplikace řízení výkonu a analýzy prostřednictvím Azure Monitor protokolů Společnost *Microsoft doporučuje, abyste nakonfigurovali připojení VPN nebo ExpressRoute pro správu a import dat do referenční architektury. podsíť.*

![PaaS webová aplikace pro NIST SP 800-171 – diagram referenční architektury](images/nist171-paaswa-architecture.png "PaaS webová aplikace pro NIST SP 800-171 – diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v části [architektura nasazení](#deployment-architecture) .

- Azure Virtual Machines
    - (1) Správa/bastionu (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 sítí
    - (4)/24 sítí
    - (4) skupiny zabezpečení sítě
- Azure Application Gateway
    - Firewall webových aplikací
        - Režim brány firewall: prevence
        - Sada pravidel: OWASP
        - Port naslouchacího procesu: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (protokoly)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Azure Resource Manager**: [Správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) můžou zákazníci použít k práci s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci používají šablonu pro nasazení. Šablona může fungovat v různých prostředích, jako je testování, příprava a produkce. Správce prostředků poskytuje funkce zabezpečení, auditování a označování, které zákazníkům pomohou spravovat prostředky po nasazení.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem záznamu, který uživatelé můžou použít pro přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje pouze vzdálený provoz z veřejných IP adres v bezpečném seznamu. Aby bylo možné provoz vzdálené plochy povolit, musí být zdroj provozu definovaný v NSG.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Rozšíření Azure Diagnostics](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pomocí Key Vault.
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají.
-   [Ochrana Credential Guard v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému.

**Web Apps**: [Web Apps](https://docs.microsoft.com/azure/app-service/) je funkce Azure App Service. Zákazníci je můžou použít k sestavování a hostování webových aplikací v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost. Podporuje Windows a Linux a umožňuje automatizované nasazení z GitHubu, Azure DevOps nebo jakéhokoli úložiště Git.

**App Service Environment**: [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce App Service. Poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku.

Prostředí App Service je izolované za účelem spuštění pouze jedné aplikace. Je vždycky nasazená do virtuální sítě. Vzhledem k funkci izolace má referenční architektura úplnou izolaci tenanta a je odebraný z víceklientské prostředí Azure. Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikace. Aplikace můžou vytvořit vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům. Zákazníci mohou pomocí App Service Environment automaticky škálovat na základě metrik zatížení, dostupného rozpočtu nebo definovaného plánu.

Použití App Service Environment pro tuto architekturu poskytuje následující ovládací prvky a konfigurace:

- Hostitel v zabezpečené virtuální síti Azure a pravidlech zabezpečení sítě.
- Certifikát interního nástroje pro vyrovnávání zatížení, který je podepsaný svým držitelem pro komunikaci HTTPS Osvědčeným postupem je, že společnost Microsoft doporučuje používat pro rozšířené zabezpečení používání důvěryhodné certifikační autority.
- [Režim interního vyrovnávání zatížení](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (režim 3).
- Zakažte [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Změňte [šifru TLS](../../app-service/environment/app-service-app-service-environment-custom-settings.md).
- Řízení [příchozího provozu s porty s/W](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).
- [Firewall webových aplikací – omezte data](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md).
- Povolí [Azure SQL Database provoz](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md).

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupin zabezpečení sítě](../../virtual-network/virtual-network-vnet-plan-design-arm.md) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupin zabezpečení sítě se dá použít k zabezpečení provozu na úrovni podsítě nebo jednotlivého virtuálního počítače. Následující skupin zabezpečení sítě existuje:
- Jeden NSG pro Application Gateway
- Jeden NSG pro App Service Environment
- Jeden NSG pro SQL Database
- Jeden NSG pro hostitele bastionu

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každý NSG povoleny následující konfigurace:
  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povoleny a uloženy v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Podsítě**: Každá podsíť je přidružená k odpovídajícímu NSG.

**Azure DNS**: Služba DNS (Domain Name System) zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure DNS podporuje i privátní domény DNS.

**Azure Load Balancer**: Zákazníci můžou pomocí [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) škálovat své aplikace a vytvářet vysokou dostupnost služeb. Load Balancer podporuje scénáře příchozího a odchozího přenosu. Poskytuje nízkou latenci a vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datovými centry Azure a z nich. Všechny transakce, které se mají Azure Storage prostřednictvím Azure Portal probíhat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data

Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné splňovat požadavky na zašifrovaná data v klidovém stavu, všechna [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](../../storage/common/storage-service-encryption.md). Tato funkce pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných v NIST SP 800-171.

**Azure Disk Encryption**: [Šifrování disku](../azure-security-disk-encryption-overview.md) používá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](../../sql-database/sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase k ochraně informací v klidovém čase. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](../../sql-database/sql-database-threat-detection.md) umožňuje detekci a reakci na potenciální hrozby, když k nim dojde. Poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.
-   [Šifrované sloupce](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajišťují, že citlivá data se v databázovém systému nikdy neobjevují jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem k klíčům.
- [Dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že je maskování dat pro uživatele nebo aplikace neprivilegovaných oprávnění. Může automaticky zjistit potenciálně citlivá data a navrhnout vhodné masky pro použití. Dynamické maskování dat pomáhá snižovat přístup, takže citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. *Zákazníci zodpovídají za úpravu nastavení, aby odpovídaly schématu databáze.*

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je cloudová cloudová služba a služba pro správu identit založená na víceklientské architektuře Microsoftu. Všichni uživatelé pro toto řešení se vytvářejí ve službě Azure AD a zahrnují uživatele, kteří přistupují k databázi SQL.
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Šifrování sloupců databáze také používá Azure AD k ověření aplikace pro SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Správci služby [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) můžou použít k definování jemně odstupňovaných přístupových oprávnění. Díky tomu můžou udělit jenom množství přístupu, které uživatelé potřebují k provádění svých úloh. Místo udělení přístupu každému uživateli bez omezení přístupu k prostředkům Azure můžou správci pro přístup k prostředkům a datům udělit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Zákazníci můžou použít [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) k minimalizaci počtu uživatelů, kteří mají přístup k určitým informacím. Správci můžou pomocí Azure AD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace. Konfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace. Prošetří také podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Následující možnosti Key Vault můžou zákazníkům chránit data:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typem klíče je hardwarový klíč chráněný modulem zabezpečení 2048.
- Všem uživatelům a identitám se uděluje minimální požadovaná oprávnění pomocí RBAC.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Azure Security Center**: Díky [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Security Center taky přistupuje k existujícím konfiguracím služeb Azure, aby poskytovala doporučení týkající se konfigurace a služeb, která vám pomůžou zdokonalit stav zabezpečení a chránit data.

Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se spouštějí při výskytu hrozby nebo podezřelé aktivity. Zákazníci můžou pomocí [vlastních pravidel upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení na základě dat, která už jsou shromážděná z jejich prostředí.

Security Center poskytuje prioritní výstrahy zabezpečení a incidenty. Security Center usnadňuje zákazníkům zjišťovat a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Týmy reakce na incidenty mohou sestavy používat při vyšetřování a nápravě hrozeb.

**Application Gateway Azure**: Architektura snižuje riziko ohrožení zabezpečení pomocí aplikační brány s nakonfigurovanou bránou firewall webových aplikací a povolenou sadou pravidel OWASP. Mezi další možnosti patří:

- [Koncová SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Povolte [přesměrování zpracování SSL](../../application-gateway/create-ssl-portal.md).
- Zakažte [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall webových aplikací](../../application-gateway/waf-overview.md) (režim prevence).
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) se sadou pravidel OWASP 3,0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Vlastní sondy stavu](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Security Center také nabízí systém reputace.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí systému Windows, protokoly úložiště, Key Vault protokoly auditu a protokoly brány firewall Application Gateway a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uživatelé můžou nakonfigurovat dobu uchování až 730 dní, aby splnily konkrétní požadavky.

**Protokoly Azure monitor**: Protokoly se konsolidují do [protokolů Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění dat je tato data uspořádána do samostatných tabulek pro každý datový typ v Log Analytics pracovní prostory. Tímto způsobem lze analyzovat všechna data společně bez ohledu na její původní zdroj. Security Center se integruje s protokoly Azure Monitor. Zákazníci můžou pomocí dotazů Kusto přistupovat ke svým datům události zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](../../monitoring/monitoring-solutions.md) Azure:
-   [Vyhodnocení služby Active Directory](../../azure-monitor/insights/ad-assessment.md): Řešení pro kontrolu stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Poskytuje prioritní seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Vyhodnocení SQL](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografická distribuce. Také oznamuje, kolik agentů neodpovídá a kolik agentů odesílá provozní data.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z SQL Database. Zákazníci můžou pomocí řešení automatizace [Change Tracking](../../automation/change-tracking.md) snadno identifikovat změny v prostředí.

**Azure Monitor**: [Monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, zachovat zabezpečení a identifikovat trendy. Organizace je můžou použít k auditu, vytváření výstrah a archivaci dat. Můžou taky sledovat volání rozhraní API ve svých prostředcích Azure.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba pro správu výkonu aplikací pro webové vývojáře na různých platformách. Application Insights detekuje anomálie výkonu. Zákazníci je můžou použít k monitorování živé webové aplikace. Application Insights obsahuje výkonné analytické nástroje, které zákazníkům pomohou diagnostikovat problémy a pochopit, co uživatelé s aplikací dělají. Je&#39;navržená tak, aby pomáhala zákazníkům s nepřetržitým zlepšením výkonu a použitelnosti.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/nist171-paaswa-tm) nebo najdete tady. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Webová aplikace PaaS pro model hrozeb NIST SP 800-171](images/nist171-paaswa-threat-model.png "Webová aplikace PaaS pro model hrozeb NIST SP 800-171")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
[Tabulka zodpovědnosti zákazníka podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171](https://aka.ms/nist171-crm) obsahuje všechny ovládací prvky zabezpečení, které vyžaduje NIST SP 800-171. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[Podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171 PaaS pro implementaci ovládacího prvku webové aplikace](https://aka.ms/nist171-paaswa-cim) poskytuje informace o tom, které ovládací prvky NIST SP 800-171 jsou řešeny architekturou webové aplikace PaaS. Obsahuje podrobný popis způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace PaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení probíhá přes Internet a umožňuje zákazníkům bezpečně "Tunnel" informace v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, nabízí společnost Microsoft ještě ještě bezpečnější možnost připojení. ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Připojení ExpressRoute se připojují přímo k poskytovateli telekomunikačních služeb zákazníka. V důsledku toho se data necestují přes Internet a nejsou vystavená. Tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
