---
title: Navrhování odolných řešení s azure ddos ochranou
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
ms.openlocfilehash: 8be1f1161ac1c4611ddb2a5ec61592394014c488
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548679"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection – návrh odolných řešení

Tento článek je určen pro osoby s rozhodovací pravomocí v oblasti IT a bezpečnostní pracovníky. Očekává, že jste obeznámeni s Azure, sítí a zabezpečení.
DDoS je typ útoku, který se pokouší vyčerpat prostředky aplikace. Cílem je ovlivnit dostupnost aplikace a její schopnost zpracovávat legitimní požadavky. Útoky jsou stále sofistikovanější a větší co do velikosti a dopadu. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet. Návrh odolnosti proti chybám distribuovaného odmítnutí služby (DDoS) vyžaduje plánování a navrhování pro různé režimy selhání. Azure poskytuje trvalou ochranu proti útokům DDoS. Tato ochrana je integrovaná do platformy Azure ve výchozím nastavení a bez dalších nákladů.

Kromě základní ochrany DDoS v platformě poskytuje [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) pokročilé možnosti zmírňování ddos proti síťovým útokům. Automaticky se vyladí, aby chránil vaše konkrétní prostředky Azure. Ochrana je jednoduchá při vytváření nových virtuálních sítí. To lze také provést po vytvoření a nevyžaduje žádné změny aplikace nebo prostředků.

![Role ochrany Azure DDoS při ochraně zákazníků a virtuální sítě před útočníkem](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Základní osvědčené postupy

V následujících částech jsou uvedeny normativní pokyny pro vytváření služeb odolných vůči DDoS v Azure.

### <a name="design-for-security"></a>Navrhování s důrazem na zabezpečení

Ujistěte se, že zabezpečení je prioritou po celou dobu životnosti aplikace, od návrhu a implementace až po nasazení a operace. Aplikace mohou mít chyby, které umožňují relativně nízký objem požadavků používat nadměrné množství prostředků, což má za následek výpadku služby. 

Chcete-li chránit službu spuštěnou v Microsoft Azure, měli byste mít dobré znalosti o architektuře aplikací a zaměřit se na [pět pilířů kvality softwaru](/azure/architecture/guide/pillars).
Měli byste znát typické objemy provozu, model připojení mezi aplikací a jinými aplikacemi a koncové body služby, které jsou vystaveny veřejnému internetu.

Zajištění, že aplikace je dostatečně odolná pro zpracování odmítnutí služby, která je zaměřena na samotnou aplikaci je nejdůležitější. Zabezpečení a ochrana osobních údajů jsou integrované do platformy Azure, počínaje [životní cyklus vývoje zabezpečení (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL řeší zabezpečení v každé fázi vývoje a zajišťuje, že Azure je průběžně aktualizován, aby byl ještě bezpečnější.

### <a name="design-for-scalability"></a>Návrh škálovatelnosti

Škálovatelnost je, jak dobře systém zvládne zvýšené zatížení. Navrhněte své aplikace tak, aby [se škálovaly vodorovně](/azure/architecture/guide/design-principles/scale-out) tak, aby splňovaly požadavky na zesílené zatížení, konkrétně v případě útoku DDoS. Pokud vaše aplikace závisí na jedné instanci služby, vytvoří jeden bod selhání. Zřizování více instancí umožňuje váš systém odolnější a škálovatelnější.

Pro [Azure App Service](/azure/app-service/app-service-value-prop-what-is)vyberte plán [služby App Service,](/azure/app-service/overview-hosting-plans) který nabízí více instancí. V azure cloudových službách nakonfigurujte každou z vašich rolí tak, aby [používala více instancí](/azure/cloud-services/cloud-services-choose-me). Pro [virtuální počítače Azure](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ujistěte se, že vaše architektura virtuálních počítačů (VM) obsahuje více než jeden virtuální počítač a že každý virtuální počítač je součástí [skupiny dostupnosti](/azure/virtual-machines/virtual-machines-windows-manage-availability). Doporučujeme používat [škálovací sady virtuálních strojů](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) pro možnosti automatického škálování.

### <a name="defense-in-depth"></a>Hloubková ochrana

Myšlenkou obrany do hloubky je řídit rizika pomocí různých obranných strategií. Vrstvení zabezpečení obrany v aplikaci snižuje pravděpodobnost úspěšného útoku. Doporučujeme implementovat zabezpečené návrhy pro vaše aplikace pomocí integrovaných funkcí platformy Azure.

Například riziko útoku se zvyšuje s velikostí (*povrchovou plochou)* aplikace. Můžete snížit plochu pomocí whitelisting uzavřete exponované IP adresní prostor a naslouchání porty, které nejsou potřeba na vyrovnávání zatížení[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) a [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal) [Skupiny zabezpečení sítě (NSG)](/azure/virtual-network/security-overview) jsou dalším způsobem, jak snížit prostor pro útok.
[Značky služeb](/azure/virtual-network/security-overview#service-tags) a [skupiny zabezpečení aplikací](/azure/virtual-network/security-overview#application-security-groups) můžete použít k minimalizaci složitosti při vytváření pravidel zabezpečení a konfiguraci zabezpečení sítě jako přirozené rozšíření struktury aplikace.

Služby Azure byste měli nasadit ve [virtuální síti,](/azure/virtual-network/virtual-networks-overview) kdykoli je to možné. Tento postup umožňuje prostředky služby komunikovat prostřednictvím privátních IP adres. Provoz služeb Azure z virtuální sítě ve výchozím nastavení používá veřejné IP adresy jako zdrojové IP adresy. Pomocí [koncových bodů služby](/azure/virtual-network/virtual-network-service-endpoints-overview) přepne provoz služby používat privátní adresy virtuální sítě jako zdrojové IP adresy, když přistupují ke službě Azure z virtuální sítě.

Často vidíme místní prostředky zákazníků, které jsou napadeny spolu s jejich prostředky v Azure. Pokud připojujete místní prostředí k Azure, doporučujeme minimalizovat vystavení místních prostředků veřejnému internetu. Škálovací a pokročilé možnosti ochrany DDoS v Azure můžete použít nasazením známých veřejných entit v Azure. Vzhledem k tomu, že tyto veřejně přístupné entity jsou často cílem útoků DDoS, jejich uvedení do Azure snižuje dopad na místní prostředky.

## <a name="azure-offerings-for-ddos-protection"></a>Nabídky Azure pro ochranu Před DoS

Azure má dvě nabídky služeb DDoS, které poskytují ochranu před síťovými útoky (vrstva 3 a 4): DDoS Protection Basic a DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>Základní ochrana DDoS

Základní ochrana je ve výchozím nastavení integrována do Azure bez dalších nákladů. Škálování a kapacita globálně nasazené sítě Azure poskytuje ochranu proti běžným útokům na síťové vrstvě prostřednictvím nepřetržitého monitorování provozu a zmírňování stavu v reálném čase. DDoS Protection Basic nevyžaduje žádné změny konfigurace uživatele nebo aplikace. DDoS Protection Basic pomáhá chránit všechny služby Azure, včetně služeb PaaS, jako je Azure DNS.

![Mapa reprezentace sítě Azure s textem "Globální přítomnost zmírnění DDoS" a "Vedoucí kapacita pro zmírnění DDoS"](./media/ddos-best-practices/image3.png)

Základní ochrana DDoS v Azure se skládá ze softwarových i hardwarových komponent. Letadlo řízení softwaru rozhoduje o tom, kdy, kde a jaký typ provozu by měl být řízen prostřednictvím hardwarových zařízení, která analyzují a odstraňují provoz útoku. Řídicí rovina činí toto rozhodnutí na základě zásad ochrany DDoS pro celou *infrastrukturu*. Tato zásada je staticky nastavená a univerzálně použitá pro všechny zákazníky Azure.

Například zásady ochrany DDoS určuje, na jakém objemu provozu by měla být *ochrana spuštěna.* (To znamená, že provoz nájemce by měl být směrován prostřednictvím čisticích zařízení.) Zásada pak určuje, jak by měly zařízení pro čištění *zmírnit* útok.

Služba Azure DDoS Protection Basic je zaměřená na ochranu infrastruktury a ochranu platformy Azure. Zmírňuje provoz, pokud překročí rychlost, která je tak významná, že může ovlivnit více zákazníků ve víceklientském prostředí. Neposkytuje upozornění nebo zásady přizpůsobené pro zákazníka.

### <a name="ddos-protection-standard"></a>Standard ochrany DDoS

Standardní ochrana poskytuje rozšířené funkce pro zmírnění ddos. Je automaticky vyladěný tak, aby pomohl chránit vaše konkrétní prostředky Azure ve virtuální síti. Ochrana je jednoduchá povolit v jakékoli nové nebo existující virtuální sítě a nevyžaduje žádné změny aplikace nebo prostředků. Má několik výhod oproti základní službě, včetně protokolování, upozorňování a telemetrie. V následujících částech jsou popsány klíčové funkce služby Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptivní ladění v reálném čase

Služba Azure DDoS Protection Basic pomáhá chránit zákazníky a předcházet dopadům na ostatní zákazníky. Například pokud je služba zřízena pro typický objem legitimní příchozí provoz, který je menší než *spouštěcí rychlost* zásady ochrany DDoS pro celou infrastrukturu, útok DDoS na prostředky tohoto zákazníka může zůstat bez povšimnutí. Obecněji řečeno, složitost nedávných útoků (například vícevektorové DDoS) a chování klientů specifické pro aplikaci vyžadují zásady vlastní ochrany pro jednotlivé zákazníky. Služba provádí toto přizpůsobení pomocí dvou přehledů:

- Automatické učení o vzorcích provozu na zákazníka (na IP) pro vrstvu 3 a 4.

- Minimalizace falešných poplachů, vzhledem k tomu, že škálování Azure umožňuje absorbovat značné množství provozu.

![Diagram fungování standardu DDoS Protection Standard s "Generováním zásad" v kroužku](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetrie ochrany DDoS, monitorování a upozorňování

DDoS Protection Standard poskytuje bohatou telemetrii prostřednictvím [Azure Monitor](/azure/azure-monitor/overview) po dobu trvání útoku DDoS. Můžete nakonfigurovat výstrahy pro některou z metrik Azure Monitor, které používá DDoS Protection. Protokolování můžete integrovat pomocí Splunk (Azure Event Hubs), protokolů Azure Monitor a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní Diagnostika azure monitoru.

##### <a name="ddos-mitigation-policies"></a>Zásady zmírňování ddos

Na webu Azure Portal vyberte **Monitor Metrics** > **Metrics**. V podokně **Metriky** vyberte skupinu prostředků, vyberte typ prostředku **veřejné IP adresy**a vyberte veřejnou IP adresu Azure. Metriky DDoS jsou viditelné v podokně **Dostupné metriky.**

DDoS Protection Standard používá tři zásady automatického zmírňování (TCP SYN, TCP a UDP) pro každou veřejnou IP adresu chráněného prostředku ve virtuální síti, která má povoleno DDoS. Prahové hodnoty zásad můžete zobrazit výběrem **metriky Příchozí pakety pro aktivaci zmírnění DDoS**.

![Graf dostupných metrik a metrik](./media/ddos-best-practices/image7.png)

Prahové hodnoty zásad jsou automaticky konfigurovány prostřednictvím profilování síťového provozu založeného na strojovém učení. Zmírnění DDoS dochází pro IP adresu pod útokem pouze v případě, že je překročena prahová hodnota zásady.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrika pro IP adresu v rámci útoku DDoS

Pokud je veřejná IP adresa pod útokem, hodnota metriky **Under DDoS attack nebo nese** změní na 1 jako DDoS Protection provádí zmírnění provozu útoku.

![Metrika "Pod DDoS útokem nebo ne" a graf](./media/ddos-best-practices/image8.png)

Doporučujeme nakonfigurovat výstrahu na tuto metriku. Poté budete upozorněni, pokud je aktivní zmírnění DDoS provedené na vaší veřejné IP adrese.

Další informace najdete [v tématu Správa Azure DDoS Protection Standard pomocí portálu Azure](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Brána firewall webové aplikace pro útoky prostředků

Specifické pro útoky prostředků na aplikační vrstvě, měli byste nakonfigurovat bránu firewall webové aplikace (WAF) pro zabezpečení webových aplikací. WAF kontroluje příchozí webový provoz blokovat sql injekce, skriptování mezi weby, DDoS a další útoky vrstvy 7. Azure poskytuje [WAF jako funkci Application Gateway](/azure/application-gateway/application-gateway-web-application-firewall-overview) pro centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. Od partnerů Azure jsou k dispozici další nabídky WAF, které by mohly být vhodnější pro vaše potřeby prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Dokonce i firewally webových aplikací jsou náchylné k volumetrickým útokům a útokům vyčerpání stavu. Důrazně doporučujeme povolit Standard ochrany DDoS ve virtuální síti WAF, který pomáhá chránit před objemovým a protokolovým útoky. Další informace naleznete v části [Referenční architektury ochrany DDoS.](#ddos-protection-reference-architectures)

### <a name="protection-planning"></a>Plánování ochrany

Plánování a příprava jsou rozhodující pro pochopení toho, jak bude systém fungovat během útoku DDoS. Součástí tohoto úsilí je návrh plánu reakce na incidenty.

Pokud máte Standard ochrany DDoS, ujistěte se, že je povolena ve virtuální síti koncových bodů směřujících k internetu. Konfigurace výstrah DDoS vám pomůže neustále sledovat případné útoky na vaši infrastrukturu. 

Sledujte své aplikace nezávisle. Pochopit normální chování aplikace. Připravte se jednat, pokud se aplikace během útoku DDoS nechová očekávaným způsobem.

#### <a name="testing-through-simulations"></a>Testování prostřednictvím simulací

Je vhodné otestovat vaše předpoklady o tom, jak budou vaše služby reagovat na útok prováděním pravidelných simulací. Během testování ověřte, zda vaše služby nebo aplikace nadále fungují podle očekávání a že nedojde k narušení uživatelského prostředí. Identifikujte mezery z hlediska technologie i procesu a začleňte je do strategie odezvy DDoS. Doporučujeme provádět takové testy v pracovních prostředích nebo během mimo špičky, abyste minimalizovali dopad na produkční prostředí.

Spolupracujeme se [společností BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) na vytvoření rozhraní, kde mohou zákazníci Azure generovat provoz proti veřejným koncovým bodům s podporou ochrany DDoS pro simulace. Simulaci [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) můžete použít k:

- Ověřte, jak Azure DDoS Protection pomáhá chránit prostředky Azure před útoky DDoS.

- Optimalizujte proces reakce na incidenty v rámci útoku DDoS.

- Dokumentovat dodržování ddos.

- Trénujte své týmy zabezpečení sítě.

Kybernetická bezpečnost vyžaduje neustálé inovace v oblasti obrany. Azure DDoS Standard protection je nejmodernější nabídka s efektivním řešením pro zmírnění stále složitějších útoků DDoS.

## <a name="components-of-a-ddos-response-strategy"></a>Součásti strategie odezvy DDoS

Útok DDoS, který cílí prostředky Azure obvykle vyžaduje minimální zásah z hlediska uživatele. Přesto začlenění zmírnění DDoS jako součást strategie reakce na incidenty pomáhá minimalizovat dopad na kontinuitu podnikání.

### <a name="microsoft-threat-intelligence"></a>Microsoft threat intelligence

Společnost Microsoft má rozsáhlou síť pro analýzu hrozeb. Tato síť využívá kolektivní znalosti rozšířené komunity zabezpečení, která podporuje online služby společnosti Microsoft, partnery společnosti Microsoft a vztahy v rámci komunity zabezpečení Internetu. 

Jako poskytovatel kritické infrastruktury obdrží společnost Microsoft včasná upozornění na hrozby. Společnost Microsoft shromažďuje informace o hrozbách ze svých online služeb a ze své globální zákaznické základny. Microsoft začleňuje všechny tyto analýzy hrozeb zpět do produktů Azure DDoS Protection.

Jednotka Microsoft Digital Crimes Unit (DCU) také provádí útočné strategie proti botnetům. Botnety jsou běžným zdrojem velení a řízení pro Útoky DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Hodnocení rizik vašich prostředků Azure

Je nutné pochopit rozsah vašeho rizika z útoku DDoS průběžně. Pravidelně se zeptejte sami sebe:

- Jaké nové veřejně dostupné prostředky Azure potřebují ochranu?

- Existuje jediný bod selhání ve službě? 

- Jak lze služby izolovat, aby se omezil dopad útoku a zároveň byly služby k dispozici platným zákazníkům?

- Existují virtuální sítě, kde by měl být povolen standard DDoS Protection Standard, ale není? 

- Jsou moje služby aktivní/aktivní s převzetím služeb při selhání ve více oblastech?

### <a name="customer-ddos-response-team"></a>Tým odezvy zákazníka DDoS

Vytvoření týmu odezvy DDoS je klíčovým krokem v reakci na útok rychle a efektivně. Identifikujte kontakty ve vaší organizaci, které budou dohlížet na plánování i provádění. Tento tým odpovědi DDoS by měl důkladně pochopit službu Azure DDoS Protection Standard. Ujistěte se, že tým může identifikovat a zmírnit útok koordinací s interními a externími zákazníky, včetně týmu podpory společnosti Microsoft.

Pro váš tým odezvy DDoS doporučujeme použít simulační cvičení jako normální součást dostupnosti služeb a plánování kontinuity. Tato cvičení by měla zahrnovat testování v měřítku.

### <a name="alerts-during-an-attack"></a>Upozornění během útoku

Azure DDoS Protection Standard identifikuje a zmírňuje útoky DDoS bez zásahu uživatele. Chcete-li získat upozornění, když je aktivní zmírnění chráněné veřejné IP, můžete [nakonfigurovat upozornění](/azure/virtual-network/ddos-protection-manage-portal) na metriku **Pod Útok DDoS nebo ne**. Můžete vytvořit výstrahy pro ostatní metriky DDoS, abyste pochopili rozsah útoku, přenos, který byl vynechán, a další podrobnosti.

#### <a name="when-to-contact-microsoft-support"></a>Kdy kontaktovat podporu microsoftu

- Během útoku DDoS zjistíte, že výkon chráněného prostředku je vážně snížena nebo prostředek není k dispozici.

- Myslíte si, že služba DDoS Protection se nechová podle očekávání. 

  Služba DDoS Protection spustí zmírnění pouze v případě, že zásady hodnoty **metriky pro aktivaci zmírnění DDoS (TCP/TCP SYN/UDP)** jsou nižší než přenosy přijaté na chráněném veřejném prostředku IP.

- Plánujete virovou událost, která výrazně zvýší váš síťový provoz.

- Herec pohrozil, že zahájí DDoS útok proti vašim zdrojům.

- Pokud potřebujete povolit seznam IP nebo IP rozsah z Azure DDoS Protection Standard. Běžným scénářem je povolit seznam IP, pokud je provoz směrován z externího cloudu WAF do Azure. 

Pro útoky, které mají kritický obchodní dopad, vytvořte [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)závažnosti A .

### <a name="post-attack-steps"></a>Kroky po útoku

Je to vždy dobrá strategie udělat posmrtný po útoku a upravit strategii odezvy DDoS podle potřeby. Co je třeba zvážit:

- Došlo k narušení služby nebo uživatelského prostředí z důvodu nedostatku škálovatelné architektury?

- Které aplikace nebo služby utrpěly nejvíce?

- Jak účinná byla strategie reakce DDoS a jak ji lze zlepšit?

Pokud máte podezření, že jste pod útokem DDoS, eskalovat prostřednictvím běžných kanálů podpory Azure.

## <a name="ddos-protection-reference-architectures"></a>Referenční architektury DDoS Protection

DDoS Protection Standard je určen [pro služby, které jsou nasazeny ve virtuální síti](/azure/virtual-network/virtual-network-for-azure-services). Pro ostatní služby platí výchozí služba DDoS Protection Basic. Následující referenční architektury jsou uspořádány podle scénářů, se vzory architektury seskupeny dohromady.

### <a name="virtual-machine-windowslinux-workloads"></a>Úlohy virtuálních strojů (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Aplikace spuštěná na virtuálních počítačích s vyrovnáváním zatížení

Tato referenční architektura zobrazuje sadu osvědčených postupů pro spuštění více virtuálních počítačů se systémem Windows ve škálovací sadě za vykladačem zatížení, aby se zlepšila dostupnost a škálovatelnost. Tuto architekturu lze použít pro jakékoli bezstavové úlohy, jako je například webový server.

![Diagram referenční architektury pro aplikaci spuštěnou na virtuálních počítačích s vyrovnáváním zatížení](./media/ddos-best-practices/image9.png)

V této architektuře jsou úlohy distribuované napříč několika instancemi virtuálního počítače. Existuje jedna veřejná IP adresa a internetový provoz je distribuován do virtuálních počítačů prostřednictvím vyrovnávání zatížení. DDoS Protection Standard je povolená ve virtuální síti nástroje pro vyrovnávání zatížení Azure (internet), ke kterému je přidružena veřejná IP adresa.

Vyrovnávání zatížení distribuuje příchozí požadavky na internet do instancí virtuálních aplikací. Škálovací sady virtuálních strojů umožňují škálování počtu virtuálních počítačů v ručním nebo vyzouvaného nebo automaticky na základě předdefinovaných pravidel. To je důležité, pokud je prostředek pod útokem DDoS. Další informace o této referenční architektuře naleznete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Aplikace spuštěná na vrstvě Windows N

N-vrstvou architekturu je možné implementovat mnoha způsoby. Následující diagram znázorňuje typickou třívrstvou webovou aplikaci. Tato architektura vychází z článku [Spustit virtuální počítačs s vyrovnáváním zatížení pro škálovatelnost a dostupnost](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Webové a obchodní vrstvy používají virtuální počítače s vyrovnáváním zatížení.

![Diagram referenční architektury pro aplikaci spuštěnou na vrstvě Windows N](./media/ddos-best-practices/image10.png)

V této architektuře je ve virtuální síti povolen standard ochrany DDoS. Všechny veřejné IP adresy ve virtuální síti získají ochranu DDoS pro vrstvu 3 a 4. Pro ochranu vrstvy 7 nasaďte aplikační bránu ve skladové jednotce WAF. Další informace o této referenční architektuře naleznete v [tomto článku](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>Webová aplikace PaaS

Tato referenční architektura zobrazuje spuštění aplikace služby Azure App Service v jedné oblasti. Tato architektura zobrazuje sadu osvědčených postupů pro webovou aplikaci, která používá [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) a [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Pohotovostní oblast je nastavena pro scénáře převzetí služeb při selhání.

![Diagram referenční architektury pro webovou aplikaci PaaS](./media/ddos-best-practices/image11.png)

Azure Traffic Manager směruje příchozí požadavky na aplikační bránu v jedné z oblastí. Během normálních operací směruje požadavky na aplikační bránu v aktivní oblasti. Pokud tato oblast přestane být k dispozici, Traffic Manager převezme služby při selhání aplikační brány v oblasti pohotovostního režimu.

Veškerý provoz z internetu určený do webové aplikace je směrován na [veřejnou IP adresu Aplikační brány](/azure/application-gateway/application-gateway-web-app-overview) prostřednictvím Traffic Manageru. V tomto scénáři samotná aplikační služba (webová aplikace) není přímo externě orientovaná a je chráněna aplikační bránou. 

Doporučujeme nakonfigurovat sku WAF (zabránit režimu) aplikační brány, která pomáhá chránit před útoky vrstvy 7 (HTTP/HTTPS/WebSocket). Webové aplikace jsou navíc nakonfigurovány tak, aby [přijímaly pouze přenosy z](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP adresy aplikační brány.

Další informace o této referenční architektuře naleznete v [tomto článku](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>Zmírnění pro non-web PaaS služby

#### <a name="hdinsight-on-azure"></a>HDInsight v Azure

Tato referenční architektura zobrazuje konfiguraci standardu DDoS Protection Standard pro [cluster Azure HDInsight](/azure/hdinsight/). Ujistěte se, že cluster HDInsight je propojen s virtuální sítí a že je ve virtuální síti povolena ochrana DDoS.

![Podokna "HDInsight" a "Upřesnit nastavení" s nastavením virtuální sítě](./media/ddos-best-practices/image12.png)

![Výběr pro povolení ochrany DDoS](./media/ddos-best-practices/image13.png)

V této architektuře je provoz určený do clusteru HDInsight z internetu směrován na veřejnou IP adresu přidruženou k vyvyčovávači zatížení brány HDInsight. Provyčitatel zatížení brány pak odešle provoz do hlavníu uzlu nebo pracovníu uzlů přímo. Vzhledem k tomu, že standard Ochrany DDoS je povolen ve virtuální síti HDInsight, všechny veřejné IP adresy ve virtuální síti získají ochranu DDoS pro vrstvu 3 a 4. Tuto referenční architekturu lze kombinovat s referenčníarchitektury N-tier a více oblastí.

Další informace o této referenční architektuře najdete v [tématu Rozšíření Azure HDInsight pomocí](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentace virtuální sítě Azure.


> [!NOTE]
> Prostředí Azure App Service Environment pro PowerApps nebo správu rozhraní API ve virtuální síti s veřejnou IP adresou nejsou nativně podporované.

## <a name="next-steps"></a>Další kroky

* [Sdílená odpovědnost v cloudu](shared-responsibility.md)

* [Stránka produktu Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Dokumentace azure ddos ochrany](/azure/virtual-network/ddos-protection-overview)
