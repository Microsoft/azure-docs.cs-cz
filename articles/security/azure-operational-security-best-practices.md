---
title: Osvědčené postupy zabezpečení pro ochranu vašich prostředků – Microsoft Azure
description: Tento článek obsahuje sadu aplikovatelné nejlepší postupy pro ochranu vašich dat, aplikací a dalších prostředků v Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 0418d325f3b3719549181a48fc0432a677f695d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795904"
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy Azure provozní zabezpečení
Tento článek obsahuje sadu aplikovatelné nejlepší postupy pro ochranu vašich dat, aplikací a dalších prostředků v Azure.

Osvědčené postupy jsou založené na konsenzus názorů a práci s aktuální možnosti platformy Azure a sady funkcí. Názory a technologie v průběhu času měnit a tento článek se aktualizuje v pravidelných intervalech, aby tyto změny projevily.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definovat a nasazovat postupy silné provozní zabezpečení
Azure operational security odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků v Azure. Provozní zabezpečení Azure je založen na rozhraní, která zahrnuje poznatky získané díky funkcím, které jsou jedinečné pro Microsoft, včetně [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programu a hloubkové povědomí o zabezpečení.

## <a name="manage-and-monitor-user-passwords"></a>Správa a monitorování uživatelských hesel
V následující tabulce jsou uvedeny některé osvědčené postupy související se správou hesel uživatelů:

**Osvědčený postup**: Ujistěte se, že máte správnou úroveň ochrany hesla v cloudu.   
**Podrobnosti o**: Postupujte podle pokynů v [pokyny heslo Microsoft](https://www.microsoft.com/research/publication/password-guidance/), které působí na uživatele platformy Microsoft identity (účet služby Azure Active Directory, služby Active Directory a Microsoft).

**Osvědčený postup**: Monitorování podezřelé akce související s vašimi uživatelskými účty.   
**Podrobnosti o**: Monitorování pro [ohrožení uživatelé](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) a [rizikových přihlášení](../active-directory/reports-monitoring/concept-risk-events.md) zabezpečení Azure AD pomocí sestav.

**Osvědčený postup**: Automaticky se rozpoznají a opraví hesla s vysokým rizikem.   
**Podrobnosti o**: [Azure AD Identity Protection](../active-directory/identity-protection/overview.md) je funkce edice Azure AD Premium P2, která vám umožní:

- Zjistit potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci
- Nakonfigurovat automatické odpovědi na zjištěné podezřelé akce, které se vztahují na identity vaší organizace
- Prozkoumat podezřelé incidenty a přijímat odpovídající opatření pro jejich řešení

## <a name="receive-incident-notifications-from-microsoft"></a>Incident oznámení dostávat od Microsoftu
Ujistěte se, že váš provozní tým zabezpečení přijímá Azure incidentu oznámení od Microsoftu. Incidentu oznámení umožňuje bezpečnostní tým vědět, že máte prozrazeny prostředky Azure, abyste mohli rychle reagovat na a opravte potenciální bezpečnostní rizika.

V portálu registrace v Azure můžete zajistit kontaktní údaje správce zahrnuje podrobnosti, které operace zabezpečení upozornění. Kontaktní údaje je e-mailovou adresu a telefonní číslo.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Uspořádání předplatných Azure do skupin pro správu
Pokud má vaše organizace více předplatných, můžete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů u těchto předplatných. [Skupiny pro správu Azure](../governance/management-groups/create.md) zajišťují určitou úroveň obor, který je vyšší než předplatných. Uspořádání předplatných do kontejnerů názvem skupiny pro správu a použít vaše podmínky zásad správného řízení do skupin pro správu. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Můžete vytvářet flexibilní strukturu skupin pro správu a předplatné do adresáře. Každý adresář je přiřazena nejvyšší úrovně správy skupinu s názvem kořenové skupině pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Skupinu root management umožňuje globální zásady a přiřazení RBAC uplatňovat na úrovni adresáře.

Tady jsou některé osvědčené postupy při používání skupin pro správu:

**Osvědčený postup**: Ujistěte se, že nová předplatná vztahovat zásad správného řízení prvky, jako jsou zásady a oprávnění, když jsou přidávány.   
**Podrobnosti o**: Pomocí skupiny pro správu kořenového přiřaďte prvky zabezpečení na podnikové úrovni, které platí pro všechny prostředky Azure. Zásady a oprávnění jsou příklady prvků.

**Osvědčený postup**: Zarovnejte nejvyšší úrovně skupin pro správu s segmentace strategie zajištění bod pro řízení a zásad konzistenci v rámci každého segmentu.   
**Podrobnosti o**: Vytvoření jedna skupina pro správu pro každý segment podle skupinu root management. Nevytvářejte žádné jiné skupiny pro správu v kořenovém adresáři.

**Osvědčený postup**: Omezení správy skupiny hloubky, aby nedocházelo k záměně, který omezuje operace a zabezpečení.   
**Podrobnosti o**: Omezte hierarchii na tři úrovně, včetně kořenové.

**Osvědčený postup**: Pečlivě vyberte položky, které chcete použít pro celou organizaci kořenové skupině pro správu.   
**Podrobnosti o**: Zkontrolujte kořenové elementy skupiny management mají vymazat, je potřeba použít ve všech prostředcích a jste malý vliv.

Vhodné zahrnout:

- Zákonné požadavky, které mají vymazat dopad na chod firmy (například omezení se týkají suverenity)
- Požadavky se nevyžaduje skoro žádnou potenciál negativní vliv na operace, jako jsou zásady auditu nebo RBAC pečlivě zkontrolovat přiřazení oprávnění

**Osvědčený postup**: Pečlivě naplánovat a otestovat všechny změny na podnikové úrovni na skupinu root management před použitím (zásady, RBAC model a tak dále).   
**Podrobnosti o**: Změny ve skupině pro správu kořenového mohou ovlivnit každý prostředek v Azure. Zatímco poskytují efektivní způsob, jak zajistit konzistenci v rámci podniku, chyby nebo nesprávné použití může negativně ovlivnit výrobní činnosti. Testování všech změn ke kořenové skupině pro správu v testovacím nebo produkčním pilotního nasazení.

## <a name="streamline-environment-creation-with-blueprints"></a>Zjednodušení vytváření prostředí s podrobné plány.
[Plány Azure](../governance/blueprints/overview.md) služba umožňuje cloudovým architektům a centrální informace technologie skupiny k definování opakovatelné sadu prostředků Azure, která implementuje a dodržuje standardy, vzory a požadavky vaší organizace. Plány Azure umožňuje vývojářské týmy umožňující rychlé sestavení a vytvoření nové prostředí se sadou integrované komponenty znalosti a jistotu, že vytváříte těchto prostředí v rámci dodržování předpisů v organizaci.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorování služby storage pro neočekávaným změnám v chování
Diagnostika a řešení potíží v distribuované aplikaci hostované v cloudovém prostředí můžou být složitější než v tradičních prostředí. Aplikace můžete nasadit v infrastruktuře PaaS nebo IaaS v místním prostředí, na mobilním zařízení, nebo určitou kombinaci těchto prostředích. Síťový provoz vaší aplikace můžou procházet veřejný a privátní sítě a vaše aplikace může používat více technologií úložišť.

Nepřetržitě, měli byste sledovat služby úložiště, které vaše aplikace používá pro všechny neočekávaným změnám v chování (například pomalejší doby odezvy). Použití protokolování shromažďovat detailní data a k analýze problému do hloubky. Diagnostické informace, které můžete získat z monitorování a protokolování vám umožní zjistit původní příčinu problému, který vaše aplikace zjistila. Potom můžete tento problém vyřešit a určit příslušné kroky k jeho řešení.

[Azure Storage Analytics](../storage/storage-analytics.md) provádí protokolování a poskytuje data metriky pro účet úložiště Azure. Doporučujeme, abyste tato data použít k trasování požadavků, analýze trendů použití a diagnostikovat problémy s vaším účtem úložiště.

## <a name="prevent-detect-and-respond-to-threats"></a>Hrozbami, detekci a reakce na hrozby
[Azure Security Center](../security-center/security-center-intro.md) pomáhá zabránit, zjišťovat a reagovat na ně díky zvýšené viditelnosti (a kontrolu nad) zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s různá řešení zabezpečení.

Úroveň Free služby Security Center nabízí omezené zabezpečení jenom prostředky Azure. Úroveň Standard tyto možnosti rozšiřuje do místní i v jiných cloudech. Security Center úrovně Standard vám pomůže najít a opravit chyby zabezpečení, použít řízení přístupu a aplikací blokuje škodlivé aktivity, detekce hrozeb pomocí analýz a inteligentních funkcí a rychle reagovat v případě útoku. Službu Security Center v cenové úrovni Standard si můžete zdarma vyzkoušet na 60 dní. Doporučujeme vám [upgradu vašeho předplatného Azure na Security Center úrovně Standard](../security-center/security-center-get-started.md).

Použití služby Security Center k získání přehledu o stavu zabezpečení všech vašich prostředků Azure. Na první pohled ověřte, zda kontrolních mechanismů zabezpečení jsou na místě a správně nakonfigurován a rychle identifikovat všechny prostředky, které vyžadují pozornost.

Security Center se také integruje s [Windows Defender Advanced Threat Protection (ATP)](../security-center/security-center-wdatp.md), která poskytuje rozsáhlé možnosti koncový bod zjišťování a odpovědi (EDR). Integrace ochrany ATP v programu Windows Defender můžete vybírat anomálie. Můžete také zjistit a reagovat na pokročilých útoků na koncové body serveru monitorovaný pomocí služby Security Center.

Téměř všechny organizace mají událostí systému správy zabezpečení (SIEM) vám pomůže identifikovat vznikajícími hrozbami konsolidací informace protokolu ze zařízení s různými signál shromažďování. Protokoly se poté analyzovaným systém pro analýzy dat vám pomůže identifikovat, co je "zajímavý" od šumu, který je nevyhnutelné ve všech shromažďování protokolů a řešení pro analýzu.

[Azure Sentinel](../sentinel/overview.md) zabezpečení automatizované Orchestrace odpovědi (SOAR) řešení a škálovatelné nativní pro cloud, Správa zabezpečení a událostí (SIEM). Azure Sentinel poskytuje inteligentní zabezpečení analýz a analýz hrozeb intelligence prostřednictvím výstrahy detekce, viditelnost před internetovými útoky, proaktivní typu a reakce na hrozby automatizované.

Tady jsou některé osvědčené postupy pro prevenci, detekci a reakce na hrozby:

**Osvědčený postup**: Zvyšuje rychlost a škálovatelnost řešení SIEM s použitím SIEM založené na cloudu.   
**Podrobnosti o**: Prozkoumat funkce a možnosti [Azure Sentinelu](../sentinel/overview.md) a jejich porovnání s funkcemi to, co právě pomocí místní. Zvažte zavedení Sentinelu Azure splňuje požadavky vaší organizace SIEM.

**Osvědčený postup**: Najdete nejvážnější ohrožení zabezpečení, můžete určit prioritu prozkoumání.   
**Podrobnosti o**: Zkontrolujte vaše [Azure zabezpečené skóre](../security-center/security-center-secure-score.md) zobrazíte doporučení plynoucí z Azure zásad a iniciativy integrovaná v Azure Security Center. Tato doporučení pomáhají adresu největších rizik, jako jsou aktualizace zabezpečení, ochrany koncového bodu, šifrování, konfigurace zabezpečení, chybějící WAF, virtuální počítače připojené k Internetu a mnoho dalších.

Zabezpečené skóre, která je založena na System Center pro ovládací prvky pro zabezpečení Internetu (CIS), umožňuje provádění srovnávacích testů Azure zabezpečení vaší organizace proti externích zdrojů. Externí ověřování pomáhá ověřte a rozšiřovat strategie zabezpečení vašeho týmu.

**Osvědčený postup**: Monitorujte stav zabezpečení počítačů, sítí, úložiště a datových služeb a aplikací k zjišťování a stanovení priorit potenciální potíže se zabezpečením.  
**Podrobnosti o**: Postupujte podle [doporučení zabezpečení](../security-center/security-center-recommendations.md) v Security Center od verze položek s nejvyšší prioritou.

**Osvědčený postup**: Integrace výstrah služby Security Center do řešení pro správu (SIEM) událostí zabezpečení.   
**Podrobnosti o**: Většina organizací se SIEM jej použít jako o centrální středisku pro výstrahy zabezpečení, které vyžadují při reakci na analytika. Zpracované události vytvořené službou Security Center se publikují do protokolu aktivit Azure, jednoho z protokolů, které jsou k dispozici prostřednictvím služby Azure Monitor. Azure Monitor nabízí konsolidované kanálu pro směrování všech dat monitorování do nástroje SIEM. Zobrazit [integrace řešení zabezpečení v Security Center](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) pokyny. Pokud používáte Azure Sentinelu, přečtěte si téma [připojit Azure Security Center](../sentinel/connect-azure-security-center.md).

**Osvědčený postup**: Integrace Azure protokoly s vaším řešením SIEM.   
**Podrobnosti o**: Použití [Azure Monitor k shromáždění a exportu dat](../azure-monitor/overview.md#integrate-and-export-data). Tento postup je velmi důležité pro povolení šetření incidentů zabezpečení a uchovávání protokolů online je omezený. Pokud používáte Azure Sentinelu, přečtěte si téma [připojení zdroje dat](../sentinel/connect-data-sources.md).

**Osvědčený postup**: Urychlení vyšetřování a myslivost procesy a snížil počet falešných poplachů integrací funkce pro koncový bod zjišťování a odpovědi (EDR) do vaší vyšetřování útoku.   
**Podrobnosti o**: [Povolení ochrany ATP v programu Windows Defender integrace](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) prostřednictvím zásad zabezpečení Security Center. Zvažte použití Azure Sentinelu typu hrozeb a reakce na incidenty.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Sledování monitorování sítě založené na scénářích začátku do konce
Můžou zákazníci sestavovat síť začátku do konce v Azure díky kombinaci síťovým prostředkům, jako jsou virtuální síť, ExpressRoute, Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředků.

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) je místní služba. Použijte jeho diagnostické a vizualizační nástroje můžete monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure.

Níže jsou osvědčené postupy pro monitorování a k dispozici nástroje sítě.

**Osvědčený postup**: Automatizace monitorování vzdálené sítě pomocí zachytávání paketů.  
**Podrobnosti o**: Monitorování a diagnostice síťových potíží není nutné se připojit k vašim virtuálním počítačům pomocí Network Watcher. Aktivační událost [zachytávání paketů](../network-watcher/network-watcher-alert-triggered-packet-capture.md) tím, že nastavení výstrah a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Když narazíte na problém, můžete ho prozkoumat podrobněji a lépe diagnostikovat.

**Osvědčený postup**: Získejte přehled o provozu vaší sítě pomocí protokolů toků.  
**Podrobnosti o**: Vytvářet lepší představu o vaší síti vzory provozu s využitím [protokoly toků skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informace v protokolech toku umožňuje shromažďovat data pro dodržování předpisů, auditování a monitorování profilu síťového zabezpečení.

**Osvědčený postup**: Diagnostika potíží s připojením VPN.  
**Podrobnosti o**: Používat Network Watcher, aby [diagnostikovat nejběžnější problémy brány VPN a připojení](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Můžete nejenom identifikovat problém, ale také použít podrobné protokoly k hlubšímu prošetření.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Zabezpečení nasazení s použitím prověřené nástroje DevOps
K zajištění, že vaše organizace a týmy produktivnějších a efektivnějších použijte následující osvědčené postupy DevOps.

**Osvědčený postup**: Automatizace sestavení a nasazení služeb.  
**Podrobnosti o**: [Infrastruktura jako kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) představují sadu technik a postupů, které pomáhají odborníkům v odebrání si museli dělat starosti každodenní sestavení a správu modulární infrastruktury. Umožňuje odborníkům na IT vytvářet a spravovat jejich prostředí moderních serverových způsobem, který je třeba jak vývojářům softwaru vytvářet a spravovat kód aplikace.

Můžete použít [Azure Resource Manageru](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Použít stejnou šablonu k opakovanému nasazení aplikace v každé fázi životního cyklu aplikací.

**Osvědčený postup**: Automaticky sestavit a nasadit do Azure web apps nebo cloudové služby.  
**Podrobnosti o**: Můžete nakonfigurovat projektů Azure DevOps [automaticky vytvářet a nasazovat](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) do Azure web apps nebo cloud services. Azure DevOps automaticky nasadí binární soubory po provedení po každé kód vrácení se změnami sestavení do Azure. Proces sestavení balíčku je ekvivalentní příkazu balíčku v sadě Visual Studio a postup publikování je ekvivalentní příkazu Publikovat v sadě Visual Studio.

**Osvědčený postup**: Automatizace správy vydaných verzí.  
**Podrobnosti o**: [Kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) je řešení pro automatizaci nasazení více fázi procesu vydávání verzí. Vytvoření spravovaných postupů plynulého nasazování kanálů k uvolnění rychlé, snadné a časté. Kanály Azure můžete automatizovat proces vydávání verzí a může mít předdefinované schvalovacích pracovních postupů. Nasazení v místním prostředí a do cloudu, rozšíření a přizpůsobení podle potřeby.

**Osvědčený postup**: Kontrola výkonu vaší aplikace předtím, než ji spustit nebo nasadit aktualizace do produkčního prostředí.  
**Podrobnosti o**: Spuštění založené na cloudu [zátěžové testy](/azure/devops/test/load-test/overview#alternatives) na:

- Zjišťování problémů s výkonem ve vaší aplikaci.
- Zlepšení kvality nasazení.
- Ujistěte se, že vaše aplikace je vždy k dispozici.
- Ujistěte se, že vaše aplikace dokáže zpracovat provoz pro další spuštění nebo marketingové kampani.

[Apache JMeter](https://jmeter.apache.org/) je zdarma, Oblíbené open source nástroj s silné komunitou zálohování.

**Osvědčený postup**: Monitorování výkonu aplikací.  
**Podrobnosti o**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) je služby extensible application performance management (APM) pro webovým vývojářům na více platforem. Monitorování živé webové aplikace pomocí Application Insights. Automaticky zjišťuje anomálie výkonu. Zahrnuje analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé dělají s vaší aplikací. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

## <a name="mitigate-and-protect-against-ddos"></a>Omezení rizik a chránit proti před útoky DDoS
Distribuované s cílem odepření služeb (DDoS) je typ útoku, který se pokouší vyčerpání prostředků aplikace. Cílem je mít vliv na dostupnost vaší aplikace a její schopnost zpracovávat oprávněné požadavky. Tyto útoky se stávají propracované a větší velikost a dopad. Můžete ji cílit na libovolný koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Návrh a vývoj aplikací pro odolnost proti chybám před útoky DDoS vyžaduje plánování a návrh pro různé režimy selhání. Toto jsou osvědčené postupy pro vytváření před útoky DDoS odolných služeb v Azure.

**Osvědčený postup**: Ujistěte se, že zabezpečení je priorita v průběhu celého životního cyklu aplikace, od návrhu a implementace až po nasazení a provoz. Aplikace může mít chyby, které umožňují relativně nízký objem požadavků na použití velké množství prostředků, což vede k výpadku služeb.  
**Podrobnosti o**: K ochraně služby spuštěné v Microsoft Azure, musí mít dostatečné povědomí o vaší aplikace architektury a zaměřit [pět pilířů kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars). Typické provozem, měli byste vědět model připojení mezi aplikací a další aplikace a koncové body služby, které jsou vystaveny do veřejného Internetu.

Zajištění, že aplikace je dostatečně odolné pro zpracování odepření služby, který je zaměřený na vlastní aplikace je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované do platformy Azure počínaje [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). Do procesu SDL zajišťuje zabezpečení ve všech fázích vývoje a zajišťuje, že Azure se průběžně aktualizuje, aby byl ještě větší zabezpečení.

**Osvědčený postup**: Navrhnout aplikace tak [škálovat horizontálně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) podle potřeby zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud je aplikace závislá na jednu instanci služby, vzniká jediný bod selhání. Zřízení více instancí je váš systém větší škálovatelnost a odolnost.  
**Podrobnosti o**: Pro [služby Azure App Service](../app-service/app-service-value-prop-what-is.md)vyberte [plán služby App Service](../app-service/overview-hosting-plans.md) , který nabízí více instancí.

Pro Azure Cloud Services, nakonfigurovat všechny své role používat [více instancí](../cloud-services/cloud-services-choose-me.md).

Pro [Azure Virtual Machines](../virtual-machines/windows/overview.md), ujistěte se, že vaše Architektura virtuálního počítače obsahuje více než jeden virtuální počítač a že každý virtuální počítač je součástí [dostupnosti](../virtual-machines/virtual-machines-windows-manage-availability.md). Doporučujeme, abyste pomocí virtuálního počítače škálovací sady pro možnosti automatického škálování.

**Osvědčený postup**: Vrstvení obrana zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Zabezpečené vzory pro vaše aplikace implementovat pomocí integrované funkce platformy Azure.  
**Podrobnosti o**: Hodnota se zvyšuje riziko útoku s velikost (styčné plochy) aplikace. Můžete snížit styčné plochy pomocí přidávání na seznam povolených uzavřeny vystavené adresní prostor IP adres a naslouchání porty, které nejsou potřebné v nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) a [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Skupiny zabezpečení sítě](../virtual-network/security-overview.md) představují jiný způsob omezení možností útoku. Můžete použít [značky služeb](../virtual-network/security-overview.md#service-tags) a [skupiny zabezpečení aplikací](../virtual-network/security-overview.md#application-security-groups) minimalizovat složitost vytváření pravidla zabezpečení a konfigurace zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Měli byste nasadit služby Azure v [virtuální sítě](../virtual-network/virtual-networks-overview.md) kdykoli je to možné. Tento postup umožňuje prostředky služby pro komunikaci prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě jako zdrojové IP adresy používá veřejné IP adresy ve výchozím nastavení.

Pomocí [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md) služby přepne provoz na používání privátních adres virtuální sítě jako zdrojové IP adresy, když přistupuje služby Azure z virtuální sítě.

Vidíme často zákazníků s místními prostředky získávání napadených spolu s jejich prostředky v Azure. Pokud se připojujete do místního prostředí do Azure, omezit vystavení místních prostředků do veřejného Internetu.

Azure má dva DDoS [nabídek služeb](../virtual-network/ddos-protection-overview.md) , které poskytují ochranu před útoky ze sítě:

- Basic protection je integrovaná do Azure ve výchozím nastavení bez dalších poplatků. Škálování a kapacitu sítě globálně nasazených Azure poskytuje ochranu před běžnými útoky síťové vrstvě prostřednictvím monitorování a v reálném čase zmírnění neustále v provozu. Basic vyžaduje žádné změny v konfiguraci nebo aplikace uživatele a pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.
- Standard protection nabízí pokročilé funkce pro zmírnění útoků DDoS proti síťovým útokům. Toto pravidlo je vyladěný automaticky k ochraně vašich konkrétních prostředků Azure. Ochrana je snadno zajistit při vytváření virtuální sítě. Je možné provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředku.

## <a name="enable-azure-policy"></a>Povolit Azure Policy
[Služba Azure Policy](../governance/policy/overview.md) je služba v Azure, který použijete k vytvoření, přiřazení a Správa zásad. Tyto zásady vynucují pravidla a efekty u vašich prostředků, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvy o úrovni služeb. Služba Azure Policy splňuje tyto potřeby vyhodnocením prostředky nedodržení předpisů se zásadami přiřazený.

Povolte Azure Policy, monitorovat a vynutit písemné zásadami vaší organizace. Tím se zajistí dodržování předpisů ve vaší společnosti nebo požadavky na dodržování zabezpečení díky centrální správě zásad zabezpečení napříč vašimi hybridními cloudovými úlohami. Zjistěte, jak [vytváření a Správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md). Zobrazit [struktura definic Azure Policy](../governance/policy/concepts/definition-structure.md) přehled prvky zásad.

Tady jsou některé osvědčené postupy zabezpečení sledovat po přijmout zásady Azure:

**Osvědčený postup**: Zásady podporuje několik typů účinků. Si můžete přečíst o nich v [struktura definic Azure Policy](../governance/policy/concepts/definition-structure.md#policy-rule). Obchodní operace může být negativně ovlivněn **Odepřít** vliv a **napravit** projeví, takže začínat **auditu** efekt omezit riziko negativní dopad na chod firmy ze zásady.   
**Podrobnosti o**: [Spustit nasazení zásad v režimu auditování](../governance/policy/concepts/definition-structure.md#policy-rule) a potom později průběh na **Odepřít** nebo **napravit**. Test a zkontrolovat výsledky efekt auditu před přesunutím do **Odepřít** nebo **napravit**.

Další informace najdete v tématu [vytvoření a Správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

**Osvědčený postup**: Identifikace rolí zodpovědný za monitorování hlediska porušování zásad a zajištění, že správné nápravné akce se provede rychle.   
**Podrobnosti o**: Mít monitorování dodržování předpisů přiřazených rolí pomocí [webu Azure portal](../governance/policy/how-to/get-compliance-data.md#portal) nebo prostřednictvím [příkazového řádku](../governance/policy/how-to/get-compliance-data.md#command-line).

**Osvědčený postup**: Služba Azure Policy je technické reprezentace písemné zásad vaší organizace. Všechny zásady Azure přiřadit zásady organizace zvýšit konzistenci a redukovat nejasnosti.   
**Podrobnosti o**: Mapování dokumentu v dokumentaci k vaší organizaci nebo v Azure samotná struktura zásad tak, že přidáte odkaz na zásady organizace ve službě Azure [popis zásad](../governance/policy/concepts/definition-structure.md#display-name-and-description) nebo Azure policy [iniciativy](../governance/policy/concepts/definition-structure.md#initiatives) Popis.

## <a name="monitor-azure-ad-risk-reports"></a>Riziko sestav monitorování Azure AD
Většinu narušení zabezpečení se použijí při útočníci získají přístup k prostředí krádeží identity uživatele. Zjišťování ohrožení zabezpečení identit je žádný snadný úkol. Azure AD používá adaptivní algoritmy strojového učení a heuristik ke zjištění podezřelé akce, které souvisejí s vašimi uživatelskými účty. Každou zjištěnou podezřelé akce, které jsou uloženy v záznam nazvaný [riziková událost](../active-directory/reports-monitoring/concept-risk-events.md). Rizikové události se zaznamenávají do zabezpečení Azure AD sestavy. Další informace, přečtěte si informace o [zabezpečení Sestava ohrožených uživatelů](../active-directory/reports-monitoring/concept-user-at-risk.md) a [sestavy rizikových přihlášení zabezpečení](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Další postup
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com
