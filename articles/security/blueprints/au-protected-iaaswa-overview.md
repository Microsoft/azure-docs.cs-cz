---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-IaaS pro Austrálii CHRÁNĚNou
description: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-IaaS pro Austrálii CHRÁNĚNou
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 50e410fc439be7b3a5f4c1e8d4bab5d60c3c4f52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946928"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-IaaS pro Austrálii chráněnou

## <a name="overview"></a>Přehled
Tato Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny pro nasazení prostředí infrastruktury jako služby (IaaS), které je vhodné pro shromažďování, ukládání a načítání dat státní správy CHRÁNĚNých za běhu, která jsou v souladu s cíli Služba australské vlády Information Security Manual (ISM) vytvořená ředitelstvím Austrálie (australských signálů). Tento podrobný plán předvádí společnou referenční architekturu a pomáhá předvést správné zpracování citlivých dat státní správy v zabezpečeném a kompatibilním prostředí s více vrstvami.

Tato referenční architektura, Průvodce implementací a model hrozeb poskytují základ pro zákazníky, kteří zajišťují své vlastní plánování a akreditační procesy při schvalování systému a pomáhají zákazníkům nasazovat úlohy do Azure v souladu se standardem ASD. Zákazníci se můžou rozhodnout implementovat Azure VPN Gateway nebo ExpressRoute, aby mohli používat federované služby, a integrovat místní prostředky s prostředky Azure. Zákazníci musí vzít v úvahu důsledky zabezpečení při používání místních prostředků. K splnění všech požadavků se vyžaduje další konfigurace, která se může lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

Dosahování dodržování předpisů ASD vyžaduje, aby systém provedl audit informací zaregistrovaných v systému. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u každého řešení vytvořeného pomocí této architektury, protože požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Toto řešení nasadí referenční architekturu pro webovou aplikaci v IaaS pomocí back-endu SQL Server. Tato architektura zahrnuje webovou vrstvu, datovou vrstvu, infrastrukturu služby Active Directory, Application Gateway a Load Balancer. Virtuální počítače nasazené na webové a datové vrstvy jsou nakonfigurované ve skupině dostupnosti a SQL Server instance jsou pro vysokou dostupnost nakonfigurované ve skupině dostupnosti Always On. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory slouží k vymáhání zabezpečení a konfigurací dodržování předpisů na úrovni operačního systému. Hostitel bastionu pro správu poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům.

Tato architektura může doručovat zabezpečené hybridní prostředí, které rozšiřuje místní síť do Azure, a umožňuje tak zabezpečený webovým úlohám využívat podnikové uživatele v privátní místní síti nebo Internetu organizace. U místních řešení je zákazníkem jak účet, tak zodpovědný za všechny aspekty zabezpečení, provozu a dodržování předpisů.

Prostředky Azure, které jsou součástí tohoto řešení, se můžou připojit k místní síti nebo zařízení Canberra datacentra (např. CDC v) přes IPSec VPN pomocí Azure VPN Gateway nebo prostřednictvím ExpressRoute. Rozhodnutí o použití sítě VPN by mělo být provedeno s klasifikací přenášených dat a s ohledem na cestu k síti. Zákazníci, kteří využívají rozsáhlé a důležité úlohy s vysokými nároky na data, by měli zvážit hybridní síťovou architekturu s využitím ExpressRoute pro připojení privátních sítí ke službám Azure. Další podrobnosti o mechanismech připojení k Azure najdete v části doprovodné materiály a doporučení.

K ověřování uživatelů pomocí místních přihlašovacích údajů a přístupu ke všem prostředkům v cloudu s využitím místní infrastruktury Active Directory Federation Services (AD FS) by se měla použít federace s Azure Active Directory. Active Directory Federation Services (AD FS) může pro toto hybridní prostředí poskytovat zjednodušenou, zabezpečenou federaci identit a možnosti jednotného přihlašování k webu. Další podrobnosti Azure Active Directory instalaci najdete v části doprovodné materiály a doporučení.

Řešení používá Azure Storage účty, které můžou zákazníci nakonfigurovat tak, aby používali Šifrování služby Storage k zachování důvěrnosti dat v klidovém provozu. Azure ukládá pro zajištění odolnosti tři kopie dat v rámci vybrané oblasti zákazníka. Oblasti Azure jsou nasazené ve dvojicích odolných oblastí a geografické redundantní úložiště zajišťuje, aby se data replikují do druhé oblasti se třemi kopiemi. Tím se zabrání nežádoucí události v primárním datovém umístění zákazníka, což má za následek ztrátu dat.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Azure Active Directory řízení přístupu na základě rolí se používá pro řízení přístupu k nasazeným prostředkům a klíčům v Azure Key Vault. Stav systému je monitorován prostřednictvím Azure Security Center a Azure Monitor. Zákazníci konfigurují jak služby monitorování tak, aby zachytával protokoly a zobrazovaly stav systému v jednom snadno naviguje řídicím panelu. Azure Application Gateway je nakonfigurovaný jako brána firewall v režimu prevence a vyžaduje přenos TLS s minimální verzí 1,2.

![Referenční architektura chráněná ve au – IaaS webové aplikace](images/au-protected-iaaswa-architecture.png?raw=true "IaaS webová aplikace pro diagram referenční architektury chráněnou au") 

Toto řešení používá následující služby Azure. Další podrobnosti najdete v části [architektura nasazení](#deployment-architecture) .

- Sady dostupnosti
    - (1) uzly clusteru SQL
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Firewall webových aplikací
        - Režim brány firewall: prevence
        - Sada pravidel: OWASP 3,0
        - Port naslouchacího procesu: 443
- Sdílená složka Azure v cloudu
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Protokoly služby Azure Monitor
- Azure Storage
- Azure Virtual Machines
    - (1) Správa/bastionu (Windows Server 2016 Datacenter)
    - (2) SQL Server uzel clusteru (SQL Server 2017 v systému Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (4) skupiny zabezpečení sítě
    - Azure Network Watcher
- Recovery Services trezor

Tento podrobný plán obsahuje služby Azure, které nebyly certifikovány pro použití s chráněnou klasifikací prostřednictvím australského centra Internet Security Center (ACSC). Všechny služby zahrnuté v této referenční architektuře byly certifikovány ACSC na úrovni značek omezení rozšiřování (DLM). Společnost Microsoft doporučuje, aby si zákazníci zkontrolovali publikované sestavy zabezpečení a auditu týkající se těchto služeb Azure a používali jejich rámec řízení rizik k určení, jestli je služba Azure vhodná pro jejich interní akreditaci a použití na Chráněná klasifikace.

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem vstupu, který umožňuje uživatelům přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje vzdálený provoz z veřejných IP adres v bezpečném seznamu. Pokud chcete povolit provoz vzdálené plochy (RDP), musí být zdroj provozu definovaný ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Rozšíření Azure Diagnostics](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) pomocí Azure Key Vault
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředky v architektuře pomocí samostatné webové podsítě, podsítě databáze, podsítě služby Active Directory a podsítě pro správu v rámci virtuální sítě. Podsítě jsou logicky oddělené pomocí pravidel skupin zabezpečení sítě použitých pro jednotlivé podsítě, aby se omezil provoz mezi podsítěmi jenom na to, co je potřeba pro funkce systému a správy.

Viz konfigurace pro [skupiny zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazené v tomto řešení. Organizace můžou nakonfigurovat skupiny zabezpečení sítě úpravou výše uvedeného souboru pomocí [této dokumentace](../../virtual-network/virtual-network-vnet-plan-design-arm.md) jako příručky.

Každá z podsítí má vyhrazenou skupinu zabezpečení sítě:
- 1 skupina zabezpečení sítě pro Application Gateway (LBNSG)
- 1 skupina zabezpečení sítě pro hostitele bastionu (MGTNSG)
- 1 skupina zabezpečení sítě pro SQL servery a disk s kopií cloudu (SQLNSG)
- 1 skupina zabezpečení sítě pro webovou vrstvu (WEBNSG)

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datacentry Azure a z nich. 

Pro chráněná data při přenosu od sítí vlastněných zákazníky používá architektura Internet nebo ExpressRoute s VPN Gateway nakonfigurovaným pomocí protokolu IPSEC.

Všechny transakce do Azure prostřednictvím portálu pro správu Azure nastávají taky prostřednictvím protokolu HTTPS, který využívá protokol TLS 1,2.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím šifrování, auditování databáze a dalších měr.

**Azure Storage**: Aby bylo možné vyhovět šifrovaným datům v požadavcích REST, používá všechny [Azure Storage](https://azure.microsoft.com/services/storage/) [šifrování služby Storage](../../storage/common/storage-service-encryption.md). Tato ochrana pomáhá chránit a chránit data v rámci podpory závazků organizace a požadavků na dodržování předpisů, které definuje australský státní správa ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) využívá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro datové disky. Řešení se integruje s Azure Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**SQL Server**: Instance SQL Server používá následující bezpečnostní opatření databáze:
-   [SQL Server auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) sleduje události databáze a zapisuje je do protokolů auditu.
-   [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém čase. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Šifrované sloupce](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zajišťují, že citlivá data se v databázovém systému nikdy neobjeví v podobě prostého textu. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem ke klíčům.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) omezuje expozici citlivých dat tím, že se data maskuje na uživatele nebo aplikace bez oprávnění. Dynamické maskování dat může automaticky zjišťovat potenciálně citlivá data a navrhovat vhodné masky pro použití. To pomáhá snižovat přístup tak, že citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. **Zákazníci zodpovídají za úpravu nastavení dynamického maskování dat, aby odpovídaly schématu databáze.**

### <a name="identity-management"></a>Správa identit
Zákazníci můžou využívat místní federované služby Active Directory pro federovat s [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), což je víceklientské cloudové adresáře a služba pro správu identit od Microsoftu. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integruje místní adresáře s Azure Active Directory. Všichni uživatelé v tomto řešení vyžadují účty Azure Active Directory. S přihlašováním pomocí federace se uživatelé můžou přihlašovat k Azure Active Directory a provádět ověřování v prostředcích Azure pomocí místních přihlašovacích údajů.

Kromě toho následující Azure Active Directory možnosti pomůžou spravovat přístup k datům v prostředí Azure:
- Ověřování pro aplikaci se provádí pomocí Azure Active Directory. Další informace najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- [Řízení přístupu na základě role v Azure](../../role-based-access-control/role-assignments-portal.md) umožňuje správcům definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který uživatelé potřebují k provádění svých úloh. Místo udělení oprávnění pro všechny uživatele bez omezení pro prostředky Azure můžou správci pro přístup k datům dovolit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) zákazníkům umožňuje minimalizovat počet uživatelů, kteří mají přístup k určitým informacím. Správci můžou pomocí Azure Active Directory Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detekuje potenciální ohrožení zabezpečení ovlivňující identity&#39;organizace a nakonfiguruje automatizované odezvy na zjištěné podezřelé akce související s&#39;identitami organizace s. zkoumá podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

**Ověřování Azure Multi-Factor Authentication**: K ochraně identit by měla být implementována aplikace Multi-Factor Authentication. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) je snadné použití, škálovatelné a spolehlivé řešení, které poskytuje druhou metodu ověřování pro ochranu uživatelů. Azure Multi-Factor Authentication využívá sílu cloudu a integruje se s místními službami Active Directory a vlastními aplikacemi. Tato ochrana je rozšířená na nejdůležitější scénáře.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující možnosti Azure Key Vault můžou zákazníkům chránit a přistupovat k těmto datům:

- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typ klíče je modul hardwarového zabezpečení chráněný 2048 klíčem RSA.
- Všem uživatelům a identitám jsou udělena minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.
- Řešení je integrované s Azure Key Vault pro správu šifrovacích klíčů a tajných klíčů virtuálního počítače IaaS.

**Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje také službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které lze vytvořit aktualizované nasazení, aby se v případě potřeby daly opravit virtuální počítače.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software s konfigurovatelnými výstrahami v případě, že se známý škodlivý nebo nežádoucí software pokusí Nainstalujte nebo spusťte na chráněných virtuálních počítačích.

**Azure Security Center**: Díky [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Kromě toho Azure Security Center přistupuje k existujícím konfiguracím služeb Azure a poskytuje doporučení ke konfiguraci a službám, které vám pomůžou zlepšit zabezpečení stav a chránit data.

Azure Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují při výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla výstrah](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) v Azure Security Center umožňují zákazníkům definovat nové výstrahy zabezpečení na základě dat, která už jsou z jejich prostředí shromážděná.

Azure Security Center poskytuje prioritní výstrahy zabezpečení a incidenty, které zákazníkům zjednodušují zjišťování a řešení potenciálních problémů se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) , která pomáhá týmům reakce na incidenty při zkoumání a opravai hrozeb.

Kromě toho tato referenční architektura využívá [posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) v Azure Security Center. Po nakonfigurování partnerský Agent (např. Qualys) oznamuje data o ohrožení platformou správy partnera. Platforma pro správu partnera pak poskytuje chyby zabezpečení a sledování stavu zpět do Azure Security Center a umožňuje zákazníkům rychle identifikovat ohrožené virtuální počítače.

**Application Gateway Azure**: Architektura snižuje riziko ohrožení zabezpečení pomocí Application Gateway Azure s nakonfigurovanou bránou firewall webových aplikací a povoleným rulesetem OWASP. Mezi další možnosti patří:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](../../application-gateway/create-ssl-portal.md)
- Zakázat [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](../../application-gateway/waf-overview.md) (režim prevence)
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) s OWASP 3,0 RuleSet
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastní sondy stavu](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také nabízí systém reputace.

### <a name="business-continuity"></a>Kontinuita podnikových procesů
**Vysoká dostupnost:** Řešení nasadí všechny virtuální počítače ve [skupině dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují, že se virtuální počítače distribuují napříč více izolovanými hardwarovými clustery, aby se zlepšila dostupnost. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace Azure Virtual Machines v této architektuře. Pomocí trezoru Recovery Services mohou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače, což umožní rychlejší obnovení.

**Disk s kopií cloudu**: [Disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání ve Windows serveru 2016, který využívá Azure jako arbitrážního bodu. Disk s kopií cloudu, stejně jako jakýkoli jiný určující disk kvora, získá hlas a může se zúčastnit výpočtů kvora, ale používá standardně veřejně dostupný Blob Storage Azure. Tím se eliminuje mimořádná režie při údržbě virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](../../azure-monitor/platform/activity-logs-overview.md) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí Windows, protokoly Azure Storage, Key Vault protokoly auditu a protokoly Application Gateway přístupu a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uchovávání dat je uživatelsky konfigurovatelné, až 730 dní, aby se splnily požadavky na uchovávání specifické pro konkrétní organizaci.

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění se data organizují do samostatných tabulek pro jednotlivé datové typy, což umožňuje společnou analýzu všech dat bez ohledu na jejich původní zdroj. Kromě toho Azure Security Center integruje s protokoly Azure Monitor a umožňuje zákazníkům používat dotazy Kusto pro přístup k datům událostí zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](../../monitoring/monitoring-solutions.md) Azure:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): Řešení kontroly stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech a nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech a zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografickou distribuci, a také počet nereagujících agentů a počet agentů, kteří odesílají provozní data.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou Runbooky shromažďovat protokoly z Azure SQL Server. Řešení automatizace [Change Tracking](../../automation/change-tracking.md) zákazníkům umožňuje snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, udržovat zabezpečení a identifikovat trendy tím, že umožňují organizacím auditovat, vytvářet výstrahy a archivovat data, včetně sledovacích volání rozhraní API ve svých prostředcích Azure.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcher poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve virtuální síti Azure.  Entity Společenství by měly implementovat protokoly Network Watcher Flow pro skupin zabezpečení sítě a Virtual Machines. Tyto protokoly by se měly ukládat do vyhrazeného účtu úložiště, ve kterém jsou uložené jenom protokoly zabezpečení, a přístup k účtu úložiště by měl být zabezpečený pomocí řízení přístupu na základě rolí.

## <a name="threat-model"></a>Model hrozeb
Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/au-protected-iaaswa-tm) nebo najdete níže. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![IaaS webová aplikace pro model hrozeb chráněný na základě au](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS webová aplikace pro diagram modelu hrozeb chráněných v Au")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů
Tato dokumentace k dodržování předpisů je založená na Microsoftu na platformách a službách dostupných od Microsoftu. V důsledku nejrůznějších zákaznických nasazení poskytuje tato dokumentace zobecněný přístup k řešení, které je hostované jenom v prostředí Azure. Zákazníci mohou identifikovat a používat alternativní produkty a služby na základě vlastních operačních prostředí a obchodních výsledků. Zákazníci, kteří chtějí používat místní prostředky, musí řešit zabezpečení a operace pro tyto místní prostředky. Zdokumentované řešení je možné přizpůsobit zákazníkům, aby vyřešilo konkrétní místní a bezpečnostní požadavky.

V poli [podrobný plán zabezpečení a dodržování předpisů Azure –](https://aka.ms/au-protected-crm) v tabulce zodpovědnosti zákazníka jsou uvedeny všechny ovládací prvky zabezpečení, které jsou chráněné službou au. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[IaaS – matice implementace webové aplikace](https://aka.ms/au-protected-iaaswa-cim) , která je chráněná podrobný plán zabezpečení a dodržování předpisů Azure pomocí nástroje au, obsahuje informace o tom, které ovládací prvky chráněné službou au jsou řešeny architekturou webové aplikace IaaS, včetně podrobných popisů způsob, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

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
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) je zásadní pro správu nasazení a zřizování přístupu k pracovníkům, kteří pracují s prostředím. Existující služba Windows Server Active Directory se dá integrovat s Azure Active Directory [čtyřmi kliknutími](../../active-directory/hybrid/how-to-connect-install-express.md).

Kromě toho [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) zákazníkům umožňuje nakonfigurovat federaci s místními [Active Directory Federation Services (AD FS)]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) a Azure Active Directory. Pomocí federačního přihlášení můžou zákazníci uživatelům povolit, aby se přihlásili k Azure Active Directory službám pomocí jejich místních hesel a nemuseli zadávat hesla znovu v podnikové síti. Pomocí možnosti federace s Active Directory Federation Services (AD FS) můžete nasadit novou instalaci Active Directory Federation Services (AD FS) nebo můžete zadat existující instalaci v serverové farmě Windows Serveru 2012 R2.

Aby bylo možné zabránit tomu, aby se klasifikovaná data synchronizovaná s Azure Active Directory, můžou zákazníci omezit atributy, které se replikují do Azure Active Directory pomocí následujících nastavení v Azure Active Directory Connect:
- [Povolit filtrování](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Zakázat synchronizaci hodnot hash hesel](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- [Zakázat zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Zakázat zpětný zápis zařízení](../../active-directory/hybrid/how-to-connect-device-writeback.md)
- Ponechte výchozí nastavení, [](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) aby nedocházelo k náhodným odstraněním a [automatickému upgradu](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md) .

## <a name="disclaimer"></a>Zřeknutí se práv
- Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání.
- Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft.
- Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely.
- Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure.
- Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
- Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
