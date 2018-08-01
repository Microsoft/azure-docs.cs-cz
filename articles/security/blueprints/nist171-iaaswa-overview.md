---
title: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro SP NIST 800-171
description: Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace podle NIST SP 800-171
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f19478b29fd96cd2bd3ed5e0e994595d7b00edf3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391981"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro SP NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů obsahuje pokyny, které pomáhá zákazníkům nasadit architektura pro webové aplikace v Azure, která implementuje podmnožinu NIST SP 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů a slouží jako základ pro zákazníky, kteří k vytvoření a konfigurace webových aplikací v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky a nepoužívali-je v produkčním prostředí. Nasazení této architektury bez jakýchkoli úprav není dostatečná k SP NIST 800-171 zcela požadavkům. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů řešení vytvořené pomocí této architektury, jako požadavky se mohou lišit podle konkrétní implementace jednotlivých zákazníků.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto zabezpečení Azure a dodržování předpisů nasadí referenční architekturu pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datovou vrstvu, služby Active Directory infrastruktury, Application Gateway a nástroje pro vyrovnávání zatížení. Virtuální počítače nasazené na web a data úrovní, jsou nakonfigurované ve skupině dostupnosti a instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně a zásady skupiny služby Active Directory se používá k vynucení zabezpečení a dodržování předpisů konfigurace na úrovni operačního systému.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurovat z portálu Azure portal. Azure Storage šifruje všechna data pomocí šifrování služby Storage k údržbě důvěrnost dat v klidovém stavu. Geografické redundantní úložiště zajišťuje, že nežádoucí událost ve primárního datového centra zákazníka nebude mít za následek ztrátu dat, jako druhý kopie se uloží v samostatném umístění stovky mil okamžitě.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Řízení přístupu na základě role Azure Active Directory se používá pro řízení přístupu k prostředkům a nasadit klíče ve službě Azure Key Vault. V nástroji Azure Monitor se monitoruje stav systému. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokolů a zobrazení stavu systému v řídicím panelu jeden, lze snadno procházet.

Hostitel typu bašta správy zajišťující Správci nasazení přístup k prostředkům zabezpečené připojení. **Microsoft doporučuje nakonfigurovat připojení VPN nebo ExpressRoute pro správu a data importovat do podsítě referenční architektury.**


![Webová aplikace IaaS pro diagram referenční architektury SP NIST 800-171](images/nist171-iaaswa-architecture.png "webová aplikace IaaS pro SP NIST 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další podrobnosti najdete v [architektura nasazení](#deployment-architecture) oddílu.

- Azure Virtual Machines
    - (1) správy/bastionu (Windows Server 2016 Datacenter)
    - (2) řadič domény active Directory (Windows Server 2016 Datacenter)
    - (2) uzel clusteru SQL serveru (SQL Server 2017 na Windows serveru 2016)
    - (2) web/IIS (Windows Server 2016 Datacenter)
- Azure Virtual Network
    - (((1) /16 sítě
    - (5) /24 sítě
    - (5) skupiny zabezpečení sítě
- Skupiny dostupnosti
    - (1) řadiče domény active Directory
    - (1) uzly clusteru SQL
    - (1) web/IIS
- Azure Application Gateway
    - (1) firewall webových aplikací
        - Režim brány firewall: ochrany před únikem informací
        - Sada pravidel, která: OWASP 3.0
        - Port naslouchacího procesu: 443
- Azure Active Directory
- Azure Key Vault
- Nástroj pro vyrovnávání zatížení Azure
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Log Analytics
- Azure Automation
- Disk s kopií cloudu
- Trezor služby Recovery Services

## <a name="deployment-architecture"></a>Architektura nasazení
Následující část podrobně popisuje nasazení a implementaci prvky.

**Bastion host**: hostitel bastionu je jediný bod položku, která umožňuje uživatelům přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Zdroje přenosů pro povolení provozu vzdálené plochy (RDP), musí být definován ve skupině zabezpečení sítě.

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Azure Key Vault
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače není při použití
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) povolené tak, aby pověření a dalších tajných kódů je spouštět v chráněném prostředí, která je oddělená od operačního systému

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředků v architektuře s podsítí samostatné webové, databáze podsítě, podsíť služby Active Directory a podsítě pro správu ve virtuální síti. Podsítě jsou logicky oddělené použitý pro jednotlivé podsítě k omezení provozu mezi podsítěmi na pouze to nezbytná pro fungování systému a správu pravidel skupiny zabezpečení sítě.

Zobrazit konfiguraci [skupiny zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazeny s tímto řešením. Organizace skupiny zabezpečení sítě můžete nakonfigurovat tak, že upravíte soubor novějšímu pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každé z podsítí má vyhrazené síťové skupiny zabezpečení:
- Skupina zabezpečení sítě 1 pro Application Gateway (LBNSG)
- Skupina zabezpečení sítě 1 pro bastion host (MGTNSG)
- Skupina zabezpečení sítě 1 pro primární a záložní řadiče domény (ADNSG)
- Skupina zabezpečení sítě 1 pro servery SQL Server a cloudu s kopií clusteru (SQLNSG)
- Skupina zabezpečení sítě 1 pro webová vrstva (WEBNSG)

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Kromě toho všechny transakce do služby Azure Storage na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu pomocí více měr, včetně šifrování a auditování databáze.

**Azure Storage**: pro splnění šifrovaná data na požadavky rest, všechny [služby Azure Storage](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). To pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené SP NIST 800-171.

**Azure Disk Encryption**: Azure Disk Encryption se používá šifrování disků virtuálních počítačů IaaS s Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) využívá funkci BitLocker systému Windows k zajištění šifrování svazku pro operační systém a datové disky. Toto řešení je integrovaná se službou Azure Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**SQL Server**: instance systému SQL Server používá následující bezpečnostní opatření databáze:
-   [Auditování SQL serveru](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) sleduje události databáze a zapisuje je do protokolů auditu.
-   [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Šifrované sloupce](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) Ujistěte se, že citlivá data nezobrazí jako prostý text v databázi systému. Po povolení šifrování dat, můžou jenom klientských aplikací nebo aplikačních serverů s přístupu ke klíčům k datům ve formátu prostého textu.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat pro neprivilegované uživatele nebo aplikace. Dynamické maskování dat může automaticky zjišťuje potenciálně citlivá data a navrhnout odpovídající masky uplatňovat. To pomáhá při snížení přístup tak, aby citlivá data neexistuje databáze prostřednictvím před neoprávněným přístupem. **Pro úpravu nastavení dodržovat svoje schéma databáze maskování dynamických dat zodpovídají zákazníci.**

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro více tenantů založené na cloudu adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří v Azure Active Directory, včetně uživatele, kteří používají SQL Server.
-   Do aplikace ověřování pomocí Azure Active Directory. Další informace najdete v tématu [integrace aplikací s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Řízení přístupu na základě rolí Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) správcům umožňuje definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) umožňuje zákazníkům minimalizovat počet uživatelů, kteří mají přístup k určitým prostředkům.  Správci mohou pomocí Azure Active Directory Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce slouží taky vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci a prověří podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Azure Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení je typ klíče modulu hardwarového zabezpečení chráněných klíčů RSA 2048 bitů.
- Minimální požadovaná oprávnění pomocí řízení přístupu na základě rolí jsou udělena všechny uživatele a identity.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.
- Toto řešení je integrovaná se službou Azure Key Vault ke správě klíčů šifrování disku virtuálního počítače IaaS a tajných kódů.

**Správa oprav**: Windows virtuálních počítačů nasazených jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit aktualizované nasazení oprava virtuálním počítačům v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Virtual Machines poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software, se dají konfigurovat výstrahy Pokud známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Azure Security Center**: S [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Kromě toho Azure Security Center má přístup k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Azure Security Center používá celou řadu možností detekce zákazníkům potenciální útoky namířené prostředími proti výstrahy. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Azure Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. [Vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) ve službě Azure Security Center umožňuje zákazníkům definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Azure Security Center zajišťuje zabezpečení s určenou prioritou výstrah a incidentů, zjednodušuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozbou pro zkoumání a nápravou ohrožení pomoci týmům reakce na incidenty.

Kromě toho tato referenční architektura využívá [sken posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) ve službě Azure Security Center. Po nakonfigurování agenta partnera (třeba Qualysu) hlásí data o ohrožení zabezpečení na platformu partnera pro správu. Naopak platforma partnera pro správu poskytuje ohrožení zabezpečení a data zpět do služby Azure Security Center, monitorování stavu umožňuje zákazníkům rychle identifikovat ohrožené virtuální počítače.

**Služba Application Gateway**: architektura snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway nakonfigurovat firewall webových aplikací a pravidel OWASP povolena. Další možnosti patří:

- [Koncové k ukončení SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim ochrany před únikem informací)
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) pomocí sady pravidel OWASP 3.0
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Vlastních testů stavu paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Azure Security Center také poskytuje systém pověst.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: všechny virtuální počítače v nasazení řešení [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují distribuci virtuálních počítačů napříč několika izolovanými hardwarovými clustery pro zlepšení dostupnosti. Nejméně jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení virtuálního počítače umožňuje rychlejší obnovení.

**Cloud s kopií clusteru**: [disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání ve Windows serveru 2016, která využívá Azure jako arbitrážní bod. Disk s kopií cloudu, stejně jako všechny ostatní určující disky kvora, může hlasovat a účastnit se výpočtů kvora, ale používá standardní veřejně dostupné úložiště objektů Blob Azure. Tím se eliminují režii navíc Údržba virtuálních počítačů hostovaných ve veřejném cloudu.

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

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly z Azure SQL serveru. Automatizace [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožňuje zákazníkům snadno identifikovat změny v prostředí.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů tím, že umožňuje organizacím auditovat, vytvářet upozornění a archivovat data, včetně sledování volání rozhraní API v Azure prostředky.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-iaaswa-tm) nebo najdete níže. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace IaaS pro model hrozeb SP NIST 800-171](images/nist171-iaaswa-threat-model.png "webová aplikace IaaS pro model hrozeb SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů

[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 IaaS webové aplikace ovládací prvek implementace matice](https://aka.ms/nist171-iaaswa-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky jsou vyřešeny IaaS architektura pro webové aplikace, včetně podrobného popisu způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurovaný pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektuře webová aplikace IaaS. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

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
