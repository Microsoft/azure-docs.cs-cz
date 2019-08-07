---
title: Osvědčené postupy zabezpečení při ochraně vašich assetů – Microsoft Azure
description: Tento článek poskytuje sadu provozních osvědčených postupů pro ochranu vašich dat, aplikací a dalších prostředků v Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 97f061d94209d84b70626f8735d08f89870e7319
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828722"
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy pro provozní zabezpečení Azure
Tento článek poskytuje sadu provozních osvědčených postupů pro ochranu vašich dat, aplikací a dalších prostředků v Azure.

Osvědčené postupy jsou založené na shodě na základě konsensu a pracují s funkcemi a sadami funkcí platformy Azure. Názory a technologie se v průběhu času mění a tento článek se pravidelně aktualizuje, aby odrážel tyto změny.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definování a nasazení silných provozních postupů zabezpečení
Provozní zabezpečení Azure odkazuje na služby, ovládací prvky a funkce dostupné uživatelům pro ochranu svých dat, aplikací a dalších prostředků v Azure. Provozní zabezpečení Azure je postavené na platformě, která zahrnuje znalostní báze získané prostřednictvím možností, které jsou jedinečné pro společnost Microsoft, včetně služby [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl), [centra Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) . program a důkladné povědomí o kyberbezpečnosti hrozbách.

## <a name="manage-and-monitor-user-passwords"></a>Správa a sledování hesel uživatelů
V následující tabulce jsou uvedeny některé osvědčené postupy související se správou hesel uživatelů:

**Osvědčené postupy**: Ujistěte se, že máte správnou úroveň ochrany heslem v cloudu.   
**Podrobnosti**: Postupujte podle pokynů v [návodu k heslu Microsoftu](https://www.microsoft.com/research/publication/password-guidance/), který je vymezený pro uživatele platformy Microsoft Identity platform (Azure Active Directory, Active Directory a účet Microsoft).

**Osvědčené postupy**: Monitorujte podezřelé akce související s vašimi uživatelskými účty.   
**Podrobnosti**: Monitorujte [uživatele v](/azure/active-directory/reports-monitoring/concept-user-at-risk) případě rizikových a [rizikových přihlášení](../../active-directory/reports-monitoring/concept-risk-events.md) pomocí sestav zabezpečení Azure AD.

**Osvědčené postupy**: Automatické zjišťování a náprava hesel s vysokým rizikem   
**Podrobnosti**: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) je funkce edice Azure AD Premium P2, která umožňuje:

- Zjištění potenciálních ohrožení zabezpečení, která ovlivňují identity vaší organizace
- Konfigurace automatizovaných odpovědí na zjištěné podezřelé akce, které se vztahují k identitám vaší organizace
- Prozkoumejte podezřelé incidenty a proveďte příslušné kroky k jejich vyřešení

## <a name="receive-incident-notifications-from-microsoft"></a>Přijímání oznámení o incidentech od Microsoftu
Ujistěte se, že váš tým provozu zabezpečení přijímá oznámení o incidentech Azure od Microsoftu. Oznámení o incidentu umožňuje vašemu bezpečnostnímu týmu znát, že máte ohrožené prostředky Azure, aby mohli rychle reagovat na potenciální bezpečnostní rizika a opravit je.

Na portálu pro registraci Azure můžete zajistit, aby kontaktní údaje správce zahrnovaly podrobnosti, které upozorňují na operace zabezpečení. Kontaktní informace jsou e-mailová adresa a telefonní číslo.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Uspořádání předplatných Azure do skupin pro správu
Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tyto odběry. [Skupiny pro správu Azure](/azure/governance/management-groups/create) poskytují úroveň rozsahu, který je nad rámec předplatných. Své odběry uspořádáte do kontejnerů označovaných jako skupiny pro správu a podmínky zásad správného řízení se vztahují na skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Můžete vytvořit flexibilní strukturu skupin pro správu a odběrů do adresáře. Každému adresáři je přiřazena jedna skupina pro správu nejvyšší úrovně s názvem kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Kořenová skupina pro správu umožňuje použití globálních zásad a přiřazení RBAC na úrovni adresáře.

Zde jsou některé osvědčené postupy pro používání skupin pro správu:

**Osvědčené postupy**: Zajistěte, aby nová předplatná v průběhu přidávání použili prvky zásad správného řízení, jako jsou zásady a oprávnění.   
**Podrobnosti**: Pomocí kořenové skupiny pro správu můžete přiřadit bezpečnostní prvky pro celou organizaci, které se vztahují na všechny prostředky Azure. Zásady a oprávnění jsou příklady prvků.

**Osvědčené postupy**: Zarovnejte nejvyšší úrovně skupin pro správu pomocí strategie segmentace a poskytněte tak bod pro konzistenci řízení a zásad v rámci jednotlivých segmentů.   
**Podrobnosti**: Vytvořte jednu skupinu pro správu pro každý segment pod kořenovou skupinou pro správu. V kořenovém adresáři nevytvářejte žádné jiné skupiny pro správu.

**Osvědčené postupy**: Omezte hloubku skupiny pro správu, aby nedocházelo k záměně, která brání operacím i zabezpečení.   
**Podrobnosti**: Omezte hierarchii na tři úrovně včetně kořene.

**Osvědčené postupy**: Pečlivě vyberte, které položky se mají použít pro celou organizaci s kořenovou skupinou pro správu.   
**Podrobnosti**: Ujistěte se, že elementy kořenové skupiny pro správu musí být v každém prostředku aplikovány na jasné a že mají nízký dopad.

Mezi vhodné kandidáty patří:

- Zákonné požadavky, které mají jasný dopad na firmu (například omezení související s suverenitou dat)
- Požadavky s téměř nulovým možným negativním dopadem na operace, jako jsou zásady s účinkem auditu nebo přiřazení oprávnění RBAC, které byly pečlivě zkontrolovány

**Osvědčené postupy**: Předtím, než je použijete (zásady, model RBAC atd.), pečlivě Naplánujte a otestujte všechny změny v rámci celé organizace v kořenové skupině pro správu.   
**Podrobnosti**: Změny v kořenové skupině pro správu můžou mít vliv na všechny prostředky v Azure. I když poskytují účinný způsob, jak zajistit konzistenci napříč podnikem, chyby nebo nesprávné použití můžou negativně ovlivnit produkční operace. Otestujte všechny změny v kořenové skupině pro správu v testovacím prostředí nebo produkčním pilotním projektu.

## <a name="streamline-environment-creation-with-blueprints"></a>Zjednodušení vytváření prostředí pomocí modrotisky
Služba [Azure modrotisky](/azure/governance/blueprints/overview) umožňuje cloudovým architektům a službám centrálních informačních technologií definovat opakující se sadu prostředků Azure, které implementují a vyhovují standardům, vzorům a požadavkům organizace. Plány Azure umožňují vývojovým týmům rychle sestavit a vytvořit nová prostředí se sadou integrovaných komponent a s jistotou, že vytváří tato prostředí v rámci dodržování předpisů organizace.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorování služeb úložiště pro neočekávané změny v chování
Diagnostiku a řešení problémů v distribuované aplikaci hostované v cloudovém prostředí může být složitější než v tradičních prostředích. Aplikace se dají nasadit v infrastruktuře PaaS nebo IaaS, v místním prostředí, na mobilním zařízení nebo v některé kombinaci těchto prostředí. Síťový provoz vaší aplikace může procházet veřejné a privátní sítě a vaše aplikace může používat více technologií úložiště.

Průběžně monitorujte služby úložiště, které vaše aplikace používá pro jakékoli neočekávané změny v chování (například pomalejší doby odezvy). Pomocí protokolování můžete shromažďovat podrobnější data a analyzovat problém podrobněji. Diagnostické informace, které získáte z monitorování a protokolování, pomáhají určit hlavní příčinu problému, ke kterému došlo v aplikaci. Pak můžete problém vyřešit a zjistit, jaké kroky je potřeba opravit.

[Analýza úložiště Azure](../../storage/common/storage-analytics.md) provádí protokolování a poskytuje data metrik pro účet úložiště Azure. Tato data doporučujeme používat k trasování požadavků, analýze trendů využití a diagnostikování problémů s účtem úložiště.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevence, detekce a reakce na hrozby
[Azure Security Center](../../security-center/security-center-intro.md) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně tím, že poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure, pomáhá detekovat hrozby, které by jinak neinformovaly a pracují s různými řešeními zabezpečení.

Úroveň Free Security Center nabízí jenom omezené zabezpečení pro vaše prostředky Azure. Úroveň Standard rozšiřuje tyto možnosti na místní a jiné cloudy. Security Center Standard vám pomůže najít a opravit chyby zabezpečení, použít ovládací prvky přístupu a aplikace k blokování škodlivých aktivit, zjišťovat hrozby pomocí analýz a inteligentních funkcí a rychle reagovat při útoku. Službu Security Center v cenové úrovni Standard si můžete zdarma vyzkoušet na 60 dní. Doporučujeme [upgradovat předplatné Azure na Security Center Standard](../../security-center/security-center-get-started.md).

K získání centrálního zobrazení stavu zabezpečení všech vašich prostředků Azure použijte Security Center. Na první pohled ověřte, jestli jsou správně nastavené správné kontrolní mechanismy zabezpečení, a rychle identifikujte všechny prostředky, které vyžadují pozornost.

Security Center se taky integruje s [pokročilou ochranou před internetovými útoky v programu Windows Defender](../../security-center/security-center-wdatp.md), která poskytuje komplexní možnosti zjišťování koncových bodů a odezvy (EDR). Díky integraci ochrany ATP v programu Windows Defender můžete obdržíte nezvyklosti. Můžete také detekovat a reagovat na pokročilé útoky na koncových bodech serveru monitorovaných Security Center.

Skoro všechny podnikové organizace mají systém SIEM (Security Information and Event Management), který usnadňuje identifikaci vznikajících hrozeb tím, že konsoliduje informace protokolu z různých zařízení pro shromáždění signálu. Protokoly se pak analyzují pomocí systému pro analýzu dat, který vám usnadní identifikaci "zajímavého" z hluku, který je ve všech řešeních pro shromáždění protokolů a analytická řešení nevyhnutelný.

[Azure Sentinel](/azure/sentinel/overview) je škálovatelné, cloudové NATIVNÍ, Siem (Security and Event Management) a automatizované řešení pro orchestraci zabezpečení (společnosti). Služba Azure Sentinel poskytuje inteligentní analýzu zabezpečení a analýzy hrozeb prostřednictvím zjišťování výstrah, viditelnosti hrozeb, proaktivní lov a automatizované reakce na hrozby.

Zde jsou některé osvědčené postupy pro prevenci, detekci a reakci na hrozby:

**Osvědčené postupy**: Zvyšte rychlost a škálovatelnost svého řešení SIEM pomocí cloudového SIEMu.   
**Podrobnosti**: Prozkoumejte funkce a možnosti [Azure Sentinel](/azure/sentinel/overview) a porovnejte je s funkcemi, které aktuálně používáte místně. Pokud splňuje požadavky vaší organizace na SIEM, zvažte přijetí Azure Sentinel.

**Osvědčené postupy**: Najděte nejzávažnější slabá místa zabezpečení, abyste mohli určit prioritu šetření.   
**Podrobnosti**: Projděte si své [zabezpečené skóre Azure](../../security-center/security-center-secure-score.md) a podívejte se na doporučení vyplývající ze zásad Azure a iniciativ integrovaných v Azure Security Center. Tato doporučení vám pomůžou vyřešit nejdůležitější rizika, jako jsou aktualizace zabezpečení, Endpoint Protection, šifrování, konfigurace zabezpečení, chybějící WAF, virtuální počítače připojené k Internetu a spousta dalších.

Bezpečné skóre, které je založeno na ovládacích prvcích Center for Internet Security (CIS), umožňuje nastavit srovnávací testy zabezpečení Azure v rámci organizace proti externím zdrojům. Externí ověřování pomáhá ověřit a rozšířit strategii zabezpečení vašeho týmu.

**Osvědčené postupy**: Monitorujte stav zabezpečení počítačů, sítí, úložišť a datových služeb a aplikací pro zjišťování a stanovení priorit potenciálních problémů se zabezpečením.  
**Podrobnosti**: Postupujte podle [doporučení pro zabezpečení](../../security-center/security-center-recommendations.md) v Security Center začínajících položkami s nejvyšší prioritou.

**Osvědčené postupy**: Integrujte výstrahy Security Center do řešení Security Information and Event Management (SIEM).   
**Podrobnosti**: Většina organizací s SIEM ji používá jako centrální službu Clearinghouse pro výstrahy zabezpečení, které vyžadují reakci analytika. Zpracované události vytvořené pomocí Security Center jsou publikovány v protokolu aktivit Azure, což je jeden z protokolů, který je k dispozici prostřednictvím Azure Monitor. Azure Monitor nabízí konsolidovaný kanál pro směrování všech dat monitorování do nástroje SIEM. Pokyny najdete v tématu věnovaném [integraci řešení zabezpečení v Security Center](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) . Pokud používáte Azure Sentinel, přečtěte si téma [připojení Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Osvědčené postupy**: Integrujte protokoly Azure s SIEM.   
**Podrobnosti**: Pomocí [Azure monitor můžete shromažďovat a exportovat data](/azure/azure-monitor/overview#integrate-and-export-data). Tento postup je nezbytný pro povolení vyšetřování incidentů zabezpečení a uchování online protokolů je omezené. Pokud používáte Azure Sentinel, přečtěte si téma [připojení zdrojů dat](../../sentinel/connect-data-sources.md).

**Osvědčené postupy**: Integrací možností zjišťování koncových bodů a odpovědí (EDR) do šetření útoku urychlíte šetření a lovecké procesy a omezíte falešně pozitivní výsledky.   
**Podrobnosti**: [Povolte integraci ochrany ATP v programu Windows Defender](../../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) prostřednictvím zásad zabezpečení Security Center. Zvažte použití Azure Sentinel pro lov hrozeb a reakci na incidenty.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorování sítě v komplexním scénáři
Zákazníci vytvářejí komplexní síť v Azure kombinací síťových prostředků, jako jsou virtuální síť, ExpressRoute, Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředcích.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba. Pomocí svých nástrojů pro diagnostiku a vizualizaci můžete monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure.

Níže jsou uvedené osvědčené postupy pro monitorování sítě a dostupné nástroje.

**Osvědčené postupy**: Automatizujte vzdálené monitorování sítě pomocí zachycení paketů.  
**Podrobnosti**: Pomocí Network Watcher můžete monitorovat a diagnostikovat problémy se sítí bez přihlášení k virtuálním počítačům. Aktivovat [zachytávání paketů](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) nastavením upozornění a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Když narazíte na problém, můžete ho prozkoumat podrobněji a lépe diagnostikovat.

**Osvědčené postupy**: Pomocí protokolů toků získáte přehled o provozu v síti.  
**Podrobnosti**: Pomocí [protokolů toků skupin zabezpečení sítě](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)sestavíme hlubší porozumění vzorům síťových přenosů. Informace v protokolech Flow pomáhají shromažďovat data týkající se dodržování předpisů, auditování a monitorování profilu zabezpečení sítě.

**Osvědčené postupy**: Diagnostikujte problémy s připojením VPN.  
**Podrobnosti**: Pomocí Network Watcher můžete [diagnostikovat nejběžnější VPN Gateway a problémy s připojením](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Nemůžete jenom identifikovat problém, ale k dalšímu zkoumání použít taky podrobné protokoly.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Zabezpečené nasazení pomocí prověřených DevOps nástrojů
Použijte následující osvědčené postupy DevOps, abyste měli jistotu, že vaše organizace a týmy budou produktivní a efektivní.

**Osvědčené postupy**: Automatizujte sestavování a nasazování služeb.  
**Podrobnosti**: [Infrastruktura jako kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) je sada technik a postupů, které odborníkům v oblasti IT umožňují odebrat zatížení pro každodenní sestavování a správu modulární infrastruktury. Umožňuje IT specialistům vytvářet a udržovat moderní serverové prostředí způsobem, který je podobný způsobu, jakým vývojáři softwaru sestavují a udržují kód aplikace.

Můžete použít [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) ke zřízení aplikací pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu použijete k opakovanému nasazení aplikace v každé fázi životního cyklu aplikace.

**Osvědčené postupy**: Automatické sestavování a nasazování do Azure Web Apps nebo Cloud Services.  
**Podrobnosti**: Azure DevOps Projects můžete nakonfigurovat tak, aby se [automaticky vytvořily a](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) nasadily do Azure Web Apps nebo Cloud Services. Azure DevOps po každém vrácení se změnami kódu automaticky nasadí binární soubory po sestavení do Azure. Proces sestavení balíčku je ekvivalentní příkazu Package v aplikaci Visual Studio a kroky publikování jsou ekvivalentní příkazu Publikovat v aplikaci Visual Studio.

**Osvědčené postupy**: Automatizujte správu vydaných verzí.  
**Podrobnosti**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) je řešení pro automatizaci nasazení s více fázemi a správu procesu vydávání verzí. Vytvářejte spravované kanály průběžného nasazování k rychlému, snadnému a často vydaným verzím. Pomocí Azure Pipelines můžete automatizovat proces vydávání verzí a můžete mít předdefinované schvalovací pracovní postupy. Nasaďte místně a do cloudu, podle potřeby rozšiřujete a přizpůsobte.

**Osvědčené postupy**: Před spuštěním nebo nasazením aktualizací do produkčního prostředí si prohlédněte výkon vaší aplikace.  
**Podrobnosti**: Spusťte cloudové [zátěžové testy](/azure/devops/test/load-test/overview#alternatives) na:

- Najděte problémy s výkonem v aplikaci.
- Vylepšete kvalitu nasazení.
- Ujistěte se, že je vaše aplikace vždycky k dispozici.
- Ujistěte se, že vaše aplikace dokáže zpracovat provoz pro příští spuštění nebo marketingovou kampaň.

[Apache JMeter](https://jmeter.apache.org/) je bezplatný oblíbený otevřený zdrojový nástroj se silným komunitním zálohováním.

**Osvědčené postupy**: Monitorovat výkon aplikace.  
**Podrobnosti**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba pro správu výkonu aplikací (APM) pro webové vývojáře na různých platformách. Pomocí Application Insights můžete monitorovat živou webovou aplikaci. Automaticky detekuje anomálie výkonu. Obsahuje analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

## <a name="mitigate-and-protect-against-ddos"></a>Zmírnění a ochrana před DDoS
Distributed Denial of Service (DDoS) je typ útoku, který se pokusí o vyčerpání prostředků aplikace. Cílem je ovlivnit dostupnost aplikace a její schopnost zpracovávat legitimní požadavky. Tyto útoky se budou lépe měnit a mají větší vliv. Můžou být cílené na libovolný koncový bod, který je veřejně dosažitelný prostřednictvím Internetu.

Návrh a sestavování pro odolnost DDoS vyžaduje plánování a návrh pro nejrůznější režimy selhání. Níže jsou uvedené osvědčené postupy pro sestavování DDoS odolných služeb v Azure.

**Osvědčené postupy**: Zajistěte, aby zabezpečení bylo prioritní v celém životním cyklu aplikace, od návrhu a implementace po nasazení a provoz. Aplikace mohou mít chyby, které umožňují relativně nízké množství žádostí o použití velkého množství prostředků, což vede k výpadku služby.  
**Podrobnosti**: Aby byla zajištěna ochrana služby spuštěné v Microsoft Azure, měli byste mít dobrou představu o architektuře vaší aplikace a soustředit se na [pět pilířů kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars). Měli byste znát typické objemy přenosů dat, model připojení mezi aplikací a dalšími aplikacemi a koncové body služby, které jsou zpřístupněny veřejnému Internetu.

Důležité je zajistit, aby byla aplikace dostatečně odolná pro zpracování odepření služby, která je zaměřená na samotnou aplikaci, je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované v platformě Azure, počínaje nástrojem [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl). SDL řeší zabezpečení v každé vývojové fázi a zajišťuje průběžnou aktualizaci Azure, aby bylo ještě bezpečnější.

**Osvědčené postupy**: Navrhněte své aplikace pro [horizontální škálování](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) tak, aby splňovaly požadavky na zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud vaše aplikace závisí na jedné instanci služby, vytvoří se v jednom bodě selhání. Zřizování více instancí zajistí, že bude váš systém pružnější a lépe škálovatelný.  
**Podrobnosti**: V případě [Azure App Service](/azure/app-service/app-service-value-prop-what-is)vyberte [plán App Service](../../app-service/overview-hosting-plans.md) , který nabízí více instancí.

Pro Azure Cloud Services nakonfigurujte jednotlivé role tak, aby používaly [víc instancí](../../cloud-services/cloud-services-choose-me.md).

V případě [Azure Virtual Machines](/azure/virtual-machines/windows/overview)zajistěte, aby vaše architektura virtuálních počítačů zahrnovala více než jeden virtuální počítač a aby byl každý virtuální počítač součástí [skupiny dostupnosti](/azure/virtual-machines/virtual-machines-windows-manage-availability). Pro možnosti automatického škálování doporučujeme používat Virtual Machine Scale Sets.

**Osvědčené postupy**: Vrstvení zabezpečení v aplikaci omezuje možnost úspěšného útoku. Implementujte zabezpečené návrhy pro vaše aplikace pomocí integrovaných možností platformy Azure.  
**Podrobnosti**: Riziko útoku se zvyšuje s velikostí (oblastí Surface) aplikace. Oblast Surface můžete snížit pomocí seznamu povolených adres, abyste zavřeli vystavený adresní prostor IP adres a naslouchající porty, které nejsou potřeba na nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) a [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)).

[Skupiny zabezpečení sítě](../../virtual-network/security-overview.md) představují jiný způsob, jak omezit plochu pro útok. [Značky služeb](../../virtual-network/security-overview.md#service-tags) a [skupiny zabezpečení aplikací](../../virtual-network/security-overview.md#application-security-groups) můžete použít k minimalizaci složitosti při vytváření pravidel zabezpečení a konfiguraci zabezpečení sítě, jako přirozené rozšíření struktury aplikace.

Služby Azure ve [virtuální síti](../../virtual-network/virtual-networks-overview.md) byste měli nasazovat, kdykoli to bude možné. Tento postup umožňuje prostředkům služby komunikovat prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě ve výchozím nastavení používá veřejné IP adresy jako zdrojové IP adresy.

Použití [koncových bodů služby](../../virtual-network/virtual-network-service-endpoints-overview.md) přepne provoz služby na používání privátních adres virtuální sítě jako zdrojových IP adres při přístupu ke službě Azure z virtuální sítě.

Často se v Azure zobrazují místní prostředky zákazníků, které získávají útok na jejich prostředky. Pokud připojujete místní prostředí k Azure, minimalizujete tím expozici místních prostředků veřejnému Internetu.

Azure má dvě [nabídky služeb](../../virtual-network/ddos-protection-overview.md) DDoS, které poskytují ochranu před síťovými útoky:

- Základní ochrana je ve výchozím nastavení integrovaná do Azure bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure zajišťuje obranu proti běžným útokům na síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. Základní nevyžaduje žádné změny konfigurace nebo aplikace uživatele a pomáhá chránit všechny služby Azure, včetně služeb PaaS Services, jako je Azure DNS.
- Úroveň Standard Protection poskytuje pokročilé funkce zmírnění DDoS proti síťovým útokům. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure. Při vytváření virtuálních sítí se tato ochrana snadno povoluje. Lze ji také provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředků.

## <a name="enable-azure-policy"></a>Povolit Azure Policy
[Azure Policy](/azure/governance/policy/overview) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vysazují pravidla a účinky na vaše prostředky, takže tyto prostředky budou vyhovovat vašim firemním standardům a smlouvám o úrovni služeb. Služba Azure Policy splňuje tyto potřeby vyhodnocením prostředky nedodržení předpisů se zásadami přiřazený.

Povolí Azure Policy monitorování a prosazování napsaných zásad vaší organizace. Tím se zajistí dodržování požadavků vaší společnosti nebo regulativního zabezpečení prostřednictvím centrální správy zásad zabezpečení napříč vašimi úlohami hybridního cloudu. Naučte se [vytvářet a spravovat zásady pro prosazování dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Přehled prvků zásad najdete v tématu [Struktura definice Azure Policy](../../governance/policy/concepts/definition-structure.md) .

Tady jsou některé osvědčené postupy zabezpečení, které je potřeba provést po Azure Policy:

**Osvědčené postupy**: Zásady podporují několik typů efektů. Můžete si je přečíst v tématu [Struktura definice Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). Obchodní operace mohou negativně ovlivnit efekt **odmítnutí** a nápravu, takže zahájíte efekt **auditu** a omezíte riziko negativního dopadu zásad.   
**Podrobnosti**: [Spusťte nasazení zásad v režimu auditování](../../governance/policy/concepts/definition-structure.md#policy-rule) a později proveďte postup zamítnutí nebo **nápravy**. Než přejdete na **Odepřít** nebo **opravit**, otestujte výsledky auditu a ověřte je.

Další informace najdete v tématu [Vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md).

**Osvědčené postupy**: Identifikujte role zodpovědné za monitorování porušení zásad a zajistěte, aby byla akce správné nápravy provedena rychle.   
**Podrobnosti**: Má přiřazená role sledovat dodržování předpisů prostřednictvím [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) nebo prostřednictvím [příkazového řádku](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Osvědčené postupy**: Azure Policy je technická reprezentace písemných zásad organizace. Namapujte všechny zásady Azure na zásady organizace, abyste snížili nejasnost a zvýšili konzistenci.   
**Podrobnosti**: Mapování dokumentů v dokumentaci vaší organizace nebo v samotné zásadě Azure přidáním odkazu na zásadu organizace v [popisu zásad](../../governance/policy/concepts/definition-structure.md#display-name-and-description) Azure nebo v popisu [iniciativy](../../governance/policy/concepts/definition-structure.md#initiatives) Azure Policy.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorování sestav rizik Azure AD
Pokud útočníci získají přístup k prostředí tím, že ukrást identitu uživatele, dojde k převážné většině narušení zabezpečení. Zjišťování ohrožených identit není jednoduché. Azure AD pomocí adaptivních algoritmů strojového učení a heuristiky detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každá zjištěná podezřelá akce je uložená v záznamu s názvem [riziková událost](../../active-directory/reports-monitoring/concept-risk-events.md). Rizikové události se zaznamenávají v sestavách zabezpečení Azure AD. Další informace najdete v sestavě zabezpečení [rizikové uživatele](../../active-directory/reports-monitoring/concept-user-at-risk.md) a [zabezpečení rizikových přihlášení](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Další postup
V článku [osvědčené postupy a vzory zabezpečení Azure](best-practices-and-patterns.md) najdete v tématu Doporučené postupy zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu pro zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější verzi v zabezpečení Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde můžou být chyby zabezpečení Microsoftu, včetně problémů s Azure, nahlášené nebo prostřednictvím e-mailu.secure@microsoft.com
