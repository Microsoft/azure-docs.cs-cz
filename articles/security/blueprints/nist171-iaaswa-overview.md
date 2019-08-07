---
title: Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-IaaS pro NIST SP 800-171
description: Podrobný plán zabezpečení a dodržování předpisů Azure IaaS webová aplikace NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 9e5c894cedcbfd006d9406ce2c07fc0b17033d7c
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781040"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Webová aplikace Podrobný plán zabezpečení a dodržování předpisů Azure-IaaS pro NIST SP 800-171

## <a name="overview"></a>Přehled
[NIST Special publikace 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízených neklasifikovaných informací (CUI), které se nacházejí v nefederálních informačních systémech a organizacích. NIST SP 800-171 stanoví 14 rodin požadavků na zabezpečení pro ochranu důvěrnosti CUI.

Tento Podrobný plán zabezpečení a dodržování předpisů Azure poskytuje pokyny, které zákazníkům pomohou nasadit architekturu webové aplikace v Azure, která implementuje podmnožinu ovládacích prvků NIST SP 800-171. Toto řešení ukazuje, jak můžou zákazníci splnit konkrétní požadavky na zabezpečení a dodržování předpisů. Také slouží jako základ pro zákazníky, kteří můžou vytvářet a konfigurovat vlastní webové aplikace v Azure.

Tato referenční architektura, přidružená příručka implementace a model hrozeb, mají sloužit jako základ pro zákazníky, kteří přizpůsobují jejich konkrétní požadavky. Neměly by se používat tak, jak jsou v produkčním prostředí. Nasazení této architektury bez úprav není dostatečné pro zcela splnění požadavků nástroje NIST SP 800-171. Zákazníci zodpovídají za provádění odpovídajících posouzení zabezpečení a dodržování předpisů u všech řešení vytvořených pomocí této architektury. Požadavky se můžou lišit v závislosti na konkrétních implementacích jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a součásti
Tato Podrobný plán zabezpečení a dodržování předpisů Azure nasadí referenční architekturu pro webovou aplikaci v IaaS s SQL Server back-endu. Tato architektura zahrnuje webovou vrstvu, datovou vrstvu, infrastrukturu služby Active Directory, Azure Application Gateway a Azure Load Balancer. Virtuální počítače nasazené na webové a datové vrstvy jsou nakonfigurované ve skupině dostupnosti. Instance SQL Server jsou nakonfigurované ve skupině dostupnosti Always On pro zajištění vysoké dostupnosti. Virtuální počítače jsou připojené k doméně. Zásady skupiny služby Active Directory vynutily konfigurace zabezpečení a dodržování předpisů na úrovni operačního systému.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurují z Azure Portal. Úložiště šifruje všechna data pomocí Šifrování služby Storage a udržuje tak důvěrnost dat v klidovém umístění. Geograficky redundantní úložiště zajišťuje, že neškodná událost v primárním datovém centru zákazníka nevede ke ztrátě dat. Druhá kopie je uložená v samostatném umístění stovky mil.

Pro rozšířené zabezpečení se všechny prostředky v tomto řešení spravují jako skupina prostředků prostřednictvím Azure Resource Manager. Pro řízení přístupu k nasazeným prostředkům a klíčům v Azure Key Vault se používá Azure Active Directory (služba Azure AD) řízení přístupu na základě role (RBAC). Stav systému je monitorován prostřednictvím Azure Monitor. Zákazníci nakonfigurují protokoly služby monitoring Services pro zaznamenávání protokolů. Stav systému se zobrazuje v jednom řídicím panelu, který se snadno používá.

Hostitel bastionu pro správu poskytuje zabezpečené připojení pro správce pro přístup k nasazeným prostředkům. *Microsoft doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a import dat do podsítě referenční architektury.*


![IaaS webová aplikace pro NIST SP 800-171 – diagram referenční architektury](images/nist171-iaaswa-architecture.png "IaaS webová aplikace pro NIST SP 800-171 – diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v části [architektura nasazení](#deployment-architecture) .

- Azure Virtual Machines
    - (1) Správa/bastionu (Windows Server 2016 Datacenter)
    - (2) řadič domény služby Active Directory (Windows Server 2016 Datacenter)
    - (2) SQL Server uzel clusteru (SQL Server 2017 v systému Windows Server 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (1)/16 sítí
    - (5)/24 sítí
    - (5) skupiny zabezpečení sítě
- Skupiny dostupnosti
    - (1) řadiče domény služby Active Directory
    - (1) uzly clusteru SQL
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Firewall webových aplikací
        - Režim brány firewall: prevence
        - Sada pravidel: OWASP 3,0
        - Port naslouchacího procesu: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (protokoly)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Disk s kopií cloudu
- Trezor Recovery Services

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje prvky nasazení a implementace.

**Bastionu hostitel**: Hostitel bastionu je jediným bodem záznamu, který uživatelé můžou použít pro přístup k nasazeným prostředkům v tomto prostředí. Bastionu hostitel poskytuje zabezpečené připojení k nasazeným prostředkům tím, že povoluje pouze vzdálený provoz z veřejných IP adres v bezpečném seznamu. Aby bylo možné provoz vzdálené plochy povolit, musí být zdroj provozu definovaný ve skupině zabezpečení sítě (NSG).

Toto řešení vytvoří virtuální počítač jako hostitele bastionu připojeného k doméně s následujícími konfiguracemi:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí Key Vault.
-   [Zásady automatického](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) vypínání, které snižují spotřebu prostředků virtuálních počítačů, pokud se nepoužívají.
-   [Ochrana Credential Guard v programu Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povolená, aby přihlašovací údaje a další tajné kódy běžely v chráněném prostředí, které je izolované od běžícího operačního systému.

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředky v architektuře s oddělenými podsítěmi pro web, databázi, službu Active Directory a správu v rámci virtuální sítě. Podsítě jsou logicky oddělené NSG pravidly použitými pro jednotlivé podsítě. Pravidla omezují provoz mezi podsítěmi pouze na ta, která je nutná pro funkce systému a správy.

Podívejte se na konfiguraci pro [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazené v tomto řešení. Organizace můžou nakonfigurovat skupin zabezpečení sítě úpravou předchozího souboru pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako příručky.

Každá z podsítí má vyhrazenou NSG:
- Jeden NSG pro Application Gateway (LBNSG)
- Jeden NSG pro hostitele bastionu (MGTNSG)
- Jeden NSG pro primární a záložní řadič domény (ADNSG)
- Jeden NSG pro SQL Server a disk s kopií cloudu (SQLNSG)
- Jeden NSG pro webovou vrstvu (WEBNSG)

### <a name="data-in-transit"></a>Přenášená data
Azure ve výchozím nastavení šifruje veškerou komunikaci mezi datovými centry Azure a z nich. Kromě toho všechny transakce úložiště prostřednictvím Azure Portal probíhají prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém měřítku prostřednictvím několika měr. Mezi tyto míry patří šifrování a auditování databáze.

**Azure Storage**: Aby bylo možné splňovat požadavky na zašifrovaná data v klidovém stavu, všechna [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato funkce pomáhá chránit a chránit data při podpoře závazků zabezpečení organizace a požadavků na dodržování předpisů definovaných v NIST SP 800-171.

**Azure Disk Encryption**: Šifrování disku se používá k šifrování disků virtuálních počítačů s Windows IaaS. [Šifrování disku](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) používá funkci nástroje BitLocker systému Windows k poskytování šifrování svazku pro operační systém a datové disky. Řešení je integrované s Key Vault, které vám pomůžou řídit a spravovat šifrovací klíče disku.

**SQL Server**: Instance SQL Server používá následující bezpečnostní opatření databáze:
-   [SQL Server auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) sleduje události databáze a zapisuje je do protokolů auditu.
-   [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) provádí šifrování a dešifrování databáze, přidružených záloh a souborů protokolů transakcí v reálném čase, aby chránila informace v klidovém čase. Transparentní šifrování dat poskytuje záruku, že uložená data nepodléhají neoprávněnému přístupu.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) zabraňují všem přístupům k databázovým serverům, dokud nebudou udělena příslušná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Šifrované sloupce](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zajišťují, že citlivá data se v databázovém systému nikdy neobjevují jako prostý text. Po povolení šifrování dat budou mít přístup k datům ve formátu prostého textu jenom klientské aplikace nebo aplikační servery s přístupem k klíčům.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) omezuje vystavení citlivých dat tím, že je maskování dat pro uživatele nebo aplikace neprivilegovaných oprávnění. Může automaticky zjistit potenciálně citlivá data a navrhnout vhodné masky pro použití. Dynamické maskování dat pomáhá snižovat přístup, takže citlivá data neukončí databázi prostřednictvím neoprávněného přístupu. *Zákazníci zodpovídají za úpravu nastavení, aby odpovídaly schématu databáze.*

### <a name="identity-management"></a>Správa identit
Následující technologie poskytují možnosti pro správu přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je cloudová cloudová služba a služba pro správu identit založená na víceklientské architektuře Microsoftu. Všichni uživatelé pro toto řešení se vytvářejí v Azure AD a zahrnují uživatele, kteří přistupují k instanci SQL Server.
-   Ověřování pro aplikaci se provádí pomocí Azure AD. Další informace najdete v tématu [Integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   Správci můžou pomocí služby [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) definovat jemně odstupňovaná přístupová oprávnění, která jim umožňují udělit jenom přístup, který potřebují k provádění svých úloh. Místo udělení všech uživatelů bez omezení oprávnění pro prostředky Azure můžou správci pro přístup k datům dovolit jenom určité akce. Přístup k předplatnému je omezený na správce předplatného.
- Zákazníci můžou použít [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) k minimalizaci počtu uživatelů, kteří mají přístup k určitým prostředkům. Správci můžou pomocí Azure AD Privileged Identity Management zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce se dá v případě potřeby použít taky k prosazování přístupu pro správu za běhu na vyžádání.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detekuje potenciální ohrožení zabezpečení, která ovlivňují identity organizace. Konfiguruje automatizované reakce na zjištěné podezřelé akce související s identitami organizace. Prošetří také podezřelé incidenty, aby mohla přijmout vhodná opatření k jejich vyřešení.

### <a name="security"></a>Zabezpečení
**Správa tajných**kódů: Řešení používá [Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané v cloudových aplikacích a službách. Následující možnosti Key Vault můžou zákazníkům chránit data:
- Zásady pokročilého přístupu se konfigurují podle potřeby.
- Zásady přístupu Key Vault jsou definované s minimálními požadovanými oprávněními pro klíče a tajné kódy.
- Všechny klíče a tajné kódy v Key Vault mají datum vypršení platnosti.
- Všechny klíče v Key Vault jsou chráněny specializovanými moduly hardwarového zabezpečení. Typem klíče je hardwarový klíč chráněný modulem zabezpečení 2048.
- Všem uživatelům a identitám se uděluje minimální požadovaná oprávnění pomocí RBAC.
- Diagnostické protokoly pro Key Vault jsou povolené s dobou uchování minimálně 365 dní.
- Povolené kryptografické operace pro klíče jsou omezené na ty, které jsou povinné.
- Řešení je integrované s Key Vault pro správu šifrovacích klíčů a tajných klíčů virtuálního počítače IaaS.

**Správa oprav**: Virtuální počítače s Windows nasazené jako součást této referenční architektury jsou ve výchozím nastavení nakonfigurované tak, aby přijímaly automatické aktualizace z web Windows Update služby. Toto řešení zahrnuje taky službu [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) , prostřednictvím které se dají aktualizovat nasazení, aby se virtuální počítače v případě potřeby daly opravovat.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pro virtuální počítače poskytuje možnost ochrany v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Zákazníci můžou nakonfigurovat výstrahy, které generují, když se v chráněných virtuálních počítačích pokusí o instalaci nebo spuštění známého škodlivého softwaru.

**Azure Security Center**: Díky [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)můžou zákazníci centrálně používat a spravovat zásady zabezpečení napříč úlohami, omezovat vystavení hrozeb a rozpoznávat a reagovat na útoky. Security Center taky přistupuje k existujícím konfiguracím služeb Azure, aby poskytovala doporučení týkající se konfigurace a služeb, která vám pomůžou zdokonalit stav zabezpečení a chránit data.

Security Center využívá celou řadu možností detekce pro upozornění zákazníků na potenciální útoky, které cílí na jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center má sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , které se aktivují v případě, že dojde k ohrožení nebo k podezřelé aktivitě. Zákazníci můžou pomocí [vlastních pravidel upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení na základě dat, která už jsou shromážděná z jejich prostředí.

Security Center poskytuje prioritní výstrahy zabezpečení a incidenty. Security Center usnadňuje zákazníkům zjišťovat a řešit potenciální problémy se zabezpečením. Pro každou zjištěnou hrozbu se vygeneruje [Sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) . Týmy reakce na incidenty mohou sestavy používat při vyšetřování a nápravě hrozeb.

Tato referenční architektura využívá funkci [posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) v Security Center. Po nakonfigurování partnerský Agent (například Qualys) oznamuje data o ohrožení na platformu pro správu partnera. Platforma partnera pro správu pak poskytuje data o ohrožení zabezpečení a data monitorování stavu zpět do služby Security Center. Zákazníci můžou tyto informace využít k rychlé identifikaci zranitelných virtuálních počítačů.

**Application Gateway Azure**: Architektura snižuje riziko ohrožení zabezpečení pomocí aplikační brány s nakonfigurovanou bránou firewall webových aplikací a povolenou sadou pravidel OWASP. Mezi další možnosti patří:

- [Koncová SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Povolte [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Zakažte [TLS v 1.0 a v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim prevence).
- [Režim prevence](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) se sadou pravidel OWASP 3,0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Vlastní sondy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Security Center také nabízí systém reputace.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost:** Řešení nasadí všechny virtuální počítače ve [skupině dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují, že jsou virtuální počítače distribuované napříč několika izolovanými hardwarovými clustery, aby se zlepšila dostupnost. Během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač, který splňuje 99,95% Azure SLA.

**Recovery Services trezor**: Data zálohování [Recovery Services trezoru](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) a chrání všechny konfigurace Azure Virtual Machines v této architektuře. S úložištěm Recovery Services můžou zákazníci obnovit soubory a složky z virtuálního počítače s IaaS bez obnovení celého virtuálního počítače. Tento proces zrychluje dobu obnovení.

**Disk s kopií cloudu**: [Disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určujícího clusteru kvora clusteru s podporou převzetí služeb při selhání ve Windows serveru 2016, který jako rozhodčí bod používá Azure. Disk s kopií cloudu, stejně jako jakákoli jiná určující sdílená složka kvora, získá hlas a může se zúčastnit výpočtů kvora. Používá standardní veřejně dostupné úložiště objektů BLOB v Azure. Toto uspořádání vylučuje mimořádnou režii při údržbě virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služba Azure výrazně zaznamenává činnost systému a uživatele a také stav systému:
- **Protokoly aktivit**: [Protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operacích provedených u prostředků v rámci předplatného. Protokoly aktivit můžou přispět k určení iniciátoru operace, času výskytu a stavu.
- **Diagnostické protokoly**: [Diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obsahují všechny protokoly emitované každým prostředkem. Mezi tyto protokoly patří protokoly systému událostí systému Windows, protokoly úložiště, Key Vault protokoly auditu a protokoly brány firewall Application Gateway a brány firewall. Všechny diagnostické protokoly zapisují do centralizovaného a šifrovaného účtu Azure Storage pro účely archivace. Uživatelé můžou nakonfigurovat dobu uchování až 730 dní, aby splnily konkrétní požadavky.

**Protokoly Azure monitor**: Tyto protokoly jsou konsolidovány v [protokolech Azure monitor](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicích panelů. Po shromáždění dat se data uspořádají do samostatných tabulek pro každý datový typ v Log Analytics pracovní prostory. Tímto způsobem lze analyzovat všechna data společně bez ohledu na její původní zdroj. Security Center se integruje s protokoly Azure Monitor. Zákazníci můžou pomocí dotazů Kusto přistupovat ke svým datům události zabezpečení a kombinovat je s daty z jiných služeb.

Součástí této architektury jsou tato [řešení monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure:
-   [Vyhodnocení služby Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Řešení pro kontrolu stavu služby Active Directory posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Poskytuje prioritní seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Vyhodnocení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Řešení pro kontrolu stavu SQL posuzuje rizika a stav serverových prostředí v pravidelných intervalech. Zákazníkům nabízí seznam doporučení specifických pro nasazenou serverovou infrastrukturu.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health řešení oznamuje, kolik agentů je nasazeno a jejich geografická distribuce. Také oznamuje, kolik agentů neodpovídá a kolik agentů odesílá provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Řešení Activity Log Analytics pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure pro zákazníka.

**Azure Automation**: [Automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje Runbooky. V tomto řešení můžou sady Runbook shromažďovat protokoly z SQL Server. Zákazníci můžou pomocí řešení automatizace [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) snadno identifikovat změny v prostředí.

**Azure Monitor**: [Monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledovat výkon, zachovat zabezpečení a identifikovat trendy. Organizace je můžou použít k auditu, vytváření výstrah a archivaci dat. Můžou taky sledovat volání rozhraní API ve svých prostředcích Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici ke [stažení](https://aka.ms/nist171-iaaswa-tm) nebo najdete tady. Tento model může zákazníkům usnadnit pochopení bodů potenciálního rizika v systémové infrastruktuře při provádění úprav.

![Webová aplikace IaaS pro model hrozeb NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Webová aplikace IaaS pro model hrozeb NIST SP 800-171")

## <a name="compliance-documentation"></a>Dokumentace k dodržování předpisů

[Tabulka zodpovědnosti zákazníka podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171](https://aka.ms/nist171-crm) obsahuje všechny ovládací prvky zabezpečení, které vyžaduje NIST SP 800-171. Tato matice podrobně popisuje, zda je implementace každého ovládacího prvku zodpovědností od společnosti Microsoft, zákazníka nebo sdílená mezi nimi.

[Podrobný plán zabezpečení a dodržování předpisů Azure-NIST sp 800-171 IaaS pro implementaci ovládacího prvku webové aplikace](https://aka.ms/nist171-iaaswa-cim) poskytuje informace o tom, které ovládací prvky NIST SP 800-171 jsou řešeny architekturou webové aplikace IaaS. Obsahuje podrobný popis způsobu, jakým implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Doprovodné materiály a doporučení
### <a name="vpn-and-expressroute"></a>VPN a ExpressRoute
Zabezpečené tunelové připojení VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurované tak, aby bezpečně navázalo připojení k prostředkům nasazeným jako součást referenční architektury webové aplikace IaaS. Díky správnému nastavení sítě VPN nebo ExpressRoute můžou zákazníci přidat vrstvu ochrany dat při přenosu.

Díky implementaci zabezpečeného tunelu VPN s Azure je možné vytvořit virtuální privátní připojení mezi místní sítí a službou Azure Virtual Network. Toto připojení probíhá přes Internet. Zákazníci můžou toto připojení použít k bezpečnému propojení informací v rámci šifrovaného propojení mezi sítí zákazníka a Azure. Síť Site-to-Site VPN je zabezpečená a Vyspělá technologie, kterou vyvinuly podniky všech velikostí po desetiletí. [Režim tunelového propojení IPSec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se v této možnosti používá jako šifrovací mechanismus.

Vzhledem k tomu, že provoz v rámci tunelu VPN prochází Internetem pomocí sítě VPN typu Site-to-site, nabízí společnost Microsoft ještě ještě bezpečnější možnost připojení. ExpressRoute je vyhrazené propojení WAN mezi Azure a místním umístěním nebo poskytovatelem hostingu Exchange. Připojení ExpressRoute se připojují přímo k poskytovateli telekomunikačních služeb zákazníka. V důsledku toho se data necestují přes Internet a nejsou vystavená. Tato připojení nabízejí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení. 

[K dispozici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)jsou osvědčené postupy pro implementaci zabezpečené hybridní sítě, která rozšiřuje místní síť do Azure.

## <a name="disclaimer"></a>Zřeknutí se práv

- Tento dokument slouží pouze k informativním účelům. SPOLEČNOST MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, AŤ UŽ VÝSLOVNĚ UVEDENÉ, PŘEDPOKLÁDANÉ NEBO STATUTÁRNÍ, JAKO INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "tak, jak je". Informace a názory vyjádřené v tomto dokumentu, včetně adres URL a dalších odkazů na internetové weby, se mohou změnit bez předchozího upozornění. Zákazníci, kteří si tento dokument přečetli, nesou riziko jeho používání. 
- Tento dokument neposkytuje zákazníkům žádná zákonná práva k žádnému duševnímu vlastnictví jakéhokoli produktu nebo řešení společnosti Microsoft. 
- Zákazníci můžou tento dokument zkopírovat a používat pro interní referenční účely. 
- Některá doporučení v tomto dokumentu můžou vést k vyššímu využití dat, sítě nebo výpočetních prostředků v Azure a můžou zvýšit náklady na licence Azure a předplatné Azure. 
- Tato architektura má sloužit jako základ pro zákazníky, kteří si můžou přizpůsobit jejich konkrétní požadavky a neměla by se používat tak, jak jsou v produkčním prostředí.
- Tento dokument se vyvíjí jako referenční informace a neměl by se používat k definování všech prostředků, kterými zákazník může splnit konkrétní požadavky a předpisy dodržování předpisů. Zákazníci by měli v organizaci požádat o platnou podporu ve schválených zákaznických implementacích.
