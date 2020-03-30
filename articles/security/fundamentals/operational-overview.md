---
title: Přehled provozního zabezpečení Azure| Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled provozního zabezpečení Azure.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: bcaf45af4c02dad22e2cc611fa7ea5a32ad3a853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443884"
---
# <a name="azure-operational-security-overview"></a>Přehled provozního zabezpečení Azure

[Provozní zabezpečení Azure](/azure/security/fundamentals/operational-security) označuje služby, ovládací prvky a funkce, které jsou uživatelům k dispozici pro ochranu jejich dat, aplikací a dalších prostředků v Microsoft Azure. Je to rámec, který zahrnuje znalosti získané prostřednictvím různých funkcí, které jsou jedinečné pro Microsoft. Mezi tyto funkce patří program Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center a hluboké povědomí o prostředí kybernetických hrozeb.

## <a name="azure-management-services"></a>Služby správy Azure

Provozní tým IT je zodpovědný za správu infrastruktury, aplikací a dat datových center, včetně stability a zabezpečení těchto systémů. Získání přehledů o zabezpečení napříč stále složitějšími it prostředími však často vyžaduje, aby organizace sestavily data z více systémů zabezpečení a správy.

[Protokoly Microsoft Azure Monitor jsou](/azure/operations-management-suite/operations-management-suite-overview) cloudové řešení pro správu IT, které vám pomůže spravovat a chránit místní a cloudovou infrastrukturu. Jeho základní funkce jsou poskytovány následujícími službami, které běží v Azure. Azure zahrnuje několik služeb, které vám pomůžou spravovat a chránit místní a cloudovou infrastrukturu. Každá služba poskytuje specifickou funkci správy. Můžete kombinovat služby k dosažení různých scénářů správy. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](/azure/azure-monitor/overview) shromažďuje data ze spravovaných zdrojů do centrálních úložišť dat. Tato data mohou zahrnovat události, data o výkonu nebo vlastní data poskytnutá prostřednictvím rozhraní API. Po shromáždění jsou data k dispozici pro výstrahy, analýzu a export.

Můžete konsolidovat data z různých zdrojů a kombinovat data ze služeb Azure s vaším stávajícím místním prostředím. Protokoly Azure Monitor také jasně odděluje shromažďování dat z akce provedené na tato data, tak, aby všechny akce jsou k dispozici pro všechny druhy dat.

### <a name="automation"></a>Automatizace

[Azure Automation](/azure/automation/automation-intro) poskytuje způsob, jak automatizovat ruční, dlouhotrvající, náchylné k chybám a často opakované úlohy, které se běžně provádějí v cloudovém a podnikovém prostředí. Šetří čas a zvyšuje spolehlivost administrativních úkolů. Dokonce naplánuje, že tyto úkoly budou automaticky prováděny v pravidelných intervalech. Procesy můžete automatizovat pomocí runbooků nebo automatizovat správu konfigurace pomocí konfigurace požadovaného stavu.

### <a name="backup"></a>Zálohování

[Azure Backup](/azure/backup/backup-introduction-to-azure-backup) je služba založená na Azure, kterou můžete použít k zálohování (nebo ochraně) a obnovení dat v Microsoft Cloudu. Azure Backup nahradí vaše stávající místní řešení zálohování nebo řešení zálohování mimo pracoviště cloudovým řešením, které je spolehlivé, zabezpečené a nákladově konkurenceschopné.

Azure Backup nabízí komponenty, které stáhnete a nasadíte na příslušný počítač nebo server nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny součásti Azure Backup (ať už chráníte data místně nebo v cloudu) se můžou použít k zálohování dat do trezoru Služby Azure recovery services v Azure.

Další informace naleznete v [tabulce součástí Azure Backup](/azure/backup/backup-overview#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) poskytuje kontinuitu podnikání tím, že organizuje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud primární lokalita není k dispozici, přejdete k převzetí služeb při selhání do sekundárního umístění, aby uživatelé mohli pokračovat v práci. Po návratu systémů do funkčního stavu se vrátíte zpět. Azure Security Center slouží k inteligentnějšímu a efektivnějšímu zjišťování hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](/azure/active-directory/active-directory-enable-sso-scenario) je komplexní služba identit, která:

-   Umožňuje správu identit a přístupu (IAM) jako cloudovou službu.
-   Poskytuje správu centrálního přístupu, jednotné přihlašování (SSO) a vytváření sestav.
-   Podporuje integrovanou správu přístupu pro [tisíce aplikací](https://azure.microsoft.com/marketplace/active-directory/) na Azure Marketplace, včetně Salesforce, Google Apps, Box a Concur.

Azure AD také obsahuje úplnou sadu [funkcí správy identit](/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), včetně těchto:

- [Vícefaktorové ověřování](/azure/multi-factor-authentication/multi-factor-authentication)
- [Samoobslužná správa hesel](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobslužná správa skupin](/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Správa privilegovaného účtu](/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Řízení přístupu založené na rolích](/azure/role-based-access-control/overview)
- [Monitorování využití aplikací](/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Bohatý audit](/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorování a upozorňování na zabezpečení](/azure/operations-management-suite/oms-security-responding-alerts)

Ve službě Azure Active Directory mají všechny aplikace, které publikujete pro své partnery a zákazníky (obchodní nebo spotřebitel), stejné funkce správy identit a přístupu. To vám umožní výrazně snížit provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](/azure/security-center/security-center-intro) vám pomůže předcházet, zjišťovat a reagovat na hrozby se zvýšeným přehledem o (a kontrole) zabezpečení vašich prostředků Azure. Poskytuje integrované monitorování zabezpečení a správu zásad v rámci vašich předplatných. Pomáhá odhalovat hrozby, které by jinak mohly zůstat bez povšimnutí, a pracuje s širokým ekosystémem bezpečnostních řešení.

[Zabezpečte data virtuálních počítačů (VM)](/azure/security-center/security-center-linux-virtual-machine) v Azure tím, že poskytnete přehled o nastavení zabezpečení vašeho virtuálního počítače a monitorování hrozeb. Security Center může u virtuálních počítačů monitorovat:

- Nastavení zabezpečení operačního systému s doporučenými pravidly konfigurace.
- Chybí zabezpečení systému a důležité aktualizace.
- Doporučení pro ochranu koncového bodu.
- Ověření šifrování disku.
- Útoky založené na síti.

Security Center používá [řízení přístupu na základě rolí (RBAC)](/azure/role-based-access-control/role-assignments-portal). RBAC poskytuje [předdefinované role,](../../role-based-access-control/built-in-roles.md) které lze přiřadit uživatelům, skupinám a službám v Azure.

Security Center vyhodnocuje konfiguraci prostředků k identifikaci problémů se zabezpečením a slabými místy. V Centru zabezpečení se informace související s prostředkem zobrazují pouze v případě, že je přiřazena role vlastníka, přispěvatele nebo čtenáře pro předplatné nebo skupinu prostředků, do které prostředek patří.

>[!Note]
>Další informace o rolích a povolených akcích v Centru zabezpečení najdete v [tématu Oprávnění v Centru zabezpečení Azure](/azure/security-center/security-center-permissions).

Security Center používá agenta Microsoft Monitoring Agent. Jedná se o stejného agenta, který používá služba Azure Monitor. Data shromážděná od tohoto agenta se ukládají buď v existujícím [pracovním prostoru](/azure/log-analytics/log-analytics-manage-access) Log Analytics přidruženém k vašemu předplatnému Azure, nebo v novém pracovním prostoru s přihlédnutím ke geografické poloze virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor

Problémy s výkonem ve vaší cloudové aplikaci mohou ovlivnit vaši firmu. S více propojenými součástmi a častými uvolňováními může dojít k degradaci kdykoli. A pokud vyvíjíte aplikaci, uživatelé obvykle objeví problémy, které jste při testování nenašli. Měli byste o těchto problémech vědět okamžitě a měli byste mít nástroje pro diagnostiku a opravu problémů.

[Azure Monitor](/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) je základní nástroj pro monitorování služeb spuštěných v Azure. Poskytuje data na úrovni infrastruktury o propustnosti služby a okolním prostředí. Pokud spravujete všechny aplikace v Azure a rozhodujete se, jestli chcete vertikálně navýšit nebo snížit kapacitu prostředků, Azure Monitor je místo, kde začít.

Data monitorování můžete také použít k získání podrobné přehledy o vaší aplikaci. Tyto znalosti vám mohou pomoci zlepšit výkon nebo udržovatelnost aplikací nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Azure Monitor obsahuje následující součásti.

### <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytuje přehled o operacích, které byly provedeny na prostředky ve vašem předplatném. Dříve byl označován jako "Protokol auditu" nebo "Provozní protokol", protože hlásí události roviny řízení pro vaše předplatná.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

[Diagnostické protokoly Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou emitovány prostředek a poskytují bohaté, časté údaje o provozu tohoto prostředku. Obsah těchto protokolů se liší podle typu prostředku.

Protokoly systému událostí systému Windows jsou jednou kategorií diagnostických protokolů pro virtuální servery. Protokoly objektů blob, tabulky a fronty jsou kategorie diagnostických protokolů pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje přehled o operacích, které byly provedeny na prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, které váš prostředek provedl sám.

### <a name="metrics"></a>Metriky

Azure Monitor poskytuje telemetrii, která vám poskytuje přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitějším typem dat telemetrie Azure jsou [metriky](/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (nazývané také čítače výkonu) vyzařované většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Diagnostika Azure umožňuje shromažďování diagnostických dat v nasazené aplikaci. Můžete použít rozšíření Diagnostika z různých zdrojů. Aktuálně podporované jsou [role cloudových služeb Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service), virtuální počítače [Azure](/azure/vs-azure-tools-configure-roles-for-cloud-service) se systémem Microsoft Windows a Azure [Service Fabric](/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Zákazníci vytvářejí síť od konce v Azure tím, že orchestrují a vytvářejí jednotlivé síťové prostředky, jako jsou virtuální sítě, Azure ExpressRoute, Azure Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici u každého síťového prostředků.

Síť koncového serveru může mít složité konfigurace a interakce mezi prostředky. Výsledkem jsou složité scénáře, které potřebují monitorování založené na scénářích prostřednictvím [Azure Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher zjednodušuje monitorování a diagnostiku vaší sítě Azure. Diagnostické a vizualizační nástroje můžete použít v nástroji Sledování sítě k:

- Vezměte vzdálené zachycení paketů na virtuálním počítači Azure.
- Získejte přehled o síťovém provozu pomocí protokolů toku.
- Diagnostikujte azure vpn bránu a připojení.

Sledování sítě má v současné době následující možnosti:

- [Topologie](/azure/network-watcher/network-watcher-topology-overview): Poskytuje zobrazení různých propojení a přidružení mezi síťovými prostředky ve skupině prostředků.
- [Proměnné zachycení paketů](/azure/network-watcher/network-watcher-packet-capture-overview): Zachycuje data paketů ve virtuálním počítači a z ní. Rozšířené možnosti filtrování a jemně vyladěné ovládací prvky, jako je možnost nastavit omezení času a velikosti, poskytují všestrannost. Data paketů mohou být uložena v úložišti objektů blob nebo na místním disku ve formátu CAP.
- [Ověření toku IP](/azure/network-watcher/network-watcher-ip-flow-verify-overview): Zkontroluje, zda je paket povolen nebo odepřen na základě parametrů paketu s 5 řazány do řazených dat pro informace o toku (cílová IP adresa, zdrojová IP adresa, cílový port, zdrojový port a protokol). Pokud skupina zabezpečení paket odmítne, bude vráceno pravidlo a skupina, které paket odepřel.
- [Další směrování](/azure/network-watcher/network-watcher-next-hop-overview): Určuje další směrování pro pakety směrované v síťové struktuře Azure, takže můžete diagnostikovat všechny nesprávně nakonfigurované uživatelem definované trasy.
- [Zobrazení skupiny zabezpečení](/azure/network-watcher/network-watcher-security-group-view-overview): Získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači.
- [Protokoly toku skupiny nsg pro skupiny zabezpečení sítě](/azure/network-watcher/network-watcher-nsg-flow-logging-overview): Umožňuje zachytit protokoly související s provozem, který je povolen nebo odepřen pravidly zabezpečení ve skupině. Tok je definován informacemi o 5 řazených členech n-tice: zdrojová IP adresa, cílová ADRESA IP, zdrojový port, cílový port a protokol.
- [Brána virtuální sítě a řešení potíží s připojením](/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Poskytuje možnost řešení potíží s bránami a připojeními virtuálních sítí.
- [Omezení síťového předplatného](/azure/network-watcher/network-watcher-monitoring-overview): Umožňuje zobrazit využití síťových prostředků proti limitům.
- [Diagnostické protokoly](/azure/network-watcher/network-watcher-monitoring-overview): Poskytuje jediné podokno pro povolení nebo zakázání diagnostických protokolů pro síťové prostředky ve skupině prostředků.

Další informace naleznete v [tématu Configure Network Watcher](/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Transparentnost přístupu poskytovatele cloudových služeb

[Customer Lockbox pro Microsoft Azure](customer-lockbox-overview.md) je služba integrovaná do portálu Azure, která vám dává explicitní kontrolu ve výjimečných případech, kdy pracovník podpory Microsoftu může potřebovat přístup k vašim datům k vyřešení problému.
Existuje velmi málo instancí, jako je například problém s laděním vzdáleného přístupu, kde pracovník podpory společnosti Microsoft vyžaduje zvýšená oprávnění k vyřešení tohoto problému. V takových případech používají technici společnosti Microsoft službu just-in-time access, která poskytuje omezenou časově omezenou autorizaci s omezeným přístupem ke službě.  
Zatímco Microsoft vždy získal souhlas zákazníka pro přístup, Customer Lockbox vám nyní umožňuje kontrolovat a schvalovat nebo zamítat takové požadavky z portálu Azure Portal. Pracovníci technické podpory společnosti Microsoft nebudou mít přístup, dokud žádost neschválíte.

## <a name="standardized-and-compliant-deployments"></a>Standardizovaná a kompatibilní nasazení

[Azure Blueprints](/azure/governance/blueprints/overview) umožňují cloudovým architektům a centrálním skupinám informačních technologií definovat opakovatelnou sadu prostředků Azure, které implementují a dodržují standardy, vzory a požadavky organizace.  
To umožňuje týmům DevOps rychle vytvářet a obstát nová prostředí a důvěřovat, že je budují pomocí infrastruktury, která udržuje dodržování předpisů organizace.
Podrobné plány poskytují deklarativní způsob, jak organizovat nasazení různých šablon prostředků a dalších artefaktů, jako jsou:

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manageru
- Skupiny prostředků

## <a name="devops"></a>DevOps

Před vývojem aplikací [Pro vývojářské operace (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) měly týmy na starosti shromažďování obchodních požadavků na softwarový program a psaní kódu. Potom samostatný tým QA testoval program v izolovaném vývojovém prostředí. Pokud byly splněny požadavky, qa tým vydal kód pro operace k nasazení. Týmy nasazení byly dále fragmentovány do skupin, jako je vytváření sítí a databáze. Pokaždé, když byl softwarový program "hozen přes zeď" nezávislému týmu, přidal úzká místa.

DevOps umožňuje týmům poskytovat bezpečnější a kvalitnější řešení rychleji a levněji. Zákazníci očekávají dynamické a spolehlivé prostředí při využívání softwaru a služeb. Týmy musí rychle iterate na aktualizace softwaru a měřit dopad aktualizací. Musí rychle reagovat s novými vývojovými iteracemi, aby vyřešili problémy nebo poskytli větší hodnotu.  

Cloudové platformy, jako je Microsoft Azure, odstranily tradiční úzká místa a pomohly komoditní infrastruktuře. Software vládne v každém podniku jako klíčový diferenciátor a faktor obchodních výsledků. Žádná organizace, vývojář nebo pracovník IT může nebo by se měl vyhnout devOps hnutí.

Mature DevOps praktici přijmout několik z následujících postupů. Tyto postupy [zahrnují lidi,](https://www.visualstudio.com/learn/what-is-devops-culture/) aby vytvořili strategie založené na obchodních scénářích. Nástroje mohou pomoci automatizovat různé postupy.

- [Techniky agilního plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) se používají k plánování a izolování práce do sprintů, správě kapacity týmu a pomoci týmům rychle se přizpůsobit měnícím se obchodním potřebám.
- [Správa verzí, obvykle s Gitem](https://www.visualstudio.com/learn/what-is-git/), umožňuje týmům umístěným kdekoli na světě sdílet zdroj a integrovat se s nástroji pro vývoj softwaru pro automatizaci kanálu vydání.
- [Průběžná integrace](https://www.visualstudio.com/learn/what-is-continuous-integration/) řídí probíhající slučování a testování kódu, což vede k včasnému zjištění vad.  Mezi další výhody patří méně času promarněného problémy s bojem proti sloučení a rychlá zpětná vazba pro vývojové týmy.
- [Nepřetržité doručování](https://www.visualstudio.com/learn/what-is-continuous-delivery/) softwarových řešení do produkčních a testovacích prostředí pomáhá organizacím rychle opravit chyby a reagovat na neustále se měnící obchodní požadavky.
- [Monitorování](https://www.visualstudio.com/learn/what-is-monitoring/) spuštěných aplikací – včetně produkčních prostředí pro stav aplikace a použití zákazníka – pomáhá organizacím vytvořit hypotézu a rychle ověřit nebo vyvrátit strategie.  Bohatá data jsou zachycena a uložena v různých formátech protokolování.
- [Infrastruktura jako kód (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) je praxe, která umožňuje automatizaci a validaci vytváření a stržení sítí a virtuálních počítačů, které pomáhají s poskytováním zabezpečených a stabilních platforem pro hostování aplikací.
- [Architektura mikroslužeb](https://www.visualstudio.com/learn/what-are-microservices/) se používá k izoluje případy použití obchodní do malých opakovaně použitelných služeb.  Tato architektura umožňuje škálovatelnost a efektivitu.

## <a name="next-steps"></a>Další kroky

Další informace o řešení zabezpečení a auditu naleznete v následujících článcích:

- [Zabezpečení a dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](/azure/security-center/security-center-intro)
- [Azure Monitor](/azure/azure-monitor/overview)
