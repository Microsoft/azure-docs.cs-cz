---
title: Osvědčené postupy pro ochranu Azure DDoS & referenční architektura | Microsoft Docs
description: Informace o tom, jak můžete data protokolování a získáte přehled o hloubkové o vaší aplikaci.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Ochrana proti útoku DDoS Azure: Osvědčené postupy a referenční architektura

Tento dokument je zaměřený na vedoucím pracovníkům IT a pracovníky zabezpečení. Znalosti s Azure, sítě a zabezpečení se očekává.

Návrh distribuovaných útok DoS (Denial) vyžaduje odolnost proti chybám plánování a návrhu pro různé režimy selhání. Tento dokument obsahuje osvědčené postupy pro navrhování aplikací v Azure pro odolnost proti útokům DDoS.

## <a name="types-of-attacks"></a>Typy útoků

DDoS je typ útoku použít ve snaze vyčerpat prostředky aplikace. Cílem je mít vliv na dostupnost a schopnost zvládat oprávněné požadavky aplikace. Útoky jsou čím dál víc sofistikovaná a větší velikost a dopad. Útoky DDoS můžete cílit na všechny koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Azure poskytuje trvalou ochranu proti útokům DDoS. Tato ochrana je integrována do platformy Azure ve výchozím nastavení a bez dalších poplatků. Kromě základních ochrana proti útoku DDoS, které jsou součástí platformy, máme také novou nabídku s názvem "[Azure DDoS ochrany standardní](https://azure.microsoft.com/services/ddos-protection/)", který poskytuje pokročilé možnosti zmírnění DDoS proti síťovým útokům a je automaticky přizpůsobená pro chránit konkrétní prostředky v Azure. Ochrana je jednoduché, aby při vytváření nové virtuální sítě. To lze provést i po vytvoření počátečního a nevyžaduje žádné změny aplikace nebo prostředku.

![](media/azure-ddos-best-practices/image1.png)

Útoky DDoS můžete široce rozdělit do tří (3) různých kategorií

### <a name="volumetric-attacks"></a>Odměrné útoky

Odměrné útoky jsou nejběžnějších typů útoku DDoS. Jsou odměrné útoky hrubou silou osoby, které cílí sítě a transportní vrstvy. Pokusí se vyčerpat prostředky takové síťová propojení. Tyto útoky běžně používají různé nakažené systémy k vyplnění vrstvy sítě s vyžadovat značné množství zdánlivě legitimní provoz. V tomto typu útoku se používají různé protokolů síťové vrstvy, jako jsou zprávy protokolu ICMP (Internet Control), protokolu UDP (User Datagram) a protokolu TCP (Transmission Control).

Nejčastěji používaná síťové vrstvy DDoS útoky jsou TCP SYN zahlcení, odezvu protokolu ICMP, zahlcení UDP, DNS a NTP zesílení útoku. Tento typ útoku umožňuje nejenom dojít k přerušení služby, ale také jako smokescreen pro více nefarious a cílové sítě neoprávněných vniknutí. Je například poslední odměrné útoku [Memcached zneužití](https://www.wired.com/story/github-ddos-memcached/) , dopad na Githubu. Tento útok cílové UDP port 11211 a vygeneruje 1.35 Tb nebo s útoky svazku.

### <a name="protocol-attacks"></a>Protokol útoky

Protokol útokům cílový aplikační protokoly. Se pokusí použít až všechny dostupné prostředky v zařízení infrastruktury, jako jsou brány firewall, aplikační servery a nástroje pro vyrovnávání zatížení. Protokol útoky pomocí paketů, které jsou poškozené nebo obsahují abnormality protokolu. Tyto útoky pracovat odesláním velký počet otevřených žádostí, servery a jiné komunikace zařízení odpovědí a čekání odpověď na paket. Cíl se pokusí reagovat na otevřených žádostí, eventuálně vyvolá cílové chyby systému.

Nejčastější útoku DDoS založené na protokolu je záplava SYN TCP. V tento útok po sobě TCP SYN požadavků je určena cíl a lze ji zahlcovat. Cílem je zajistit cíle reagovat. Dyn se 2016 výpadek kromě se útoku také skládal z TCP SYN záplavy cílení na port 53 Dyn na servery DNS.

### <a name="resource-attacks"></a>Útoky prostředků

Útoky prostředků cíle aplikační vrstvu. Ve snaze zahlcovat cílovém systému se aktivovat procesy back-end. Prostředek útokům zneužití provoz, který vypadá normální, ale, že má u sebe náročná na prostředky procesoru dotazuje na server. Objem provozu, které jsou potřebné k vyčerpat prostředky je poměrně nižší než u jiných typů útoků. Provoz v rámci útoku prostředků je lišit od legitimní provoz, takže je obtížné zjistit. Nejběžnější útoky prostředků jsou v protokolu HTTP nebo HTTPS a služby DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Sdílené odpovědnost v cloudu

Podrobný strategie obrany je potřeba boj roste typy a vyspělosti útoků. Zabezpečení je sdílený odpovědnost mezi zákazníkem a Microsoft. Microsoft odkazuje jako [sdílenou odpovědnost modelu](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Následující obrázek znázorňuje tuto dělení zodpovědnosti.

![](media/azure-ddos-best-practices/image2.png)

Azure zákazníci využívat kontrola Naše osvědčené postupy a vytváření globálně distribuované aplikace navržené a testovat selhání.

## <a name="fundamental-best-practices"></a>Základní osvědčené postupy

Útoky DDoS a na růst. V následujícím oddílu najdete doporučený postup k vytvoření DDoS odolné služby v Azure.

### <a name="design-for-security"></a>Navrhování se zabezpečením

Zákazníci by měl zajistěte zabezpečení prioritu v průběhu celého cyklu aplikace, z návrhu a implementace a operací nasazení. Aplikace mohou mít chyby, které umožňují relativně nízký svazku vytvořených požadavků používat nadměrné množství prostředků, což vede k výpadku služby. K ochraně služby spuštěné v Microsoft Azure, Zákazníci by měl mít dostatečné povědomí o jejich architektury aplikace a musí zaměřit se na [5 pilíře kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars).
Zákazníci měli vědět typické provoz svazky modelu připojení mezi aplikací a další aplikace a služby na koncové body zveřejněné do veřejného Internetu.

Kromě toho máte jistotu, že aplikace je dostatečně odolné pro zpracování odepření služby zaměřený na vlastní aplikace je prvořadý význam. Zabezpečení a ochrana osobních údajů je integrována do platformy Azure, počínaje [životního cyklu SDL (Security Development)](https://www.microsoft.com/sdl/default.aspx). Procesu SDL adres zabezpečení v každé fázi vývoje a zajistí, že Azure se průběžně aktualizuje na i zabezpečení.

### <a name="design-for-scalability"></a>Návrh pro škálovatelnost

Škálovatelnost je schopnost systému zvládnout zvýšenou zátěž. Zákazníci musí návrh aplikací a [škálovat horizontálně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) poptávku zesilovací zatížení, konkrétně v události útoku DDoS. Pokud je aplikace závislá na jednu instanci služby, vytvoří jediný bod selhání. Zřizování více instancí zvyšuje odolnost a škálovatelnost.

Pro [Azure App Service](../app-service/app-service-value-prop-what-is.md), vyberte možnost [plán služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , nabízí několik instancí. Pro cloudové služby Azure, konfiguraci každé použití role [více instancí](../cloud-services/cloud-services-choose-me.md). Pro [virtuálních počítačů (VM) Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ujistěte se, že v architektuře virtuálního počítače obsahuje více než jeden virtuální počítač a že každý virtuální počítač je zahrnut v [skupinu dostupnosti](../virtual-machines/virtual-machines-windows-manage-availability.md). Doporučujeme používat [sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro možnostmi automatického škálování.

### <a name="defense-in-depth"></a>Důslednou

Rada za důslednou je řízení rizik pomocí různých Obranným strategie. Vrstvení obrany zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Doporučujeme vám, že zákazníci implementovat zabezpečené návrhy pro své aplikace pomocí integrované funkce platformy Azure.

Například riziko útoku se zvyšuje s velikost nebo útoku na aplikace. Snížení útoku se doporučuje prostřednictvím povolených na Zavřít dolů zveřejněné IP adres místo & naslouchající porty, které nejsou potřebné na služby Vyrovnávání zatížení ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[aplikace brány](../application-gateway/application-gateway-create-probe-portal.md)) nebo prostřednictvím [skupin zabezpečení (NSG) sítě.](../virtual-network/virtual-networks-nsg.md)
Můžete použít [služby značky](/virtual-network/security-overview.md) a [skupin zabezpečení aplikací](/virtual-network/security-overview.md) minimalizovat složitost pro vytvoření pravidla zabezpečení a konfigurace zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Zákazníci měli nasadit služby Azure v [virtuální sítě](../virtual-network/virtual-networks-overview.md) kdykoli je to možné. To umožňuje prostředky služby pro komunikaci prostřednictvím privátních IP adres. Služba Azure provoz z virtuální sítě používá veřejné IP adresy jako zdrojové IP adresy ve výchozím nastavení. Pomocí [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md) dojde k přepnutí provoz služby při přístupu ke službě Azure z virtuální sítě používá privátní adresy virtuální sítě zdrojové IP adresy.

Vidíte často zákazníka místních prostředků získávání napadení společně se svým prostředkům v Azure. Pokud se připojujete místní prostředí do Azure, doporučujeme vám, že minimalizujete ohrožení místních prostředků do veřejného Internetu. Škálování a pokročilých funkcí Ochrana proti útoku DDoS služby Azure můžete použít nasazením vaší dobře známé veřejné entity v Azure. Vzhledem k tomu, že tyto veřejně dostupné entity jsou často cíl útokům DDoS, je vložíte do Azure snižuje dopad na místních prostředků.

## <a name="azure-ddos-protection"></a>Azure ochrana proti útoku DDoS

Azure má dva DDoS nabídky služeb, které poskytují ochranu před útoky (vrstvy 3 a 4) - DDoS základní NAP a standardní ochrana proti útoku DDoS. 

### <a name="azure-ddos-basic-protection"></a>Azure základní ochrana proti útoku DDoS

Základní ochrany je integrována do Azure ve výchozím nastavení bez dalších poplatků. Úplné škálování a kapacitu sítě globálně nasazené Azure poskytuje ochranu proti běžné útoků síťové vrstvy prostřednictvím vždy na zmírnění provoz monitorování a v reálném čase. Ochrana DDoS Basic vyžaduje žádné změny konfigurace nebo aplikace uživatele. Všech služeb Azure včetně PaaS služby, jako je Azure DNS jsou chráněny základní ochrana proti útoku DDoS.

![](media/azure-ddos-best-practices/image3.png)

Ochrana proti útoku DDoS základní Azure se skládá z softwarové a hardwarové součásti. Rovině řízení softwaru rozhodne, kdy, kde, a jaký typ provozu měli řízenými prostřednictvím hardwaru zařízení, které analyzovat a odebrat provoz útoku. Řídicí provádí toto rozhodnutí založené na ochrana proti útoku DDoS celé infrastruktury *zásad*, který je staticky nastavit a všeobecně použitá pro všechny zákazníky Azure.

Například Ochrana proti útoku DDoS zásad určuje, jaký provoz svazek má být ochranu *aktivaci* (tedy klienta provoz by měl být směrován přes čištění zařízení) a následně, jak čištění zařízení musí *zmírnit* útoku.

DDoS ochrany základní služby Azure je zaměřený na infrastrukturu ochranu a ochranu na platformě Azure. Se snižuje provoz, když překročí rychlost, kterou je důležité, aby může mít vliv více zákazníků v prostředí s více klienty. Neposkytuje výstrahy nebo na zákazníka přizpůsobit zásady.

### <a name="azure-ddos-standard-protection"></a>Azure standardní ochrana proti útoku DDoS

Standardní ochrany obsahuje rozšířené funkce zmírnění DDoS a je automaticky přizpůsobená pro chránit konkrétní prostředky v Azure ve virtuální síti. Ochrana se snadno povolit na všechny nové nebo existující virtuální síť a nevyžaduje žádné aplikace ani změny prostředku. Má několik výhod oproti základní služby, včetně protokolování, výstrahy a telemetrie. Uvedené níže jsou klíče differentiators Azure DDoS ochrany standardní služby.

#### <a name="adaptive-realtime-tuning"></a>Ladění adaptivní v reálném čase

Azure DDoS ochrany základní služby pomáhá chránit Zákazníci ale chrání pouze, aby se zabránilo ostatních zákazníků, které mají vliv. Například, pokud služba se zřizuje pro typický objem legitimní příchozí provoz, která je menší než *aktivační událost míra* zásad ochrany DDoS celé infrastruktury, může se stát útoku DDoS na prostředky tohoto zákazníka. bez povšimnutí. Obecně platí komplexní povaha poslední útoky (například více vektoru DDoS) a také specifické pro aplikaci chování klientů volání pro na zákazníka, zásady vlastní ochrany.
To se provádí pomocí dvou statistiky:

1. Automatické učení (za IP) na zákazníka vrstvy 3 nebo 4 vzory přenosů dat, a
2. Minimalizace falešně pozitivních vzhledem k tomu, že to vyrovná se se zatížením významné množství provozu umožňuje škálování Azure.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS ochrany telemetrie, monitorování a výstrahy

S DDoS ochrany standardní zveřejňujeme bohatá telemetrie prostřednictvím [Azure monitorování](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) po dobu trvání útoku DDoS. Výstrahy lze nastavit pro libovolný metrik Azure monitorování vystavené ochrana proti útoku DDoS. Protokolování lze integrovat s Splunk (Azure Event Hubs), analýzy protokolů Azure a Azure Storage pro účely provádění pokročilých analýz prostřednictvím rozhraní Azure Diagnostics monitorování.

##### <a name="ddos-mitigation-policies"></a>Zásady omezení rizik DDoS

Na portálu Azure klikněte na tlačítko **monitorování** \> **metriky**. Na **metriky** obrazovky, vyberte skupinu prostředků, typ prostředku veřejné IP adresy a Azure veřejné IP adresy. Metriky DDoS se nebude zobrazovat v podokně dostupné metriky.

DDoS ochrany standardní platí **tři zásady zmírnění automaticky laděným (TCP SYN, TCP a UDP)** pro každou veřejnou IP adresu k chráněnému prostředku ve virtuální síti, který má DDoS povolena. Prahové hodnoty zásad můžete zobrazit výběrem metriky **'Příchozí pakety k aktivaci DDoS zmírnění'**.

![](media/azure-ddos-best-practices/image7.png)

Zásady prahové hodnoty jsou automaticky konfigurované prostřednictvím našich strojového učení profilace síťový provoz. Zmírnění dopadů DDoS dochází IP adresu napadené jenom v případě, že je překročena mezní hodnota zásad.

##### <a name="under-ddos-attack"></a>V části útoku DDoS

Pokud veřejnou IP adresu v útoku, hodnota metriky, v části DDoS útokům nebo Ne, přepne na 1 jsme provádět zmírnění dopadů na provoz útoku.

![](media/azure-ddos-best-practices/image8.png)

Doporučujeme, aby konfigurace výstrahy na tato metrika, chcete-li být upozorněni, když dojde active zmírnění DDoS provést na veřejnou IP adresu.

Další informace najdete v tématu [spravovat Azure DDoS ochrany standardní pomocí portálu Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Útoky prostředků

Specifické pro prostředek útoky na aplikační vrstvu, zákazníci měli nakonfigurovat webové aplikace brány Firewall (firewall webových aplikací) usnadňuje zabezpečení webových aplikací. Firewall webových aplikací kontroluje příchozí webové přenosy blokování SQL injekce skriptování mezi servery, DDoS a jiným útokům vrstvy 7. Azure poskytuje [firewall webových aplikací jako funkce Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) pro centralizované ochranu webových aplikací z běžných zneužití a ohrožení zabezpečení. Nejsou k dispozici od Azure partnerů, které může být vhodnější pro vaše potřeby prostřednictvím typy nabídek firewall webových aplikací [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

I brány firewall webových aplikací jsou náchylné k útokům vyčerpání odměrné & stavu. Důrazně doporučujeme zapnout ochranu DDoS Standard ve virtuální síti firewall webových aplikací k ochraně před odměrné & protokolu před útoky. Další informace najdete v části architektura odkaz.

### <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou velmi důležité pochopit, jak systém provede při útoku DDoS. Tento plánování a příprava také vám pomůže Navrhnout plán správy incidentů odpovědi.

Zákazníci by měl povolena DDoS ochrany standardní ve virtuální síti internet pohledu koncové body. Konfigurace výstrah DDoS vám pomůže uchovávat konstantní watchful oka na všechny potenciální útoky na infrastrukturu. Zákazníci měli sledovat nezávisle svých aplikací. Je třeba pochopit normální chování aplikace. Kroky musí být provedena v případě, že aplikace není chovají podle očekávání při útoku DDoS.

#### <a name="ddos-attacks-orchestration"></a>Orchestration útoky DDoS

Je vhodné pro testování vaší předpoklady o jak vašim službám reagují na útok, ještě před Odehrává se tím provádění pravidelných simulace. Během testování, ověření, že služby nebo aplikace i nadále fungovat podle očekávání, a bez narušení činnost koncového uživatele. Zjistí nesrovnalosti z hlediska technologie & proces a začlenit ve strategii odpovědi DDoS. Obecné doporučení je k provedení takových testů v testovací prostředí nebo v době mimo špičku. Chcete-li minimalizovat dopad na produkční prostředí.

Jsme spolupráci se společností [BreakingPoint cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) sestavení rozhraní, kde Azure zákazníci mohou generovat provoz proti ochrana proti útoku DDoS povoleno veřejné koncové body pro simulace. [Breakpoint – cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) simulace vám umožní:

- Ověření, jak Ochrana proti útoku DDoS Microsoft Azure chrání před útoky DDoS vašich prostředků Azure

- Optimalizace váš proces reakcí na incidenty v rámci útoku DDoS

- Dodržování předpisů DDoS dokumentu

- Cvičení týmům zabezpečení sítě

Počítačové bezpečnosti je houževnatým boj nutnosti konstantní inovace v obrany. Ochrana DDoS Standard Azure je nejmodernější nabídky nejmodernější nabídka pro zákazníky s účinným řešením pro zmírnění stále komplexní útoky DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Součástí strategie DDoS odpovědi

Ve většině případů při útoku DDoS je zaměřený na vašich prostředků Azure je minimální z hlediska koncového uživatele je nutný zásah. Přesto zařadit DDoS zmírnění jako součást strategie organizace reakcí na incidenty zajistí minimálním dopadem na kontinuity podnikových procesů.

### <a name="microsoft-threat-intelligence"></a>Analýzou hrozeb Microsoftu

Společnost Microsoft nemá je síť intelligence rozsáhlé hrozba, která používá souhrnný znalosti komunity rozšířené zabezpečení, která podporuje online službách společnosti Microsoft, partnery společnosti Microsoft a vztahy v rámci komunity zabezpečení Internetu. Jako poskytovatel kritické infrastruktury Microsoft dostane včasné upozornění o hrozbách, trvá základní analýzou hrozeb se naučili z jiných služeb Microsoft online services a globální zákazníků společnosti Microsoft. Všechny analýzou hrozeb Microsoftu jsou obsaženy zpět na jeho produktů ochrana proti útoku DDoS Azure.

Kromě toho společnosti Microsoft digitální činů jednotky (DCU) provede urážlivé strategie proti botnetů společný zdroj příkazy a ovládání útoku DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Provést vyhodnocení rizik vašich prostředků Azure

je nutné pochopit oboru riziko proti útoku DDoS průběžně. Zákazníci měli pravidelně žádat sami: nové veřejně dostupné prostředky Azure potřebují ochranu? Je jediný bod selhání v rámci služby? Jak lze služby izolované omezit dopad útoku, při stále zpřístupnění služeb zákazníkům platný? Existují virtuálních sítí, kde DDoS ochrany standardní by měla být zapnutá, ale není? Jsou mé služby aktivní/aktivní s převzetí služeb při selhání nad několika oblastmi?

### <a name="customer-ddos-response-team"></a>Zákazník DDoS týmu

Vytvoření týmu DDoS odpovědi je klíče krok zajištění efektivní a rychlou reakci na útok. Zákazníci musíte určit různé kontakty ve vaší organizaci, kteří budou dohlížejí plánování a provádění. Tým odpovědi DDoS by měl mít důkladné znalosti týkající se služby Azure DDoS ochrany standardní a mělo by být výstižný identifikace & zmírnění útok na spolupráci s různými interní a externí zákazníků, včetně tým podpory společnosti Microsoft podle potřeby.

Společnost Microsoft doporučuje zařadit cvičení plánování a simulace DDoS týmu, včetně testování jako normální součást dostupnost služeb a plánování kontinuity škálování. 

### <a name="during-an-attack"></a>Při útoku

Azure DDoS ochrany standardní bude zjišťovat a zmírňovat útoky DDoS bez jakéhokoli zásahu uživatele. Chcete-li zobrazit oznámení, pokud je active zmírnění dopadů pro chráněný veřejnou IP adresu, můžete [výstrahu](../virtual-network/ddos-protection-manage-portal.md) na metriku "v části DDoS útokům nebo ne". Můžete dál zkontrolujte a vytvářet výstrahy podle potřeby pro jiné metriky DDoS pochopit škále útoku, provoz probíhá vyřazování atd.

#### <a name="when-to-contact-microsoft-support"></a>Při kontaktování podpory společnosti Microsoft

- Pokud při útoku DDoS zjistíte, že vážně ke snížení výkonu chráněného prostředku nebo prostředku není k dispozici.

- Pokud se domníváte, že službu Ochrana proti útoku DDoS není chovají podle očekávání. Ochrana proti útoku DDoS služby pouze zahájí zmírnění dopadů, pokud se splní následující kritéria:

    - Hodnota metriky se zásady pro aktivaci zmírnění DDoS (SYN TCP/TCP/UDP) je nižší než přenosů přijatých na chráněný prostředek veřejné IP adresy.

- Pokud znáte, budete mít plánované virální událost, která povede k významnému zvýšení síťového provozu.

- Pokud má objekt actor ohrožena ke spuštění útoku DDoS proti vašich prostředků.

Pro kritické obchodní problémy, které mají vliv, vytvořte závažnost A [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroky útoku POST

Vždycky je strategii je dobré udělat postmortem po útoku a znovu nastavte strategie odpovědi DDoS podle potřeby. Co je třeba zvážit:

- Byl existuje přerušení služby nebo koncového uživatele zaznamenat nedostatek škálovatelná architektura?

- Služby nebo aplikace se vyskytla nejvíce?

- Jak efektivní byl strategie DDoS odpovědi a jak ho lze zlepšit další?

Pokud se domníváte, že jste v rámci útoku DDoS prosím eskalovat prostřednictvím vaší normální kanály podpory Azure.

## <a name="ddos-protection-reference-architectures"></a>DDoS ochrany referenční architektury

Zajišťuje DDoS ochrany standardní [pro služby, které jsou nasazeny ve virtuální síti.](../virtual-network/virtual-network-for-azure-services.md) Pro jiné služby výchozí základní ochrana proti útoku DDoS budou platit. Odkaz na konkrétní architektury, uvedených níže jsou uspořádány podle scénáře, architektura vzory seskupeny dohromady.

### <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních počítačů (Windows nebo Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikace spuštěné v zatížení vyrovnáváním virtuální počítače

Tato referenční architektura představuje sadu osvědčených postupů pro spuštění několika virtuálních počítačů s Windows ve škálovací sadě za nástrojem pro vyrovnávání zatížení, aby se zlepšila dostupnost škálovatelnost. Tato architektura lze použít pro všechny bezstavového zatížení, třeba webový server.

![](media/azure-ddos-best-practices/image9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. Existuje jediná veřejná IP adresa a internetový provoz je distribuovaný virtuálním počítačům pomocí nástroje pro vyrovnávání zatížení. Standardní ochrany DDoS je povoleno ve virtuální síti nástroje pro vyrovnávání zatížení Azure (internet), který má veřejné IP adresy s ním spojená.

Nástroje pro vyrovnávání zatížení rozloží příchozí žádosti Internetu pro instance virtuálních počítačů. Škálovatelné sady virtuálních počítačů povolit počtu virtuálních počítačů k škálovat příchozí nebo odchozí ručně nebo automaticky na základě předdefinovaných pravidel. To je důležité, pokud je v útoku DDoS. Pokus [článku](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), další informace o tuto referenční architekturu.

#### <a name="applications-running-on-windows-n-tier"></a>Aplikace běžící na Windows N-vrstvá

N-vrstvou architekturu je možné implementovat mnoha způsoby. Výše uvedený diagram znázorňuje typickou třívrstvou webovou aplikaci. Tato architektura staví na [spustit Vyrovnávání zatížení sítě virtuálních počítačů pro škálovatelnost a dostupnost](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![](media/azure-ddos-best-practices/image10.png)

V architektuře výše DDoS ochrany standardní je povoleno ve virtuální síti. Všechny veřejné IP adresy ve virtuální síti se ochrana proti útoku DDoS vrstvy 3 nebo Layer4. Pro ochranu vrstvy 7 musí být nasazené aplikace brána firewall webových aplikací SKU. Odkazovat na [to](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) článku, další informace o tuto referenční architekturu.

#### <a name="paas-web-application"></a>PaaS webové aplikace

Tuto referenční architekturu ukazuje spouštění aplikací Azure App Service v jedné oblasti. Tato architektura ukazuje nastavte osvědčené postupy pro webové aplikace, která používá [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblast je nastavený pro scénáře převzetí služeb při selhání.

![](media/azure-ddos-best-practices/image11.png)

Správce provozu směruje příchozí požadavky na aplikační brána v jednom z oblastí. Během normálních operací směruje požadavky na aplikační brána v aktivní oblasti. Pokud tuto oblast stane nedostupným, Traffic Manager převezme aplikační brány v úsporném režimu oblasti.

Všechny přenosy z Internetu určené do webové aplikace se směruje na [Application Gateway veřejnou IP adresu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) prostřednictvím Traffic Manager. V tomto scénáři app service (webové aplikace) sám není přímo externě čelí a je chráněn Application Gateway. Doporučuje se konfigurace aplikace brány firewall webových aplikací SKU (zabránit režim) k ochraně před útoky vrstvy 7 (HTTP nebo HTTPS/Websocket). Kromě toho jsou nakonfigurovány webové aplikace, aby [přijímal provoz pouze ze služby Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresu.

Odkazovat na [to](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) článku, další informace o tuto referenční architekturu.

### <a name="mitigation-for-non-web-paas-services"></a>Zmírnění dopadů pro jiné webové PaaS služby

#### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Tuto referenční architekturu zobrazuje konfiguraci ochrany standardní DDoS pro [clusteru HDInsight](https://docs.microsoft.com/azure/hdinsight/) v Azure. Musíte zajistit, že HDInsight cluster je propojena k virtuální síti a ve virtuální síti je povolena ochrana proti útoku DDoS.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

V této architektuře se provoz určený ke clusteru HDInsight z Internetu se směruje na veřejné IP adresy přidružené k vyrovnávání zatížení brány HDInsight. Nástroje pro vyrovnávání zatížení brána pak odešle provoz hlavních uzlech nebo uzlů pracovního procesu přímo. Zadané DDoS ochrany standardní je povolena ve virtuální síti HDInsight, ochrana proti útoku DDoS vrstvy 3 nebo Layer4 získá všechny veřejné IP adresy ve virtuální síti. Výše uvedené referenční architektury je možné kombinovat s N-vrstvy nebo více-Region referenční architektury.

Další podrobnosti o této referenční architektuře, najdete v části [rozšíření Azure HDInsight pomocí Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentaci.

### <a name="azure-api-management"></a>Azure API management

Tuto referenční architekturu chrání veřejný koncový bod [API management](../api-management/api-management-key-concepts.md) prostředků publikování rozhraní API pro zákazníky mimo organizaci. APIM musí být nasazené v externí virtuální síť k povolení ochrany DDoS.

![](media/azure-ddos-best-practices/image15.png)

Nakonfigurováním externí virtuální síť jsou přístupné z veřejného Internetu prostřednictvím Vyrovnávání zatížení veřejnou portál brány & vývojáře API Management. V této architektuře DDoS ochrany standardní je povolena na externí virtuální síť APIM virtuální sítě. Provoz se směruje z Internetu veřejnou IP adresu APIM, který je chráněn před útoky ze sítě vrstvy 3 nebo Layer4. Chránit před útoky vrstvy 7 protokolu HTTP nebo HTTPs, můžete nakonfigurovat služby Application Gateway v režimu firewall webových aplikací.

Seznam Další služby, které jsou nasazeny ve virtuální síti a může být nakonfigurovaná pro zachovaný DDoS ochrany standardní [zde](../virtual-network/virtual-network-for-azure-services.md). Standardní ochrany DDoS podporuje pouze prostředky Azure Resource Manager. *Vložený aplikace služby prostředí řízení, nasazení ve virtuální síti s veřejnou IP adresu není nativně podporované.* Podrobné informace o ochraně prostředí App Service Environment naleznete v této části.

## <a name="next-steps"></a>Další postup

* [Stránky Azure DDoS ochrany produktu](https://azure.microsoft.com/services/ddos-protection/)

* [Blog Azure DDoS ochrany](http://aka.ms/ddosblog)

* [Dokumentace k Azure ochrana proti útoku DDoS ](../virtual-network/ddos-protection-overview.md)
