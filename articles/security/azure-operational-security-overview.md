---
title: Přehled provozního zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek obsahuje přehled provozního zabezpečení Azure.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: 478228ccb12fbfe3ca527bf4d16af05735b7ba92
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238535"
---
# <a name="azure-operational-security-overview"></a>Přehled provozního zabezpečení Azure

[Azure operational security](https://docs.microsoft.com/azure/security/azure-operational-security) odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků ve službě Microsoft Azure. Je architektura, která zahrnuje poznatky získané prostřednictvím různých možností, které jsou jedinečné pro Microsoft. Mezi tyto možnosti patří Microsoft Security Development Lifecycle (SDL), programu Microsoft Security Response Center a hloubkové povědomí o zabezpečení.

## <a name="azure-management-services"></a>Služeb Azure pro správu

Operace má IT tým zodpovídá za správu infrastruktury datového centra, aplikace a data, včetně stabilitu a zabezpečení těchto systémů. Získávání informací o zabezpečení napříč často zvýšení komplexní IT prostředí však vyžaduje organizacím cobble dohromady data z několika systémů zabezpečení a správu.

[Microsoft Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je cloudové řešení pro správu IT, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu. Jejích klíčových funkcí poskytuje tyto služby, které běží v Azure. Azure obsahuje několik služeb, které vám pomůžou spravovat a chránit svoje místní a cloudovou infrastrukturu. Každá služba poskytuje specifickou funkci správy. Můžete kombinovat služby dosahovat různých scénářů správy. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) shromažďuje data ze spravovaných zdrojů do centrální datových úložišť. Tato data můžou obsahovat události, údaje o výkonu nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po shromáždění dat je k dispozici pro výstrahy, analýzu a export. 

Můžete sloučit data z různých zdrojů a kombinovat data ze služby Azure s existujícím místním prostředím. Log Analytics také jasně odděluje získávání dat od akcí provedených na těchto datech, tak, aby všechny akce jsou k dispozici pro všemi druhy dat.

### <a name="automation"></a>Automation

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) poskytuje způsob, jak automatizovat ruční, dlouhotrvajících, problematických a často se opakujících úloh, které se běžně provádějí v cloudovém a podnikovém prostředí. Šetří čas a zvyšuje spolehlivost úloh správy. Dokonce i naplánuje tyto úkoly provádět automaticky v pravidelných intervalech. Můžete automatizovat procesy pomocí runbooků nebo automatizovat správu konfigurace pomocí Desired State Configuration.

### <a name="backup"></a>Backup

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je služba založené na Azure, která slouží k zálohování (nebo ochraně) a obnovování vašich dat v cloudu Microsoftu. Azure Backup nahradí stávající místní nebo externí řešení zálohování cloudovým řešením, které je spolehlivé, bezpečné a nákladově srovnatelné. 

Azure Backup nabízí součásti, které můžete stáhnout a nasadit v příslušném počítači nebo na serveru nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny komponenty Azure Backup (ať chráníte data dostupná místně nebo v cloudu) slouží k zálohování dat do trezoru služby Azure Recovery Services v Azure. 

Další informace najdete v tématu [tabulce komponent Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) poskytuje obchodní kontinuity podnikových procesů tím, že orchestruje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud primární lokalita není k dispozici, převzetí služeb při selhání do sekundární lokality tak, aby uživatelé můžete pokračovat v práci. Převezmete služby po obnovení vrátit systémů proběhne. Pomocí Azure Security Center můžete provést inteligentní a efektivní detekce hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) je komplexní identity služby, který:

-   Umožňuje správu identit a přístupu (IAM) jako cloudovou službu.
-   Poskytuje centrální řízení přístupu, jednotné přihlašování (SSO) a vytváření sestav.
-   Podporuje integrované access management pro službu [nepřeberným množstvím aplikací](https://azure.microsoft.com/marketplace/active-directory/) na webu Azure Marketplace, včetně Salesforce, Google Apps, Box a Concur.

Azure AD zahrnuje také plnohodnotné sadě [možnosti správy identit](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), včetně těchto:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Samoobslužná správa hesel](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Správa privilegovaných účtů](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Sledování využití aplikací](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Bohaté auditování](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitorování zabezpečení a upozorňování](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

S Azure Active Directory, všechny aplikace, které publikujete pro partnery a zákazníky (firmy nebo spotřebitele) mají stejnou identitu a přístup k funkcím správy. To umožňuje výrazně snížit provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) pomáhá zabránit, detekci a reakce na ně prostřednictvím zvýšené viditelnosti (a kontrolu nad) zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných. To pomáhá detekovat hrozby, které jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

[Chránit data virtuálních počítačů (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) v Azure tak, že poskytuje vhled do nastavení zabezpečení vašich virtuálních počítačů a monitoruje hrozby. Security Center může u virtuálních počítačů monitorovat:

- Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Zabezpečení systému a důležité aktualizace, které nebyly nalezeny.
- Doporučení pro Endpoint protection.
- Ověření šifrování disku.
- Síťové útoky.

Security Center používá [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). RBAC poskytuje [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , které můžete přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků identifikovat problémy se zabezpečením a ohrožení zabezpečení. Ve službě Security Center zobrazí informace související s prostředkem pouze v případě, že máte přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které prostředek patří.

>[!Note]
>Další informace o rolích a povolené akce ve službě Security Center, najdete v článku [oprávnění ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Security Center používá agenta Microsoft Monitoring Agent. To je stejný agent, který používá služba Log Analytics. Data shromážděná z tohoto agenta se ukládají do obou Log Analytics existující [pracovní prostor](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) přidružený k vašemu předplatnému Azure nebo vytvořte nový zohledněním polohu virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor

Problémy s výkonem v cloudové aplikaci může ovlivnit vaše podnikání. S několika propojených součástí a časté vydané verze může dojít, snížení výkonnosti v každém okamžiku. A pokud vyvíjíte aplikaci, vaši uživatelé obvykle zjišťovat problémy, které jste nenašli při testování. Měli byste vědět o tyto problémy okamžitě a měli byste mít nástroje pro diagnostiku a řešení problémů.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) je základní nástroj pro monitorování služby spuštěné v Azure. Poskytuje data na úrovni infrastruktury o propustnosti služeb a okolního prostředí. Pokud spravujete aplikace v Azure a rozhodování, jestli chcete vertikálně navýšit nebo snížit kapacitu prostředků, Azure Monitor je místem, kde začít.

Data monitorování můžete také získat podrobný přehled o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah. 

Azure Monitor obsahuje následující součásti.

### <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokolu aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném. To se dřív označovalo jako "Protokol auditu" nebo "Operační protokol," vzhledem k tomu, že sestavy událostí rovina řízení pro vaše předplatná.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

[Diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jakém množství se vysílají prostředek a poskytují bohaté a časté informace o fungování tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku.

Protokoly událostí systému Windows jsou jednu kategorii protokolů diagnostiky pro virtuální počítače. Objekt BLOB, tabulky a fronty protokoly jsou kategorie diagnostické protokoly pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky

Platforma Azure Monitor poskytuje telemetrie, budete mít přehled o výkonu a stavu vašich úloh v Azure. Je nejdůležitější typu Azure telemetrických dat [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) většinu služeb Azure prostředky emitovány (také nazývané čítače). Platforma Azure Monitor poskytuje několik způsobů, jak konfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Diagnostika Azure

Diagnostika Azure umožňuje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z různých zdrojů. V tuto chvíli nepodporuje jsou [role Azure cloud service](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuálních počítačů Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systémem Microsoft Windows a [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Můžou zákazníci sestavovat síť začátku do konce v Azure pomocí Orchestrace a skládání jednotlivých síťovým prostředkům, například virtuálních sítí Azure ExpressRoute, Azure Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředků.

Začátku do konce síť může mít komplexní konfigurace a interakce mezi prostředky. Výsledkem je komplexní scénáře, které je třeba založené na scénářích monitorování prostřednictvím [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher usnadňuje monitorování a Diagnostika sítě Azure. Diagnostické a vizualizační nástroje můžete použít ve službě Network Watcher pro:

- Zachytávání paketů vzdálené ve virtuálním počítači Azure.
- Získejte přehled o provozu vaší sítě pomocí protokolů toků.
- Diagnostika brány Azure VPN a připojení.

Network Watcher je aktuálně má následující možnosti:

- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): poskytuje přehled různých propojení a přidružení mezi síťové prostředky ve skupině prostředků.
- [Proměnné zachytávání paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): zaznamená data o paketů do a z virtuálního počítače. Rozšířené možnosti filtrování a jemně vyladěné ovládací prvky, jako je možnost nastavit omezení času a velikosti, poskytují všestrannost. Paketu data mohou být uložena v úložišti objektů blob nebo na místní disk ve formátu CAP.
- [Ověření toku protokolu IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): zkontroluje, jestli je paket povolený nebo zakázaný založenou na parametrech paketů 5 řazené kolekce členů pro informace o toku (cílová IP adresa, Zdrojová IP adresa, cílový port, zdrojový port a protokol). Pokud skupinu zabezpečení zakazuje paket, budou vráceny pravidlo a skupiny, které paket zamítlo.
- [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Určuje další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure, takže můžete diagnostikovat všechny správně nakonfigurovaný. trasy definované uživatelem.
- [Zobrazení skupin zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): získá pravidla efektivní a použité zabezpečení, které se použijí na virtuálním počítači.
- [Protokoly toků NSG pro skupiny zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): vám umožní zachytit protokoly týkající se provozu, který je povolený nebo zakázaný podle pravidla zabezpečení ve skupině. Tok je definován informace 5 řazené kolekce členů: zdrojové IP, cílová IP adresa, zdrojový port, cílový port a protokol.
- [Brána virtuální sítě a řešení potíží s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): poskytuje možnost Poradce při potížích s brány virtuální sítě a připojení.
- [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): umožňuje zobrazit využití prostředků sítě proti omezení.
- [Diagnostické protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): poskytuje k povolení nebo zakázání diagnostické protokoly pro síťové prostředky ve skupině prostředků.

Další informace najdete v tématu [konfigurace Network Watcheru](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="cloud-service-provider-access-transparency"></a>Cloudové služby poskytovatele přístup transparentnosti

[Zákazník Lockboxu pro Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) je integrovaná služba webu Azure portal, která explicitní ovládacího prvku v výjimečných instance při přístupu k datům pro řešení problému možná bude nutné pracovníka podpory společnosti Microsoft. Existují jen několik instancí, jako je ladění vzdáleného přístupu problém, kdy pracovník technické podpory Microsoftu vyžaduje zvýšenou úroveň oprávnění k vyřešení tohoto problému. V takových případech odborníky z Microsoftu pomocí služby just-in-time přístupu, která poskytuje omezené, časově omezenou autorizace přístup omezen na službu.  
Když Microsoft získal vždy zákazník souhlas pro přístup, zákazníka Lockboxu teď umožňuje zkontrolovat a schválit nebo zamítnout žádosti z portálu Azure Portal. Pracovníci podpory společnosti Microsoft nebude udělen přístup, až schválíte žádost.

## <a name="standardized-and-compliant-deployments"></a>Nasazení standardizované a předpisy

[Plány Azure](../governance/blueprints/overview.md) povolit cloudovým architektům a centrální informace technologie skupiny k definování opakovatelné sady prostředků Azure, které implementují a dodržovat standardy, vzory a požadavky vaší organizace.  
To umožňuje týmům DevOps rychle vytvořit a vytvořit nové prostředí a důvěřovat, že se jejich vytváření s infrastrukturou, která udržuje dodržování předpisů v organizaci. Plány poskytují deklarativní způsob, jak orchestrovat nasazení různých prostředků šablony a další artefakty, jako: 

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manageru
- Skupiny prostředků

## <a name="devops"></a>DevOps

Před [vývojářských operací (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) vývoje aplikací, týmy byly starosti shromažďování podnikové požadavky na software programu a psaní kódu. Pak samostatný tým QA testovat program v izolované vývojové prostředí. Pokud byly splněny požadavky, dotazů a odpovědí tým vydal kód pro operace k nasazení. Týmy pro nasazení se dále liší do skupin, jako je síť a databáze. Pokaždé, když softwarový program "vyvolala na zeď" nezávislé týmu, přidání kritické body.

DevOps umožňuje týmům dodávat bezpečnější a kvalitnější řešení rychleji a levněji. Zákazníci očekávají dynamické a spolehlivé prostředí při využívání softwaru a služeb. Týmy musí rychle iterovat aktualizace softwaru a měřit dopad aktualizací. Musí rychle reagovat pomocí nových iterací vývoje pro řešení potíží s nebo poskytovat přidanou hodnotu.  

Cloudové platformy, jako je Microsoft Azure mají odstranil tradiční překážky a pomohl z infrastruktury udělat druh zboží. Software reigns v každé firmy jako klíčový a faktor obchodní výsledky. Žádná organizace, vývojář nebo pracovník v oblasti IT může nebo byste se vyhnout hnutí DevOps se nemůže.

Osvojte si profesionálové DevOps používají některé z následujících postupů. Tyto postupy [zahrnovat osoby](https://www.visualstudio.com/learn/what-is-devops-culture/) k vytvářejí strategie na základě obchodních scénářů. Vhodné nástroje můžou usnadnit automatizaci některých postupů.

- [Agilní plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) techniky se používají k plánování a izolaci práce do sprintů, správě kapacity týmu a pomáhají týmům rychle se přizpůsobit měnícím se potřebám podniku.
- [Správa verzí, obvykle pomocí Gitu](https://www.visualstudio.com/learn/what-is-git/), umožňuje týmům rozmístěným po celém světe sdílet zdrojové kódy a integraci s nástroji pro automatizaci vydávání vývoj softwaru.
- [Průběžná integrace](https://www.visualstudio.com/learn/what-is-continuous-integration/) pohání probíhající slučování a testování kódu, což vede k časnému odhalení chyb.  Mezi další výhody patří méně času ztraceného bojem s problémy se slučováním a rychlá zpětná vazba pro vývojové týmy.
- [Průběžné doručování](https://www.visualstudio.com/learn/what-is-continuous-delivery/) softwarových řešení do produkčních a testovacích prostředí pomáhá organizacím rychle opravovat chyby a reagovat na neustále se měnící obchodní požadavky.
- [Monitorování](https://www.visualstudio.com/learn/what-is-monitoring/) spuštěné aplikace – včetně produkčních prostředí pro stav aplikací, jakož i využití ze strany zákazníků – pomáhá organizacím formulovat hypotézy a rychle ověřit nebo vyvrátit strategie.  Podrobná data se zachytit a ukládají v různých formátech protokolování.
- [Infrastruktura jako kód (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) je postup, který umožňuje automatizaci a ověřování vytváření a odstraňování sítí a virtuálních počítačů pomáhá poskytovat zabezpečené a stabilní platformy pro hostování aplikací.
- [Mikroslužby](https://www.visualstudio.com/learn/what-are-microservices/) architektura se používá k izolaci obchodních případů použití do malých, opakovaně použitelných služeb.  Tato architektura umožňuje škálování a efektivitu.

## <a name="next-steps"></a>Další postup

Další informace o řešení zabezpečení a Audit, naleznete v následujících článcích:

- [Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)
