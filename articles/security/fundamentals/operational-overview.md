---
title: Přehled služby Azure Operational Security | Microsoft Docs
description: Další informace o provozním zabezpečení Azure najdete v tomto přehledu. Provozní zabezpečení odkazuje na služby, ovládací prvky a funkce ochrany Asset Protection.
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
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595495"
---
# <a name="azure-operational-security-overview"></a>Přehled služby Azure Operational Security

[Provozní zabezpečení Azure](./operational-security.md) odkazuje na služby, ovládací prvky a funkce, které jsou k dispozici uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Microsoft Azure. Je to architektura, která zahrnuje znalostní báze získané prostřednictvím nejrůznějších funkcí, které jsou jedinečné pro společnost Microsoft. Mezi tyto možnosti patří Microsoft Security Development Lifecycle (SDL), program Microsoft Security Response Center a důkladné povědomí o kyberbezpečnosti hrozbách.

## <a name="azure-management-services"></a>Služby správy Azure

Provozní tým IT je zodpovědný za správu infrastruktury Datacenter, aplikací a dat, včetně stability a zabezpečení těchto systémů. Nicméně získání přehledů o zabezpečení ve všech rostoucích složitých prostředích IT často vyžaduje, aby organizace Cobble data z více systémů zabezpečení a správy.

[Protokoly sledování Microsoft Azure](../../azure-monitor/overview.md) jsou cloudové řešení pro správu IT, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu. Základní funkce nabízí následující služby, které běží v Azure. Azure obsahuje několik služeb, které vám pomůžou se správou a ochranou místní a cloudové infrastruktury. Každá služba poskytuje konkrétní funkci správy. Můžete kombinovat služby a dosáhnout tak různých scénářů správy. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](../../azure-monitor/overview.md) shromažďuje data ze spravovaných zdrojů do centrálních úložišť dat. Tato data můžou zahrnovat události, data o výkonu nebo vlastní data poskytovaná prostřednictvím rozhraní API. Po shromáždění dat je k dispozici pro výstrahy, analýzu a export.

Můžete konsolidovat data z nejrůznějších zdrojů a kombinovat data ze služeb Azure s existujícím místním prostředím. Protokoly Azure Monitor také jednoznačně oddělují shromažďování dat z akce provedené u těchto dat, takže všechny akce jsou k dispozici pro všechny druhy dat.

### <a name="automation"></a>Automation

[Azure Automation](../../automation/automation-intro.md) poskytuje možnost automatizace ručních, dlouhotrvajících a často opakovaných úloh, které se běžně provádějí v cloudovém a podnikovém prostředí. Šetří čas a zvyšuje spolehlivost administrativních úloh. V pravidelných intervalech také plánuje automatické provádění těchto úkolů. Procesy můžete automatizovat pomocí runbooků nebo automatizovat správu konfigurace pomocí konfigurace požadovaného stavu.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) je služba založená na Azure, kterou můžete použít k zálohování (nebo ochraně) a obnovení dat v Microsoft Cloud. Azure Backup nahradí vaše stávající místní nebo jiné řešení zálohování cloudovým řešením, které je spolehlivé, bezpečné a nákladově konkurenční.

Azure Backup nabízí komponenty, které stáhnete a nasadíte na příslušném počítači nebo serveru nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny součásti Azure Backup (bez ohledu na to, jestli chráníte data v místním nebo cloudovém prostředí) se dají použít k zálohování dat do trezoru Azure Recovery Services v Azure.

Další informace najdete v [tabulce Azure Backup komponenty](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) zajišťuje kontinuitu podnikových prostředí tím, že orchestruje replikaci místních virtuálních a fyzických počítačů do Azure nebo do sekundární lokality. Pokud vaše primární lokalita není k dispozici, převezmete služby při selhání do sekundárního umístění, aby uživatelé mohli i nadále pracovat. Po návratu systémů do funkčního pořadí dojde k navrácení služeb po obnovení. Pomocí Azure Security Center můžete provádět inteligentnější a efektivní detekci hrozeb.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) je komplexní služba identity, která:

-   Povolí správu identit a přístupu (IAM) jako cloudovou službu.
-   Poskytuje správu centrálního přístupu, jednotné přihlašování (SSO) a vytváření sestav.
-   Podporuje správu integrovaného přístupu pro [tisíce aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) v Azure Marketplace, včetně Salesforce, Google Apps, box a Concur.

Azure AD zahrnuje také plnou sadu [funkcí pro správu identit](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports), včetně těchto:

- [Vícefaktorové ověřování](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Samoobslužná správa hesel](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Samoobslužná správa skupin](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Správa privilegovaných účtů](../../active-directory/privileged-identity-management/pim-configure.md)
- [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md)
- [Monitorování využití aplikací](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Rozšířené auditování](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Monitorování a upozorňování zabezpečení](../../security-center/security-center-managing-and-responding-alerts.md)

Díky Azure Active Directory mají všechny aplikace publikované pro vaše partnery a zákazníky (firmy nebo spotřebitele) stejné možnosti správy identit a přístupu. To vám umožňuje významně snižovat provozní náklady.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že zvýšená viditelnost (a kontrolu nad zabezpečením vašich prostředků Azure). Poskytuje integrované monitorování zabezpečení a správu zásad ve vašich předplatných. Pomáhá detekovat hrozby, které by jinak neinformovaly, a funguje s širokou ekosystémem řešení zabezpečení.

[Zabezpečte data virtuálních počítačů](../../security-center/security-center-introduction.md) v Azure tím, že zajistíte lepší nastavení zabezpečení virtuálního počítače a monitorování hrozeb. Security Center může u virtuálních počítačů monitorovat:

- Nastavení zabezpečení operačního systému s doporučenými konfiguračními pravidly.
- Chybí zabezpečení systému a kritické aktualizace.
- Doporučení pro ochranu koncového bodu.
- Ověření šifrování disku.
- Síťové útoky.

Security Center používá [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md). Služba Azure RBAC poskytuje [předdefinované role](../../role-based-access-control/built-in-roles.md) , které je možné přiřadit uživatelům, skupinám a službám v Azure.

Security Center posuzuje konfiguraci vašich prostředků pro identifikaci problémů a ohrožení zabezpečení. V Security Center se zobrazí informace týkající se prostředku pouze v případě, že máte přiřazenou roli vlastník, přispěvatel nebo čtenář pro předplatné nebo skupinu prostředků, do které prostředek patří.

>[!Note]
>Další informace o rolích a povolených akcích v Security Center najdete v tématu [oprávnění v Azure Security Center](../../security-center/security-center-permissions.md).

Security Center používá Microsoft Monitoring Agent. Toto je stejný agent, kterého používá služba Azure Monitor. Data shromážděná z tohoto agenta se ukládají do stávajícího Log Analyticsho [pracovního prostoru](../../azure-monitor/logs/manage-access.md) přidruženého k vašemu předplatnému Azure nebo novému pracovnímu prostoru, přičemž se vezme v úvahu geografická poloha virtuálního počítače.

## <a name="azure-monitor"></a>Azure Monitor

Problémy s výkonem ve vaší cloudové aplikaci můžou ovlivnit vaši firmu. S více propojenými komponentami a často vydanými verzemi může degradace probíhat kdykoli. A pokud vyvíjíte aplikaci, uživatelé obvykle zjišťují problémy, které nebyly při testování nalezeny. O těchto problémech byste měli okamžitě informovat a měli byste mít nástroje pro diagnostiku a řešení problémů.

[Azure monitor](../../azure-monitor/overview.md) je základní nástroj pro monitorování služeb spuštěných v Azure. Poskytuje data na úrovni infrastruktury týkající se propustnosti služby a okolního prostředí. Pokud spravujete své aplikace v Azure a rozhodujete, jestli chcete škálovat prostředky směrem nahoru nebo dolů, Azure Monitor je místo, kde se má začít.

K získání podrobných přehledů o vaší aplikaci můžete použít taky data monitorování. Tato znalostní báze vám může pomoci zvýšit výkon a udržovatelnost aplikace nebo automatizovat akce, které by jinak vyžadovaly ruční zásah.

Azure Monitor obsahuje následující součásti.

### <a name="azure-activity-log"></a>Protokol aktivit Azure

[Protokol aktivit Azure](../../azure-monitor/essentials/platform-logs-overview.md) poskytuje přehled o operacích, které byly provedeny u prostředků v rámci vašeho předplatného. Dříve se nazýval protokol auditu nebo provozní protokol, protože pro vaše předplatná hlásí události řízení a roviny.

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

[Diagnostické protokoly Azure](../../azure-monitor/essentials/platform-logs-overview.md) jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Obsah těchto protokolů se liší podle typu prostředku.

Protokoly událostí systému Windows jsou jednou z kategorií diagnostických protokolů pro virtuální počítače. Protokoly objektů blob, tabulek a front jsou kategoriemi diagnostických protokolů pro účty úložiště.

Diagnostické protokoly se liší od [protokolu aktivit](../../azure-monitor/essentials/platform-logs-overview.md). Protokol aktivit nabízí přehled o operacích provedených u prostředků v rámci vašeho předplatného. Diagnostické protokoly poskytují přehled o operacích, které se provedly samotným prostředkem.

### <a name="metrics"></a>Metriky

Azure Monitor poskytuje telemetrii, která vám poskytne přehled o výkonu a stavu vašich úloh v Azure. Nejdůležitější typ dat telemetrie Azure jsou [metriky](../../azure-monitor/data-platform.md) (označované taky jako čítače výkonu) vydávané většinou prostředků Azure. Azure Monitor poskytuje několik způsobů, jak nakonfigurovat a využívat tyto metriky pro monitorování a řešení potíží.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Azure Diagnostics povoluje shromažďování diagnostických dat v nasazené aplikaci. Diagnostické rozšíření můžete použít z různých zdrojů. Aktuálně se podporuje [role cloudových služeb Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuální počítače Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) se systémem Microsoft Windows a [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Zákazníci vytvářejí komplexní síť v Azure tím, že orchestrují a sestavují jednotlivé síťové prostředky, jako jsou virtuální sítě, Azure ExpressRoute, Azure Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředcích.

Koncová síť může mít složitou konfiguraci a interakce mezi prostředky. Výsledkem jsou komplexní scénáře, které vyžadují monitorování založené na scénářích prostřednictvím [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

Network Watcher zjednodušuje monitorování a diagnostiku vaší sítě Azure. Nástroje pro diagnostiku a vizualizaci v Network Watcher můžete použít k těmto akcím:

- Pořiďte vzdálené zachycení paketů na virtuálním počítači Azure.
- Získejte přehled o provozu v síti pomocí protokolů toků.
- Diagnostikujte Azure VPN Gateway a připojení.

Network Watcher v současné době má následující možnosti:

- [Topologie](../../network-watcher/view-network-topology.md): poskytuje přehled o různých propojeních a přidruženích mezi síťovými prostředky ve skupině prostředků.
- [Zachytávání paketů proměnných](../../network-watcher/network-watcher-packet-capture-overview.md): zachycuje data paketů na virtuálním počítači a z něj. Rozšířené možnosti filtrování a jemně vyladěné ovládací prvky, jako je možnost nastavit omezení času a velikosti, poskytují všestrannost. Data paketů se můžou ukládat do úložiště objektů BLOB nebo na místní disk ve formátu. Cap.
- [Ověření toku protokolu IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md): kontroluje, jestli je paket povolený nebo zakázaný na základě parametrů paketu o hodnotě 5 řazené kolekce členů (cílová IP adresa, zdrojová adresa IP, cílový port, zdrojový port a protokol). Pokud skupina zabezpečení zamítne paket, vrátí se pravidlo a skupina, které paket zamítly.
- [Další směrování](../../network-watcher/network-watcher-next-hop-overview.md): Určuje další segment směrování pro pakety směrované do síťových prostředků Azure, abyste mohli diagnostikovat všechny nesprávně nakonfigurované trasy definované uživatelem.
- [Zobrazení skupiny zabezpečení](../../network-watcher/network-watcher-security-group-view-overview.md): Získá platná a použitá pravidla zabezpečení, která jsou použita na virtuálním počítači.
- [Protokoly toku NSG pro skupiny zabezpečení sítě](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): umožňuje zachytit protokoly související s provozem, který je povolený nebo zakázaný pravidly zabezpečení ve skupině. Tok je definovaný pomocí informací o 5-řazené kolekci členů: zdrojová IP adresa, cílová IP adresa, zdrojový port, cílový port a protokol.
- [Řešení potíží s bránou virtuální sítě a připojením](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): poskytuje možnost odstraňovat brány a připojení virtuální sítě.
- [Omezení pro předplatné sítě](../../network-watcher/network-watcher-monitoring-overview.md): umožňuje zobrazit využití síťových prostředků proti limitům.
- [Diagnostické protokoly](../../network-watcher/network-watcher-monitoring-overview.md): poskytuje jedno podokno pro povolení nebo zakázání diagnostických protokolů pro síťové prostředky ve skupině prostředků.

Další informace najdete v tématu [konfigurace Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Transparentnost přístupu poskytovatele cloudové služby

[Customer Lockbox pro Microsoft Azure](customer-lockbox-overview.md) je služba integrovaná do Azure Portal, která poskytuje explicitní řízení ve vzácných instancích, když podpora Microsoftu inženýr může potřebovat přístup k vašim datům za účelem vyřešení problému.
Existuje hodně instancí, jako je například ladění problému se vzdáleným přístupem, kde podpora Microsoftu inženýr vyžaduje zvýšená oprávnění k vyřešení tohoto problému. V takových případech technici Microsoftu využívají službu pro přístup za běhu, která poskytuje omezenou časovou autorizaci s přístupem omezeným na službu.  
I když společnost Microsoft vždycky získala souhlas zákazníka s přístupem, Customer Lockbox nyní poskytuje možnost kontrolovat a schvalovat nebo odmítat takové žádosti z webu Azure Portal. Pracovníkům podpory Microsoftu nebude udělen přístup, dokud žádost neschválíte.

## <a name="standardized-and-compliant-deployments"></a>Standardizovaná a vyhovující nasazení

[Plány Azure](../../governance/blueprints/overview.md) umožňují cloudové architekty a skupiny centrálních informačních technologií definovat opakující se sadu prostředků Azure, které implementují a vyhovují standardům, vzorům a požadavkům organizace.  
Díky tomu mohou týmy DevOps rychle sestavovat a vytvářet nová prostředí a důvěřovat tomu, že jsou sestavují s infrastrukturou, která udržuje dodržování předpisů organizace.
Modrotisky poskytují deklarativní způsob pro orchestraci nasazení různých šablon prostředků a dalších artefaktů, jako jsou:

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manageru
- Skupiny prostředků

## <a name="devops"></a>DevOps

Před vývojem aplikací [(DevOps)](https://azure.microsoft.com/overview/what-is-devops/) byly týmy zpoplatněné shromažďování podnikových požadavků pro softwarový program a psaní kódu. Pak samostatný tým služby QA testoval program v izolovaném vývojovém prostředí. V případě splnění požadavků tým QA uvolnil kód pro operace, které chcete nasadit. Týmy nasazení byly dále rozděleny do skupin, jako jsou sítě a databáze. Pokaždé, když se softwarový program vyvolal přes zeď, do nezávislého týmu, přidal se k němu kritická místa.

DevOps umožňuje týmům dodávat bezpečnější a vyšší kvalitu řešení rychleji a efektivněji. Zákazníci očekávají dynamický a spolehlivý zážitek při využívání softwaru a služeb. Týmy musí rychle iterovat na aktualizace softwaru a měřit dopad aktualizací. Musí rychle reagovat pomocí nových iterací vývoje, aby vyřešily problémy nebo poskytovaly více hodnot.  

Cloudové platformy, jako je Microsoft Azure, odstranily tradiční kritické body a pomohl pomohly infrastrukturu. Vládne softwaru v každé firmě jako klíčové rozlišení a faktor v obchodních výstupech. Pohyb DevOps není možné nebo by se neměl používat žádná organizace, vývojář nebo pracovník IT.

Vyspělí DevOps lékaři přijímají několik z následujících postupů. Tyto postupy [zahrnují osoby](/azure/devops/learn/what-is-devops-culture) , které tvoří strategie založené na obchodních scénářích. Pomocí nástrojů můžete automatizovat různé postupy.

- Techniky [agilního plánování a řízení projektů](https://www.visualstudio.com/learn/what-is-agile/) slouží k plánování a izolaci práce do sprintů, správě kapacity týmu a pomáhat týmům rychle se přizpůsobit měnícím se potřebám firmy.
- Správa [verzí, obvykle s Git](/azure/devops/learn/git/what-is-git), umožňuje týmům umístěným kdekoli na světě sdílet zdroje a integrovat s nástroji pro vývoj softwaru za účelem automatizace kanálu vydávání verzí.
- [Průběžná integrace](/azure/devops/learn/what-is-continuous-integration) Drive průběžné slučování a testování kódu, který vede k brzkému nalezení vad.  Mezi další výhody patří méně času v boji proti problémům sloučení a rychlé zpětné vazby pro vývojové týmy.
- [Průběžné doručování](/azure/devops/learn/what-is-continuous-delivery) softwarových řešení do produkčních a testovacích prostředí pomáhá organizacím rychle opravovat chyby a reagovat na neustále se měnící obchodní požadavky.
- [Monitorování](/azure/devops/learn/what-is-monitoring) spuštěných aplikací – včetně produkčních prostředí pro stav aplikací a zákaznického využití – pomáhá organizacím vytvořit hypotézu a rychle ověřit nebo prokázat strategie.  Bohatá data jsou zachycena a ukládána v různých formátech protokolování.
- [Infrastruktura jako kód (IAC)](/azure/devops/learn/what-is-infrastructure-as-code) je postup, který umožňuje automatizaci a ověřování vytváření a rozboru sítí a virtuálních počítačů při poskytování zabezpečených a stabilních hostujících platforem aplikací.
- Architektura [mikroslužeb](/azure/devops/learn/what-are-microservices) se používá k izolaci případů podnikového použití do malých opakovaně použitelných služeb.  Tato architektura umožňuje škálovatelnost a efektivitu.

## <a name="next-steps"></a>Další kroky

Další informace o řešení Security and Audit najdete v následujících článcích:

- [Zabezpečení a dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)