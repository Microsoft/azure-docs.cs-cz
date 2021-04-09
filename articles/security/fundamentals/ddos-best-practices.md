---
title: Navrhování odolných řešení pomocí Azure DDoS Protection
description: Přečtěte si, jak můžete pomocí dat protokolování získat podrobné informace o vaší aplikaci.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498927"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection – navrhování odolných řešení

Tento článek je určen pro tvůrce rozhodnutí IT a bezpečnostní pracovníky. Očekává se, že máte zkušenosti s Azure, sítí a zabezpečením.
DDoS je typ útoku, který se pokouší vyčerpat prostředky aplikace. Cílem je ovlivnit dostupnost aplikace a její schopnost zpracovávat legitimní požadavky. Útoky se stávají propracovanější a mají větší dopad na velikost a dopad. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet. Návrh pro distribuovanou odolnost s cílem odepření služby (DDoS) vyžaduje plánování a navrhování pro nejrůznější režimy selhání. Azure poskytuje nepřetržitou ochranu před DDoS útoky. Tato ochrana je ve výchozím nastavení integrovaná do platformy Azure a nemá žádné dodatečné náklady.

Kromě základní ochrany DDoS na platformě nabízí [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) rozšířené možnosti zmírnění DDoS proti útokům na síť. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure. Při vytváření nových virtuálních sítí se ochrana snadno povoluje. Lze ji také provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředků.

![Role Azure DDoS Protection v ochraně zákazníků a virtuální sítě od útočníka](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Základní osvědčené postupy

V následujících částech najdete doporučené pokyny k sestavování DDoS odolných služeb v Azure.

### <a name="design-for-security"></a>Navrhování s důrazem na zabezpečení

Zajistěte, aby zabezpečení bylo prioritní v celém životním cyklu aplikace, od návrhu a implementace po nasazení a provoz. Aplikace mohou mít chyby, které umožňují relativně nízké množství požadavků na použití nadměrné množství prostředků, což vede k výpadku služby. 

Aby byla zajištěna ochrana služby spuštěné v Microsoft Azure, měli byste mít dobrou představu o architektuře vaší aplikace a soustředit se na [pět pilířů kvality softwaru](/azure/architecture/guide/pillars).
Měli byste znát typické objemy přenosů dat, model připojení mezi aplikací a dalšími aplikacemi a koncové body služby, které jsou zpřístupněny veřejnému Internetu.

Důležité je zajistit, aby byla aplikace dostatečně odolná pro zpracování odepření služby, která je zaměřená na samotnou aplikaci, je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované v platformě Azure, počínaje nástrojem [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx). SDL řeší zabezpečení v každé vývojové fázi a zajišťuje průběžnou aktualizaci Azure, aby bylo ještě bezpečnější.

### <a name="design-for-scalability"></a>Návrh pro škálovatelnost

Škálovatelnost je to, jak dobře dokáže systém zvládnout zvýšené zatížení. Navrhněte své aplikace pro [horizontální škálování](/azure/architecture/guide/design-principles/scale-out) tak, aby splňovaly požadavky na zesilovací zatížení, konkrétně v případě útoku DDoS. Pokud vaše aplikace závisí na jedné instanci služby, vytvoří se v jednom bodě selhání. Zřizování více instancí zajistí, že bude váš systém pružnější a lépe škálovatelný.

V případě [Azure App Service](../../app-service/overview.md)vyberte [plán App Service](../../app-service/overview-hosting-plans.md) , který nabízí více instancí. Pro Azure Cloud Services nakonfigurujte jednotlivé role tak, aby používaly [víc instancí](../../cloud-services/cloud-services-choose-me.md). V případě [Azure Virtual Machines](../../virtual-machines/index.yml)zajistěte, aby architektura virtuálních počítačů (VM) zahrnovala více než jeden virtuální počítač a aby každý virtuální počítač byl součástí [skupiny dostupnosti](../../virtual-machines/windows/tutorial-availability-sets.md). Pro možnosti automatického škálování doporučujeme používat [Virtual Machine Scale Sets](../../virtual-machine-scale-sets/overview.md) .

### <a name="defense-in-depth"></a>Hloubková ochrana

Cílem důkladné obrany je spravovat rizika pomocí různých obrannou linií strategií. Vrstvení zabezpečení v aplikaci omezuje možnost úspěšného útoku. Doporučujeme, abyste pro své aplikace implementovali zabezpečené návrhy pomocí integrovaných možností platformy Azure.

Například riziko útoku se zvyšuje s velikostí (*oblastí Surface*) aplikace. Oblast povrchu můžete zmenšit pomocí seznamu schválení a uzavřít tak vystavený adresní prostor IP adres a naslouchající porty, které nejsou potřebné v nástrojích pro vyrovnávání zatížení ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) a [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)). [Skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/network-security-groups-overview.md) představují jiný způsob, jak omezit plochu pro útok.
[Značky služeb](../../virtual-network/network-security-groups-overview.md#service-tags) a [skupiny zabezpečení aplikací](../../virtual-network/network-security-groups-overview.md#application-security-groups) můžete použít k minimalizaci složitosti při vytváření pravidel zabezpečení a konfiguraci zabezpečení sítě, jako přirozené rozšíření struktury aplikace.

Služby Azure ve [virtuální síti](../../virtual-network/virtual-networks-overview.md) byste měli nasazovat, kdykoli to bude možné. Tento postup umožňuje prostředkům služby komunikovat prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě ve výchozím nastavení používá veřejné IP adresy jako zdrojové IP adresy. Použití [koncových bodů služby](../../virtual-network/virtual-network-service-endpoints-overview.md) přepne provoz služby na používání privátních adres virtuální sítě jako zdrojových IP adres při přístupu ke službě Azure z virtuální sítě.

Často se v Azure zobrazují místní prostředky zákazníků, které získávají útok na jejich prostředky. Pokud připojujete místní prostředí k Azure, doporučujeme, abyste minimalizovali expozici místních prostředků veřejnému Internetu. V Azure můžete využít možnosti škálování a rozšířené ochrany DDoS, a to nasazením známých veřejných entit v Azure. Vzhledem k tomu, že tyto veřejně přístupné entity jsou často cílem útoků DDoS, jejich vložení do Azure snižuje dopad na vaše místní prostředky.

## <a name="azure-offerings-for-ddos-protection"></a>Nabídky Azure pro DDoS Protection

Azure má dvě nabídky služeb DDoS, které poskytují ochranu před útoky ze sítě (vrstva 3 a 4): DDoS Protection Basic a DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Základní ochrana je ve výchozím nastavení integrovaná do Azure, a to bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure zajišťuje obranu proti běžným útokům na síťové vrstvy prostřednictvím nepřetržitého monitorování provozu a zmírnění rizik v reálném čase. DDoS Protection Basic nevyžaduje žádné změny konfigurace nebo aplikace uživatele. DDoS Protection Basic pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.

![Mapování reprezentace sítě Azure s textem "globální DDoS zmírnění rizika" a "špičková kapacita DDoS"](./media/ddos-best-practices/image3.png)

Základní ochrana DDoS v Azure se skládá z hardwarových i hardwarových součástí. Rovina softwarového řízení se rozhodne, kde a jaké typy přenosů by měly být řízeny hardwarovými zařízeními, která analyzují a odebírá útok na útok. Rovina ovládacího prvku zajišťuje toto rozhodnutí na základě *zásad* DDoS Protection pro celé infrastruktury. Tato zásada se staticky nastavuje a univerzálně používá pro všechny zákazníky Azure.

Například zásada DDoS Protection určuje, v jakém objemu provozu má být ochrana *aktivována.* (To znamená, že provoz klienta by měl být směrován přes zařízení s čištěním dat.) Zásada pak určuje, jak by měly zařízení pro čištění *zmírnit* útok.

Služba Azure DDoS Protection Basic je zaměřená na ochranu infrastruktury a ochrany platformy Azure. Snižuje přenos dat, když překročí sazbu, která je tak významná, že by mohla ovlivnit více zákazníků ve víceklientském prostředí. Neposkytuje výstrahy nebo přizpůsobené zásady pro zákazníka.

### <a name="ddos-protection-standard"></a>DDoS Protection úrovně Standard

Standardní ochrana poskytuje rozšířené funkce pro zmírnění DDoS. Automaticky se vyladěna tak, aby chránila vaše konkrétní prostředky Azure ve virtuální síti. Ochranu je snadné povolit v jakékoli nové nebo existující virtuální síti a nevyžaduje žádné změny aplikací nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. Následující části popisují klíčové funkce služby Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptivní optimalizace v reálném čase

Služba Azure DDoS Protection Basic pomáhá chránit zákazníky a zabránit dopadům na jiné zákazníky. Pokud je například služba zřízena pro typický objem legitimního příchozího provozu, který je menší než *míra triggeru* zásady DDoS Protection v rámci infrastruktury, může se DDoS útok na prostředky tohoto zákazníka nevšimnout. Obecně platí, že složitost nedávných útoků (například DDoS s více vektory) a chování klientů, které jsou specifické pro jednotlivé zákazníky, přizpůsobené zásady ochrany. Služba provádí tyto úpravy pomocí dvou přehledů:

- Automatické učení vzorů přenosu podle zákazníka (podle IP adresy) pro vrstvy 3 a 4.

- Minimalizace falešně pozitivních hodnot, vzhledem k tomu, že škálování Azure umožňuje, aby mohla absorbovat významné množství provozu.

![Diagram způsobu, jakým funguje DDoS Protection Standard, s kruhovým generováním zásad](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetrie, monitorování a upozorňování

DDoS Protection Standard zpřístupňuje bohatou telemetrii prostřednictvím [Azure monitor](../../azure-monitor/overview.md) po dobu trvání útoku DDoS. Výstrahy pro libovolnou Azure Monitor metriky, které DDoS Protection používá, můžete nakonfigurovat. Protokolování můžete integrovat s Splunk (Azure Event Hubs), protokoly Azure Monitor a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure Monitor.

##### <a name="ddos-mitigation-policies"></a>Zásady zmírnění DDoS

V Azure Portal vyberte **monitorovat**  >  **metriky**. V podokně **metriky** vyberte skupinu prostředků, vyberte typ prostředku **Veřejná IP adresa** a vyberte veřejnou IP adresu Azure. Metriky DDoS jsou viditelné v podokně **dostupné metriky** .

DDoS Protection Standard aplikuje tři zásady zmírnění rizik (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, ve které je povolený DDoS. Prahové hodnoty zásad můžete zobrazit tak, že vyberete **příchozí pakety metriky, které budou aktivovat zmírnění DDoS**.

![Graf dostupných metrik a metrik](./media/ddos-best-practices/image7.png)

Prahové hodnoty zásad se konfigurují prostřednictvím profilace síťového provozu založeného na strojovém učení. K zmírňování DDoS dochází u IP adresy s útokem jenom v případě, že se překročení prahové hodnoty zásad.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrika pro IP adresu v rámci útoku DDoS

Pokud je veřejná IP adresa v rámci útoku, hodnota pro metriku **v rámci útoku DDoS nebo se** nemění na 1, protože DDoS Protection provádí zmírnění přenosů útoku.

!["V části útok DDoS nebo není" metrika a graf](./media/ddos-best-practices/image8.png)

Pro tuto metriku doporučujeme nakonfigurovat výstrahu. Pak budete upozorněni, když dojde k aktivnímu zmírnění DDoS na vaší veřejné IP adrese.

Další informace najdete v tématu [správa Azure DDoS Protection Standard pomocí Azure Portal](../../ddos-protection/manage-ddos-protection.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall webových aplikací pro útoky na prostředky

Pro usnadnění zabezpečení webových aplikací byste měli nakonfigurovat bránu firewall webových aplikací (WAF), která je specifická pro útoky na prostředky v aplikační vrstvě. WAF kontroluje příchozí webový provoz, aby blokoval vkládání SQL, skriptování mezi weby, DDoS a další útoky vrstvy 7. Azure poskytuje [WAF jako funkci Application Gateway](../../web-application-firewall/ag/ag-overview.md) pro centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Od partnerů Azure jsou k dispozici další nabídky WAF, které mohou být vhodnější pro vaše potřeby prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Dokonce i brány firewall webových aplikací jsou náchylné k navýšení a útokům na vyčerpání stavu. Důrazně doporučujeme povolit DDoS Protection Standard ve virtuální síti WAF, aby bylo možné lépe chránit před útoky z objemu a protokolu. Další informace najdete v části [referenční architektury DDoS Protection](#ddos-protection-reference-architectures) .

### <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou zásadní pro pochopení, jak systém provede během DDoS útoku. Návrh plánu odezvy správy incidentů je součástí tohoto úsilí.

Pokud máte DDoS Protection Standard, ujistěte se, že je povolená ve virtuální síti koncových bodů směřujících k Internetu. Konfigurace výstrah DDoS vám pomůže trvale sledovat případné útoky v infrastruktuře. 

Monitorujte své aplikace nezávisle. Pochopení normálního chování aplikace Připravte se na jednání, pokud se aplikace během útoku DDoS nechová podle očekávání.

#### <a name="testing-through-simulations"></a>Testování prostřednictvím simulací

Je dobrým zvykem otestovat vaše předpoklady, jak budou vaše služby reagovat na útok díky provádění pravidelných simulací. Během testování ověřte, že vaše služby nebo aplikace nadále fungují podle očekávání a že nedochází k přerušení činnosti koncového uživatele. Identifikujte mezery z hlediska technologie a procesu a zahrňte je do strategie DDoS Response. Tyto testy doporučujeme provádět v přípravném prostředí nebo během období mimo špičku, abyste minimalizovali dopad na produkční prostředí.

Spolupracujeme s [cloudem BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) a vytvoříte rozhraní, ve kterém můžou zákazníci Azure vygenerovat provoz z veřejných koncových bodů s povoleným DDoS Protection pro simulace. Simulaci [cloudu BreakingPoint](https://www.ixiacom.com/products/breakingpoint-cloud) můžete použít k těmto akcím:

- Ověření, jak Azure DDoS Protection pomáhá chránit vaše prostředky Azure před útoky DDoS

- Optimalizaci procesu reakce na incidenty v případě probíhajícího útoku DDoS

- Dokumentaci dodržování předpisů v rámci ochrany před útoky DDoS

- Trénování týmů starajících se o zabezpečení sítě

Kyberbezpečnosti vyžaduje stálé inovace v případě obrany. Azure DDoS Standard Protection je špičková nabídka s efektivním řešením pro zmírnění stále se složitých útoků DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenty strategie reakce na útoky DDoS

Útok DDoS, který cílí na prostředky Azure, obvykle vyžaduje minimální zásah z pohledu uživatele. I když v rámci strategie reakce na incidenty, která zahrnuje zmírnění DDoS, pomáhá minimalizovat dopad na kontinuitu podnikových aplikací.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft má rozsáhlou síť s přehledem hrozeb. Tato síť využívá kolektivní znalosti rozšířené komunity zabezpečení, která podporuje Microsoft online služby, partnery Microsoftu a vztahy v rámci komunity Internet Security. 

Microsoft jako důležitý poskytovatel infrastruktury obdrží včasné upozornění na hrozby. Společnost Microsoft shromažďuje analýzy hrozeb ze svých online služby a ze své globální zákaznické základny. Microsoft zahrnuje veškerou tuto analýzu hrozeb zpátky do Azure DDoS Protection produktů.

Také jednotka digitálních zločinů společnosti Microsoft (DCU) provádí urážlivé strategie proti botnety. Botnety jsou společný zdroj příkazů a řízení pro útoky DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Hodnocení rizik vašich prostředků Azure

Je naprosto důležité porozumět vašemu riziku, co je DDoS útok, nepřetržitě. Pravidelně se zeptat:

- Jaké nové veřejně dostupné prostředky Azure potřebují chránit?

- Je ve službě jediný bod selhání? 

- Jak se dají izolovat služby, aby se omezil dopad útoku, zatímco stále jsou dostupné služby pro platné zákazníky?

- Existují virtuální sítě, ve kterých by měl být povolený DDoS Protection Standard, ale ne? 

- Jsou moje služby aktivní/aktivní s převzetím služeb při selhání napříč více oblastmi?

### <a name="customer-ddos-response-team"></a>Tým zákaznických odpovědí na DDoS

Vytvoření týmu DDoS Response je klíčový krok v reakci na útok rychle a efektivně. Identifikujte kontakty ve vaší organizaci, kteří budou dohlížet na plánování i provádění. Tento tým DDoS Response by měl důkladně pochopit službu Azure DDoS Protection Standard. Ujistěte se, že tým může identifikovat a zmírnit útok pomocí koordinace s interními a externími zákazníky, včetně týmu podpory společnosti Microsoft.

Pro váš tým DDoS Response doporučujeme používat pro simulaci cvičení jako běžnou součást plánování dostupnosti a kontinuity služeb. Tato cvičení by měla zahrnovat testování škálování.

### <a name="alerts-during-an-attack"></a>Výstrahy během útoku

Azure DDoS Protection Standard identifikuje a zmírnit útoky DDoS bez zásahu uživatele. Chcete-li dostávat oznámení, když dojde k aktivnímu zmírnění chráněné veřejné IP adresy, můžete [nakonfigurovat výstrahu](../../ddos-protection/manage-ddos-protection.md) u metriky **v části útok DDoS nebo ne**. Můžete se rozhodnout, že vytvoříte výstrahy pro ostatní metriky DDoS, abyste porozuměli rozsahu útoku, zahození provozu a dalším podrobnostem.

#### <a name="when-to-contact-microsoft-support"></a>Kdy kontaktovat podporu Microsoftu

- Během útoku DDoS zjistíte, že výkon chráněného prostředku je vážně snížený, nebo že prostředek není k dispozici.

- Myslíte si, že se služba DDoS Protection nechová podle očekávání. 

  Služba DDoS Protection zahájí zmírnění rizik pouze v případě, že zásada hodnoty metriky **, která má aktivovat zmírnění rizik DDoS (TCP/TCP syn/UDP)** , je nižší než přenos přijatý na chráněném prostředku veřejné IP adresy.

- Plánujete virální událost, která výrazně zvýší váš síťový provoz.

- Objekt actor způsobil útok na spuštění DDoS útoku na vaše prostředky.

- Pokud potřebujete, aby se v seznamu Azure DDoS Protection Standard povolil rozsah IP adres nebo IP adres. Běžným scénářem je povolení seznamu IP adres v případě, že je přenos směrován z externího cloudového WAF do Azure. 

U útoků, které mají zásadní dopad na chod firmy, vytvořte [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)se závažností.

### <a name="post-attack-steps"></a>Kroky po útoku

Je vždycky dobré strategii Postmortem po útoku a podle potřeby upravit strategii DDoS Response. Co je potřeba vzít v úvahu:

- Mohlo dojít k narušení provozu služby nebo uživatele z důvodu nedostatku škálovatelné architektury?

- Které aplikace nebo služby utrpěly nejvíc?

- Jak efektivní je strategie DDoS Response a jak se dá zlepšit?

Pokud máte podezření, že jste s útokem na DDoS, Projděte si normální kanály podpory Azure.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection referenční architektury

DDoS Protection Standard je určený [pro služby, které jsou nasazené ve virtuální síti](../../virtual-network/virtual-network-for-azure-services.md). Pro ostatní služby platí výchozí služba DDoS Protection Basic. Následující referenční architektury jsou uspořádané podle scénářů a jsou seskupeny pomocí schémat architektury.

### <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních počítačů (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikace spuštěná na virtuálních počítačích s vyrovnáváním zatížení

Tato referenční architektura ukazuje sadu osvědčených postupů pro spuštění několika virtuálních počítačů s Windows ve škálované sadě za nástrojem pro vyrovnávání zatížení, aby se zlepšila dostupnost a škálovatelnost. Tuto architekturu je možné použít pro jakékoli bezstavové úlohy, jako je například webový server.

![Diagram referenční architektury pro aplikaci spuštěnou na virtuálních počítačích s vyrovnáváním zatížení](./media/ddos-best-practices/image9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. V nástroji pro vyrovnávání zatížení je k virtuálním počítačům distribuována jedna veřejná IP adresa a Internetová data. Ve virtuální síti nástroje pro vyrovnávání zatížení Azure (Internet) je povolený DDoS Protection Standard, ke kterému je přidružená veřejná IP adresa.

Nástroj pro vyrovnávání zatížení distribuuje příchozí internetové požadavky do instancí virtuálních počítačů. Virtual Machine Scale Sets umožňuje, aby byl počet virtuálních počítačů v ručním nebo na základě předdefinovaných pravidel škálované nebo automaticky rozložen. To je důležité, pokud je prostředek pod útokem DDoS. Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikace běžící na N-vrstvách Windows

N-vrstvou architekturu je možné implementovat mnoha způsoby. Následující diagram znázorňuje typickou webovou aplikaci na třech úrovních. Tato architektura je založena na článku [spuštění virtuálních počítačů s vyrovnáváním zatížení pro zajištění škálovatelnosti a dostupnosti](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![Diagram referenční architektury pro aplikaci běžící na N-vrstvách Windows](./media/ddos-best-practices/image10.png)

V této architektuře je ve virtuální síti povolená DDoS Protection Standard. Všechny veřejné IP adresy ve virtuální síti získají DDoS ochranu pro vrstvu 3 a 4. V případě ochrany vrstvy 7 nasaďte Application Gateway v SKU WAF. Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Webová aplikace PaaS

Tato referenční architektura ukazuje spuštění aplikace Azure App Service v jedné oblasti. Tato architektura ukazuje sadu osvědčených postupů pro webovou aplikaci, která používá [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblast je nastavená pro scénáře převzetí služeb při selhání.

![Diagram referenční architektury pro webovou aplikaci v PaaS](./media/ddos-best-practices/image11.png)

Azure Traffic Manager směruje příchozí žádosti do Application Gateway v jedné z oblastí. Během normálních operací směruje požadavky na Application Gateway v aktivní oblasti. Pokud tato oblast nebude k dispozici, Traffic Manager převezme Application Gateway v pohotovostní oblasti.

Veškerý provoz z Internetu určeného do webové aplikace je směrován do [Application Gateway veřejné IP adresy](../../application-gateway/application-gateway-web-app-overview.md) prostřednictvím Traffic Manager. V tomto scénáři se služba App Service (samotná webová aplikace) přímo netýká a chrání ji Application Gateway. 

Doporučujeme, abyste nakonfigurovali Application Gateway WAF SKU (režim prevence), která vám umožní chránit proti útokům vrstvy 7 (HTTP/HTTPS/WebSocket). Kromě toho jsou webové aplikace nakonfigurované tak, aby [přijímaly jenom přenosy z Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresy.

Další informace o této referenční architektuře najdete v [tomto článku](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Zmírnění rizik pro jiné než webové služby PaaS Services

#### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Tato referenční architektura ukazuje konfiguraci DDoS Protection standard pro [cluster Azure HDInsight](../../hdinsight/index.yml). Ujistěte se, že je cluster HDInsight propojený s virtuální sítí a že DDoS Protection je ve virtuální síti povolený.

![Podokna HDInsight a rozšířená nastavení s nastavením virtuální sítě](./media/ddos-best-practices/image12.png)

![Výběr pro povolení DDoS Protection](./media/ddos-best-practices/image13.png)

V této architektuře je provoz určený pro cluster HDInsight z Internetu směrován do veřejné IP adresy přidružené k nástroji pro vyrovnávání zatížení brány HDInsight. Nástroj pro vyrovnávání zatížení brány pak pošle provoz na hlavní uzly nebo pracovní uzly přímo. Vzhledem k tomu, že je ve virtuální síti HDInsight povolený DDoS Protection Standard, všechny veřejné IP adresy ve virtuální síti získají DDoS ochranu pro vrstvu 3 a 4. Tuto referenční architekturu je možné kombinovat s referenčními architekturami N-vrstvých a více oblastí.

Další informace o této referenční architektuře najdete v tématu věnovaném [rozšiřování Azure HDInsight pomocí dokumentace k azure Virtual Network](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment pro PowerApps nebo API Management ve virtuální síti s veřejnou IP adresou není nativně podporovaná.

## <a name="next-steps"></a>Další kroky

* [Sdílená odpovědnost v cloudu](shared-responsibility.md)
* [Stránka Azure DDoS Protection produktu](https://azure.microsoft.com/services/ddos-protection/)
* [Dokumentace k Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)