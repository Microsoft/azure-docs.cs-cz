---
title: Přehled Azure provozní zabezpečení | Microsoft Docs
description: Tento článek obsahuje přehled o zabezpečení Azure provozu.
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
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 20dfbd2c5c2c38abddf332dc6a20ddb271c95ace
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-operational-security-overview"></a>Přehled Azure provozního zabezpečení
Zabezpečení provozu Azure se odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure. [Zabezpečení provozu Azure](https://docs.microsoft.com/azure/security/azure-operational-security) je rozhraní, které zahrnuje znalosti získaných prostřednictvím nejrůznějších možností, které jsou jedinečné pro společnosti Microsoft, včetně Microsoft SDL Security Development Lifecycle (), Microsoft Security Response Center program a hloubkové povědomí o povahu hrozeb kybernetického zabezpečení.

V tomto článku Přehled zabezpečení provozu Azure se zaměřuje na tyto oblasti:

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure sledovací proces sítě
-   Azure Storage analytics
-   Azure Active directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
IT oddělení je zodpovědný za správu infrastruktuře datacentra, aplikace a data, včetně stability a zabezpečení těchto systémech. Získat přehled o zabezpečení napříč zvýšení komplexní prostředí IT často však vyžaduje organizacím cobble společně data z několika systémů, zabezpečení a správu.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury.

OMS je cloudové řešení správy IT s mnoha nabídky, jako je například IT automatizace, zabezpečení a dodržování předpisů, analýzy protokolů a zálohování a obnovení. Jako takový je ideální podpory pro správu a ochranu infrastrukturu – místně a v cloudu.

Základní funkce OMS poskytuje sada služeb, které běží v Azure. Každá služba poskytuje specifickou funkci správy a kombinováním služeb je možné dosahovat různých scénářů správy. Který zahrnuje:

-   Log Analytics
-   Automation
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) poskytuje služby monitorování pro OMS získáváním dat ze spravovaných prostředků do centrálního úložiště. Tato data mohou zahrnovat události, výkonnostní data nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po získání jsou data dostupná pro výstrahy, analýzu a export. Tato metoda vám umožňuje konsolidovat data z různých zdrojů, takže můžete kombinovat data ze služeb Azure s existujícím místním prostředím. Také jasně odděluje získávání dat od akcí provedených na těchto datech, takže všechny akce jsou dostupné pro všechny druhy dat.

### <a name="automation"></a>Automation
Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) poskytuje způsob, jak uživatelům možnost automatizace ruční, dlouhotrvajících, problematických a často se opakujících úloh, které se běžně provádějí v cloudovém a podnikovém prostředí. Šetří čas a zvyšuje spolehlivost běžných administrativních úloh a umí je dokonce naplánovat, aby se v pravidelných intervalech prováděly automaticky. Procesy můžete automatizovat pomocí runbooků nebo můžete automatizovat správu konfigurace pomocí DSC (požadovaný stav konfigurace).

### <a name="backup"></a>Backup
[Zálohování Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je služba na základě Azure můžete zálohovat (nebo chránit) a obnovování vašich dat v cloudu Microsoft. Azure Backup nahrazuje současná řešení místního nebo odlehlého zálohování spolehlivým, bezpečným a cenově konkurenceschopným cloudovým řešením. Azure Backup nabízí několik komponent, které můžete stáhnout a nasadit na vhodném počítači, na serveru, nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny komponenty služby Azure Backup (bez ohledu na to, jestli chráníte data v místním nebo cloudovém úložišti) je možné použít k zálohování dat do trezoru služby Recovery Services v Azure. Najdete v článku [tabulky Azure Backup součásti](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Obnovení lokality
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) poskytuje kontinuitu podnikových procesů tím, že orchestruje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud není vaše primární lokalita dostupná, proběhne převzetí služeb při selhání sekundárním umístěním, aby mohli uživatelé pokračovat v práci, a po obnovení funkce systémů proběhne navrácení služeb. Použití Azure Security Center provést inteligentní a efektivní detekce hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) je společnosti Microsoft komplexní Identity jako služby (IDaaS) řešení který:

-   Jako cloudová služba umožňuje IAM
-   Poskytuje správu centrální přístupu, jednotného přihlašování (SSO) a vytváření sestav
-   Podporuje správu integrovaného přístupu k [tisíce aplikace](https://azure.microsoft.com/marketplace/active-directory/) v galerii aplikací, včetně služby Salesforce, Google Apps, pole, Concur a další.

Azure AD také zahrnuje úplná sada [funkce správy identit](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports) včetně [služby Multi-Factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [registrace zařízení]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [ hesla pomocí samoobslužné služby správy](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [privilegovaný účet správy](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [řízení přístupu na základě role](https://docs.microsoft.com/azure/role-based-access-control/overview), [sledování využití aplikací](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [bohaté auditování](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), a [sledování a výstrah zabezpečení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

S Azure Active Directory všechny aplikace publikujete pro partnery a zákazníky (obchodní nebo příjemce) mají stejnou identitu a přístup k možnosti správy. To vám umožňuje výrazně snížit provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) pomáhá předcházet hrozbám, rozpoznávat je a reagovat na ně a nabízí lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) pomáhá zabezpečit data virtuálního počítače v Azure poskytuje přehled o nastavení zabezpečení virtuálního počítače a monitorování hrozby. Security Center může u virtuálních počítačů monitorovat:

-   Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly
-   Zabezpečení systému a chybějící kritické aktualizace
-   Doporučení ochrany koncových bodů
-   Ověření šifrování disku
-   Útoky ze sítě

Azure Security Center používá [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), který poskytuje [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , lze přiřadit uživatelům, skupinám a službám v Azure.

Security Center vyhodnocuje konfigurace vaše prostředky a identifikují problémy se zabezpečením a ohrožení zabezpečení. V Centru zabezpečení zobrazí jenom informace týkající se prostředek v případě jsou přiřazenou roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které daný prostředek patří.

>[!Note]
>V tématu [oprávnění v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions) Další informace o rolích a povolených akcí v Centru zabezpečení.

Security Center používá službu Microsoft Monitoring Agent – to je stejné agent používá služba Operations Management Suite a analýzy protokolů. Data shromážděná z tohoto agenta je uložen v buď existující analýzy protokolů [prostoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) přidružený k předplatnému Azure nebo nové pracovních prostorů, vezme v úvahu informace o zeměpisné poloze virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor
Problémy s výkonem v cloudové aplikace může mít vliv na vaši firmu. S více vzájemně propojena součástmi a často verzích může dojít, degradations kdykoli. A pokud vyvíjíte aplikace, uživatelé obvykle zjistit problémy, které nebyl nalezen v testování. Měli vědět o tyto problémy okamžitě a mít nástroje pro diagnostiku a řešení problémů.

[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) je základní nástroj pro monitorování služby spuštěné v Azure. Nabízí data na úrovni infrastruktury o propustnost služby a okolního prostředí. Pokud spravujete své aplikace v Azure, rozhodování o škálování směrem nahoru nebo dolů prostředky, pak monitorování Azure vám dává používáte ke spuštění.

Kromě toho můžete data monitorování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah. Obsahuje:

-   Protokol činnosti Azure
-   Azure diagnostických protokolů
-   Metriky
-   Diagnostika Azure

### <a name="azure-activity-log"></a>Protokol činnosti Azure
Je protokol, který poskytuje vhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. [Protokol aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) byla dříve označována jako "Protokoly auditu" nebo "Provozní protokoly,", protože oznámí události rovině řízení pro vaše předplatné.

### <a name="azure-diagnostic-logs"></a>Azure diagnostických protokolů
[Azure diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou vygenerované prostředek a poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku.

Například protokoly událostí systému Windows jsou jednu kategorii protokolů diagnostiky pro virtuální počítače a objektů blob, table a queue protokoly jsou kategorie diagnostické protokoly pro účty úložiště.

Diagnostické protokoly se liší od [protokol aktivit (dříve označované jako protokol auditů nebo operační protokol)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje náhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Diagnostické protokoly poskytují přehled o operacích, které provedly samotné vaše prostředky.

### <a name="metrics"></a>Metriky
Azure monitorování umožňuje využívat telemetrie a získáte přehled o výkonu a stavu úlohy v Azure. Nejdůležitější typ Azure telemetrická data je metriky (také nazývané čítače výkonu) vysílaných prostředků nejvíce Azure. Monitorování Azure poskytuje několik způsobů, jak nakonfigurovat a využívat tyto [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Diagnostika Azure
Je funkce v rámci Azure, která umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z různých různých zdrojů. Aktuálně podporované jsou [webové služby Azure Cloud a rolí pracovního procesu](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuální počítače Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systémem Microsoft Windows, a [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Zákazníci vytvářet síť začátku do konce v Azure tak, že orchestruje a skládání různé jednotlivých síťovým prostředkům, například virtuální síť, ExpressRoute, aplikační bránu, nástroje pro vyrovnávání zatížení a další. Monitorování je k dispozici na všech síťových prostředků.

Koncová sítě může mít komplexní konfigurace a interakce mezi prostředky, vytváření komplexní scénáře, které je třeba na základě scénáře monitorování prostřednictvím sledovací proces sítě.

[Sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) bude usnadňuje monitorování a Diagnostika sítě Azure. Diagnostiky a vizualizace nástroje, které jsou k dispozici sledovací proces sítě povolit, můžete provést vzdálené paketu zaznamená na virtuální počítač Azure, získat přehled o vaší síti provozu pomocí protokolů z toku a diagnostiku brány sítě VPN a připojení.

Sledovací proces sítě aktuálně má následující možnosti:

- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -poskytuje zobrazení úrovně sítě zobrazuje různé propojení a přidružení mezi síťovým prostředkům ve skupině prostředků.
-   [Proměnné zachytáváním paketů](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -zaznamená data paketů do/z virtuálního počítače. Pokročilé možnosti filtrování a podrobně nastavit ovládací prvky, jako je například moct nastavit čas a velikost omezení poskytují univerzálnost. Paketu data mohou být uložena v úložišti objektů blob nebo na místní disk ve formátu CAP.
-   [Ověřte IP toky](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) – ověří, zda je paket povolený nebo zakázaný na základě toku informace 5 řazené kolekce členů paketu parametrů (cílovou IP adresu, zdrojové IP adresy, cílový Port, zdrojový Port a protokol). Pokud paketu je zakázané skupiny zabezpečení, je vrácena pravidlo a skupiny, který odepřen paketu.
-   [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -určí další segment pro pakety směrovány v prostředcích infrastruktury sítě Azure umožňuje diagnostikovat žádné špatně nakonfigurovaný trasy definované uživatelem.
-   [Zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.
-   [Protokolování toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) -toku protokoly pro skupinu zabezpečení sítě umožňují zaznamenat protokoly související s přenosy, které jsou povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definována informací o 5-n-tice – zdrojové adresy IP, cílovou IP adresu, zdrojový Port, cílový Port a protokol.
-   [Brána virtuální sítě a řešení potíží s připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -poskytuje možnost Poradce při potížích brány virtuální sítě a připojení.
-   [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -umožňuje zobrazit využití prostředků sítě proti omezení.
-   [Konfigurace protokolu diagnostiky](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – poskytuje k povolení nebo zakázání diagnostické protokoly pro síťové prostředky ve skupině prostředků.

Další informace o tom, jak nakonfigurovat síť sledovacích procesů najdete [konfigurace sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Operace Developer (DevOps)
Před vývoj aplikací DevOps byly týmy starosti shromažďování podnikových požadavků pro softwarový program a psaní kódu. Samostatný tým QA pak testů v prostředí izolované vývoj program, pokud byly splněny požadavky a uvolní kód pro operace k nasazení. Týmy nasazení jsou další rozdělené na zastaralý skupin, jako je sítě a databáze. Pokaždé, když program softwaru je "vyvolána přes stěny" nezávislé týmu ho přidá kritická místa.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) týmům umožňuje poskytovat bezpečnější, kvalitnější řešení zrychluje a zlevňuje. Zákazníci očekávat dynamické a spolehlivé prostředí při využívání softwaru a služeb.  Týmy musí rychle iterovat aktualizace softwaru, měřit dopad aktualizace a rychle odpovídat nový vývoj iterací řešit problémy nebo zadejte další hodnotu.  Cloudové platformy, jako je Microsoft Azure mají odebrat tradiční kritická místa a pomohl commoditize infrastruktury. Software reigns v každé firmy jako klíčovým rozdílem a faktorem obchodní výsledky. Žádná organizace, vývojáře nebo pracovníka IT může nebo byste neměli DevOps pohyb.

Profesionálové zabývající vyspělá DevOps přijmout řadu následující postupy. Tyto postupy [zahrnují osoby](https://www.visualstudio.com/learn/what-is-devops-culture/) do formuláře strategie, podle obchodních scénářů.  Nástrojů můžete automatizovat různé postupy:

-   [Agilní plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) techniky slouží k plánování a izolovat pracovní do sprintů, spravovat team kapacitu a pomůže rychle se měnící se firemní potřeby přizpůsobit týmy.
-   [Správa verzí, obvykle s Gitem](https://www.visualstudio.com/learn/what-is-git/), umožňuje týmy umístěným kdekoliv na světě sdílet zdroje a integrovat s vývojářskými nástroji softwaru k automatizaci verzi kanálu.
-   [Průběžnou integraci](https://www.visualstudio.com/learn/what-is-continuous-integration/) jednotky Probíhající sloučení a testování kódu, což vede k hledání defekty již v rané fázi.  Mezi další výhody patří méně času ke znehodnocení části na požárů sloučení problémy a rychlé zpětnou vazbu pro vývojové týmy.
-   [Nastavené průběžné doručování](https://www.visualstudio.com/learn/what-is-continuous-delivery/) řešení softwaru do produkčního prostředí a testovací prostředí nápovědy organizace rychle opravit chyby a reagovat na proměnlivých podnikové požadavky.
-   [Monitorování](https://www.visualstudio.com/learn/what-is-monitoring/) spuštěných aplikací, včetně produkčního prostředí pro stavu aplikace jako i jako zákazník využití pomoc organizacím formuláře předpoklad a rychle ověření nebo disprove strategie.  Bohaté data jsou zachytit a uložená v různých formátech protokolování.
-   [Infrastruktura jako kód (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) postupem, který umožňuje automatizace a ověření vytvoření a rušením sítí a virtuálních počítačů pomáhají s doručováním hostování platformy zabezpečené, stabilní aplikace je.
-   [Mikroslužeb](https://www.visualstudio.com/learn/what-are-microservices/) architektura je využít k izolaci případy obchodního použití do malých opakovaně použitelné služby.  Tato architektura umožňuje škálovatelnost a efektivitu.

## <a name="next-steps"></a>Další postup
Další informace o řešení OMS zabezpečení a Audit, naleznete v následujících článcích:

- [Služby Operations Management Suite | Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Monitorování a zpracování výstrah zabezpečení v Operations Management Suite zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).
- [Sledování prostředků v Operations Management Suite zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources).
