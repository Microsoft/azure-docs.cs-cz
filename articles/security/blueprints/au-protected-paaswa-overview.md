---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro Austrálii CHRÁNĚNou
description: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro Austrálii CHRÁNĚNou
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 294716052869dac03db42feea9ade15d610551e6
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781127"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-PaaS pro Austrálii CHRÁNĚNou

## <a name="overview"></a>Přehled

Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny pro nasazení prostředí platformy jako služby (PaaS), které je vhodné pro shromažďování, ukládání a načítání dat státní správy CHRÁNĚNých za běhu, která jsou v souladu s cíli Australský státní ředitel pro informace o zabezpečení (ISM), který vytvořil australský signalský ředitelství (ASD). Tento podrobný plán předvádí společnou referenční architekturu a pomáhá předvést správné zpracování citlivých dat státní správy v zabezpečeném a kompatibilním prostředí s více vrstvami.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří zajišťují své vlastní plánování a akreditační procesy při schvalování systému a pomáhají zákazníkům nasazovat úlohy do Azure v souladu se standardem ASD. Zákazníci se můžou rozhodnout implementovat Azure VPN Gateway nebo ExpressRoute, aby mohli používat federované služby, a integrovat místní prostředky s prostředky Azure. Zákazníci musí vzít v úvahu důsledky zabezpečení při používání místních prostředků. K splnění všech požadavků se vyžaduje další konfigurace, která se může lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Dosahování dodržování předpisů ASD vyžaduje, aby systém provedl audit informací zaregistrovaných v systému. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení poskytuje referenční architekturu pro webovou aplikaci v PaaS s Azure SQL Database back-end. Webová aplikace je hostovaná v izolovaném Azure App Service Environment, což je privátní vyhrazené prostředí v datacentru Azure. Zatížení prostředí vyrovnává provoz pro webovou aplikaci napříč virtuálními počítači, které spravuje Azure. Všechna připojení webových aplikací vyžadují protokol TLS s minimální verzí 1,2. Tato architektura zahrnuje taky skupiny zabezpečení sítě, Application Gateway, Azure DNS a Load Balancer.

Tato architektura může doručovat zabezpečené hybridní prostředí, které rozšiřuje místní síť do Azure, a umožňuje tak zabezpečený webovým úlohám využívat podnikové uživatele v privátní místní síti nebo Internetu organizace. U místních řešení je zákazníkem jak účet, tak zodpovědný za všechny aspekty zabezpečení, provozu a dodržování předpisů.

Prostředky Azure, které jsou součástí tohoto řešení, se můžou připojit k místní síti nebo zařízení Canberra datacentra (např. CDC v) prostřednictvím sítě VPN s protokolem IPSec pomocí VPN Gateway a prostřednictvím ExpressRoute. Rozhodnutí o použití sítě VPN by mělo být provedeno s klasifikací přenášených dat a s ohledem na cestu k síti. Zákazníci, kteří využívají rozsáhlé a důležité úlohy s vysokými nároky na data, by měli zvážit hybridní síťovou architekturu s využitím ExpressRoute pro připojení privátních sítí ke službám Azure. Další podrobnosti o mechanismech připojení k Azure najdete v části [doprovodné materiály a doporučení](#guidance-and-recommendations) .

K ověřování uživatelů pomocí místních přihlašovacích údajů a přístupu ke všem prostředkům v cloudu s využitím místní infrastruktury Active Directory Federation Services (AD FS) by se měla použít federace s Azure Active Directory. Active Directory Federation Services (AD FS) může pro toto hybridní prostředí poskytovat zjednodušenou, zabezpečenou federaci identit a možnosti jednotného přihlašování k webu. Další podrobnosti Azure Active Directory instalaci najdete v části [doprovodné materiály a doporučení](#guidance-and-recommendations) .

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá pro zajištění odolnosti tři kopie dat v rámci vybrané oblasti zákazníka. Oblasti Azure jsou nasazené ve dvojicích odolných oblastí a geografické redundantní úložiště zajišťuje, aby se data replikují do druhé oblasti se třemi kopiemi. Tím se zabrání nežádoucí události v primárním datovém umístění zákazníka, což má za následek ztrátu dat.

Pro rozšířené zabezpečení se všechny prostředky Azure v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Azure Active Directory řízení přístupu na základě rolí se používá pro řízení přístupu k nasazeným prostředkům a klíčům v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu. Azure Application Gateway je nakonfigurovaný jako brána firewall v režimu prevence a nepovoluje provoz, který není TLS verze 1.2 nebo vyšší. Řešení využívá Azure Application Service Environment v2 k izolaci webové vrstvy v nevíceklientském prostředí.

![Referenční architektura chráněná ve au – PaaS webové aplikace](images/au-protected-paaswa-architecture.png?raw=true "PaaS webová aplikace pro diagram referenční architektury chráněnou au")

Toto řešení používá následující služby Azure. Další podrobnosti najdete v části [architektura nasazení](#deployment-architecture) .

- Application Gateway
    - Firewall webových aplikací
        - Režim brány firewall: prevence
        - Sada pravidel: OWASP
        - Port naslouchacího procesu: 443
- Application Insights
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Protokoly služby Azure Monitor
- Azure Virtual Network
    - (1)/16 sítí
    - (4)/24 sítí
    - Skupiny zabezpečení sítě
- Skupiny zabezpečení sítě
- Recovery Services trezor
- Webové aplikace Azure

Tento podrobný plán obsahuje služby Azure, které nebyly certifikovány pro použití s chráněnou klasifikací prostřednictvím australského centra Internet Security Center (ACSC). Společnost Microsoft doporučuje, aby si zákazníci zkontrolovali publikované sestavy zabezpečení a auditu týkající se těchto služeb Azure a používali jejich rámec řízení rizik k určení, jestli je služba Azure vhodná pro jejich interní akreditaci a použití na Chráněná klasifikace.

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umožňuje zákazníkům pracovat s prostředky v řešení jako se skupinou. Zákazníci mohou nasadit, aktualizovat nebo odstranit všechny prostředky pro řešení v rámci jediné koordinované operace. Zákazníci používají šablonu pro nasazení a tato šablona může fungovat v různých prostředích, jako jsou testování, příprava a produkce. Správce prostředků poskytuje funkce zabezpečení, auditování a označování, které zákazníkům pomohou spravovat prostředky po nasazení.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který umožňuje uživatelům přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje vzdálený provoz z veřejných IP adres v bezpečném seznamu. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Azure Key Vault
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají

**App Service Environment v2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) je funkce App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku.

Prostředí App Service jsou izolovaná jenom tak, aby se spouštěly jenom aplikace jednoho zákazníka a nasadily se vždycky do virtuální sítě. Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikací a aplikace můžou vytvořit vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům.

Použití prostředí App Service pro tuto architekturu umožňuje tyto ovládací prvky a konfigurace:

- App Service prostředí musí být nakonfigurovaná tak, aby používala plán izolované služby.
    - Konfigurace různých App Service prostředí pro aplikace v různých klasifikacích
- Hostování v zabezpečené virtuální síti Azure a pravidlech zabezpečení sítě
- App Service prostředí konfigurovaná s certifikátem interního nástroje pro vyrovnávání zatížení pro komunikaci pomocí protokolu HTTPS. Osvědčeným postupem je, že společnost Microsoft doporučuje používat pro rozšířené zabezpečení používání důvěryhodné certifikační autority.
- [Režim interního vyrovnávání zatížení](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (režim 3)
- Zakázat [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Změnit [šifru TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Řízení [příchozího provozu za sekundu porty](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Firewall webových aplikací – omezení dat](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Povolení [Azure SQL Database provozu](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Webová aplikace Azure**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) umožňuje zákazníkům vytvářet a hostovat webové aplikace v programovacím jazyce podle svého výběru bez nutnosti spravovat infrastrukturu. Nabízí automatické škálování a vysokou dostupnost, podporuje systémy Windows a Linux a umožňuje automatizované nasazení z Githubu, Azure DevOps Services nebo libovolného úložiště Git.

### <a name="virtual-network"></a>Virtuální sítě
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahují seznamy řízení přístupu, které povolují nebo zakazují provoz v rámci virtuální sítě. Skupiny zabezpečení sítě se dají použít k zabezpečení provozu na úrovni podsítě nebo jednotlivých virtuálních počítačů. Existují následující skupiny zabezpečení sítě:
- 1 skupina zabezpečení sítě pro Application Gateway
- 1 skupina zabezpečení sítě pro App Service Environment
- 1 skupina zabezpečení sítě pro Azure SQL Database
- 1 skupina zabezpečení sítě pro hostitele bastionu

Každá z skupin zabezpečení sítě má otevřené konkrétní porty a protokoly, aby řešení mohlo fungovat bezpečně a správně. Kromě toho jsou pro každou skupinu zabezpečení sítě povolené tyto konfigurace:

  - [Diagnostické protokoly a události](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) jsou povolené a uložené v účtu úložiště.
  - Protokoly Azure Monitor jsou připojené k [diagnostice skupiny zabezpečení sítě](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) .

**Podsítě**: Každá podsíť je přidružená ke své odpovídající skupině zabezpečení sítě.

**Azure DNS**: Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Azure. Díky hostování domén v Azure můžou uživatelé spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. Azure DNS podporuje i privátní domény DNS.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) umožňuje zákazníkům škálovat své aplikace a vytvářet vysokou dostupnost služeb. Load Balancer podporuje příchozí i odchozí scénáře a poskytuje nízkou latenci, vysokou propustnost a škáluje až milionů toků pro všechny aplikace TCP a UDP.

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datacentry Azure a z nich. 

Pro chráněná data při přenosu od sítí vlastněných zákazníky používá architektura Azure Internet nebo ExpressRoute s VPN Gateway nakonfigurovaným pomocí protokolu IPSEC.

Všechny transakce do Azure prostřednictvím portálu pro správu Azure nastávají taky prostřednictvím protokolu HTTPS, který využívá TLS v 1.2.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato ochrana pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů, které definují australské vlády ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**Azure SQL Database**: Instance Azure SQL Database používá následující bezpečnostní opatření databáze:
-   [Ověřování a autorizace služby Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) umožňují správu identit uživatelů databáze a dalších služeb Microsoftu v jednom centrálním umístění.
-   [Auditování služby SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) sleduje události databáze a zapisuje je do protokolu auditu v účtu úložiště Azure.
-   Azure SQL Database je nakonfigurované pro použití [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), které provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém znění. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Detekce hrozeb SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umožňuje detekci a reakci na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro podezřelé databázové aktivity, potenciální ohrožení zabezpečení, útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi. Detekce hrozeb SQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), které obsahují podrobnosti o podezřelé aktivitě a doporučené akce týkající se toho, jak tuto hrozbu prozkoumat a zmírnit.
-   [Always Encrypted sloupců](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zajistí, že se citlivá data v databázovém systému nikdy neobjeví jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [SQL Database dynamické maskování dat](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) omezuje vystavení citlivých dat tím, že se data maskují na uživatele nebo aplikace bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky pro použití. To pomáhá snižovat přístup tak, že citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. Zákazníci budou muset upravit nastavení dynamického maskování dat tak, aby odpovídalo schématu databáze.

### <a name="identity-management"></a>Správa identit
Zákazníci můžou využívat místní federované služby Active Directory pro federovat s [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), což je víceklientské cloudové adresáře a služba pro správu identit od Microsoftu. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integruje místní adresáře s Azure Active Directory. Všichni uživatelé v tomto řešení vyžadují účty Azure Active Directory, včetně uživatelů, kteří přistupují k Azure SQL Database. S přihlašováním pomocí federace se uživatelé můžou přihlašovat k Azure Active Directory a provádět ověřování v prostředcích Azure pomocí místních přihlašovacích údajů.

Kromě toho následující Azure Active Directory možnosti pomůžou spravovat přístup k datům v prostředí Azure:
- Ověřování pro aplikaci se provádí pomocí Azure Active Directory. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Kromě toho šifrování sloupce databáze používá Azure Active Directory k ověření aplikace pro Azure SQL Database. Další informace najdete v tématu Jak [chránit citlivá data v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Řízení přístupu na základě role v Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení pro prostředky Azure můžou správci pro přístup k datům dovolit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detekuje potenciální ohrožení zabezpečení, která mají vliv na identity organizace, nakonfigurují automatizované odpovědi na zjištěné podezřelé akce související s identitami organizace a prošetří podezřelé incidenty, které přijmou vhodná opatření k jejich vyřešení.

**Ověřování Azure Multi-Factor Authentication**: K ochraně identit by měla být implementována aplikace Multi-Factor Authentication. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) je snadné použití, škálovatelné a spolehlivé řešení, které poskytuje druhou metodu ověřování pro ochranu uživatelů. Azure Multi-Factor Authentication využívá sílu cloudu a integruje se s místními službami Active Directory a vlastními aplikacemi. Tato ochrana je rozšířená na nejdůležitější scénáře.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit data:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je modul hardwarového zabezpečení chráněný 2048 klíčem RSA.
- Všem uživatelům a identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.

**Azure Security Center**: Díky [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují při výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla výstrah](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) , která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

**Application Gateway**: Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway s bránou firewall webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří:

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

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy, což umožňuje společnou analýzu všech dat bez ohledu na jejich původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z Azure SQL Database. Řešení automatizace [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) zákazníkům umožňuje snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.

Network Watcher Azure: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network.  Entity Společenství by měly implementovat protokoly Network Watcher Flow pro skupin zabezpečení sítě a Virtual Machines. Tyto protokoly by se měly ukládat do vyhrazeného účtu úložiště, ve kterém jsou uložené jenom protokoly zabezpečení, a přístup k účtu úložiště by měl být zabezpečený pomocí řízení přístupu na základě rolí.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/au-protected-paaswa-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![PaaS webová aplikace pro model hrozeb chráněný na základě au](images/au-protected-paaswa-threat-model.png?raw=true "PaaS webová aplikace pro diagram modelu hrozeb chráněných v Au")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
Tato dokumentace k dodržování předpisů je založená na Microsoftu na platformách a službách dostupných od Microsoftu. V důsledku nejrůznějších zákaznických nasazení poskytuje tato dokumentace zobecněný přístup k řešení, které je hostované jenom v prostředí Azure. Zákazníci mohou identifikovat a používat alternativní produkty a služby na základě vlastních operačních prostředí a obchodních výsledků. Zákazníci, kteří chtějí používat místní prostředky, musí řešit zabezpečení a operace pro tyto místní prostředky. Zdokumentované řešení je možné přizpůsobit zákazníkům, aby vyřešilo konkrétní místní a bezpečnostní požadavky.

V tabulce [zákaznická zodpovědnost za podrobný plán zabezpečení a dodržování předpisů Azure-au](https://aka.ms/au-protected-crm) jsou uvedené všechny ovládací prvky zabezpečení, které potřebuje au-prot. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[PaaSa implementace webové aplikace](https://aka.ms/au-protected-paaswa-cim) , která je chráněná za běhu, obsahuje informace o tom, které ovládací prvky chráněné službou au jsou řešeny architekturou webové aplikace PaaS, včetně podrobných popisů podrobný plán zabezpečení a dodržování předpisů Azure způsob, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute

Pro klasifikované informace je třeba nakonfigurovat zabezpečené tunelové připojení IPSec pro IPSec, aby bylo možné bezpečně navázat spojení s prostředky nasazenými jako součást referenční architektury webové aplikace IaaS. Díky správnému nastavení sítě VPN s protokolem IPSec můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Když implementujete zabezpečené tunelové propojení VPN IPSec s Azure, můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení může probíhat přes Internet a zákazníkům umožňuje bezpečně "tunelové" informace uvnitř šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. 

Vzhledem k tomu, že přenosy v tunelu VPN procházejí Internetem pomocí sítě VPN typu Site-to-site, společnost Microsoft nabízí možnost privátního připojení. Azure ExpressRoute je vyhrazený odkaz mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange a považuje se za privátní síť. Vzhledem k tomu, že připojení ExpressRoute nepřecházejí přes Internet, tato připojení nabízejí spolehlivější, rychlejší a nižší latenci než typická připojení přes Internet. Vzhledem k tomu, že se jedná o přímé připojení zprostředkovatele telekomunikačních komunikací zákazníka, data necestují přes Internet, a proto se k nim nezveřejňují.

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure. 

Aby bylo možné lépe chránit klasifikovaná data, ať už používáte Internet nebo Azure ExpressRoute, musí si zákazníci nakonfigurovat protokol IPSec VPN, a to použitím následujících nastavení:

• Iniciátor VPN zákazníka musí být nakonfigurovaný tak, aby životnost přidružení zabezpečení nepřesahoval 14400 sekund.
• Iniciátor VPN zákazníka musí zakázat režim IKEv1 agresivní.
• Iniciátor VPN zákazníka musí nakonfigurovat Perfect Forward Secrecy.
• Iniciátor VPN zákazníka musí nakonfigurovat použití HMAC-SHA256 nebo vyšší.

Možnosti konfigurace pro zařízení VPN a parametry protokolu IPSec/IKE jsou [k dispozici](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) ke kontrole.

### <a name="azure-active-directory-setup"></a>Instalace Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat s Azure Active Directory [čtyřmi kliknutími](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express).

Kromě toho [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) zákazníkům umožňuje nakonfigurovat federaci s místními [Active Directory Federation Services (AD FS)]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) a Azure Active Directory. Pomocí federačního přihlášení můžou zákazníci uživatelům povolit, aby se přihlásili k Azure Active Directory službám pomocí jejich místních hesel a nemuseli zadávat hesla znovu v podnikové síti. Pomocí možnosti federace s Active Directory Federation Services (AD FS) můžete nasadit novou instalaci Active Directory Federation Services (AD FS) nebo můžete zadat existující instalaci v serverové farmě Windows Serveru 2012 R2.

Aby bylo možné zabránit tomu, aby se klasifikovaná data synchronizovaná s Azure Active Directory, můžou zákazníci omezit atributy, které se replikují do Azure Active Directory pomocí následujících nastavení v Azure Active Directory Connect:

- [Povolit filtrování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Zakázat synchronizaci hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Zakázat zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Zakázat zpětný zápis zařízení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Ponechte výchozí nastavení, [](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) aby nedocházelo k náhodným odstraněním a [automatickému upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade) .


## <a name="disclaimer"></a>Zřeknutí se práv

 - Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
 - Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
 - Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
 - Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
 - Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
 - Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
