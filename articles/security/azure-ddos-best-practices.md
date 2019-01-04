---
title: Azure DDoS Protection osvědčené postupy a referenční architektury | Dokumentace Microsoftu
description: Další informace o použití protokolování dat získat podrobný přehled o vaší aplikaci.
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
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: 4496a0fd3ec220d03c4cc279876234b503b0dbb6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720862"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Azure DDoS Protection: Osvědčené postupy a referenční architektury

Tento článek je určený pro IT pracovníky s rozhodovací pravomocí a zabezpečení pracovníků. Očekává, že jste obeznámeni s Azure, sítě a zabezpečení.

Navrhování pro distribuované s cílem odepření služeb (DDoS) odolnost proti chybám vyžaduje plánování a návrh pro různé režimy selhání. Tento článek obsahuje osvědčené postupy pro navrhování aplikací v Azure pro odolnost proti chybám před útoky DDoS.

## <a name="types-of-attacks"></a>Typy útoků

Před útoky DDoS je typ útoku, který se pokouší vyčerpání prostředků aplikace. Cílem je mít vliv na dostupnost vaší aplikace a její schopnost zpracovávat oprávněné požadavky. Útokům se stávají propracované a větší velikost a dopad. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.

Azure poskytuje trvalou ochranu před útoky DDoS. Tato ochrana je integrovaná do platformy Azure ve výchozím nastavení a bez dalších poplatků. 

Kromě základní ochranu před útoky DDoS na platformě [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) poskytuje pokročilé funkce pro zmírnění útoků DDoS proti síťovým útokům. Toto pravidlo je vyladěný automaticky k ochraně vašich konkrétních prostředků Azure. Ochrana je snadno zajistit při vytváření nové virtuální sítě. Je možné provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředku.

![Role služby Azure DDoS Protection při ochraně před případným útočníkem zákazníků a virtual network](media/azure-ddos-best-practices/image1.png)

Útoky DDoS je možné rozdělit do tří kategorií: odměrné, protokol a prostředků.

### <a name="volumetric-attacks"></a>Odměrné útoky

Odměrné útoky jsou nejběžnějším typem útoky DDoS. Způsoby odměrné útoky hrubou silou osoby, cílených sítě a přenosové vrstvy. Se pokusí vyčerpání prostředků, jako je síťová propojení. 

Tyto útoky často používají více nakažené systémy k zahlcení sítě vrstvy se zdánlivě legitimní provozu. Používají protokoly síťové vrstvy, jako je například ovládací prvek zpráva ICMP (Internet Protocol), protokolu UDP (User Datagram) a protokolu TCP (Transmission Control).

Útoky DDoS nejčastěji používané síťové vrstvy jsou TCP SYN zahlcení odezvu protokolu ICMP, zahlcení, DNS, UDP a útoky zesílení NTP. Tento typ útoku slouží pouze k dojít k přerušení služby, ale také jako smokescreen pro neslouží pro nekalé účely a cílové napadení sítě. Je například poslední odměrné útoku [Memcached zneužít](https://www.wired.com/story/github-ddos-memcached/) , která měla vliv na Githubu. Tento útok cílového portu UDP 11211 a generované 1.35 Tb za sekundu objemu útoku.

### <a name="protocol-attacks"></a>Protokol útoky

Protokol útokům cílové aplikační protokoly. Se pokusí použít všechny dostupné prostředky v zařízeních infrastruktury, jako jsou brány firewall, aplikační servery a nástroje pro vyrovnávání zatížení. Protokol útoky pomocí paketů, které jsou poškozené nebo obsahovat anomálie protokolu. Tyto útoky fungovat na odeslání velkého počtu otevřených žádostí, servery a jiná sdělení odpovědět a čekat na odpověď paketů. Cíl se pokusí odpovědět na otevřených žádostí, eventuálně chybu systému.

Nejběžnějším příkladem s útoky DDoS založené na protokolu je TCP SYN zahlcení. V tento útok sledu očekávání TCP SYN požadavků pokusí zahlcovat cíl. Cílem je, aby cíle reagovat. Výpadek Dyn 2016, kromě probíhá útok aplikační vrstvu se skládal z TCP SYN floods tento cílový port 53 Dyn na servery DNS.

### <a name="resource-attacks"></a>Útoky prostředků

Prostředek útoky cílit na aplikační vrstvu. Back endové procesy se aktivuje ve snaze zahlcovat systému. Prostředek útokům zneužití provoz, který vypadá normální ale, že se mělo náročnou na procesor dotazů na server. Objem provozu, které jsou potřebné k vyčerpání prostředků je nižší než u jiných typů útoků. Provoz v rámci útoku prostředků je nerozeznatelná od legitimního provoz díky tomu je obtížné rozpoznat. Nejběžnější útoky prostředků jsou na HTTP/HTTPS a služby DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Sdílená odpovědnost v cloudu

V obrany strategie pomáhá boji s rostoucí celé řady a vyspělost kybernetických útoků. Zabezpečení je sdílenou odpovědnost mezi zákazníkem a Microsoft. Označuje jako Microsoft [sdílená odpovědnost modelu](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). Následující obrázek znázorňuje tuto dělení zodpovědnosti:

![Odpovědnosti zákazníka a Azure](media/azure-ddos-best-practices/image2.png)

Zákazníci Azure využívat kontrola osvědčených postupů Microsoftu a vytváření globálně distribuovaných aplikací, které jsou navrženy a jsou testovány z hlediska selhání.

## <a name="fundamental-best-practices"></a>Základní osvědčené postupy

Následující oddíly poskytují doporučený postup pro vytváření před útoky DDoS odolných služeb v Azure.

### <a name="design-for-security"></a>Navrhování se zabezpečením

Ujistěte se, že zabezpečení je priorita v průběhu celého životního cyklu aplikace, od návrhu a implementace až po nasazení a provoz. Aplikace může mít chyby, které umožňují relativně nízký objem požadavků na použití nadměrné množství prostředků, což vede k výpadku služeb. 

K ochraně služby spuštěné v Microsoft Azure, musí mít dostatečné povědomí o vaší aplikace architektury a zaměřit [pět pilířů kvality softwaru](https://docs.microsoft.com/azure/architecture/guide/pillars).
Typické provozem, měli byste vědět model připojení mezi aplikací a další aplikace a koncové body služby, které jsou vystaveny do veřejného Internetu.

Zajištění, že aplikace je dostatečně odolné pro zpracování odepření služby, který je zaměřený na vlastní aplikace je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované do platformy Azure počínaje [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Do procesu SDL zajišťuje zabezpečení ve všech fázích vývoje a zajišťuje, že Azure se průběžně aktualizuje, aby byl ještě větší zabezpečení.

### <a name="design-for-scalability"></a>Návrh pro zajištění škálovatelnosti

Škálovatelnost je, jak dobře může systému zvládnout zvýšenou zátěž. Je třeba navrhnout svoje aplikace na [škálovat horizontálně](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) podle potřeby zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud je aplikace závislá na jednu instanci služby, vzniká jediný bod selhání. Zřízení více instancí je váš systém větší škálovatelnost a odolnost.

Pro [služby Azure App Service](../app-service/app-service-value-prop-what-is.md)vyberte [plán služby App Service](../app-service/overview-hosting-plans.md) , který nabízí více instancí. Pro Azure Cloud Services, nakonfigurovat všechny své role používat [více instancí](../cloud-services/cloud-services-choose-me.md). Pro [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ujistěte se, že vaše Architektura virtuálního počítače (VM) obsahuje více než jeden virtuální počítač a že každý virtuální počítač je součástí [dostupnosti](../virtual-machines/virtual-machines-windows-manage-availability.md). Doporučujeme používat [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro možnosti automatického škálování.

### <a name="defense-in-depth"></a>Obrana dopodrobna

Myšlenku za vás ubrání je řízení rizik pomocí různých obranné strategii. Vrstvení obrana zabezpečení v aplikaci snižuje pravděpodobnost úspěšného útoku. Doporučujeme implementovat zabezpečení návrhů pro vaše aplikace pomocí integrované funkce platformy Azure.

Například, hodnota se zvyšuje riziko útoku s velikost (*plocha*) aplikace. Můžete snížit styčné plochy pomocí přidávání na seznam povolených uzavřeny vystavené adresní prostor IP adres a naslouchání porty, které nejsou potřebné v nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) a [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Skupiny zabezpečení sítě (Nsg)](../virtual-network/security-overview.md) představují jiný způsob omezení možností útoku.
Můžete použít [značky služeb](../virtual-network/security-overview.md#service-tags) a [skupiny zabezpečení aplikací](../virtual-network/security-overview.md#application-security-groups) minimalizovat složitost vytváření pravidla zabezpečení a konfigurace zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Měli byste nasadit služby Azure v [virtuální sítě](../virtual-network/virtual-networks-overview.md) kdykoli je to možné. Tento postup umožňuje prostředky služby pro komunikaci prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě jako zdrojové IP adresy používá veřejné IP adresy ve výchozím nastavení. Pomocí [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md) přepne provoz služby na používání privátních adres virtuální sítě jako zdrojové IP adresy, když přistupuje služby Azure z virtuální sítě.

Vidíme často zákazníků s místními prostředky získávání napadených spolu s jejich prostředky v Azure. Pokud se připojujete do místního prostředí do Azure, doporučujeme, minimalizujete ohrožení místních prostředků do veřejného Internetu. Škálování a rozšířené možnosti ochrany před útoky DDoS Azure můžete použít nasazením veřejného dobře známé entity v Azure. Protože tyto veřejně dostupné entity jsou často cílem pro útoky DDoS, umístit je do Azure sníží to dopad na vaše místní prostředky.

## <a name="azure-offerings-for-ddos-protection"></a>Nabídky Azure DDoS Protection

Azure má dva nabídky služeb DDoS, které poskytují ochranu před síťovými útoky (vrstva 3 a 4): Před útoky DDoS Basic Protection a před útoky DDoS Standard Protection. 

### <a name="ddos-protection-basic"></a>Před útoky DDoS Basic Protection

Basic protection je integrovaná do Azure ve výchozím nastavení bez dalších poplatků. Škálování a kapacitu sítě globálně nasazených Azure poskytuje ochranu před běžnými útoky síťové vrstvě prostřednictvím monitorování a v reálném čase zmírnění neustále v provozu. DDoS Protection základní vyžaduje žádné změny v konfiguraci nebo aplikaci uživatele. DDoS Protection základní k ochraně všech služeb Azure, včetně služeb PaaS, jako je Azure DNS.

![Mapování reprezentace síti Azure a za text "globální DDoS zmírnění přítomnosti" a "Úvodní kapacitu zmírnění útoků DDoS"](media/azure-ddos-best-practices/image3.png)

Základní ochranu před útoky DDoS v Azure se skládá z softwarové a hardwarové součásti. Rovina řízení softwaru rozhodne, kdy, kde, a jaký typ provoz by měl řízena prostřednictvím hardwarová zařízení, které analyzovat a odeberte provoz útoku. Rovina řízení provádí toto rozhodnutí založené na DDoS Protection celou infrastrukturu *zásady*. Tato zásada je staticky nastavit a použít univerzálně pro všechny zákazníky Azure.

Například zásady ochrany před útoky DDoS Určuje, na jaký objem provozu by měl být ochranu *aktivuje.* (To znamená, tenanta provoz by měl být směrován přes scrubbingu zařízení.) Potom tato zásada určuje, jak by měl scrubbingu zařízení *zmírnit* útoku.

Služba Azure DDoS Protection na úrovni Basic je zaměřený na ochranu infrastruktury a ochranu platformy Azure. To omezuje provoz když překročí kurz, který je proto důležité, že by mohla ovlivnit více zákazníků ve víceklientském prostředí. Neposkytuje výstrahy nebo přizpůsobené zásady na zákazníka.

### <a name="ddos-protection-standard"></a>Před útoky DDoS Standard Protection

Standard protection poskytuje vylepšené funkce, které před útoky DDoS omezení rizik. Toto pravidlo je vyladěný automaticky k ochraně vašich konkrétní prostředků ve virtuální síti Azure. Ochrana se snadno povolit na všechny nové nebo existující virtuální sítě a nevyžaduje žádné změny aplikace nebo prostředku. Má několik výhod oproti základní služby, včetně protokolování, výstrahy a telemetrie. Následující oddíly popisují klíčové funkce služby Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptivní ladění v reálném čase

Služba Azure DDoS Protection na úrovni Basic pomáhá zákazníkům chránit a bránit dopady ostatním zákazníkům. Například, pokud služba se zřizuje pro typický objem legitimní příchozí provoz, který je menší než *rychlost triggeru* zásad ochrany před útoky DDoS celé infrastruktury, je možné dát s útoky DDoS na prostředky daného zákazníka bez povšimnutí. Obecně platí složitost nedávné útoky (například více vektoru DDoS) a chování specifické pro aplikaci tenantů volání pro na zákazníka, přizpůsobené ochraně zásady. Služba provede toto přizpůsobení pomocí dvou insights:

- Automatické učení vzory provozu (IP) za zákazníka pro vrstvy 3 a 4.

- Minimalizuje počet falešně pozitivních výsledků, vzhledem k tomu, že škálování Azure umožňuje chránit před značné množství provozu.

![Diagram před útoky DDoS Protection standardní fungování s "Generování zásad" v kruhu](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Služba DDoS Protection telemetrických dat, monitorování a upozorňování

DDoS Protection Standard poskytuje bohaté telemetrická data prostřednictvím [Azure Monitor](../azure-monitor/overview.md) po dobu trvání s útoky DDoS. Můžete nakonfigurovat výstrahy pro některé z metrik Azure monitoru, který používá ochranu před útoky DDoS. Protokolování lze integrovat s Splunk (služby Azure Event Hubs), Azure Log Analytics a Azure Storage pro pokročilé analýzy prostřednictvím rozhraní Azure Diagnostics monitorování.

##### <a name="ddos-mitigation-policies"></a>Zásady omezení rizik před útoky DDoS

Na webu Azure Portal, vyberte **monitorování** > **metriky**. V **metriky** podokně, vyberte skupinu prostředků, vyberte typ prostředku **veřejnou IP adresu**a vyberte Azure veřejné IP adresy. Jsou viditelné v metrik DDoS **dostupné metriky** podokně.

DDoS Protection standardní použije tři automaticky laděným zásady omezení rizik (TCP SYN, TCP a UDP) pro každé veřejné IP adresy chráněného prostředku ve virtuální síti, který má povolené před útoky DDoS. Prahové hodnoty zásady můžete zobrazit výběrem metriky **příchozí pakety pro aktivaci omezení rizik útoků DDoS**.

![Dostupné metriky a graf metrik](media/azure-ddos-best-practices/image7.png)

Prahové hodnoty zásad jsou automaticky konfigurované prostřednictvím machine learning-based sítě provoz profilování. Omezení rizik útoků DDoS dojde k IP adrese terčem útoku pouze v případě, že je překročena mezní hodnota zásad.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metriky pro IP adresu s útoky DDoS

Pokud je veřejná IP adresa je v části útoku, hodnotu pro metriku **útoku DDoS pod nebo není** mění DDoS Protection provede omezení rizik útoků provoz na hodnotu 1.

!["V části před útoky DDoS útoku nebo není" metriky a diagram](media/azure-ddos-best-practices/image8.png)

Doporučujeme, abyste konfiguraci upozornění na tuto metriku. Potom obdržíte po aktivní omezení rizik útoků DDoS provedené na veřejnou IP adresu.

Další informace najdete v tématu [spravovat Azure DDoS Protection Standard pomocí webu Azure portal](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall webových aplikací pro útoky prostředků

Specifické pro prostředek útoky na aplikační vrstvě, měli byste nakonfigurovat firewall webových aplikací (WAF) Chcete-li pomoci zabezpečit webové aplikace. WAF kontroluje příchozí webový provoz a blokují injektáže SQL, před útoky DDoS a další vrstvy 7 útoky skriptování napříč weby. Azure poskytuje [jako funkce služby Application Gateway WAF](../application-gateway/application-gateway-web-application-firewall-overview.md) pro centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožení zabezpečení. Nejsou k dispozici od partnerů Azure, které může být vhodnější pro vaše potřeby prostřednictvím dalších nabídek WAF [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Dokonce i firewallů webových aplikací jsou však náchylné k útokům vyčerpání odměrné federace a jednotlivých států. Důrazně doporučujeme povolit použití standardní před útoky DDoS Protection ve virtuální síti WAF k ochraně před odměrné a útoky protokolu. Další informace najdete v tématu [DDoS Protection referenční architektury](#ddos-protection-reference-architectures) oddílu.

### <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou zásadní pro pochopení, jak systém provede během s útoky DDoS. Navrhování plán správy incidentů odpovědi je součástí tohoto úsilí.

Pokud máte před útoky DDoS Protection standardní, ujistěte se, že je povoleno ve virtuální síti internetových koncových bodů. Konfigurace výstrah před útoky DDoS umožňuje neustále sledovat všechny potenciální útoky na infrastrukturu. 

Vaše aplikace, měli byste sledovat nezávisle na sobě. Vysvětlení normálního chování aplikace. Připravte tak, aby fungoval, pokud aplikace se nechová podle očekávání při útoku DDoS.

#### <a name="testing-through-simulations"></a>Testování v prostředích s simulace

Je vhodné otestovat předpokladů o jak služby bude reagovat na útok prováděním pravidelné simulace. Během testování, ověřte, že služby nebo aplikace i nadále fungovat podle očekávání a neexistuje žádné přerušení činnost koncového uživatele. Identifikovat mezery z pohledu technologií i proces a je začlenit do strategie odpověď před útoky DDoS. Doporučujeme provádět tyto testy pracovní prostředí, nebo během hodin mimo špičku, chcete-li minimalizovat dopad na produkční prostředí.

Jsme uzavřeli partnerství s [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) k sestavení rozhraní, kde Zákazníci Azure mohou generovat provoz s podporou služba DDoS Protection veřejné koncové body pro simulace. Můžete použít [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) simulace do:

- Ověření, jak Azure DDoS Protection pomáhá chránit prostředky Azure před útoky DDoS.

- Optimalizace procesu reakce na incidenty v rámci útoky DDoS.

- Zdokumentujte dodržování předpisů před útoky DDoS.

- Trénování vaše týmy zabezpečení sítě.

Kybernetického zabezpečení vyžaduje stálé inovace v obrany. Služba Azure DDoS Standard protection je stavu z – moderní s efektivním řešením pro zmírnění čím dál složitějších útoky DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Součástí strategie odpověď před útoky DDoS

S útoky DDoS, které cílí na prostředky Azure obvykle vyžaduje minimem zásahů z pohledu uživatele. Stále zahrnutí před útoky DDoS zmírnění jako součást strategie reakce na incidenty pomáhá minimalizovat dopad na obchodní kontinuity podnikových procesů.

### <a name="microsoft-threat-intelligence"></a>Analýza hrozeb Microsoftu

Microsoft má síť rozsáhlé threat intelligence. Tato síť používá kolektivních znalostí komunitě rozšířené zabezpečení, který podporuje online služeb Microsoftu, partnerů Microsoftu a relace v rámci komunity zabezpečení Internetu. 

Jako poskytovatel důležitou infrastrukturu společnost Microsoft obdrží včasného upozornění na hrozby. Microsoft shromažďuje hrozeb ze svých služeb online services a globální zákaznické základny. Microsoft zahrnuje všechny této analýzy hrozeb zpět do služby Azure DDoS Protection produktů.

Navíc Microsoft Digital Crimes Unit (DCU) provádí urážlivé strategie proti botnetů. Botnetů jsou běžné příčiny příkazy a ovládání pro útoky DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Hodnocení rizika z vašich prostředků Azure

Je nutné pochopit tak rozsah nebezpečí před útoky DDoS průběžně. Pravidelně položte si otázku: 
- Jaké nové prostředky veřejně dostupné Azure potřebujete ochrany?

- Představuje jediný bod selhání ve službě? 

- Jak lze služby izolované omezit dopad útoku při stále zpřístupnění služby zákazníkům platná?

- Existují virtuálních sítí, kde před útoky DDoS Protection Standard by měly být povolené, ale není? 

- Jsou Moje služby aktivní/aktivní s převzetí služeb při selhání ve více oblastech?

### <a name="customer-ddos-response-team"></a>Tým zákazníků před útoky DDoS

Vytvoření týmu odpověď před útoky DDoS je klíče krokem při reagování na útok, rychle a efektivně. Identifikujte kontakty ve vaší organizaci, kteří budou získejte přehled o plánování a provádění. Tento tým před útoky DDoS, třeba důkladně porozumět služby Azure DDoS Protection Standard. Ujistěte se, že tým včasně identifikovat a zmírní útok, tím, že koordinuje s interním i externím zákazníkům, včetně tým podpory společnosti Microsoft.

Pro tým odpovědi před útoky DDoS doporučujeme použít simulace cvičení jako normální součást dostupnosti služeb a plánování kontinuity podnikových procesů. Tato cvičení by měly zahrnovat testování škálování.

### <a name="alerts-during-an-attack"></a>Upozornění během útoku

Azure DDoS Protection standardní identifikuje a omezuje útoky DDoS bez jakéhokoli zásahu uživatele. Máme vás upozornit, když je aktivní omezení rizik pro chráněné veřejné IP adresy, můžete [výstraha](../virtual-network/ddos-protection-manage-portal.md) na metriku **útoku DDoS pod nebo není**. Můžete vytvářet upozornění pro jiné metriky před útoky DDoS porozumět rozsahu útoku, provoz probíhá vyřazování a další podrobnosti.

#### <a name="when-to-contact-microsoft-support"></a>Při kontaktování podpory Microsoftu

- Při útoku DDoS zjistit, že je vážně snížení výkonu chráněných prostředků nebo prostředek není k dispozici.

- Si myslíte, že služba DDoS Protection se nechová podle očekávání. 

  Pouze v případě spuštění služby DDoS Protection ke zmírnění hodnota metriky **zásady pro aktivaci omezení rizik útoků DDoS (TCP/TCP SYN/UDP)** je nižší než přenosů přijatých na chráněný prostředek veřejné IP.

- Máte v plánu virálního událostí, který výrazně zvyšuje provozu vaší sítě.

- Prvek "actor" má spustit s útoky DDoS na vaše prostředky ohrožené.

Vytvořit útoků, které mají kritický dopad na firmu, A závažnost [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Kroky po útoku

Je vždy vhodné strategie postmortem po útoku a podle potřeby upravte strategie odpověď před útoky DDoS. Co je třeba zvážit:

- Byl existuje případnému přerušení služby nebo uživatelské prostředí kvůli chybějícímu škálovatelnou architekturu?

- Které aplikace nebo služby, kde nejvíce?

- Jak efektivní je strategie odpověď před útoky DDoS a jak ji bylo možné zlepšit?

Pokud máte podezření, že jste v části s útoky DDoS, požadavek eskalujte prostřednictvím běžných kanálů podpory Azure.

## <a name="ddos-protection-reference-architectures"></a>Služba DDoS Protection referenční architektury

DDoS Protection Standard je navržená [pro služby, které jsou nasazené ve virtuální síti](../virtual-network/virtual-network-for-azure-services.md). Pro jiné služby použije výchozí služba DDoS Protection základní. Následující referenční architektury jsou uspořádané podle scénářů, s vzory architektury seskupené dohromady.

### <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních počítačů (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikace běžící na virtuálních počítačích s vyrovnáváním zatížení

Tato referenční architektura představuje sadu osvědčených postupů pro spuštění několika virtuálních počítačů Windows ve škálovací sadě za nástrojem pro vyrovnávání zatížení, ke zlepšení dostupnosti a škálovatelnosti. Tuto architekturu je možné pro jakoukoli bezstavovou úlohu, například webový server.

![Diagram referenční architektury pro aplikace běžící na virtuálních počítačích s vyrovnáváním zatížení](media/azure-ddos-best-practices/image9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. Existuje jedna veřejná IP adresa a internetový provoz je distribuovaný virtuálním počítačům přes nástroj pro vyrovnávání zatížení. DDoS Protection Standard je povolené ve službě virtual network Azure (internet) nástroje pro vyrovnávání zatížení, který má veřejnou IP adresu s ním spojená.

Nástroje pro vyrovnávání zatížení distribuuje příchozí Internetové žádosti mezi instance virtuálních počítačů. Škálovací sady virtuálních počítačů umožňují určitému počtu virtuálních počítačů pro horizontální snížení nebo navýšení kapacity ručně nebo automaticky na základě předdefinovaných pravidel. To je důležité, pokud je prostředek terčem útoku DDoS. Další informace o této referenční architektuře, najdete v části [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikace běžící na Windows. N-vrstvá

N-vrstvou architekturu je možné implementovat mnoha způsoby. Následující diagram znázorňuje typickou třívrstvou webovou aplikaci. Tato architektura vychází z článku [s vyrovnáváním zatížení virtuálních počítačů pro zajištění škálovatelnosti a dostupnosti](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![Diagram referenční architektury pro aplikace běžící na Windows. N-vrstvá](media/azure-ddos-best-practices/image10.png)

V této architektuře před útoky DDoS Protection Standard je povolena ve virtuální síti. Všechny veřejné IP adresy ve virtuální síti získáte ochranu před útoky DDoS pro vrstvy 3 a 4. Pro zajištění ochrany vrstvy 7 nasaďte službu Application Gateway v WAF SKU. Další informace o této referenční architektuře, najdete v části [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Webová aplikace PaaS

Tato referenční architektura ukazuje spuštění aplikace Azure App Service v jedné oblasti. Tato architektura představuje sadu osvědčených postupů pro webovou aplikaci, která používá [služby Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblasti je nastavený pro scénáře převzetí služeb při selhání.

![Diagram referenční architektury pro webové aplikace PaaS](media/azure-ddos-best-practices/image11.png)

Azure Traffic Manager směruje příchozí žádosti do Application Gateway v oblastech. V normálním provozu směruje žádosti do Application Gateway v aktivní oblast. Pokud tato oblast stane nedostupnou, Traffic Manager převezme služby při selhání aplikační brány v pohotovostním režimu oblasti.

Veškerý provoz z Internetu směřující do webové aplikace se směruje do [veřejné IP adresy služba Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) pomocí Traffic Manageru. V tomto scénáři, app service (webová aplikace) sám není přímo externě připojena a je chráněný službou Application Gateway. 

Doporučujeme, abyste nakonfigurovali Application Gateway WAF SKU (zabránění režimu) k ochraně před útoky vrstvy 7 (HTTP/HTTPS/WebSocket). Kromě toho web apps umožňují [přijímat pouze komunikaci ze služby Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresu.

Další informace o této referenční architektuře, najdete v části [v tomto článku](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Zmírnění dopadů pro služby PaaS mimo web

#### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Tato referenční architektura ukazuje před útoky DDoS Protection úrovně Standard pro konfiguraci [clusteru Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Ujistěte se, že clusteru HDInsight je propojený s virtuální sítí a že služba DDoS Protection je povolená ve virtuální síti.

!["HDInsight" a "Nastavení" podoken s nastavení virtuální sítě](media/azure-ddos-best-practices/image12.png)

![Výběr pro povolení ochrany před útoky DDoS](media/azure-ddos-best-practices/image13.png)

V této architektuře se směruje provoz směřující do clusteru HDInsight z Internetu do veřejné IP adresy spojené s nástrojem load balancer HDInsight bránu. Nástroj pro vyrovnávání zatížení brána pak odešle provoz k hlavním uzlům nebo pracovní uzly přímo. Protože standardní před útoky DDoS Protection je povolená na virtuální síť HDInsight, všechny veřejné IP adresy ve virtuální síti získáte ochranu před útoky DDoS pro vrstvy 3 a 4. Tato referenční architektura zkombinovat s N-vrstvé a více oblastí referenční architektury.

Další informace o této referenční architektuře, najdete v článku [rozšíření Azure HDInsight pomocí Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentaci.


> [!NOTE]
> Azure App Service Environment pro PowerApps nebo rozhraní API management ve virtuální síti s veřejnou IP adresu jsou podporované nativně.

## <a name="next-steps"></a>Další postup

* [Produktová stránka Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Blog o Azure DDoS Protection](https://aka.ms/ddosblog)

* [Dokumentace ke službě Azure DDoS Protection](../virtual-network/ddos-protection-overview.md)
