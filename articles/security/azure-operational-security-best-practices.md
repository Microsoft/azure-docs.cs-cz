---
title: Osvědčené postupy provozní zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje sadu osvědčených postupů pro provozní zabezpečení Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: ae6eeb2506eb82160c68e15e17eeb95c1e2ec046
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853605"
---
# <a name="azure-operational-security-best-practices"></a>Osvědčené postupy Azure provozní zabezpečení
Azure operational security odkazuje na služby, ovládací prvky a funkce, které jsou dostupné pro uživatele k ochraně svých dat, aplikací a dalších prostředků v Azure. Provozní zabezpečení Azure je založen na rozhraní, která zahrnuje poznatky získané díky funkcím, které jsou jedinečné pro Microsoft, včetně [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programu a hloubkové povědomí o zabezpečení.

V tomto článku se podíváme na kolekci osvědčené postupy zabezpečení. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s zabezpečení služby Azure database a prostředí zákazníků, jako je sami.

Pro každý osvědčeným postupem je vám vysvětlíme:
-   Jaký je doporučený postup
-   Proč chcete povolit tento osvědčený postup
-   Pokud chcete povolit osvědčený postup, co mohou být způsobeny
- Jak lze zjistíte, jak povolit osvědčený postup

Tento článek osvědčené postupy provozní zabezpečení Azure je založen na stanovisko shody a možnostech platformy Azure a sady funkcí, protože existují v okamžiku, kdy byla zapsána v tomto článku. Názory a technologie v průběhu času měnit a tento článek bude aktualizován v pravidelných intervalech, aby tyto změny projevily.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorování služby storage pro neočekávaným změnám v chování
Diagnostika a řešení potíží v distribuované aplikaci hostované v cloudovém prostředí můžou být složitější než v tradičních prostředí. Aplikace můžete nasadit v infrastruktuře PaaS nebo IaaS v místním prostředí, na mobilním zařízení, nebo určitou kombinaci těchto prostředích. Síťový provoz vaší aplikace můžou procházet veřejný a privátní sítě a vaše aplikace může používat více technologií úložišť.

Nepřetržitě, měli byste sledovat služby úložiště, které vaše aplikace používá pro všechny neočekávaným změnám v chování (například pomalejší doby odezvy). Použití protokolování shromažďovat detailní data a k analýze problému do hloubky. Diagnostické informace, které můžete získat z monitorování a protokolování vám umožní zjistit původní příčinu problému, který vaše aplikace zjistila. Potom můžete tento problém vyřešit a určit příslušné kroky k jeho řešení.

[Azure Storage Analytics](../storage/storage-analytics.md) provádí protokolování a poskytuje data metriky pro účet úložiště Azure. Doporučujeme, abyste tato data použít k trasování požadavků, analýze trendů použití a diagnostikovat problémy s vaším účtem úložiště.

## <a name="prevent-detect-and-respond-to-threats"></a>Hrozbami, detekci a reakce na hrozby
[Azure Security Center](../security-center/security-center-intro.md) pomáhá zabránit, detekci a reakce na ně prostřednictvím zvýšené viditelnosti (a kontrolu nad) zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s různá řešení zabezpečení.

Bezplatná úroveň Security Center nabízí omezené zabezpečení vašich prostředků Azure pouze. Úroveň Standard tyto možnosti rozšiřuje do místní i v jiných cloudech. Security Center úrovně Standard pomáhá vyhledávat a opravovat ohrožení zabezpečení, blokovat škodlivou aktivitu pomocí ovládacích prvků přístupu a aplikací, detekovat hrozby s využitím analýz a inteligentních funkcí a rychle reagovat v případě útoku. Službu Security Center v cenové úrovni Standard si můžete zdarma vyzkoušet na 60 dní. Doporučujeme vám [připojení předplatného Azure na Security Center úrovně Standard](../security-center/security-center-get-started.md).

Použití služby Security Center k získání přehledu o stavu zabezpečení všech vašich prostředků Azure. Na první pohled ověřte, zda kontrolních mechanismů zabezpečení jsou na místě a správně nakonfigurován a rychle identifikovat všechny prostředky, které vyžadují pozornost.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Sledování monitorování sítě založené na scénářích začátku do konce
Můžou zákazníci sestavovat síť začátku do konce v Azure díky kombinaci síťovým prostředkům, jako jsou virtuální síť, ExpressRoute, Application Gateway a nástroje pro vyrovnávání zatížení. Monitorování je k dispozici na všech síťových prostředků.

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) je místní služba. Použijte jeho diagnostické a vizualizační nástroje můžete monitorovat a diagnostikovat podmínky na úrovni síťového scénáře v, do a z Azure.

Níže jsou osvědčené postupy pro monitorování a k dispozici nástroje sítě.

**Osvědčený postup**: automatizace monitorování vzdálené sítě pomocí zachytávání paketů.  
**Podrobnosti o**: monitorování a diagnostice síťových potíží není nutné se připojit k vašim virtuálním počítačům pomocí Network Watcher. Aktivační událost [zachytávání paketů](../network-watcher/network-watcher-alert-triggered-packet-capture.md) tím, že nastavení výstrah a získat přístup k informacím o výkonu v reálném čase na úrovni paketů. Když narazíte na problém, můžete ho prozkoumat podrobněji a lépe diagnostikovat.

**Osvědčený postup**: získat přehled o provozu vaší sítě pomocí protokolů toků.  
**Podrobnosti o**: vytvářet lepší představu o vaší síti vzory provozu s využitím [protokoly toků skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Informace v protokolech toku umožňuje shromažďovat data pro dodržování předpisů, auditování a monitorování profilu síťového zabezpečení.

**Osvědčený postup**: problémy s připojením VPN diagnostikovat.  
**Podrobnosti o**: Network Watcher, aby [diagnostikovat nejběžnější problémy brány VPN a připojení](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Můžete nejenom identifikovat problém, ale také použít podrobné protokoly k hlubšímu prošetření.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Zabezpečení nasazení s použitím prověřené nástroje DevOps
K zajištění, že vaše organizace a týmy produktivnějších a efektivnějších použijte následující osvědčené postupy DevOps.

**Osvědčený postup**: automatizace sestavení a nasazení služeb.  
**Podrobnosti o**: [infrastruktura jako kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) představují sadu technik a postupů, které pomáhají odborníkům v odebrání si museli dělat starosti každodenní sestavení a správu modulární infrastruktury. Umožňuje odborníkům na IT vytvářet a spravovat jejich prostředí moderních serverových způsobem, který je třeba jak vývojářům softwaru vytvářet a spravovat kód aplikace.

Můžete použít [Azure Resource Manageru](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) zřizovat aplikace pomocí deklarativní šablony. S jednou šablonou můžete nasadit několik služeb společně s jejich závislostmi. Použít stejnou šablonu k opakovanému nasazení aplikace v každé fázi životního cyklu aplikací.

**Osvědčený postup**: automaticky sestavit a nasadit do webové aplikace Azure nebo cloudových služeb.  
**Podrobnosti o**: můžete použít Azure kanály, abyste mohli [automaticky vytvářet a nasazovat](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) do Azure web apps nebo cloud services. Kanály Azure automaticky nasadí binární soubory po provedení po každé kód vrácení se změnami sestavení do Azure. Proces sestavení balíčku je ekvivalentní příkazu balíčku v sadě Visual Studio a postup publikování je ekvivalentní příkazu Publikovat v sadě Visual Studio.

**Osvědčený postup**: použijte průběžné nasazování.  
**Podrobnosti o**: [kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) je řešení pro automatizaci nasazení více fázi procesu vydávání verzí. Vytvoření spravovaných postupů plynulého nasazování kanálů k uvolnění rychlé, snadné a časté. Kanály Azure můžete automatizovat proces vydávání verzí a může mít předdefinované schvalovacích pracovních postupů. Nasazení v místním prostředí a do cloudu, rozšíření a přizpůsobení podle potřeby.

**Osvědčený postup**: Zkontrolujte výkon vaší aplikace předtím, než ji spustit nebo nasadit aktualizace do produkčního prostředí.  
**Podrobnosti o**: spuštění založené na cloudu [zátěžové testy](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) s použitím Azure testovací plány na:

- Zjišťování problémů s výkonem ve vaší aplikaci.
- Zlepšení kvality nasazení.
- Ujistěte se, že vaše aplikace je vždy k dispozici.
- Ujistěte se, že vaše aplikace dokáže zpracovat provoz pro další spuštění nebo marketingové kampani.

**Osvědčený postup**: monitorování výkonu aplikací.  
**Podrobnosti o**: [Azure Application Insights](../application-insights/app-insights-overview.md) je služby extensible application performance management (APM) pro webovým vývojářům na více platforem. Monitorování živé webové aplikace pomocí Application Insights. Automaticky zjišťuje anomálie výkonu. Zahrnuje analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé dělají s vaší aplikací. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

## <a name="mitigate-and-protect-against-ddos"></a>Omezení rizik a chránit proti před útoky DDoS
Distribuované s cílem odepření služeb (DDoS) je typ útoku, který se pokouší vyčerpání prostředků aplikace. Cílem je mít vliv na dostupnost vaší aplikace a její schopnost zpracovávat oprávněné požadavky. Tyto útoky se stávají propracované a větší velikost a dopad. Můžete ji cílit na libovolný koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Návrh a vývoj aplikací pro odolnost proti chybám před útoky DDoS vyžaduje plánování a návrh pro různé režimy selhání.

Toto jsou osvědčené postupy pro vytváření před útoky DDoS odolných služeb v Azure.

**Osvědčený postup**: Ujistěte se, že zabezpečení priority v průběhu celého životního cyklu aplikace, od návrhu a implementace až po nasazení a provoz. Aplikace může mít chyby, které umožňují relativně nízký objem požadavků na použití velké množství prostředků, což vede k výpadku služeb.  
**Podrobnosti o**: K ochraně služby spuštěné v Microsoft Azure, musí mít dostatečné povědomí o vaší aplikace architektury a zaměřit [pět pilířů kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars). Typické provozem, měli byste vědět model připojení mezi aplikací a další aplikace a koncové body služby, které jsou vystaveny do veřejného Internetu.

Zajištění, že aplikace je dostatečně odolné pro zpracování odepření služby, který je zaměřený na vlastní aplikace je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované do platformy Azure počínaje [Security Development Lifecycle (SDL)](https://www.microsoft.com/en-us/sdl). Do procesu SDL zajišťuje zabezpečení ve všech fázích vývoje a zajišťuje, že Azure se průběžně aktualizuje, aby byl ještě větší zabezpečení.

**Osvědčený postup**: navrhnout aplikace tak [škálovat horizontálně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) podle potřeby zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud je aplikace závislá na jednu instanci služby, vzniká jediný bod selhání. Zřízení více instancí je váš systém větší škálovatelnost a odolnost.  
**Podrobnosti o**: pro [služby Azure App Service](../app-service/app-service-value-prop-what-is.md)vyberte [plán služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , který nabízí více instancí.

Pro Azure Cloud Services, nakonfigurovat všechny své role používat [více instancí](../cloud-services/cloud-services-choose-me.md).

Pro [Azure Virtual Machines](../virtual-machines/windows/overview.md), ujistěte se, že vaše Architektura virtuálního počítače obsahuje více než jeden virtuální počítač a že každý virtuální počítač je součástí [dostupnosti](../virtual-machines/virtual-machines-windows-manage-availability.md). Doporučujeme, abyste pomocí virtuálního počítače škálovací sady pro možnosti automatického škálování.

**Osvědčený postup**: vrstvení obrana zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Zabezpečené vzory pro vaše aplikace implementovat pomocí integrované funkce platformy Azure.  
**Podrobnosti o**: hodnota se zvyšuje riziko útoku s velikost (styčné plochy) aplikace. Můžete snížit styčné plochy pomocí přidávání na seznam povolených uzavřeny vystavené adresní prostor IP adres a naslouchání porty, které nejsou potřebné v nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) a [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Skupiny zabezpečení sítě](../virtual-network/security-overview.md) představují jiný způsob omezení možností útoku. Můžete použít [značky služeb](../virtual-network/security-overview.md#service-tags) a [skupiny zabezpečení aplikací](../virtual-network/security-overview.md#application-security-groups) minimalizovat složitost vytváření pravidla zabezpečení a konfigurace zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Měli byste nasadit služby Azure v [virtuální sítě](../virtual-network/virtual-networks-overview.md) kdykoli je to možné. Tento postup umožňuje prostředky služby pro komunikaci prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě jako zdrojové IP adresy používá veřejné IP adresy ve výchozím nastavení.

Pomocí [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md) služby přepne provoz na používání privátních adres virtuální sítě jako zdrojové IP adresy, když přistupuje služby Azure z virtuální sítě.

Vidíme často zákazníků s místními prostředky získávání napadených spolu s jejich prostředky v Azure. Pokud se připojujete do místního prostředí do Azure, omezit vystavení místních prostředků do veřejného Internetu.

Azure má dva DDoS [nabídek služeb](../virtual-network/ddos-protection-overview.md) , které poskytují ochranu před útoky ze sítě:

- Basic protection je integrovaná do Azure ve výchozím nastavení bez dalších poplatků. Škálování a kapacitu sítě globálně nasazených Azure poskytuje ochranu před běžnými útoky síťové vrstvě prostřednictvím monitorování a v reálném čase zmírnění neustále v provozu. Basic vyžaduje žádné změny v konfiguraci nebo aplikace uživatele a pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.
- Standard protection nabízí pokročilé funkce pro zmírnění útoků DDoS proti síťovým útokům. Toto pravidlo je vyladěný automaticky k ochraně vašich konkrétních prostředků Azure. Ochrana je snadno zajistit při vytváření virtuální sítě. Je možné provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředku.

## <a name="next-steps"></a>Další postup
Zobrazit [osvědčené postupy zabezpečení Azure a vzory](security-best-practices-and-patterns.md) pro další doporučené postupy zabezpečení, mají použít, když jste návrhu, nasazení a správa cloudových řešení pomocí služby Azure.

Jsou následující prostředky vám poskytnou další obecné informace o zabezpečení Azure a související služby Microsoftu:
* [Blog týmu Azure zabezpečení](https://blogs.msdn.microsoft.com/azuresecurity/) – aktuální informace o nejnovější vydání v Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – tam, kde mohou být hlášeny chyby zabezpečení společnosti Microsoft, včetně problémů s Azure, nebo prostřednictvím e-mailu secure@microsoft.com
