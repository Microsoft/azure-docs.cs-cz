---
title: Doporučené postupy zabezpečení pro vaše datové zdroje Azure
titleSuffix: Azure security
description: Tento článek obsahuje sadu provozních doporučených postupů pro ochranu dat, aplikací a dalších prostředků v Azure.
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
ms.openlocfilehash: 5724a9e4308f05a82df84ae6a7d5602747f5a140
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757374"
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy pro provozní zabezpečení Azure
Tento článek obsahuje sadu provozních doporučených postupů pro ochranu dat, aplikací a dalších prostředků v Azure.

Osvědčené postupy jsou založeny na konsensu názoru a pracují s aktuálními funkcemi platformy Azure a sadami funkcí. Názory a technologie se v průběhu času mění a tento článek je pravidelně aktualizován, aby tyto změny odrážel.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definování a nasazení silných postupů provozního zabezpečení
Provozní zabezpečení Azure označuje služby, ovládací prvky a funkce, které jsou uživatelům k dispozici pro ochranu jejich dat, aplikací a dalších prostředků v Azure. Provozní zabezpečení Azure je integrované na architektu, který zahrnuje znalosti získané prostřednictvím funkcí, které jsou jedinečné pro Microsoft, včetně [životního cyklu vývoje zabezpečení (SDL),](https://www.microsoft.com/sdl)programu Microsoft Security Response [Center](https://www.microsoft.com/msrc?rtc=1) a hlubokého povědomí o prostředí kybernetických hrozeb.

## <a name="manage-and-monitor-user-passwords"></a>Správa a sledování uživatelských hesel
V následující tabulce jsou uvedeny některé doporučené postupy týkající se správy uživatelských hesel:

**Osvědčený postup**: Ujistěte se, že máte správnou úroveň ochrany heslem v cloudu.   
**Podrobnosti**: Postupujte podle pokynů v [pokynech k heslem společnosti Microsoft](https://www.microsoft.com/research/publication/password-guidance/), které jsou vymezeny pro uživatele platforem identit Microsoftu (Azure Active Directory, Active Directory a účet Microsoft).

**Osvědčený postup:** Sledujte podezřelé akce související s vašimi uživatelskými účty.   
**Podrobnosti**: Monitorování [pro uživatele v ohrožení](/azure/active-directory/reports-monitoring/concept-user-at-risk) a riskantní [přihlášení](../../active-directory/reports-monitoring/concept-risk-events.md) pomocí sestav zabezpečení Azure AD.

**Osvědčený postup**: Automaticky rozpoznáa a naváděna vysoce riziková hesla.   
**Podrobnosti:** [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) je funkce edice Azure AD Premium P2, která umožňuje:

- Zjištění potenciálních chyb zabezpečení, které ovlivňují identity vaší organizace
- Konfigurace automatických odpovědí na zjištěné podezřelé akce, které souvisejí s identitami vaší organizace
- Vyšetřit podezřelé incidenty a přijmout vhodná opatření k jejich vyřešení

## <a name="receive-incident-notifications-from-microsoft"></a>Příjem oznámení o incidentech od společnosti Microsoft
Ujistěte se, že váš tým operací zabezpečení obdrží oznámení o incidentech Azure od Microsoftu. Oznámení o incidentu umožňuje vašemu týmu zabezpečení vědět, že jste ohrozili prostředky Azure, aby mohli rychle reagovat na potenciální bezpečnostní rizika a napravit je.

Na portálu registrace Azure můžete zajistit, aby kontaktní informace správce obsahují podrobnosti, které upozorňují na operace zabezpečení. Kontaktní údaje jsou e-mailová adresa a telefonní číslo.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Uspořádání předplatných Azure do skupin pro správu
Pokud má vaše organizace mnoho předplatných, můžete potřebovat způsob, jak efektivně u těchto předplatných spravovat přístup, zásady a dodržování předpisů. [Skupiny pro správu Azure](/azure/governance/management-groups/create) poskytují úroveň oboru, která je nad předplatnými. Můžete uspořádat odběry do kontejnerů s názvem skupiny pro správu a použít podmínky zásad správného řízení pro skupiny pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Do adresáře můžete vytvořit flexibilní strukturu skupin pro správu a předplatných. Každému adresáři je přidělena jedna skupina pro správu nejvyšší úrovně nazvaná kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Kořenová skupina pro správu umožňuje použití globálních zásad a přiřazení RBAC na úrovni adresáře.

Tady jsou některé doporučené postupy pro používání skupin pro správu:

**Osvědčený postup**: Ujistěte se, že nová předplatná použít prvky zásad správného řízení, jako jsou zásady a oprávnění, jak jsou přidány.   
**Podrobnosti**: Pomocí kořenové skupiny pro správu můžete přiřadit prvky zabezpečení pro celou řadu pro celou řadu, které se vztahují na všechny prostředky Azure. Zásady a oprávnění jsou příklady prvků.

**Osvědčený postup:** Sladění nejvyšších úrovní skupin pro správu se strategií segmentace, aby byl zajištěn bod pro řízení a konzistenci zásad v rámci každého segmentu.   
**Podrobnosti**: Vytvořte jednu skupinu pro správu pro každý segment v rámci kořenové skupiny pro správu. Nevytvářejte žádné jiné skupiny pro správu pod kořenem.

**Osvědčený postup**: Omezte hloubku skupiny pro správu, abyste předešli nejasnostem, které brání operacím i zabezpečení.   
**Detail**: Omezte hierarchii na tři úrovně, včetně kořenového adresáře.

**Osvědčený postup**: Pečlivě vyberte položky, které chcete použít pro celý podnik s kořenovou skupinou pro správu.   
**Podrobnosti**: Ujistěte se, že prvky skupiny pro správu kořenové správy mají jasnou potřebu použít ve všech prostředcích a že mají nízký dopad.

Mezi dobré kandidáty patří:

- Regulační požadavky, které mají jasný dopad na podnikání (například omezení související se suverenitou dat)
- Požadavky s téměř nulovým potenciálním negativním dopadem na operace, jako jsou zásady s auditním účinkem nebo přiřazení oprávnění RBAC, které byly pečlivě přezkoumány

**Osvědčený postup**: Pečlivě naplánujte a otestujte všechny změny v rámci celé ho organizace v kořenové skupině správy před jejich použitím (zásada, model RBAC a tak dále).   
**Podrobnosti**: Změny ve skupině pro správu kořenové správy může ovlivnit všechny prostředky v Azure. I když poskytují účinný způsob, jak zajistit konzistenci v rámci celého podniku, chyby nebo nesprávné použití mohou negativně ovlivnit výrobní operace. Otestujte všechny změny kořenové skupiny pro správu v testovací laboratoři nebo produkčním pilotním projektu.

## <a name="streamline-environment-creation-with-blueprints"></a>Zjednodušte vytváření prostředí pomocí podrobných plánů
Služba [Azure Blueprints](/azure/governance/blueprints/overview) umožňuje cloudovým architektům a centrálním skupinám informačních technologií definovat opakovatelnou sadu prostředků Azure, která implementuje a dodržuje standardy, vzory a požadavky organizace. Azure Blueprints umožňuje vývojovým týmům rychle vytvářet a obsazovat nová prostředí pomocí sady integrovaných komponent a jistoty, že tato prostředí vytvářejí v rámci dodržování předpisů organizace.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Sledování neočekávaných změn v chování
Diagnostika a řešení problémů v distribuované aplikaci hostované v cloudovém prostředí může být složitější než v tradičních prostředích. Aplikace lze nasadit v infrastruktuře PaaS nebo IaaS, místně, na mobilním zařízení nebo v nějaké kombinaci těchto prostředí. Síťový provoz vaší aplikace může procházet veřejnými a soukromými sítěmi a aplikace může používat více technologií úložiště.

Měli byste průběžně sledovat služby úložiště, které vaše aplikace používá pro všechny neočekávané změny v chování (například pomalejší doby odezvy). Protokolování slouží ke shromažďování podrobnějších dat a k analýze problému do hloubky. Diagnostické informace, které získáte z monitorování a protokolování vám pomůže určit hlavní příčinu problému, který vaše aplikace došlo. Potom můžete problém vyřešit a určit příslušné kroky k jeho nápravě.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) provádí protokolování a poskytuje data metrik pro účet úložiště Azure. Doporučujeme použít tato data k trasování požadavků, analýze trendů využití a diagnostice problémů s účtem úložiště.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevence, detekce a reakce na hrozby
[Azure Security Center](../../security-center/security-center-intro.md) vám pomůže předcházet, zjišťovat a reagovat na hrozby tím, že poskytuje lepší přehled o (a kontrolu nad) zabezpečení prostředků Azure. Poskytuje integrované monitorování zabezpečení a správu zásad v rámci vašich předplatných Azure, pomáhá detekovat hrozby, které by jinak mohly zůstat bez povšimnutí, a pracuje s různými řešeními zabezpečení.

Úroveň Free centra zabezpečení nabízí omezené zabezpečení pouze pro vaše prostředky Azure. Úroveň Standard rozšiřuje tyto možnosti na místní a další cloudy. Security Center Standard vám pomůže najít a opravit slabá místa zabezpečení, použít ovládací prvky přístupu a aplikací k blokování škodlivých aktivit, detekovat hrozby pomocí analýzy a inteligence a rychle reagovat při útoku. Službu Security Center v cenové úrovni Standard si můžete zdarma vyzkoušet na 60 dní. Doporučujeme [upgradovat předplatné Azure na Standard Security Center](../../security-center/security-center-get-started.md).

Pomocí Centra zabezpečení můžete získat centrální zobrazení stavu zabezpečení všech prostředků Azure. Na první pohled ověřte, zda jsou příslušné ovládací prvky zabezpečení na místě a správně nakonfigurovány, a rychle identifikujte všechny prostředky, které vyžadují pozornost.

Security Center se také integruje s [programem Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)který poskytuje komplexní funkce detekce koncových bodů a odezvy (EDR). Díky integraci ochrany ATP v programu Microsoft Defender můžete rozpoznat abnormality. Můžete také zjistit a reagovat na pokročilé útoky na koncové body serveru monitorované Security Center.

Téměř všechny podnikové organizace mají systém správy informací o zabezpečení a událostí (SIEM), který pomáhá identifikovat vznikající hrozby konsolidací informací protokolu z různých zařízení pro shromažďování signálů. Protokoly jsou pak analyzovány systémem analýzy dat, který pomáhá identifikovat, co je "zajímavé" z šumu, který je nevyhnutelný ve všech řešeních shromažďování protokolů a analytických řešeních.

[Azure Sentinel](/azure/sentinel/overview) je škálovatelné cloudové nativní řešení pro správu informací a událostí (SIEM) a automatické odpovědi orchestrace zabezpečení (SOAR). Azure Sentinel poskytuje inteligentní analýzy zabezpečení a analýzy hrozeb prostřednictvím detekce výstrah, viditelnosti hrozeb, proaktivního lovu a automatizované reakce na hrozby.

Zde jsou některé osvědčené postupy pro prevenci, detekci a reakci na hrozby:

**Osvědčený postup:** Zvyšte rychlost a škálovatelnost řešení SIEM pomocí cloudového SIEM.   
**Podrobnosti**: Prozkoumejte funkce a možnosti [Azure Sentinelu](/azure/sentinel/overview) a porovnejte je s možnostmi toho, co aktuálně používáte místně. Zvažte přijetí Azure Sentinelu, pokud splňuje požadavky SIEM vaší organizace.

**Osvědčený postup**: Najděte nejzávažnější chyby zabezpečení, abyste mohli upřednostnit vyšetřování.   
**Podrobnosti**: Zkontrolujte [zabezpečené skóre Azure](../../security-center/secure-score-security-controls.md) a podívejte se na doporučení vyplývající z zásad a iniciativ Azure integrovaných v Azure Security Center. Tato doporučení pomáhají řešit hlavní rizika, jako jsou aktualizace zabezpečení, ochrana koncových bodů, šifrování, konfigurace zabezpečení, chybějící WAF, virtuální počítače připojené k internetu a mnoho dalších.

Zabezpečené skóre, které je založeno na ovládacích prvcích Centra pro zabezpečení Internetu (CIS), umožňuje porovnat zabezpečení Azure vaší organizace s externími zdroji. Externí ověření pomáhá ověřit a obohatit strategii zabezpečení vašeho týmu.

**Osvědčený postup**: Sledujte stav zabezpečení počítačů, sítí, služeb úložiště a datových služeb a aplikací za účelem zjištění a stanovení priority potenciálních problémů se zabezpečením.  
**Podrobnosti**: Postupujte podle [bezpečnostních doporučení](../../security-center/security-center-recommendations.md) v centru zabezpečení počínaje, s nejvyšší prioritou položek.

**Osvědčený postup:** Integrace výstrah Centra zabezpečení do řešení pro správu informací o zabezpečení a událostí (SIEM).   
**Podrobnosti**: Většina organizací se siem použít jako centrální clearinghouse pro výstrahy zabezpečení, které vyžadují odpověď analytika. Zpracované události vytvořené Security Center jsou publikovány do protokolu aktivit Azure, jeden z protokolů dostupných prostřednictvím Azure Monitor. Azure Monitor nabízí konsolidovaný kanál pro směrování libovolného data monitorování do nástroje SIEM. Pokyny naleznete [v tématu Výstrahy zabezpečení exportu a doporučení.](../../security-center/continuous-export.md#configuring-siem-integration-via-azure-event-hubs) Pokud používáte Azure Sentinel, přečtěte [si informace o připojení Centra zabezpečení Azure](../../sentinel/connect-azure-security-center.md).

**Osvědčený postup:** Integrace protokolů Azure s siem.   
**Detail**: Pomocí [Azure Monitoru shromažďujte a exportujte data](/azure/azure-monitor/overview#integrate-and-export-data). Tento postup je rozhodující pro povolení vyšetřování incidentů zabezpečení a uchovávání protokolu online je omezené. Pokud používáte Azure Sentinel, přečtěte si informace [o připojení zdrojů dat](../../sentinel/connect-data-sources.md).

**Osvědčený postup**: Urychlete procesy vyšetřování a lovu a snižte falešně pozitivní výsledky integrací funkcí detekce a odezvy koncového bodu (EDR) do vašeho vyšetřování útoku.   
**Podrobnosti**: [Povolte integraci ochrany ATP programu Microsoft Defender](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) prostřednictvím zásad zabezpečení Centra zabezpečení. Zvažte použití Azure Sentinelu pro hledání hrozeb a reakci na incidenty.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitorování monitorování sítě založené na komplexních scénářích
Zákazníci vytvářejí síť v Azure jako end- spojením síťových prostředků, jako je virtuální síť, ExpressRoute, Aplikační brána a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici u každého síťového prostředků.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) je místní služba. Pomocí diagnostických a vizualizačních nástrojů můžete monitorovat a diagnostikovat stavy na úrovni síťového scénáře v Azure, do a z Azure.

Níže jsou uvedeny osvědčené postupy pro monitorování sítě a dostupné nástroje.

**Osvědčený postup:** Automatizace vzdáleného monitorování sítě pomocí zachytávání paketů.  
**Podrobnosti**: Sledování a diagnostika problémů se sítí bez přihlášení k virtuálním počítačům pomocí sledování sítě. Aktivujte [sběr paketů](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) nastavením výstrah a získejte přístup k informacím o výkonu v reálném čase na úrovni paketů. Když uvidíte problém, můžete podrobně prozkoumat pro lepší diagnózy.

**Osvědčený postup**: Získejte přehled o síťovém provozu pomocí protokolů toku.  
**Podrobnosti**: Pomocí [protokolů toku skupiny zabezpečení sítě](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)můžete lépe porozumět vzorcům síťového provozu . Informace v protokolech toku vám pomohou shromažďovat data pro dodržování předpisů, auditování a monitorování profilu zabezpečení sítě.

**Osvědčený postup:** Diagnostikujte problémy s připojením vpn.  
**Podrobnosti**: Pomocí sledovacího programu sítě [můžete diagnostikovat nejběžnější bránu VPN a problémy s připojením](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Můžete nejen identifikovat problém, ale také použít podrobné protokoly k dalšímu prošetření.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Bezpečné nasazení pomocí osvědčených nástrojů DevOps
Pomocí následujících doporučených postupů pro devops zajistíte, že vaše podnikajejí a týmy budou produktivní a efektivní.

**Osvědčený postup**: Automatizace sestavení a nasazení služeb.  
**Detail**: [Infrastruktura jako kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) je sada technik a postupů, které pomáhají IT profesionálům odstranit zátěž každodenního budování a správy modulární infrastruktury. Umožňuje IT profesionálům vytvářet a udržovat jejich moderní serverové prostředí způsobem, který je jako jak vývojáři softwaru vytvářejí a udržují kód aplikace.

[Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) můžete použít k zřízení aplikací pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Stejnou šablonu používáte k opakovanému nasazení aplikace v každé fázi životního cyklu aplikace.

**Osvědčený postup:** Automaticky sestavujte a nasazujte do webových aplikací Azure nebo cloudových služeb.  
**Podrobnosti**: Projekty Azure DevOps můžete nakonfigurovat tak, aby [se automaticky vytvářel a nasazoval](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) do webových aplikací nebo cloudových služeb Azure. Azure DevOps automaticky nasadí binární soubory po provedení sestavení do Azure po každém vrácení kódu. Proces sestavení balíčku je ekvivalentní příkazu Package v sadě Visual Studio a kroky publikování jsou ekvivalentní příkazu Publikovat v sadě Visual Studio.

**Osvědčený postup:** Automatizace správy verzí.  
**Podrobnosti:** [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) je řešení pro automatizaci vícestupňového nasazení a správu procesu vydání. Vytvořte spravované kanály průběžného nasazení, které se budou rychle, snadno a často vydávat. S Azure Pipelines můžete automatizovat proces vydání a můžete mít předdefinované pracovní postupy schvalování. Nasazujte místně a do cloudu, rozšiřujte a přizpůsobujte podle potřeby.

**Osvědčený postup:** Před spuštěním nebo nasazením aktualizací do produkčního prostředí zkontrolujte výkon aplikace.  
**Podrobnosti**: Spusťte cloudové [zátěžové testy](/azure/devops/test/load-test/overview#alternatives) tak, aby:

- Ve své aplikaci najdete problémy s výkonem.
- Zlepšete kvalitu nasazení.
- Ujistěte se, že je vaše aplikace vždy dostupná.
- Ujistěte se, že vaše aplikace zvládne provoz pro další spuštění nebo marketingovou kampaň.

[Apache JMeter](https://jmeter.apache.org/) je bezplatný, populární open source nástroj se silnou podporou komunity.

**Osvědčený postup**: Sledování výkonu aplikace.  
**Podrobnosti:** [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) je rozšiřitelná služba správy výkonu aplikací (APM) pro webové vývojáře na více platformách. Ke sledování živé webové aplikace použijte Application Insights. Automaticky detekuje anomálie výkonu. Obsahuje analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

## <a name="mitigate-and-protect-against-ddos"></a>Zmírnit a chránit před DDoS
Distribuované odmítnutí služby (DDoS) je typ útoku, který se pokouší vyčerpat prostředky aplikace. Cílem je ovlivnit dostupnost aplikace a její schopnost zpracovávat legitimní požadavky. Tyto útoky jsou stále sofistikovanější a větší co do velikosti a dopadu. Mohou být zaměřeny na libovolný koncový bod, který je veřejně dosažitelný prostřednictvím internetu.

Navrhování a vytváření odolnosti ddos vyžaduje plánování a navrhování pro různé režimy selhání. Níže jsou uvedeny doporučené postupy pro vytváření služeb odolných vůči DDoS v Azure.

**Osvědčený postup**: Ujistěte se, že zabezpečení je prioritou po celou dobu životnosti aplikace, od návrhu a implementace až po nasazení a operace. Aplikace mohou mít chyby, které umožňují relativně nízký objem požadavků používat velké množství prostředků, což má za následek výpadku služby.  
**Podrobnosti**: Chcete-li chránit službu spuštěnou v Microsoft Azure, měli byste mít dobré znalosti architektury aplikací a zaměřit se na [pět pilířů kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars). Měli byste znát typické objemy provozu, model připojení mezi aplikací a jinými aplikacemi a koncové body služby, které jsou vystaveny veřejnému internetu.

Zajištění, že aplikace je dostatečně odolná pro zpracování odmítnutí služby, která je zaměřena na samotnou aplikaci je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované do platformy Azure, počínaje [životní cyklus vývoje zabezpečení (SDL)](https://www.microsoft.com/sdl). SDL řeší zabezpečení v každé fázi vývoje a zajišťuje, že Azure je průběžně aktualizován, aby byl ještě bezpečnější.

**Osvědčený postup**: Navrhněte aplikace tak, aby [škálovaly vodorovně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) tak, aby splňovaly požadavky na zesílené zatížení, konkrétně v případě útoku DDoS. Pokud vaše aplikace závisí na jedné instanci služby, vytvoří jeden bod selhání. Zřizování více instancí umožňuje váš systém odolnější a škálovatelnější.  
**Podrobnosti**: Pro [Službu Azure App Service](/azure/app-service/app-service-value-prop-what-is)vyberte [plán služby App Service,](../../app-service/overview-hosting-plans.md) který nabízí více instancí.

V azure cloudových službách nakonfigurujte každou z vašich rolí tak, aby [používala více instancí](../../cloud-services/cloud-services-choose-me.md).

Pro [virtuální počítače Azure](/azure/virtual-machines/windows/overview), ujistěte se, že vaše architektura virtuálních počítačů obsahuje více než jeden virtuální počítač a že každý virtuální počítač je součástí sady [dostupnosti](/azure/virtual-machines/virtual-machines-windows-manage-availability). Doporučujeme používat škálovací sady virtuálních strojů pro možnosti automatického škálování.

**Osvědčený postup**: Vrstvení obrany zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Implementujte zabezpečené návrhy pro vaše aplikace pomocí integrovaných funkcí platformy Azure.  
**Detail**: Riziko útoku se zvyšuje s velikostí (povrchovou plochou) aplikace. Můžete snížit plochu pomocí whitelisting uzavřete exponované IP adresní prostor a naslouchání porty, které nejsou potřeba na vyrovnávání zatížení[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) a [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal)

[Skupiny zabezpečení sítě](../../virtual-network/security-overview.md) jsou dalším způsobem, jak snížit prostor pro útok. [Značky služeb](../../virtual-network/security-overview.md#service-tags) a [skupiny zabezpečení aplikací](../../virtual-network/security-overview.md#application-security-groups) můžete použít k minimalizaci složitosti při vytváření pravidel zabezpečení a konfiguraci zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Služby Azure byste měli nasadit ve [virtuální síti,](../../virtual-network/virtual-networks-overview.md) kdykoli je to možné. Tento postup umožňuje prostředky služby komunikovat prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě ve výchozím nastavení používá veřejné IP adresy jako zdrojové IP adresy.

Použití [koncových bodů služby](../../virtual-network/virtual-network-service-endpoints-overview.md) přepne provoz služby používat privátní adresy virtuální sítě jako zdrojové IP adresy, když přistupují ke službě Azure z virtuální sítě.

Často vidíme místní prostředky zákazníků, které jsou napadeny spolu s jejich prostředky v Azure. Pokud připojujete místní prostředí k Azure, minimalizujte vystavení místních prostředků veřejnému internetu.

Azure má dvě [nabídky služeb](../../virtual-network/ddos-protection-overview.md) DDoS, které poskytují ochranu před síťovými útoky:

- Základní ochrana je ve výchozím nastavení integrovaná do Azure bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure poskytuje ochranu proti běžným útokům na síťové vrstvě prostřednictvím nepřetržitého monitorování provozu a zmírňování stavu v reálném čase. Základní nevyžaduje žádné změny konfigurace uživatele nebo aplikace a pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.
- Standardní ochrana poskytuje pokročilé možnosti zmírnění DDoS proti síťovým útokům. Automaticky se vyladí, aby chránil vaše konkrétní prostředky Azure. Ochrana je jednoduchá při vytváření virtuálních sítí. To lze také provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředků.

## <a name="enable-azure-policy"></a>Povolení zásad Azure
[Azure Policy](/azure/governance/policy/overview) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vynucují pravidla a efekty oproti vašim prostředkům, aby tyto prostředky zůstaly v souladu s vašimi podnikovými standardy a smlouvami o úrovni služeb. Služba Azure Policy splňuje tuto potřebu vyhodnocením vašich prostředků z hlediska nedodržování přiřazených zásad.

Povolte zásady Azure ke sledování a vynucení písemných zásad vaší organizace. Tím zajistíte dodržování požadavků vaší společnosti nebo regulačního zabezpečení centrální správou zásad zabezpečení napříč hybridními cloudovými úlohami. Přečtěte si, jak [vytvářet a spravovat zásady pro vynucení dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md). Přehled prvků zásad najdete v [článku Struktura definic zásad Azure.](../../governance/policy/concepts/definition-structure.md)

Tady jsou některé doporučené postupy zabezpečení, které je třeba dodržovat po přijetí zásad Azure:

**Osvědčený postup**: Zásady podporují několik typů efektů. Můžete si o nich přečíst ve [struktuře definice zásad Azure](../../governance/policy/concepts/definition-structure.md#policy-rule). Obchodní operace mohou být negativně ovlivněny **efektem zamítnutí** a **nápravným** účinkem, takže začněte s **auditním** účinkem, abyste omezili riziko negativního dopadu politiky.   
**Podrobnosti**: [Spusťte nasazení zásad v režimu auditování](../../governance/policy/concepts/definition-structure.md#policy-rule) a později postupujte tak, aby **bylo odepření** nebo **náprava**. Před přesunutím k **zamítnutí** nebo **nápravě**otestujte výsledky efektu auditu.

Další informace naleznete v [tématu Vytvoření a správa zásad k vynucení dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md).

**Osvědčený postup**: Určete role odpovědné za sledování porušení zásad a zajištění rychlého přijetí správných nápravných opatření.   
**Podrobnosti**: Mít přiřazenou úlohu monitorování dodržování předpisů prostřednictvím [portálu Azure](../../governance/policy/how-to/get-compliance-data.md#portal) nebo prostřednictvím [příkazového řádku](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Osvědčený postup**: Zásady Azure jsou technickou reprezentací písemných zásad organizace. Namapujte všechny zásady Azure na zásady organizace, abyste snížili nejasnosti a zvýšili konzistenci.   
**Podrobnosti**: Mapování dokumentů v dokumentaci vaší organizace nebo v samotné mase Azure přidáním odkazu na zásady organizace v [popisu zásad](../../governance/policy/concepts/definition-structure.md#display-name-and-description) Azure nebo v popisu [iniciativy](../../governance/policy/concepts/definition-structure.md#initiatives) zásad Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorování sestav rizik Azure AD
K převážné většině narušení zabezpečení dochází, když útočníci získají přístup k prostředí krádeží identity uživatele. Zjišťování kompromitovaných identit není snadný úkol. Azure AD používá adaptivní algoritmy strojového učení a heuristiky k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty. Každá zjištěná podezřelá akce je uložena v záznamu nazývaném [detekce rizik](../../active-directory/reports-monitoring/concept-risk-events.md). Detekce rizik se zaznamenávají v sestavách zabezpečení Azure AD. Další informace naleznete v přehledu o [zabezpečení ohrožených uživatelů](../../active-directory/reports-monitoring/concept-user-at-risk.md) a o zprávě o zabezpečení [rizikových přihlášení](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Další kroky
Podívejte se na [osvědčené postupy a vzory zabezpečení Azure, kde](best-practices-and-patterns.md) najdete další doporučené postupy zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure.

K dispozici jsou následující prostředky, které poskytují obecnější informace o zabezpečení Azure a souvisejících službách Microsoftu:
* [Blog týmu zabezpečení Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovějších informacích o azure securityu
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – kde lze nahlásit chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo e-mailemsecure@microsoft.com
