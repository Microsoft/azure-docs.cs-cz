---
title: Přehled Azure provozní zabezpečení | Microsoft Docs
description: Tento článek obsahuje přehled zabezpečení provozu Azure.
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
ms.openlocfilehash: c0413678aad16105f732ef23fb60c61fddcdad45
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="azure-operational-security-overview"></a>Přehled Azure provozního zabezpečení
[Zabezpečení provozu Azure](https://docs.microsoft.com/azure/security/azure-operational-security) odkazuje na službách, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svá data, aplikace a dalších prostředků ve službě Microsoft Azure. Je rozhraní, které zahrnuje poznatky získané při celou řadu funkcí, které jsou jedinečné pro Microsoft. Tyto schopnosti zahrnují Microsoft SDL Security Development Lifecycle (), programu Microsoft Security Response Center a hloubkové povědomí o povahu hrozeb počítačové bezpečnosti.

## <a name="operations-management-suite"></a>Operations Management Suite
Jeho provozní tým IT zodpovídá za správu infrastruktuře datacentra, aplikace a data, včetně stability a zabezpečení těchto systémech. Získat přehled o zabezpečení napříč zvýšení komplexní prostředí IT často však vyžaduje organizacím cobble společně data z několika systémů, zabezpečení a správu.

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je cloudové správy řešení IT, která pomáhá spravovat a chránit místní a cloudové infrastruktury. Základními funkcemi poskytuje tyto služby, které běží v Azure. Každá služba poskytuje funkce správy specifických. Můžete kombinovat služby k dosažení správy různé scénáře. 

### <a name="log-analytics"></a>Log Analytics
[Azure Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) zajišťuje služby monitorování pro služby Operations Management Suite ve shromažďování dat ze spravované prostředky do centrální úložiště. Tato data můžou obsahovat události, údaje o výkonu nebo vlastní data poskytnutá prostřednictvím rozhraní API. Po data jsou shromažďována, je k dispozici pro výstrahy, analýzu a exportu. 

Můžete sloučit data z různých zdrojů a kombinovat data ze služeb Azure s vaší stávající místní prostředí. Kolekce dat analýzy protokolů také jasně odděluje od akci provedenou se tato data, tak, aby byly k dispozici pro všechny typy dat všechny akce.

### <a name="automation"></a>Automation
[Služby Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) poskytuje způsob, jak automatizovat ruční, dlouhotrvajících, problematických a často se opakujících úloh, které se běžně provádějí v cloudovém a podnikovém prostředí. To šetří čas a zvyšuje spolehlivost úloh správy. Naplánuje i tyto úlohy automaticky provádět v pravidelných intervalech. Můžete automatizovat procesy pomocí sad runbook nebo automatizovat správu konfigurace pomocí konfigurace požadovaného stavu.

### <a name="backup"></a>Backup
[Zálohování Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) je služba na základě Azure, které můžete použít k zálohování (nebo chránit) a obnovování vašich dat v cloudu Microsoft. Zálohování Azure nahradí existující místního nebo odlehlého zálohování řešení cloudové řešení, která je spolehlivé, zabezpečené a cenově konkurenceschopným. 

Zálohování Azure nabízí součásti, které můžete stáhnout a nasadit na příslušné počítače nebo serveru nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny součásti Azure Backup (jestli chcete chránit data místně nebo v cloudu) můžete použít k zálohování dat do trezoru služeb zotavení Azure, služby v Azure. 

Další informace najdete v tématu [tabulky Azure Backup součásti](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) poskytuje provozní kontinuitu tím, že orchestruje replikaci lokálních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud primární lokalita není k dispozici, můžete převzetí služeb při selhání do sekundárního umístění tak, aby uživatelé můžou pokračovat v práci. Můžete služeb při selhání zpět, když systémy vrátit fungují správně. Použití Azure Security Center provést inteligentní a efektivní detekce hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) je komplexní identity služby který:

-   Umožňuje správu identit a přístupu (IAM) jako cloudová služba.
-   Poskytuje správu centrální přístupu, jednotné přihlašování (SSO) a vytváření sestav.
-   Podporuje správu integrovaného přístupu k [tisíce aplikace](https://azure.microsoft.com/marketplace/active-directory/) v Azure Marketplace, včetně služby Salesforce, Google Apps, pole a Concur.

Azure AD také zahrnuje úplná sada [funkce správy identit](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), včetně těchto:

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Registrace zařízení]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview)
- [Hesla pomocí samoobslužné služby správy](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobslužná správa skupin](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Správa privilegovaných účtů](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Sledování využití aplikací](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Bohaté auditování](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Zabezpečení monitorování a výstrahy](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

S Azure Active Directory, všechny aplikace, které publikujete pro partnery a zákazníky (obchodní nebo příjemce) mají stejnou identitu a přístup k možnosti správy. To vám umožňuje výrazně snížit provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) pomáhá zabránit, zjišťovat a nabízí lepší přehled reagovat na hrozby (a ovládat) zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných. Pomáhá zjišťovat hrozby, kterých byste jinak nevšimli, a funguje s řadou řešení zabezpečení.

[Chrání data virtuálního počítače (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) v Azure poskytuje přehled o nastavení zabezpečení virtuálního počítače a monitorování hrozby. Security Center může u virtuálních počítačů monitorovat:

-   Nastavení zabezpečení operačního systému s doporučenou konfiguraci pravidla.
-   Zabezpečení systému a důležité aktualizace, které chybí.
-   Koncový bod ochrana doporučení.
-   Ověření šifrování disku.
-   Útoky ze sítě.

Security Center používá [řízení přístupu na základě Role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Poskytuje RBAC [předdefinované role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) , lze přiřadit uživatelům, skupinám a službám v Azure.

Security Center vyhodnocuje konfigurace vaše prostředky a identifikují problémy se zabezpečením a ohrožení zabezpečení. V Centru zabezpečení najdete informace týkající se prostředek jenom v případě, že jste přiřadili roli vlastník, Přispěvatel nebo Čtenář pro předplatné nebo skupinu prostředků, které daný prostředek patří.

>[!Note]
>Další informace o rolích a povolené akce v Centru zabezpečení, najdete v části [oprávnění v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Security Center používá službu Microsoft Monitoring Agent. Toto je stejný agent, který Operations Management Suite a analýzy protokolů služby používat. Data shromážděná z tohoto agenta je uložen v buď existující analýzy protokolů [prostoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) přidružený k předplatnému Azure nebo nový pracovní prostor, vezme v úvahu informace o zeměpisné poloze virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor
Problémy s výkonem v cloudové aplikace může ovlivnit vaší firmy. S více vzájemně propojena součástmi a často verzích může dojít, degradations kdykoli. A pokud vyvíjíte aplikace, uživatelé obvykle zjistit problémy, které nebyl nalezen v testování. Měli byste vědět o tyto problémy okamžitě a byste měli mít nástroje pro diagnostiku a řešení problémů.

[Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) je základní nástroj pro monitorování služby spuštěné v Azure. Nabízí data na úrovni infrastruktury o propustnost služby a okolního prostředí. Pokud spravujete své aplikace v Azure a rozhodování o škálování směrem nahoru nebo dolů prostředky, monitorování Azure je místo, kde spustit.

Můžete taky data monitorování a získáte přehled o hloubkové o vaší aplikaci. Dané znalosti můžete dozvíte, jak zlepšit výkon aplikace nebo udržovatelnosti nebo automatizaci akcí, které by jinak vyžadují ruční zásah. 

Azure monitorování obsahuje následující součásti.

### <a name="azure-activity-log"></a>Protokol činnosti Azure
[Protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) poskytuje vhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Ho se dřív označovala jako "Protokol auditu" nebo "Operační protokol", protože oznámí události rovině řízení pro vaše předplatné.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure
[Azure diagnostické protokoly](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) jsou vygenerované prostředek a poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku.

Protokoly událostí systému Windows jsou jednu kategorii diagnostické protokoly pro virtuální počítače. Objekt BLOB, tabulky a fronty protokoly jsou kategorie diagnostické protokoly pro účty úložiště.

Diagnostické protokoly se liší od [protokol aktivit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Protokol aktivit poskytuje náhled do činnosti, které byly provedeny v prostředky ve vašem předplatném. Diagnostické protokoly získat přehled o operace, aby prostředku provedeny sám sebe.

### <a name="metrics"></a>Metriky
Monitorování Azure poskytuje telemetrická data, která poskytuje přehled o výkonu a stavu úlohy v Azure. Je nejdůležitější typ Azure telemetrická data [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (také nazývané čítače) vysílaných prostředků nejvíce Azure. Monitorování Azure poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Diagnostika Azure
Azure Diagnostics umožňuje shromažďování diagnostických dat na nasazené aplikace. Můžete použít rozšíření diagnostiky z různých zdrojů. Aktuálně podporované jsou [role Azure cloudové služby](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuální počítače Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) systémem Microsoft Windows, a [Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="azure-network-watcher"></a>Sledovací proces sítě Azure
Zákazníci sestavení síť začátku do konce v Azure tak, že orchestruje a skládání jednotlivých síťovým prostředkům, například virtuální sítě Azure ExpressRoute, Azure Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředků.

Komplexní konfigurace a interakce mezi prostředky, může mít sítě začátku do konce. Výsledkem je komplexní scénáře, které je třeba na základě scénáře monitorování prostřednictvím [sledovací proces sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Sledovací proces sítě usnadňuje monitorování a Diagnostika sítě Azure. Můžete použít nástroje pro diagnostiku a vizualizace v sledovací proces sítě na:
- Trvat zachycení vzdálené paketů na virtuální počítač Azure.
- Získat přehled o přenosy v síti pomocí protokolů toku.
- Diagnostikujte Azure VPN Gateway a připojení.

Sledovací proces sítě aktuálně má následující možnosti:

- [Topologie](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): poskytuje přehled o různých propojení a přidružení mezi síťovým prostředkům ve skupině prostředků.
-   [Zachytáváním paketů proměnné](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): zaznamená data paketů do/z virtuálního počítače. Pokročilé možnosti filtrování a podrobně nastavit ovládací prvky, jako je například možnost nastavení času a velikost omezení, zadejte univerzálnost. Paketu data mohou být uložena v úložišti objektů blob nebo na místní disk ve formátu CAP.
-   [Ověřte IP toku](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): ověří, zda je paket povolený nebo zakázaný na základě 5 řazené kolekce členů paketu parametrů toku informace (cílovou IP adresu, zdrojové IP adresy, cílový port, zdrojový port a protokol). Pokud skupina zabezpečení odmítne paketu, vrátí se pravidlo a skupiny, který odepřen paketu.
-   [Další směrování](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Určuje další směrování pro pakety směrování v rámci prostředků infrastruktury síť Azure, můžete určit všechny příčiny špatně nakonfigurovaný trasy definované uživatelem.
-   [Zobrazení skupiny zabezpečení](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): získá zabezpečení efektivní a použitých pravidel, která se použijí na virtuálním počítači.
-   [Tok protokolů NSG pro skupiny zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): umožňují zaznamenat protokoly související s provoz, který je povolené nebo zakázané pravidla zabezpečení ve skupině. Tok je definované informace 5 řazené kolekce členů: zdrojového IP, cílovou IP adresu, zdrojový port, cílový port a protokol.
-   [Brána virtuální sítě a řešení potíží s připojení](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): poskytuje možnost Poradce při potížích brány virtuální sítě a připojení.
-   [Sítě limity předplatného](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): umožňuje zobrazit využití prostředků sítě proti omezení.
-   [Diagnostické protokoly](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): poskytuje k povolení nebo zakázání diagnostické protokoly pro síťové prostředky ve skupině prostředků.

Další informace najdete v tématu [konfigurace sledovací proces sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="devops"></a>DevOps
Před [vývojáře operací (DevOps)](https://www.visualstudio.com/learn/what-is-devops/) vývoj aplikací týmy byly starosti shromažďování podnikových požadavků pro softwarový program a psaní kódu. Potom samostatný tým QA otestovat program izolované vývojovém prostředí. Pokud byly splněny požadavky, vydal QA team kód pro operace k nasazení. Týmy nasazení byly další fragmentované do skupin, jako jsou sítě a databáze. Pokaždé, když softwarový program "došlo k přes stěny" nezávislé týmu, přidat kritická místa.

DevOps týmům umožňuje poskytovat bezpečnější, kvalitnější řešení rychleji a levněji. Zákazníci očekávat dynamické a spolehlivé prostředí při využívání softwaru a služeb. Týmy musí rychle iterovat aktualizace softwaru a měřit dopad aktualizace. Musí rychle odpovídat nový vývoj iterací řešit problémy nebo zadejte další hodnotu.  

Cloudové platformy, jako je Microsoft Azure mají odebrat tradiční kritická místa a pomohl commoditize infrastruktury. Software reigns v každé firmy jako klíčovým rozdílem a faktorem obchodní výsledky. Žádná organizace, vývojáře nebo pracovníka IT může nebo byste neměli DevOps pohyb.

Profesionálové zabývající vyspělá DevOps přijmout řadu následující postupy. Tyto postupy [zahrnují osoby](https://www.visualstudio.com/learn/what-is-devops-culture/) do formuláře strategie, podle obchodních scénářů. Nástrojů můžete automatizovat různé postupy.

-   [Agilní plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) techniky slouží k plánování a izolovat pracovní do sprintů, spravovat team kapacitu a pomůže rychle se měnící se firemní potřeby přizpůsobit týmy.
-   [Správa verzí, obvykle s Gitem](https://www.visualstudio.com/learn/what-is-git/), umožňuje týmy umístěným kdekoliv na světě sdílet zdroje a integrovat s vývojářskými nástroji softwaru k automatizaci verzi kanálu.
-   [Průběžnou integraci](https://www.visualstudio.com/learn/what-is-continuous-integration/) jednotky Probíhající sloučení a testování kódu, což vede k hledání defekty již v rané fázi.  Mezi další výhody patří méně času ke znehodnocení části na požárů sloučení problémy a rychlé zpětnou vazbu pro vývojové týmy.
-   [Nastavené průběžné doručování](https://www.visualstudio.com/learn/what-is-continuous-delivery/) řešení softwaru do produkčního prostředí a testovací prostředí pomáhá organizacím rychle opravit chyby a reagovat na proměnlivých podnikové požadavky.
-   [Monitorování](https://www.visualstudio.com/learn/what-is-monitoring/) spuštěných aplikací – včetně produkčního prostředí pro stavu aplikací, stejně jako zákazník využití – pomáhá organizacím formuláři předpoklad a rychle ověření nebo disprove strategie.  Bohaté data jsou zachytit a uložená v různých formátech protokolování.
-   [Infrastruktura jako kód (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) je postup, který umožňuje automatizace a ověření vytvoření a rušením sítí a virtuálních počítačů pomáhají s doručováním hostování platformy zabezpečené, stabilní aplikace.
-   [Mikroslužeb](https://www.visualstudio.com/learn/what-are-microservices/) architektura je použít k izolování případy obchodního použití do malých opakovaně použitelné služby.  Tato architektura umožňuje škálovatelnost a efektivitu.

## <a name="next-steps"></a>Další postup
Další informace o řešení Operations Management Suite zabezpečení a Audit, naleznete v následujících článcích:

- [Zabezpečení a dodržování předpisů](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Monitorování a zpracování výstrah zabezpečení v řešení pro zabezpečení Operations Management Suite a auditu](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)
- [Sledování prostředků v Operations Management Suite zabezpečení a Audit řešení](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)
