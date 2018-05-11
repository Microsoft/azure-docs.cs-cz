---
title: Azure ochrana proti útoku DDoS osvědčené postupy a referenční architektury | Microsoft Docs
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
ms.openlocfilehash: 042dd4876a63e5881e67456b449570b01cb967a5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Ochrana proti útoku DDoS Azure: Osvědčené postupy a referenční architektury

Tento článek je vedoucím pracovníkům IT a pracovníky zabezpečení. Se očekává, že jste obeznámeni s Azure, sítě a zabezpečení.

Návrh distribuovaných útok DoS (Denial) vyžaduje odolnost proti chybám plánování a návrhu pro různé režimy selhání. Tento článek obsahuje osvědčené postupy pro navrhování aplikací v Azure pro odolnost proti útokům DDoS.

## <a name="types-of-attacks"></a>Typy útoků

DDoS je typ útoku, který se pokouší vyčerpat prostředky aplikace. Cílem je mít vliv na dostupnost aplikace a schopnost zpracovávat oprávněné požadavky. Útoky jsou čím dál víc sofistikovaná a větší velikost a dopad. Útoky DDoS můžete cílit na všechny koncový bod, který je veřejně dostupný prostřednictvím Internetu.

Azure poskytuje trvalou ochranu proti útokům DDoS. Tato ochrana je integrována do platformy Azure ve výchozím nastavení a v žádné peníze navíc. 

Kromě základních ochrana proti útoku DDoS v platformě [Azure DDoS ochrany standardní](https://azure.microsoft.com/services/ddos-protection/) poskytuje pokročilé možnosti zmírnění DDoS před útoky ze sítě. Je automaticky nastaven chránit konkrétní prostředky v Azure. Ochrana je jednoduché, aby při vytváření nové virtuální sítě. To lze provést i po vytvoření a nevyžaduje žádné změny aplikace nebo prostředku.

![Role ochrana proti útoku DDoS Azure při ochraně před případným útočníkem zákazníků a virtuální sítě](media/azure-ddos-best-practices/image1.png)

Útoky DDoS můžete rozdělit do tří kategorií: odměrné, protokol a prostředků.

### <a name="volumetric-attacks"></a>Odměrné útoky

Odměrné útoky jsou nejběžnější typ útoku DDoS. Jsou odměrné útoky hrubou silou osoby, které cílí sítě a transportní vrstvy. Pokusu vyčerpat prostředky, jako je síťová propojení. 

Tyto útoky často používají více nakažené systémy k vyplnění vrstvy sítě s zdánlivě legitimní přenosy. Používají protokolů síťové vrstvy, například zprávy protokolu ICMP (Internet Control), protokolu UDP (User Datagram) a protokolu TCP (Transmission Control).

Útoky DDoS nejčastěji používané síťové vrstvy jsou TCP SYN zahlcení odezvu protokolu ICMP, UDP zahlcení, DNS, a NTP zesílení útoků. Tento typ útoku umožňuje nejenom dojít k přerušení služby, ale také jako smokescreen pro více nefarious a cílové sítě neoprávněných vniknutí. Je například poslední odměrné útoku [Memcached zneužití](https://www.wired.com/story/github-ddos-memcached/) , vliv na Githubu. Tento útok cílové UDP port 11211 a vygeneruje 1.35 Tb nebo s útoky svazku.

### <a name="protocol-attacks"></a>Protokol útoky

Protokol útokům cílový aplikační protokoly. Pokouší se spotřebovávají všechny dostupné prostředky v zařízení infrastruktury, jako jsou brány firewall, aplikační servery a nástroje pro vyrovnávání zatížení. Protokol útoky pomocí paketů, které jsou poškozené nebo obsahují abnormality protokolu. Tyto útoky fungovat na odeslání velkého počtu otevřených žádostí, serverů a dalších zařízení komunikace odpovědět a čekat na odpověď paketů. Cíl pokusí reagovat na otevřených žádostí, eventuálně vyvolá chybu systému.

Nejčastější útoku DDoS založené na protokolu je záplava TCP SYN. V tento útok po sobě TCP SYN požadavků pokusí zahlcovat cíl. Cílem je zajistit cíle reagovat. Dyn se 2016 výpadek kromě se útoku, se skládal z TCP SYN floods tento cílový port 53 Dyn na servery DNS.

### <a name="resource-attacks"></a>Útoky prostředků

Útoky prostředků cíle aplikační vrstvu. Ve snaze zahlcovat systému budou aktivovat procesy back-end. Prostředek útokům zneužití provoz, který vypadá normální, ale, že má u sebe náročná na prostředky procesoru dotazuje na server. Objem provozu, které jsou potřebné k vyčerpat prostředky je nižší než u jiných typů útoků. Provoz v rámci útoku prostředků je lišit od legitimní provoz, takže je obtížné zjistit. Nejběžnější útoky prostředků jsou v protokolu HTTP nebo HTTPS a služby DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Sdílené odpovědnost v cloudu

Strategie obrany do hloubky pomáhá boj roste různých a vyspělosti útoků. Zabezpečení je sdílený odpovědnost mezi zákazníkem a Microsoft. Microsoft passphrase [sdílenou odpovědnost modelu](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Následující obrázek znázorňuje tuto dělení zodpovědnosti:

![Odpovědnosti zákazníka a Azure](media/azure-ddos-best-practices/image2.png)

Azure zákazníci využívat kontrola osvědčených postupů Microsoftu a vytváření globálně distribuované aplikace, které jsou vytvořeny a testována selhání.

## <a name="fundamental-best-practices"></a>Základní osvědčené postupy

Doporučený postup k vytvoření odolné DDoS služby v Azure naleznete v následujících oddílech.

### <a name="design-for-security"></a>Navrhování se zabezpečením

Zajistěte, aby zabezpečení prioritu v průběhu celého cyklu aplikace, z návrhu a implementace a operací nasazení. Aplikace mohou mít chyby, které umožňují relativně nízký objem požadavků používat nadměrné množství prostředků, což vede k výpadku služby. 

K ochraně služby spuštěné v Microsoft Azure, by měl mít dostatečné povědomí o architektuře aplikace a zaměřit se na [pět pilíře kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars).
Typické provoz svazky, byste měli vědět modelu připojení mezi aplikací a jinými aplikacemi a koncové body služby, které jsou umístěny do veřejného Internetu.

Je nejdůležitější zajistit, aby aplikace dostatečně odolné pro zpracování odepření služby, který je zaměřený na vlastní aplikace. Zabezpečení a ochrana osobních údajů jsou součástí platformy Azure, počínaje [životního cyklu SDL (Security Development)](https://www.microsoft.com/sdl/default.aspx). Procesu SDL adres zabezpečení v každé fázi vývoje a zajistí, že Azure se průběžně aktualizuje na i zabezpečení.

### <a name="design-for-scalability"></a>Návrh pro škálovatelnost

Škálovatelnost je, jak dobře může systém zpracování zvýšené zátěže. Je třeba navrhnout vašich aplikací [škálovat horizontálně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) poptávku zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud je aplikace závislá na jednu instanci služby, vytvoří jediný bod selhání. Zřizování více instancí díky systém pružnější a větší škálovatelnost.

Pro [Azure App Service](../app-service/app-service-value-prop-what-is.md), vyberte možnost [plán služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , nabízí několik instancí. Pro cloudové služby Azure, konfiguraci každé použití role [více instancí](../cloud-services/cloud-services-choose-me.md). Pro [virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ujistěte se, že vaše Architektura virtuálního počítače (VM) obsahuje více než jeden virtuální počítač a že každý virtuální počítač je zahrnut v [skupinu dostupnosti](../virtual-machines/virtual-machines-windows-manage-availability.md). Doporučujeme používat [sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro možnostmi automatického škálování.

### <a name="defense-in-depth"></a>Důslednou

Rada za důslednou je řízení rizik pomocí různých Obranným strategie. Vrstvení obrany zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Doporučujeme, abyste implementovat zabezpečené návrhy pro vaše aplikace pomocí integrované funkce platformy Azure.

Například zvyšuje riziko útoku s velikostí (*surface area*) aplikace. Můžete snížit plochy s využitím povolených uzavřeny zveřejněné adresní prostor IP adres a naslouchání portům, které nejsou potřebné na služby Vyrovnávání zatížení ([Vyrovnávání zatížení Azure](../load-balancer/load-balancer-get-started-internet-portal.md) a [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Skupin zabezpečení (Nsg) sítě](../virtual-network/virtual-networks-nsg.md) jsou jiný způsob, jak omezit možnost útoku.
Můžete použít [služby značky](/virtual-network/security-overview.md) a [skupin zabezpečení aplikací](/virtual-network/security-overview.md) minimalizovat složitost pro vytváření pravidel zabezpečení a konfigurace zabezpečení sítě, jako přirozené rozšíření struktury aplikace.

Měli byste nasadit služby Azure v [virtuální sítě](../virtual-network/virtual-networks-overview.md) kdykoli je to možné. Tento postup umožňuje prostředky služby pro komunikaci prostřednictvím privátních IP adres. Služba Azure provoz z virtuální sítě používá veřejné IP adresy jako zdrojové IP adresy ve výchozím nastavení. Pomocí [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md) dojde k přepnutí provoz služby při jejich získáváte přístup k službě Azure z virtuální sítě používá privátní adresy virtuální sítě zdrojové IP adresy.

Vidíte často zákazníků místních prostředků získávání napadení spolu s jejich prostředky v Azure. Pokud se připojujete místní prostředí do Azure, doporučujeme vám, že minimalizujete ohrožení místních prostředků do veřejného Internetu. Škálování a rozšířené možnosti ochrany DDoS Azure můžete použít nasazením vaší dobře známé veřejné entity v Azure. Protože tyto veřejně dostupné entity jsou často cíl útokům DDoS, je vložíte do Azure snižuje dopad na místních prostředků.

## <a name="azure-offerings-for-ddos-protection"></a>Nabídky Azure pro ochrana proti útoku DDoS

Azure má dva DDoS nabídky služeb, které zajišťují ochranu před útoky ze sítě (vrstvy 3 a 4): DDoS ochrany Basic a Standard ochrany DDoS. 

### <a name="ddos-protection-basic"></a>DDoS ochrany Basic

Základní ochrany je integrována do Azure ve výchozím nastavení bez dalších poplatků. Škálování a kapacitu globálně nasazené síť Azure poskytuje ochranu proti běžné síťové vrstvy útoků prostřednictvím zmírnění vždy na provoz monitorování a v reálném čase. Základní ochrany DDoS vyžaduje žádné změny konfigurace nebo aplikace uživatele. Základní ochrany DDoS pomáhá chránit všech služeb Azure, včetně PaaS služby, jako je Azure DNS.

![Mapování reprezentace síť Azure, se text "globální DDoS zmírnění přítomnosti" a "Úvodní DDoS zmírnění kapacity"](media/azure-ddos-best-practices/image3.png)

Základní ochrana proti útoku DDoS v Azure se skládá z softwarové a hardwarové součásti. Rovině řízení softwaru rozhodne, kdy, kde, a jaký typ provozu měli řízenými prostřednictvím hardwaru zařízení, které analyzovat a odebrat provoz útoku. Řídicí provádí toto rozhodnutí založené na ochrana proti útoku DDoS celé infrastruktury *zásad*. Tato zásada je staticky nastavit a všeobecně použitá pro všechny zákazníky Azure.

Například Ochrana proti útoku DDoS zásad určuje, jaký provoz svazek má být ochranu *aktivaci.* (To znamená, klienta provoz by měl být směrován přes čištění zařízení.) Pak tato zásada určuje, jak by měla čištění dat zařízení *zmírnit* útoku.

Službu Azure DDoS ochrany na úrovni Basic je zaměřený na ochranu infrastruktury a ochranu na platformě Azure. Se snižuje provoz, když překročí rychlost, kterou je proto důležité, může to ovlivnit několik zákazníků ve víceklientském prostředí. Neposkytuje výstrahy nebo na zákazníka přizpůsobit zásady.

### <a name="ddos-protection-standard"></a>Standardní ochrany DDoS

Standardní ochrany poskytuje rozšířené funkce zmírnění DDoS. Toto pravidlo je přizpůsobená automaticky k ochraně vaší konkrétní prostředky ve virtuální síti Azure. Ochrana se snadno povolit na všechny nové nebo existující virtuální síť a nevyžaduje žádné změny aplikace nebo prostředku. Má několik výhod oproti základní služby, včetně protokolování, výstrahy a telemetrie. Následující oddíly popisují klíčové funkce Azure DDoS ochrany standardní služby.

#### <a name="adaptive-real-time-tuning"></a>Adaptivní v reálném čase ladění

Službu Azure DDoS ochrany základní pomáhá chránit zákazníků a zabránit ovlivňuje ostatním zákazníkům. Například, pokud služba se zřizuje pro typický objem legitimní příchozí provoz, která je menší než *aktivační událost míra* zásady ochrana proti útoku DDoS celé infrastruktury, může přejít útoku DDoS na prostředky tohoto zákazníka. bez povšimnutí. Obecně platí složitost poslední útoky (například více vektoru DDoS) a specifické pro aplikaci chování klientů volání pro na zákazníka, zásady vlastní ochrany. Služba provede tato přizpůsobení pomocí dvou statistiky:

- Automatické učení vzory přenosů dat (podle IP) na zákazníka pro vrstvy 3 a 4.

- Minimalizace falešně pozitivních zjištění, vzhledem k tomu, že škále Azure umožňuje vyrovná se se zatížením významné množství provozu.

![Diagram jak DDoS ochrany standardní funguje, se "Zásady generací" v kroužku](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Ochrana proti útoku DDoS telemetrie, monitorování a výstrahy

DDoS ochrany standardní zpřístupní bohatá telemetrie prostřednictvím [Azure monitorování](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) po dobu trvání útoku DDoS. Můžete nakonfigurovat výstrahy pro libovolný Azure monitorování metriky, které používá ochrana proti útoku DDoS. Protokolování lze integrovat s Splunk (Azure Event Hubs), analýzy protokolů Azure a Azure Storage pro účely provádění pokročilých analýz prostřednictvím rozhraní Azure Diagnostics monitorování.

##### <a name="ddos-mitigation-policies"></a>Zásady omezení rizik DDoS

Na portálu Azure vyberte **monitorování** > **metriky**. V **metriky** podokně, vyberte skupinu prostředků, vyberte typ prostředku **veřejnou IP adresu**a vyberte Azure veřejné IP adresy. Metriky DDoS jsou viditelné v **dostupné metriky** podokně.

DDoS ochrany standardní použije tři zásady zmírnění automaticky laděným (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu k chráněnému prostředku ve virtuální síti, který má DDoS povolena. Prahové hodnoty zásad můžete zobrazit výběrem metriky **příchozí pakety k aktivaci DDoS zmírnění**.

![K dostupným metrikám a metriky graf](media/azure-ddos-best-practices/image7.png)

Zásady prahové hodnoty jsou automaticky konfigurované prostřednictvím profilace provoz sítě na základě learning počítače. Zmírnění dopadů DDoS dochází IP adresu napadené jenom v případě, že je překročena mezní hodnota zásad.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrika pro IP adresu v útoku DDoS

Pokud je veřejná IP adresa v části útoku, hodnoty pro metriku **útoku DDoS pod nebo není** jako ochrana proti útoku DDoS provede zmírnění dopadů na útok provoz se změní na 1.

![Metrika "V části DDoS útoku nebo není" a graf](media/azure-ddos-best-practices/image8.png)

Doporučujeme, abyste konfigurace výstrahy na tato metrika. Potom obdržíte po active zmírnění dopadů DDoS provést na veřejnou IP adresu.

Další informace najdete v tématu [spravovat Azure DDoS ochrany standardní pomocí portálu Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brány firewall webových aplikací pro útoky prostředků

Specifické pro prostředek útoky na aplikační vrstvu, měli byste nakonfigurovat brány firewall webových aplikací (firewall webových aplikací) usnadňuje zabezpečení webových aplikací. Firewall webových aplikací kontroluje příchozí webové přenosy zablokujte vložení SQL, webů skriptování, DDoS a jiným útokům vrstvy 7. Azure poskytuje [firewall webových aplikací jako funkce Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md) pro centralizované ochranu webových aplikací z běžných zneužití a ohrožení zabezpečení. Nejsou k dispozici od Azure partnerů, které může být vhodnější pro vaše potřeby prostřednictvím jiné nabídky firewall webových aplikací [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

I brány firewall webových aplikací jsou náchylné k útokům vyčerpání odměrné a stavu. Důrazně doporučujeme povolení DDoS ochrany standardní ve virtuální síti firewall webových aplikací k ochraně před odměrné a protokol útoků. Další informace najdete v tématu [ochrana proti útoku DDoS referenční architektury](#ddos-protection-reference-architectures) části.

### <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou velmi důležité pochopit, jak systém provede při útoku DDoS. Navrhování plán správy incidentů odpovědi je součástí této úsilí.

Pokud máte DDoS ochrany standardní, ujistěte se, že je povolena ve virtuální síti internetových koncových bodů. Konfigurace výstrah DDoS můžete neustále podívejte se na všechny potenciální útoky na infrastrukturu. 

Vaše aplikace je třeba sledovat nezávisle. Pochopte normální chování aplikace. Připravte tak, aby fungoval, pokud aplikace není chovají podle očekávání při útoku DDoS.

#### <a name="testing-through-simulations"></a>Testování prostřednictvím simulace

Je vhodné pro testování vaší předpoklady o tom, jak vaše služby reagovat na útok provádění pravidelných simulace. Během testování, ověření, že služby nebo aplikace i nadále fungovat podle očekávání, a bez přerušení uživatelského rozhraní. Zjistí nesrovnalosti z hlediska technologie i proces a začleňte je v odpovědi strategie DDoS. Doporučujeme provést tyto testy, v pracovním prostředí nebo v době mimo špičku. Chcete-li minimalizovat dopad na produkční prostředí.

Jsme spolupráci se společností [BreakingPoint cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) k sestavení rozhraní, kde Azure zákazníci mohou generovat provoz proti povolena ochrana proti útoku DDoS veřejné koncové body pro simulace. Můžete použít [BreakingPoint cloudu](https://www.ixiacom.com/products/breakingpoint-cloud) simulace na:

- Ověření, jak Ochrana proti útoku DDoS Azure pomáhá chránit před útoky DDoS vašich prostředků Azure.

- Optimalizujte váš proces reakcí na incidenty v rámci útoku DDoS.

- Zdokumentujte DDoS dodržování předpisů.

- Cvičení týmům zabezpečení sítě.

Počítačové bezpečnosti vyžaduje konstantní inovace v obrany. Azure DDoS Standard ochrana se stav systému – Nejaktuálnější nabízí účinným řešením pro zmírnění stále komplexní útokům DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Součástí strategie DDoS odpovědi

Útoku DDoS zacílený prostředků Azure vyžaduje minimem zásahů z hlediska uživatelů. Přesto zařadit DDoS zmírnění jako součást strategie reakcí na incidenty pomáhá minimalizovat dopad na kontinuity podnikových procesů.

### <a name="microsoft-threat-intelligence"></a>Analýzou hrozeb Microsoftu

Společnost Microsoft nemá síť intelligence rozsáhlé hrozeb. Tato síť používá souhrnný znalosti komunity rozšířené zabezpečení, která podporuje služeb Microsoft online services, partnery společnosti Microsoft a vztahy v rámci komunity zabezpečení Internetu. 

Jako poskytovatel kritické infrastruktury Microsoft obdrží včasné upozornění o hrozbách. Microsoft shromažďuje analýzou hrozeb z jeho online služeb a jeho základní globální zákazníka. Microsoft zahrnuje všechny tento analýzou hrozeb zpět do produktů ochrana proti útoku DDoS Azure.

Jednotky služby společnosti Microsoft digitální činů (DCU) také provede urážlivé strategie proti botnetů. Botnetů jsou běžné zdroj příkazy a ovládání útoku DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Vyhodnocení rizik vašich prostředků Azure

je nutné pochopit oboru riziko proti útoku DDoS průběžně. Pravidelně položte si: 
- Nové veřejně dostupné prostředky Azure potřebujete ochrany?

- Je jediný bod selhání v rámci služby? 

- Jak lze služby izolované omezit dopad útoku, při stále zpřístupnění služeb zákazníkům platný?

- Existují virtuálních sítí, kde DDoS ochrany standardní by měla být zapnutá, ale není? 

- Jsou mé služby aktivní/aktivní s převzetí služeb při selhání nad několika oblastmi?

### <a name="customer-ddos-response-team"></a>Zákazník DDoS týmu

Vytvoření týmu DDoS odpovědi je klíče krok reagovat na útok rychle a efektivně. Identifikujte kontakty ve vaší organizaci, kteří budou dohlížejí plánování a provádění. Tento tým odpovědi DDoS třeba důkladně porozumět službu Azure DDoS ochrany standardní. Ujistěte se, že tým může zjišťovat a zmírňovat útoku ve spolupráci s interních a externích zákazníků, včetně tým podpory společnosti Microsoft.

Pro váš tým odpovědi DDoS doporučujeme použít simulace cvičení jako normální součást dostupnost služeb a plánování kontinuity. Těchto cvičení by měla obsahovat testování škálování.

### <a name="alerts-during-an-attack"></a>Výstrahy při útoku

Azure DDoS ochrany standardní identifikuje a snižuje útoky DDoS bez jakéhokoli zásahu uživatele. Chcete-li zobrazit oznámení, pokud je active zmírnění dopadů pro chráněný veřejnou IP adresu, můžete [výstrahu](../virtual-network/ddos-protection-manage-portal.md) na metriku **útoku DDoS pod nebo není**. Můžete vytvářet výstrahy pro jiné metriky DDoS pochopit škále útoku, provoz probíhá vyřazování a další podrobnosti.

#### <a name="when-to-contact-microsoft-support"></a>Při kontaktování podpory společnosti Microsoft

- Při útoku DDoS zjistíte, že výkon chráněného prostředku je vážně sníženou nebo prostředek není k dispozici.

- Si myslíte, že službu Ochrana proti útoku DDoS není chovají podle očekávání. 

  Spuštění služby ochrana proti útoku DDoS zmírnění pouze, pokud hodnota metriky **zásady pro aktivaci zmírnění DDoS (SYN TCP/TCP/UDP)** je nižší než přenosů přijatých na chráněný prostředek veřejné IP.

- Plánování virální událost, která výrazně zvýší síťový provoz.

- Objekt actor má ohrožena ke spuštění útoku DDoS proti vašich prostředků.

Pro vytvoření útoků, které mají dopad na kritické obchodní, závažnost A [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroky po útoku

Vždycky je strategii je dobré udělat postmortem po útoku a podle potřeby upravte strategie DDoS odpovědi. Co je třeba zvážit:

- Byl existuje přerušení služby nebo uživatele zaznamenat nedostatek škálovatelná architektura?

- Služby nebo aplikace se vyskytla nejvíce?

- Jak efektivní byl strategie DDoS odpověď, a jak ji bylo možné zlepšit?

Pokud se domníváte, že jste v části útoku DDoS, eskalovat prostřednictvím vaší normální kanály podpory Azure.

## <a name="ddos-protection-reference-architectures"></a>Ochrana proti útoku DDoS referenční architektury

Zajišťuje DDoS ochrany standardní [pro služby, které jsou nasazeny ve virtuální síti](../virtual-network/virtual-network-for-azure-services.md). Pro jiné služby použije výchozí DDoS ochrany základní služba. Následující odkaz architektury jsou uspořádány podle scénáře, architektura vzory seskupeny dohromady.

### <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních počítačů (Windows nebo Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikace běžící na virtuálních počítačích s vyrovnáváním zatížení

Tuto referenční architekturu zobrazuje sadu osvědčené postupy pro spuštění několika virtuálních počítačů Windows ve škále nastavit za službou Vyrovnávání zatížení, zvýšit dostupnost a škálovatelnost. Tato architektura lze použít pro všechny bezstavového zatížení, třeba webový server.

![Diagram architektury odkaz pro aplikace běžící na virtuálních počítačích s vyrovnáváním zatížení](media/azure-ddos-best-practices/image9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. Existuje jednu adresu IP a přenosy z Internetu je distribuován do virtuálních počítačů pomocí služby Vyrovnávání zatížení. Standardní ochrany DDoS je povoleno ve virtuální síti nástroje pro vyrovnávání zatížení Azure (internet), který má veřejné IP adresy s ním spojená.

Nástroje pro vyrovnávání zatížení rozloží příchozí žádosti Internetu pro instance virtuálních počítačů. Sady škálování virtuálního počítače povolit počtu virtuálních počítačů k škálovat příchozí nebo odchozí ručně nebo automaticky na základě předdefinovaných pravidel. To je důležité, pokud je v útoku DDoS. Další informace o této referenční architektuře, najdete v části [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikace běžící v systému Windows N-vrstvá

N-vrstvou architekturu je možné implementovat mnoha způsoby. Následující diagram znázorňuje Typická třívrstvá webové aplikace. Tato architektura staví na článek [spustit Vyrovnávání zatížení sítě virtuálních počítačů pro škálovatelnost a dostupnost](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![Diagram architektury odkaz pro aplikace běžící v systému Windows N-vrstvá](media/azure-ddos-best-practices/image10.png)

V této architektuře DDoS ochrany standardní je povoleno ve virtuální síti. Všechny veřejné IP adresy ve virtuální síti získat ochrana proti útoku DDoS pro vrstvy 3 a 4. Pro ochranu vrstvy 7 nasaďte Aplikační brána v SKU firewall webových aplikací. Další informace o této referenční architektuře, najdete v části [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS webové aplikace

Tuto referenční architekturu ukazuje spouštění aplikací Azure App Service v jedné oblasti. Tato architektura zobrazuje sadu osvědčené postupy pro webové aplikace, která používá [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblast je nastavený pro scénáře převzetí služeb při selhání.

![Diagram architektury odkaz pro webovou aplikaci PaaS](media/azure-ddos-best-practices/image11.png)

Azure Traffic Manager směruje příchozí požadavky na aplikační brána v jednom z oblastí. Během normálních operací směruje požadavky na aplikační brána v oblasti aktivní. Pokud tuto oblast stane nedostupným, Traffic Manager převezme aplikační brány v oblasti pohotovostní.

Všechny přenosy z Internetu určených k webové aplikaci se směruje na [Application Gateway veřejnou IP adresu](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) prostřednictvím Traffic Manager. V tomto scénáři app service (webové aplikace) sám není přímo externě čelí a je chráněn Application Gateway. 

Doporučujeme nakonfigurovat aplikace skladová položka brány firewall webových aplikací (zabránit režim) k ochraně před útoky vrstvy 7 (HTTP nebo HTTPS/WebSocket). Kromě toho jsou nakonfigurovány webové aplikace, aby [přijímal pouze provoz ze služby Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresu.

Další informace o tuto referenční architekturu najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Zmírnění dopadů pro jiné webové PaaS služby

#### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Zobrazí tuto referenční architekturu DDoS ochrany Standard pro konfiguraci [cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Ujistěte se, že clusteru HDInsight je propojena k virtuální síti a že je povolena ochrana proti útoku DDoS ve virtuální síti.

!["HDInsight" a "Upřesňující nastavení" podokna s nastavení virtuální sítě](media/azure-ddos-best-practices/image12.png)

![Výběr pro povolení ochrany DDoS](media/azure-ddos-best-practices/image13.png)

Přenosy určené do clusteru HDInsight z Internetu se v této architektuře směruje na veřejné IP adresy přidružené k vyrovnávání zatížení brány HDInsight. Nástroje pro vyrovnávání zatížení brána pak odešle provoz hlavních uzlech nebo uzlů pracovního procesu přímo. Protože DDoS ochrany standardní je povoleno ve virtuální síti HDInsight, získat všechny veřejné IP adresy ve virtuální síti ochrana proti útoku DDoS pro vrstvy 3 a 4. Tuto referenční architekturu zkombinovat s N-vrstvá a více oblast referenční architektury.

Další informace o této referenční architektuře, najdete v článku [rozšíření Azure HDInsight pomocí Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentaci.

### <a name="azure-api-management"></a>Azure API Management

Tento referenční architektuře, která pomáhá chránit veřejný koncový bod [Azure API Management](../api-management/api-management-key-concepts.md) prostředků publikování rozhraní API pro zákazníky mimo organizaci. Nasazení v externí virtuální síť k povolení ochrany DDoS API Management.

![Diagram architektury reference pro API Management](media/azure-ddos-best-practices/image15.png)

Při konfiguraci externí virtuální síť, jsou přístupné z veřejného Internetu prostřednictvím Vyrovnávání zatížení veřejnou API brány a vývojáře portálu pro správu. V této architektuře DDoS ochrany standardní Management je povolená na externí virtuální sítě pro rozhraní API. Provoz se směruje z Internetu na veřejnou IP adresu služby API Management, který je chráněn před útoky ze sítě na vrstvy 3 a 4. Chránit před útoky vrstvy 7 protokolu HTTP nebo HTTPS, můžete nakonfigurovat Aplikační brána v režimu firewall webových aplikací.

Seznam dalších služeb, které jsou nasazeny ve virtuální síti a mohou být konfigurovány pro standardní DDoS ochrany najdete v tématu [v tomto článku](../virtual-network/virtual-network-for-azure-services.md). Standardní ochrany DDoS podporuje pouze prostředky Azure Resource Manager. 

> [!NOTE]
> Vložený nasazení App Service Environment pro PowerApps ve virtuální síti s veřejnou IP adresu není nativně podporované. Podrobné informace o ochraně App Service Environment najdete v této části.

## <a name="next-steps"></a>Další postup

* [Stránku produktů Azure ochrana proti útoku DDoS](https://azure.microsoft.com/services/ddos-protection/)

* [Blog Azure ochrana proti útoku DDoS](http://aka.ms/ddosblog)

* [Dokumentace k Azure ochrana proti útoku DDoS](../virtual-network/ddos-protection-overview.md)
