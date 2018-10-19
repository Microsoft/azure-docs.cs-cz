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
ms.openlocfilehash: 78c92f8d738dd675ac20c31bd8171bd4370a56f6
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406241"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Zabezpečení Azure a dodržování předpisů – IaaS webové aplikace pro SP NIST 800-171

## <a name="overview"></a>Přehled
[Speciální publikace NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) poskytuje pokyny pro ochranu řízené neutajované informace (CUI), který se nachází v nonfederal informační systémy a organizace. SP NIST 800-171 vytváří 14 rodiny požadavky na zabezpečení pro ochranu utajení CUI.

Toto zabezpečení Azure a dodržování předpisů obsahuje pokyny, které pomáhá zákazníkům nasadit architektura pro webové aplikace v Azure, která implementuje podmnožinu NIST SP 800-171 ovládací prvky. Toto řešení ukazuje způsoby, ve které zákazníci mohou pokrýt konkrétní bezpečnostní a požadavky na dodržování předpisů. Slouží také jako základ pro zákazníky, kteří k vytvoření a konfigurace webových aplikací v Azure.

Tato referenční architektura, Průvodce přidružené implementací a model hrozeb mají sloužit jako základ pro zákazníky, abyste je přizpůsobili své specifické požadavky. Neměl by se používat jako-je v produkčním prostředí. Nasazení této architektury bez jakýchkoli úprav není dostatečná k SP NIST 800-171 zcela požadavkům. Zákazníci odpovídají za provedení zabezpečení a hodnocení dodržování předpisů vytvořené pomocí této architektury řešení. Požadavky může lišit v závislosti na konkrétní implementaci každého zákazníka.

## <a name="architecture-diagram-and-components"></a>Diagram architektury a komponenty
Toto zabezpečení Azure a dodržování předpisů nasadí referenční architekturu pro webovou aplikaci IaaS s back-end serveru SQL Server. Architektura zahrnuje webovou vrstvu, datovou vrstvu, služby Active Directory infrastruktury, Azure Application Gateway a nástroje pro vyrovnávání zatížení Azure. Virtuální počítače (VM) nasadí do webových a data úrovní, jsou nakonfigurované ve skupině dostupnosti. Instance systému SQL Server jsou nakonfigurované ve skupině dostupnosti AlwaysOn pro vysokou dostupnost. Virtuální počítače jsou připojené k doméně. Zásady skupiny služby Active Directory vynucovat konfigurace zabezpečení a dodržování předpisů na úrovni operačního systému.

Celé řešení je postavené na Azure Storage, které zákazníci nakonfigurovat z portálu Azure portal. Storage šifruje všechna data pomocí šifrování služby Storage k údržbě důvěrnost dat v klidovém stavu. Geograficky redundantní úložiště zajišťuje, že nežádoucí událost ve primárního datového centra zákazníka nebude mít za následek ztrátu dat. Druhé kopie je uložena v samostatném umístění stovky mil okamžitě.

Pro zvýšení zabezpečení všechny prostředky v tomto řešení se spravují jako skupiny prostředků prostřednictvím Azure Resource Manageru. Azure Active Directory (Azure AD) řízení přístupu na základě rolí (RBAC) se používá k řízení přístupu k nasazených prostředků a klíčů ve službě Azure Key Vault. V nástroji Azure Monitor se monitoruje stav systému. Zákazníci nakonfigurovat i služby monitorování pro zachycení protokoly. Zobrazí se stav systému v jednom řídicím panelu, který se snadno používá.

Hostitel typu bašta správy zajišťující Správci nasazení přístup k prostředkům zabezpečené připojení. *Společnost Microsoft doporučuje nakonfigurovat připojení VPN nebo Azure ExpressRoute pro správu a data importovat do podsítě referenční architektury.*


![Webová aplikace IaaS pro diagram referenční architektury SP NIST 800-171](images/nist171-iaaswa-architecture.png "webová aplikace IaaS pro SP NIST 800-171 diagram referenční architektury")

Toto řešení používá následující služby Azure. Další informace najdete v tématu [architektura nasazení](#deployment-architecture) oddílu.

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

**Bastion host**: hostitel bastionu je jediný bod položku, kterou uživatelé mohou používat pro přístup k nasazené prostředky v tomto prostředí. Hostitel bastionu poskytuje zabezpečené připojení k nasazené prostředky tím, že pouze vzdálený provoz z veřejné IP adresy na seznamu bezpečných. Pro povolení provozu vzdálené plochy, musí být definován zdrojový provoz ve skupině zabezpečení sítě (NSG).

Toto řešení vytvoří virtuální počítač jako hostitel typu bašta připojených k doméně s následující konfigurací:
-   [Antimalwarové rozšíření](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) pomocí služby Key Vault.
-   [Automatické vypnutí zásad](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) ke snížení spotřeby prostředků virtuálního počítače, když se nepoužívá.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) je povoleno spuštění pověření a dalších tajných kódů v chráněném prostředí, která bude izolovaná od operačního systému.

### <a name="virtual-network"></a>Virtuální síť
Architektura definuje privátní virtuální síť s adresním prostorem 10.200.0.0/16.

**Skupiny zabezpečení sítě**: Toto řešení nasadí prostředků v architektuře s oddělené podsítě pro web, databáze, služby Active Directory a správa ve virtuální síti. Pravidla skupiny zabezpečení sítě použitá na jednotlivé podsítě jsou logicky oddělené podsítě. Pravidla omezení provozu mezi podsítěmi na pouze to nezbytná pro fungování systému a správu.

Zobrazit konfiguraci [skupin zabezpečení sítě](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) nasazeny s tímto řešením. Organizace můžete nakonfigurovat skupiny zabezpečení sítě tak, že upravíte předchozí soubor pomocí [této dokumentace](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) jako vodítko.

Každé z podsítí má vyhrazené skupiny zabezpečení sítě:
- Jedna skupina zabezpečení sítě pro službu Application Gateway (LBNSG)
- Jedna skupina NSG pro bastion host (MGTNSG)
- Jedna skupina NSG pro primární a záložní řadiče domény (ADNSG)
- Jedna skupina NSG pro SQL servery a Cloudovou kopii clusteru (SQLNSG)
- Jedna skupina NSG pro webová vrstva (WEBNSG)

### <a name="data-in-transit"></a>Přenášená data
Azure šifruje veškerá komunikace do a z datových center Azure ve výchozím nastavení. Kromě toho všechny transakce úložiště na webu Azure portal nastat prostřednictvím protokolu HTTPS.

### <a name="data-at-rest"></a>Neaktivní uložená data
Architektura chrání data v klidovém stavu pomocí více měr. Tato opatření zahrnovat auditování databáze a šifrování.

**Azure Storage**: pro splnění požadavků pro šifrovaná data v klidovém stavu, všechny [úložiště](https://azure.microsoft.com/services/storage/) používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/storage-service-encryption). Tato funkce pomáhá zabezpečit a chránit data na závazky zabezpečení organizace a požadavky na dodržování předpisů, které jsou určené SP NIST 800-171.

**Azure Disk Encryption**: šifrování disku se používá k šifrovanými disky virtuálních počítačů IaaS Windows. [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) používá funkci BitLocker systému Windows pro zajištění šifrování svazku operačního systému a datové disky. Toto řešení je integrovaná se službou Key Vault pomáhá řídit a spravovat klíče šifrování disku.

**SQL Server**: instance systému SQL Server používá následující bezpečnostní opatření databáze:
-   [Auditování SQL serveru](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) sleduje události databáze a zapisuje je do protokolů auditu.
-   [Transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) provádí v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory transakčních protokolů k ochraně informací v klidovém stavu. Transparentní šifrování dat poskytuje jistotu, že se ukládají data nebyla v souladu s před neoprávněným přístupem.
-   [Pravidla brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) brání veškerému přístupu k databázové servery, dokud se správná oprávnění. Brána firewall uděluje přístup k databázím v závislosti na zdrojové IP adrese každého požadavku.
-   [Šifrované sloupce](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) Ujistěte se, že citlivá data nezobrazí jako prostý text v systému databáze. Po povolení šifrování dat pouze klientských aplikací nebo aplikačních serverů s přístupu ke klíčům můžou k datům ve formátu prostého textu.
- [Dynamické maskování dat](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) omezuje riziko ohrožení citlivých dat pomocí jejich maskování dat neoprávněným uživatelům nebo aplikacím. Může automaticky zjistit potenciálně citlivých data a navrhnout odpovídající masky uplatňovat. Dynamické maskování dat pomáhá omezit přístup tak, aby citlivá data nejsou ukončení databáze prostřednictvím před neoprávněným přístupem. *Zákazníci odpovídají za nastavení dodržovat svoje schéma databáze.*

### <a name="identity-management"></a>Správa identit
Tyto technologie nabízejí možnosti ke správě přístupu k datům v prostředí Azure:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) je služba Microsoftu pro víceklientské cloudové adresáře a identity management. Všechny uživatele tohoto řešení se vytvoří ve službě Azure AD a zahrnovat uživatele, kteří přistupují k instanci systému SQL Server.
-   Pomocí služby Azure AD se provádí ověřování do aplikace. Další informace najdete v tématu Jak [integrace aplikací s Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) umožňuje správci definovat oprávnění velice přesně kontrolovat přístup poskytnout pouze takovou úroveň přístupu, že uživatelé potřebují k provádění svých úloh. Namísto udělení každé oprávnění uživateli neomezený pro prostředky Azure, mohou správci povolit pouze určité akce pro přístup k datům. Přístup k předplatnému je omezená na správce předplatného.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) je možné zákazníky, chcete-li minimalizovat počet uživatelů, kteří mají přístup k určitým prostředkům. Správci můžou používat Azure AD Privileged Identity Management Pokud chcete zjišťovat, omezovat a monitorovat privilegované identity a jejich přístup k prostředkům. Tato funkce také umožňuje vynutit na vyžádání, just-in-time pro správu v případě potřeby přístup.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) zjistí potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Nakonfiguruje automatické odpovědi na zjištěné podezřelé akce související s identity ve vaší organizaci. Prozkoumá také podezřelé incidenty na přijmout vhodná opatření k jejich řešení.

### <a name="security"></a>Zabezpečení
**Správa tajných kódů**: Toto řešení využívá [služby Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů a tajných kódů. Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Následující funkce služby Key Vault zákazníkům pomáhat s ochranou dat:
- Pokročilé zásady přístupu jsou nakonfigurované na základě potřeba.
- Zásady přístupu trezoru klíčů jsou definovány minimální požadovaná oprávnění pro klíče a tajné kódy.
- Všechny klíče a tajné kódy ve službě Key Vault mít data vypršení platnosti.
- Všechny klíče ve službě Key Vault jsou chráněné moduly specializované hardwarového zabezpečení. Typ klíče je hardwaru chráněné modulem zabezpečení 2048bitový klíč RSA.
- Všichni uživatelé a identity jsou udělena minimální požadovaná oprávnění pomocí RBAC.
- Jsou povolené diagnostické protokoly pro Key Vault s dobu uchování o délce alespoň 365 dnů.
- Povolené kryptografických operací s klíči jsou omezené na ty povinné.
- Toto řešení je integrovaná se službou Key Vault ke správě virtuálních počítačů IaaS šifrování disku klíče a tajné kódy.

**Správa oprav**: virtuální počítače s Windows nasazené jako součást této referenční architektury jsou standardně nakonfigurovaní tak příjem automatických aktualizací ze služby Windows Update. Toto řešení zahrnuje také [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) službu, jejímž prostřednictvím je možné vytvořit nasazení aktualizované o opravu virtuálních počítačů v případě potřeby.

**Ochrana proti malwaru**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro virtuální počítače poskytuje možnost ochrany v reálném čase, že pomáhá zjistit a odebrat viry, spyware a jiný škodlivý software. Zákazníci můžou nakonfigurovat výstrahy, které generují při označuje, že škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit na chráněných virtuálních počítačů.

**Azure Security Center**: S [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), zákazníci mohou centrálně použít a spravovat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat a reagovat na útoky. Security Center také přistupuje k existující konfigurace služby Azure a poskytuje configuration a služby doporučení, která pomůžou zlepšit stav zabezpečení a chránit data.

Security Center používá celou řadu možností detekce zákazníkům potenciální útoky výstrahy, které se zaměřují jejich prostředí. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Security Center obsahuje sadu [předdefinované výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) , který se zobrazí, když hrozby nebo podezřelé aktivity. Zákazníci můžou využít [vlastní pravidla upozornění](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) definovat nové výstrahy zabezpečení podle data, která je již shromážděná z jejich prostředí.

Security Center nabízí výstrahy zabezpečení seřazené podle priority a incidentů. Security Center usnadňuje zákazníkům objevování a vyřešit potenciální problémy se zabezpečením. A [sestava analýzy hrozeb](https://docs.microsoft.com/azure/security-center/security-center-threat-report) se vygeneruje pro každou zjištěnou hrozeb. Reakce na incidenty týmy mohou pomocí sestavy, když vyšetřením a odstraněním hrozeb.

Tato referenční architektura využívá [sken posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) schopností ve službě Security Center. Po dokončení konfigurace, agent partnera (například Qualysu) hlásí data o ohrožení zabezpečení na platformu partnera pro správu. Platforma partnera pro správu pak poskytuje data o ohrožení zabezpečení a data monitorování stavu zpět do služby Security Center. Zákazníky můžete použít tyto informace rychle identifikovat virtuální počítače jsou zranitelné.

**Azure Application Gateway**: architektura snižuje riziko ohrožení zabezpečení pomocí služby application gateway firewall webových aplikací nakonfigurované a sady pravidel OWASP povolena. Další možnosti patří:

- [Koncové k ukončení SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Povolit [přesměrování zpracování SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Zakázat [TLS verze 1.0 a verze 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (režim ochrany před únikem informací).
- [Režim ochrany před únikem informací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) sady pravidel OWASP 3.0.
- Povolit [protokolování diagnostiky](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondy stavu vlastní](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) a [Azure Advisoru](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) poskytují dodatečnou ochranu a oznámení. Security Center také poskytuje systém pověst.

### <a name="business-continuity"></a>Kontinuita podnikových procesů

**Vysoká dostupnost**: všechny virtuální počítače v nasazení řešení [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Skupiny dostupnosti zajišťují distribuci virtuálních počítačů napříč několika izolovanými hardwarovými clustery pro zlepšení dostupnosti. Alespoň jeden virtuální počítač je k dispozici během událostí plánované i neplánované údržby, který splňuje 99,95 % Azure SLA.

**Trezor služby Recovery Services**: [trezor služby Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) jsou uloženy zálohovaných dat a chrání všechny konfigurace virtuálních počítačů Azure v této architektuře. Pomocí trezoru služby Recovery Services zákazníci obnovit soubory a složky z virtuálního počítače IaaS bez obnovení celého virtuálního počítače. Tento proces urychluje dobu obnovení.

**Cloud s kopií clusteru**: [disk s kopií cloudu](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) je typ určující disk kvora clusteru převzetí služeb při selhání ve Windows serveru 2016, která používá Azure jako arbitrážní bod. Cloudová kopie clusteru, stejně jako všechny ostatní určující disky kvora, může hlasovat a účastnit se výpočtů kvora. Používá standardní veřejně dostupné úložiště objektů Blob v Azure. Toto uspořádání eliminuje režijní náklady navíc Údržba virtuálních počítačů hostovaných ve veřejném cloudu.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Služby Azure výrazně protokolu systému a aktivity uživatelů, jakož i stavu systému:
- **Protokoly aktivit**: [protokoly aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytují přehled o operace prováděné s prostředky v rámci předplatného. Protokoly aktivit, vám pomůže určit operace iniciátoru čas výskytu a stav.
- **Diagnostické protokoly**: [diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) zahrnout všechny protokoly, protože ho vygeneroval každý prostředek. Tyto protokoly patří protokoly událostí systému Windows, protokoly úložiště, protokoly auditu služby Key Vault a protokolů Application Gateway přístup a brány firewall. Všechny diagnostické protokoly zápis do účtu centralizovaný a šifrovaného úložiště Azure pro archivaci. Uživatelé můžou konfigurovat dobu uchování až do 730 dnů podle svých specifických požadavků.

**Log Analytics**: tyto protokoly jsou konsolidovány do [Log Analytics](https://azure.microsoft.com/services/log-analytics/) pro zpracování, ukládání a vytváření sestav řídicího panelu. Po shromáždění dat jsou uspořádány do samostatných tabulek pro jednotlivé datové typy v rámci pracovních prostorů Log Analytics. Tímto způsobem lze všechna data pohromadě, analyzovat bez ohledu na jejich původní zdroj. Security Center umožňuje integrací s Log Analytics. Zákazníci mohou pomocí dotazy Log Analytics pro přístup k datům událostí zabezpečení a to v kombinaci s daty z jiných služeb.

Následující Log Analytics [řešení pro správu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) jsou zahrnuty jako součást této architektury:
-   [Active Directory assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): The Active Directory Health řešení Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Poskytuje seznam seřazený podle priority doporučení specifická pro nasazenou serverové infrastruktury.
- [Posouzení SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): řešení SQL Health Check posuzuje v pravidelných intervalech rizika a stav prostředí serveru. Zákazníkům přináší uspořádaný seznam doporučení, které jsou specifické pro nasazený server infrastruktury.
- [Stav agenta](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): sestavy řešení Agent Health tom, kolik agentů je nasazených a jejich geografické distribuce. Zároveň udává, kolik agentů jsou reagovat a počet agentů, které odesílají provozní data.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): The Activity Log Analytics řešení pomáhá s analýzou protokolů aktivit Azure napříč všemi předplatnými Azure zákazníka.

**Azure Automation**: [automatizace](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) ukládá, spouští a spravuje sady runbook. V tomto řešení sady runbook pomůže shromažďovat protokoly ze systému SQL Server. Zákazníci můžou využít automatizaci [řešení Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) řešení umožní snadno identifikovat změny v prostředí.

**Azure Monitor**: [monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomáhá uživatelům sledování výkonu, zabezpečení a rozpoznávání trendů. Organizace slouží k auditovat, vytvářet upozornění a archivovat data. Také mohou sledovat volání rozhraní API ve svých prostředků Azure.

## <a name="threat-model"></a>Model hrozeb

Diagram toku dat pro tuto referenční architekturu je k dispozici pro [Stáhnout](https://aka.ms/nist171-iaaswa-tm) nebo najdete tady. Tento model může pomoci zákazníkům pochopit body potenciální riziko v infrastruktuře systému při provádění změn.

![Webová aplikace IaaS pro model hrozeb SP NIST 800-171](images/nist171-iaaswa-threat-model.png "webová aplikace IaaS pro model hrozeb SP NIST 800-171")

## <a name="compliance-documentation"></a>Dokumentace ke službě dodržování předpisů

[Azure zabezpečení a dodržování předpisů – matice odpovědnosti zákazníka SP NIST 800-171](https://aka.ms/nist171-crm) vypisuje všechny ovládací prvky zabezpečení vyžadované SP NIST 800-171. Tato matice podrobně popisuje, zda je odpovědnost společnosti Microsoft, Zákazník, provádění každý ovládací prvek nebo sdílet mezi nimi.

[Azure zabezpečení a dodržování předpisů - SP NIST 800-171 IaaS webové aplikace ovládací prvek implementace matice](https://aka.ms/nist171-iaaswa-cim) poskytuje informace, na které SP NIST 800-171 ovládací prvky jsou vyřešeny architektura pro webové aplikace IaaS. Obsahuje podrobný popis způsobu implementace splňuje požadavky každého zahrnutého ovládacího prvku.

## <a name="guidance-and-recommendations"></a>Pokyny a doporučení
### <a name="vpn-and-expressroute"></a>Připojení VPN a ExpressRoute
Zabezpečené tunelové propojení sítě VPN nebo [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být nakonfigurován pro zabezpečené připojení k prostředky nasazené jako součást této referenční architektuře IaaS webové aplikace. Odpovídajícím způsobem nastavíte síť VPN nebo ExpressRoute, můžete zákazníky přidat vrstvu ochrany dat během přenosu.

Implementací zabezpečeného tunelového propojení sítě VPN s Azure můžete vytvořit virtuální privátní připojení mezi místní sítí a virtuální sítí Azure. Toto připojení se provádí přes Internet. Zákazníci mohou používat toto připojení k bezpečně "tunelového propojení" informací uvnitř šifrované propojení mezi sítí zákazníka a Azure. Site-to-site VPN je zabezpečené, Vyspělá technologie, která byla nasazena podniky všech velikostí desítky let. [Režimu tunelového propojení IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) se používá při použití této možnosti jako mechanismus pro šifrování.

Vzhledem k tomu, že provoz v rámci tunelového připojení sítě VPN prochází přes Internet prostřednictvím sítě site-to-site VPN, společnost Microsoft nabízí další ještě větší zabezpečení možností připojení. ExpressRoute je vyhrazené sítě WAN propojení mezi Azure a místním umístěním nebo poskytovatele hostingu serveru Exchange. Připojení ExpressRoute připojují přímo k poskytovatele telekomunikačních zákazníka. Data v důsledku toho nebude procházet přes Internet a není vystavený. Tato připojení nabízí další spolehlivost, vyšší rychlost, nižší latenci a lepší zabezpečení než Typická připojení. 

Osvědčené postupy pro implementaci zabezpečenou hybridní síť, která rozšiřuje místní síť do Azure jsou [dostupné](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Právní omezení

- Tento dokument slouží pouze k informačním účelům. MICROSOFT NEPOSKYTUJE ŽÁDNÉ ZÁRUKY, VÝSLOVNÝCH, ODVOZENÝCH NEBO ZÁKONNÝCH, INFORMACE V TOMTO DOKUMENTU. Tento dokument se poskytuje "jako-je." Informace a názory vyjádřené v tomto dokumentu včetně adres URL a jiných odkazů na internetové weby, mohou změnit bez předchozího upozornění. Tento dokument, zákazníci nese riziko jeho použití. 
- Tento dokument neposkytuje žádná zákonná práva na duševní vlastnictví v libovolném produkt společnosti Microsoft nebo řešení zákazníkům. 
- Zákazníci mohou kopírovat a používat tento dokument pro interní referenční účely. 
- Některá doporučení v tomto dokumentu může vést k vyšší dat, sítě nebo výpočetní využití prostředků v Azure a můžou zvýšit náklady Azure licenci předplatného zákazníka. 
- Tato architektura má sloužit jako základ pro zákazníky, chcete-li upravit jejich konkrétním požadavkům by se neměl používat jako-je v produkčním prostředí.
- Tento dokument je mimo jiné vyvinuto odkaz a neměl by se definovat všechny prostředky, které zákazník dokáže splnit požadavky na konkrétní dodržování předpisů a nařízení. Zákazníci by měl hledání právní podporu – od organizace na implementace schválené zákazníků.
