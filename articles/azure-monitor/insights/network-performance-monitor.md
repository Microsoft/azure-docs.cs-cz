---
title: Řešení Network Performance Monitor v Azure | Microsoft Docs
description: Network Performance Monitor v Azure vám pomůže monitorovat výkon sítí v reálném čase a zjišťovat a vyhledávat tak slabá místa výkonu sítě.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 92326ea4d3de896a67b2953fb612b79948044d08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573419"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Řešení Network Performance Monitor v Azure

![Symbol Network Performance Monitor](./media/network-performance-monitor/npm-symbol.png)

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. Můžete pokračovat v používání testů vytvořených před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, [migrujte testy z Network Performance Monitor na nové monitorování připojení](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) v Azure Network Watcher před 29. února 2024.

Network Performance Monitor je cloudové řešení monitorování hybridní sítě, které vám pomůže monitorovat výkon sítě mezi různými body v infrastruktuře vaší sítě. Umožňuje rovněž monitorovat síťové připojení ke službě a koncovým bodům aplikace a monitorovat výkon Azure ExpressRoute. 

Network Performance Monitor detekuje problémy se sítí, jako jsou přenosy dat blackholing, chyby směrování a problémy, které nedokáže detekovat konvenční metody monitorování sítě. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Network Performance Monitor nabízí tři široké možnosti: 

* [Sledování výkonu](network-performance-monitor-performance-monitor.md): můžete monitorovat síťové připojení mezi nasazeními v cloudu a místními umístěními, několika datacentry a firemními pobočkami a vysoce vícevrstvými aplikacemi nebo mikroslužbami. Pomocí nástroje sledování výkonu můžete detekovat problémy se sítí ještě předtím, než je uživatelé vystěžují.

* [Monitorování připojení služby](network-performance-monitor-service-connectivity.md): můžete monitorovat připojení uživatelů ke službám, které vás zajímají, určit infrastrukturu v cestě a určit, kde dochází k kritickým bodům sítě. Můžete znát výpadky před vašimi uživateli a zobrazit přesné umístění problémů podél síťové cesty. 

    Tato schopnost vám pomůže provádět testy založené na protokolech HTTP, HTTPS, TCP a ICMP a monitorovat téměř v reálném čase nebo historicky dostupnou dostupnost a dobu odezvy vaší služby. Můžete také monitorovat příspěvek sítě při výpadku a latenci paketů. Pomocí mapy topologie sítě můžete izolovat zpomalování sítě. Můžete identifikovat problematické body, ke kterým dojde podél síťové cesty z uzlu ke službě, a data latence u každého segmentu směrování. Pomocí integrovaných testů můžete monitorovat síťové připojení k Microsoft 365 a Dynamics CRM bez nutnosti předem nakonfigurovat. Díky této funkci můžete monitorovat síťové připojení k jakémukoli koncovému bodu podporujícímu protokol TCP, jako jsou websites, aplikace SaaS, PaaS aplikace a databáze SQL.

* [Monitorování ExpressRoute](network-performance-monitor-expressroute.md): monitorujte komplexní konektivitu a výkon mezi firemními pobočkami a Azure, a to prostřednictvím Azure ExpressRoute.  

Další informace o různých funkcích podporovaných nástrojem [Network Performance Monitor](../../networking/network-monitoring-overview.md) jsou k dispozici online.
 
## <a name="supported-regions"></a>Podporované oblasti
NPM může monitorovat propojení mezi sítěmi a aplikacemi v jakékoli části světa, a to z pracovního prostoru hostovaného v jedné z následujících oblastí:
* Severní Evropa
* West Europe
* Švýcarsko – sever
* Francie – střed
* Jižní Afrika – sever
* Střední Kanada
* USA – západ
* USA – středozápad
* USA – středosever
* Středojižní USA
* Střední USA
* East US
* USA – východ 2
* Západní USA 2
* Východní Japonsko
* Jihovýchodní Asie
* Austrálie – jihovýchod
* Austrálie – střed
* Austrálie – východ
* Jižní Velká Británie
* Východní Asie
* Jižní Korea – střed
* Indie – střed
* ) – Virginia vlády USA
* Arizona vlády USA
* Čína – východ 2


Seznam podporovaných oblastí pro monitorování ExpressRoute je k dispozici v [dokumentaci](../../expressroute/how-to-npm.md?utm_swu=8117).


## <a name="set-up-and-configure"></a>Nastavení a konfigurace

> [!NOTE]
> Můžete se také podívat na Log Analytics agenta označovaného jako Microsoft Monitoring Agent (MMA) nebo agent OMS Linux.

### <a name="install-and-configure-agents"></a>Instalace a konfigurace agentů 

Pomocí základních procesů nainstalujete agenty na [počítačích s Windows připojit k Azure monitor](../agents/agent-windows.md), [připojíte počítače se systémem Linux k Azure monitor (Preview)](../../virtual-machines/extensions/oms-linux.md) a [připojíte Operations Manager k Azure monitor](../agents/om-agents.md).

### <a name="where-to-install-the-agents"></a>Kam nainstalovat agenty 

* **Sledování výkonu**: Nainstalujte agenty Log Analytics v alespoň jednom uzlu připojeném ke všem podsítím, ze kterých chcete monitorovat síťové připojení k ostatním podsítím.

    Chcete-li monitorovat síťové propojení, nainstalujte agenty do obou koncových bodů tohoto propojení. Pokud si nejste jisti topologií sítě, nainstalujte agenty na serverech s kritickými úlohami, mezi kterými chcete monitorovat výkon sítě. Například pokud chcete monitorovat síťové připojení mezi webovým serverem a serverem se systémem SQL, nainstalujte agenta na oba servery. Agenti monitorují připojení k síti (propojení) mezi hostiteli, nikoli se samotnými hostiteli. 

* **Monitorování připojení služby**: na všechny uzly, ze kterých chcete monitorovat síťové připojení ke koncovému bodu služby, nainstalujte agenta Log Analytics. Příkladem je, že chcete monitorovat síťové připojení k Microsoft 365 z webů Office označených O1, O2 a O3. Nainstalujte agenta Log Analytics na alespoň jeden uzel každý v umístění O1, O2 a O3. 

* **ExpressRoute monitor**: ve vaší virtuální síti Azure nainstalujte aspoň jednoho agenta Log Analytics. Nainstalujte také alespoň jednoho agenta do místní podsítě, který je připojen prostřednictvím privátního partnerského vztahu ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů Log Analytics pro monitorování 

Network Performance Monitor používá syntetické transakce k monitorování výkonu sítě mezi zdrojovými a cílovými agenty. Můžete zvolit TCP a ICMP jako protokol pro monitorování v možnosti monitorování výkonu a sledování připojení služby. Jako monitorovací protokol pro ExpressRoute monitor je k dispozici pouze protokol TCP. Ujistěte se, že brána firewall umožňuje komunikaci mezi Log Analytics agenty, které se používají pro monitorování protokolu, který zvolíte. 

* **Protokol TCP**: Pokud jako protokol pro monitorování zvolíte TCP, otevřete port brány firewall u agentů používaných pro Network Performance Monitor a monitorování ExpressRoute a ujistěte se, že se agenti mohou vzájemně připojit. V případě počítačů s Windows otevřete tento port spuštěním skriptu [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell bez parametrů v okně PowerShellu s oprávněními správce.
U počítačů se systémem Linux je nutné změnit použití číslo_portu ručně. 
* Přejděte na cestu:/var/opt/Microsoft/omsagent/npm_state. 
* Otevřít soubor: npmdregistry
* Změna hodnoty pro číslo portu ```“PortNumber:<port of your choice>”```

 Upozorňujeme, že použitá čísla portů by se měla shodovat se všemi agenty používanými v pracovním prostoru. 

Skript vytvoří klíče registru vyžadované řešením. Vytvoří také pravidla brány Windows Firewall, která agentům umožní vytvářet připojení TCP mezi sebou. Klíče registru vytvořené skriptem určují, jestli se mají protokolovat protokoly ladění a cesta k souboru protokolů. Skript také definuje port TCP agenta, který se používá pro komunikaci. Hodnoty těchto klíčů jsou automaticky nastaveny pomocí skriptu. Tyto klíče neměňte ručně. Ve výchozím nastavení je port otevřený 8084. Vlastní port můžete použít zadáním parametru číslo_portu ke skriptu. Použijte stejný port na všech počítačích, na kterých se skript spouští. 

   >[!NOTE]
   > Skript nakonfiguruje pouze místní bránu firewall systému Windows. Pokud máte bránu firewall sítě, ujistěte se, že umožňuje provoz určený pro port TCP používaný Network Performance Monitor.

   >[!NOTE]
   > Pro monitorování připojení služby nemusíte spouštět skript prostředí [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell.

    

* **Protokol ICMP**: Pokud jako protokol pro monitorování ZVOLÍTE protokol ICMP, povolte pro spolehlivé využívání protokolu ICMP následující pravidla brány firewall:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurace řešení 

1. Přidejte řešení Network Performance Monitor do svého pracovního prostoru z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). Můžete také použít proces popsaný v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md). 
2. Otevřete pracovní prostor Log Analytics a vyberte dlaždici s **přehledem** . 
3. Vyberte dlaždici **Network Performance Monitor** s řešením zpráv *vyžaduje další konfiguraci*.

   ![Dlaždice Network Performance Monitor](media/network-performance-monitor/npm-config.png)

4. Na stránce **instalace** se zobrazí možnost instalovat agenty Log Analytics a nakonfigurovat agenty pro monitorování v zobrazení **Společná nastavení** . Jak už bylo vysvětleno, pokud jste nainstalovali a nakonfigurovali agenty Log Analytics, vyberte zobrazení **Nastavení** a nakonfigurujte možnost, kterou chcete použít. 

   **Sledování výkonu**: zvolte protokol, který se má použít pro syntetické transakce ve **výchozím** pravidlu sledování výkonu, a vyberte **Uložit & pokračovat**. Tento výběr protokolu se drží jenom pro výchozí pravidlo generované systémem. Protokol musíte zvolit při explicitním vytvoření pravidla nástroje sledování výkonu. Na kartě **sledování výkonu** můžete vždycky přejít na **výchozí** nastavení pravidla (zobrazí se po dokončení konfigurace dne 0) a pozdější změnu protokolu. Pokud nechcete, aby funkce sledování výkonu nesledovala, můžete výchozí pravidlo zakázat z **výchozího** nastavení pravidla na kartě **sledování výkonu** .

   ![Zobrazení sledování výkonu](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitorování připojení služby**: funkce poskytuje integrované předkonfigurované testy pro monitorování připojení k síti Microsoft 365 a Dynamics 365 od vašich agentů. Zvolte Microsoft 365 a služby Dynamics 365, které chcete monitorovat, zaškrtnutím políček vedle nich. Chcete-li vybrat agenty, ze kterých chcete monitorovat, vyberte možnost **přidat agenty**. Pokud tuto funkci nechcete používat nebo ji chcete nastavit později, nevybírejte nic a vyberte **uložit & pokračovat**.

   ![Zobrazení monitorování připojení služby](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute monitor**: vyberte **Vyhledat** a vyhledejte všechny soukromé partnerské vztahy ExpressRoute, které jsou připojené k virtuálním sítím v předplatném Azure propojené s tímto pracovním prostorem Log Analytics. 

   ![Zobrazení monitorování ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Po dokončení zjišťování jsou zjištěné okruhy a partnerské vztahy uvedeny v tabulce. 

   ![Konfigurační stránka Network Performance Monitor](media/network-performance-monitor/npm-private-peerings.png)
    
Monitorování těchto okruhů a partnerských vztahů je zpočátku v zakázaném stavu. Vyberte všechny prostředky, které chcete monitorovat, a nakonfigurujte monitorování pro ně ze zobrazení podrobností na pravé straně. Kliknutím na **Uložit** uložte konfiguraci. Další informace najdete v článku Konfigurace monitorování ExpressRoute. 

Po dokončení instalace bude trvat 30 minut až hodinu, než se data naplní. I když řešení agreguje data z vaší sítě, zobrazí se na dlaždici s **přehledem** Network Performance Monitor, že tato zpráva *vyžaduje další konfiguraci* . Po shromáždění a indexování dat se ikona **přehledu** změní a informuje o stavu vaší sítě ve shrnutí. Pak můžete upravit monitorování uzlů, na kterých jsou nainstalované agenti Log Analytics, a také podsítě zjištěné ve vašem prostředí.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Úprava nastavení monitorování pro podsítě a uzly 

Všechny podsítě s alespoň jedním nainstalovaným agentem jsou uvedeny na kartě **podsítě** na stránce konfigurace. 


Povolení nebo zakázání monitorování konkrétních podsítí:

1. Zaškrtněte nebo zrušte zaškrtnutí políčka vedle **ID podsítě**. Pak se ujistěte, že je podle potřeby zapnuté nebo nezaškrtnuté políčko **použít pro monitorování** . Můžete vybrat nebo zrušit výběr více podsítí. V případě zakázání se podsítě nemonitorují a agenti se aktualizují tak, aby zastavili příkazy pro otestování dalších agentů. 
2. Vyberte uzly, které chcete monitorovat v konkrétní podsítí. Vyberte ze seznamu požadovanou síť a přesuňte požadované uzly mezi seznamy, které obsahují nemonitorované a monitorované uzly. Do podsítě můžete přidat vlastní popis.
3. Kliknutím na **Uložit** uložte konfiguraci. 

#### <a name="choose-nodes-to-monitor"></a>Vyberte uzly, které chcete monitorovat.

Všechny uzly, které mají nainstalovaného agenta, jsou uvedeny na kartě **uzly** . 

1. Zaškrtněte nebo zrušte zaškrtnutí uzlů, které chcete monitorovat nebo zastavit monitorování. 
2. Vyberte možnost **použít pro monitorování** nebo ji podle potřeby vymažte. 
3. Vyberte **Uložit**. 


Nakonfigurujte možnosti, které chcete:

- [Monitorování výkonu](network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování připojení služby](network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Podrobnosti shromažďování dat
Pokud chcete shromažďovat informace o ztrátě a latenci, Network Performance Monitor využívá pakety handshake TCP SYN-SYNACK-ACK, když jako protokol zvolíte TCP. Network Performance Monitor používá odezvu ECHO ICMP protokolu ICMP, když jako protokol zvolíte protokol ICMP. Trase trasování se používá také k získání informací o topologii.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují pro Network Performance Monitor.

| Platforma | Přímý Agent | Agent System Center Operations Manager | Azure Storage | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Zprávy TCP handshake/ICMP ECHO každých 5 sekund, data odesílaná každé 3 minuty |
 

 
Řešení používá syntetické transakce k vyhodnocení stavu sítě. Agenti Log Analytics nainstalovanou v různých fázích paketů protokolu TCP sítě Exchange nebo ozvěny ICMP mezi sebou. Zda agenti používají pakety TCP nebo ozvěnu protokolu ICMP, závisí na protokolu, který jste vybrali pro monitorování. V tomto procesu se agenti učí dobu odezvy a ztrátu paketů, pokud existují. Každý agent pravidelně provádí trasu trasování ostatním agentům k nalezení všech různých tras v síti, které je třeba testovat. Pomocí těchto dat můžou agenti odvodit hodnoty latence sítě a ztráty paketů. Testy se opakují každých pět sekund. Data se před odesláním do pracovního prostoru Log Analytics v Azure Monitor agreguje přibližně o tři minuty od agentů.



>[!NOTE]
> I když agenti spolu často komunikují, negenerují významné síťové přenosy při provádění testů. Agenti spoléhají jenom na pakety protokolu TCP SYN-SYNACK-ACK handshake k určení ztráty a latence. Vyměňovány se žádné datové pakety. Během tohoto procesu vzájemně komunikují agenti, a to v případě potřeby. Komunikační topologie agenta je optimalizovaná tak, aby snižovala síťový provoz.

## <a name="use-the-solution"></a>Použití řešení 

### <a name="network-performance-monitor-overview-tile"></a>Dlaždice přehledu Network Performance Monitor 

Po povolení řešení Network Performance Monitor poskytuje dlaždice řešení na stránce **Přehled** rychlý přehled stavu sítě. 

 ![Dlaždice přehledu Network Performance Monitor](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Řídicí panel Network Performance Monitor 

* **Nejčastější události stavu sítě**: Tato stránka obsahuje seznam nejaktuálnějších událostí stavu a výstrah v systému a čas, kdy byly události aktivní. Událost stavu nebo výstraha je vygenerována vždy, když hodnota zvolené metriky (ztráta, latence, doba odezvy nebo využití šířky pásma) pro pravidlo monitorování překračuje prahovou hodnotu. 

* **ExpressRoute monitor**: Tato stránka poskytuje souhrn stavů pro různé připojení ExpressRoute partnerských vztahů, které řešení monitoruje. Dlaždice **topologie** zobrazuje počet síťových cest přes okruhy ExpressRoute, které jsou monitorovány ve vaší síti. Kliknutím na tuto dlaždici přejdete do zobrazení **topologie** .

* **Monitorování připojení služby**: Tato stránka poskytuje souhrny stavu pro různé testy, které jste vytvořili. Dlaždice **topologie** znázorňuje počet monitorovaných koncových bodů. Kliknutím na tuto dlaždici přejdete do zobrazení **topologie** .

* **Sledování výkonu**: Tato stránka obsahuje souhrny stavu pro **síťové** **odkazy a odkazy na podsítě,** které řešení monitoruje. Dlaždice **topologie** zobrazuje počet síťových cest, které jsou monitorovány ve vaší síti. Kliknutím na tuto dlaždici přejdete do zobrazení **topologie** . 

* **Běžné dotazy**: Tato stránka obsahuje sadu vyhledávacích dotazů, které přímo načítají data monitorování nezpracovaných sítí. Tyto dotazy můžete použít jako výchozí bod k vytvoření vlastních dotazů pro přizpůsobené vytváření sestav. 

   ![Řídicí panel Network Performance Monitor](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Přechod k podrobnostem pro hloubku 

Můžete vybrat různé odkazy na řídicím panelu řešení a přejít k podrobnostem v jakékoli oblasti zájmu. Pokud se například na řídicím panelu zobrazí výstraha nebo není síťové propojení v pořádku, vyberte ho pro další prošetření. Stránka obsahuje seznam všech odkazů na podsítě pro konkrétní síťové propojení. Můžete zobrazit informace o ztrátě, latenci a stavu jednotlivých propojení mezi sítěmi. Můžete rychle zjistit, které propojení v síti způsobuje problémy. Výběrem **Zobrazit odkazy na uzel** zobrazíte všechna propojení uzlů pro odkaz není v pořádku. Pak můžete zobrazit jednotlivá propojení mezi uzly a najít odkazy uzlů, které nejsou v pořádku. 

Vyberte **Zobrazit topologii** pro zobrazení topologie směrování po směrování tras mezi zdrojovým a cílovým uzlem. Trasy, které nejsou v pořádku, se zobrazí červeně. Můžete zobrazit latenci, kterou přispěly jednotlivé segmenty směrování, abyste mohli problém rychle identifikovat na určitou část sítě.

 

### <a name="network-state-recorder-control"></a>Ovládací prvek zapisovače stavu sítě

Každé zobrazení zobrazuje snímek stavu vaší sítě v určitém časovém okamžiku. Ve výchozím nastavení se zobrazuje poslední stav. Pruh v horní části stránky zobrazuje bod v čase, pro který se zobrazí stav. Pokud chcete zobrazit snímek stavu vaší sítě v předchozím čase, vyberte **Akce**. Při zobrazení nejnovějšího stavu můžete taky povolit nebo zakázat automatické aktualizace každé stránky. 

 ![Záznam o stavu sítě](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafy trendů 

Na každé úrovni, kterou přecházíte k podrobnostem, uvidíte trend příslušné metriky. Může se jednat o ztrátu, latenci, dobu odezvy nebo využití šířky pásma. Chcete-li změnit časový interval pro trend, použijte ovládací prvek čas v horní části grafu. 

Grafy trendů zobrazují historický perspektivu výkonu metriky výkonu. Některé problémy se sítí jsou přechodným charakterem a je obtížné je zachytit tím, že prohlížíte pouze aktuální stav sítě. Problémy se můžou rychle domístit a zmizí před tím, než je někdo vystavuje, stačí se znovu podívat v pozdějším čase. Tyto přechodné problémy můžou být pro Správce aplikací obtížné. Problémy se často zobrazují jako neobjasněné zvýšení doby odezvy aplikace, a to i v případě, že se některé součásti aplikace zdají běžet plynule. 

Tyto typy problémů můžete snadno detekovat tak, že si prohlížíte graf trendu. Problém se jeví jako náhlé špička v případě latence sítě nebo ztráty paketů. Chcete-li tento problém prozkoumat, pomocí ovládacího prvku pro záznam o stavu sítě zobrazte snímek a topologii sítě pro daný bod v čase, kdy k problému došlo.

 
![Grafy trendů](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa topologie 

Network Performance Monitor se zobrazuje topologie směrování po směrování mezi zdrojovým a cílovým koncovým bodem na interaktivní mapě topologie. Mapu topologie zobrazíte tak, že vyberete dlaždici **topologie** na řídicím panelu řešení. Můžete také vybrat odkaz **Zobrazit topologii** na stránkách s podrobnostmi o rozbalení. 

Mapa topologie zobrazuje, kolik tras mezi zdrojovým a cílovým způsobem a jaké cesty datové pakety přebírají. K dispozici je také latence, kterou přispěly k jednotlivým segmentům směrování sítě. Všechny cesty, pro které je Celková latence cesty nad prahovou hodnotou (nastavenou v odpovídajícím pravidlu monitorování), jsou zobrazeny červeně. 

Když na mapě topologie vyberete uzel nebo najedete myší, zobrazí se vlastnosti uzlu, jako je například plně kvalifikovaný název domény a IP adresa. Vyberte směrování, abyste viděli jeho IP adresu. Komplikované síť můžete identifikovat tak, že všímáte latenci, kterou přispívá. Chcete-li filtrovat konkrétní trasy, použijte filtry v podokně sbalitelné akce. Chcete-li zjednodušit síťové topologie, skryjte mezilehlé směrování pomocí posuvníku v podokně akce. Mapu topologie můžete přiblížit nebo oddálit pomocí kolečka myši. 

Topologie zobrazená v mapě je topologie vrstvy 3 a neobsahuje zařízení a připojení vrstvy 2. 

 
![Mapa topologie](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Dotazy protokolu v Azure Monitor

Všechna data, která jsou zveřejněna prostřednictvím řídicího panelu Network Performance Monitor a stránky s přehledem podrobností, jsou také k dispozici v [protokolových dotazech](../logs/log-query-overview.md). Můžete provádět interaktivní analýzu dat v úložišti a korelovat data z různých zdrojů. Můžete také vytvořit vlastní výstrahy a zobrazení a exportovat data do aplikace Excel, Power BI nebo odkaz Shared. Oblast **Common dotazů** na řídicím panelu obsahuje několik užitečných dotazů, které můžete použít jako výchozí bod k vytváření vlastních dotazů a sestav. 

## <a name="alerts"></a>Výstrahy

Network Performance Monitor používá funkce upozorňování [Azure monitor](../alerts/alerts-overview.md).

To znamená, že všechna oznámení jsou spravovaná pomocí [skupin akcí](../alerts/action-groups.md).  

Pokud jste uživatelem NPM, který vytváří upozornění prostřednictvím Log Analytics: 
1. Zobrazí se odkaz, který vás přesměruje na Azure Portal. Kliknutím na něj získáte přístup k portálu.
2. Klikněte na dlaždici řešení Network Performance Monitor. 
3. Přejděte ke konfiguraci.  
4. Vyberte test, na kterém chcete vytvořit výstrahu, a postupujte podle níže uvedených kroků.

Pokud jste uživatelem NPM, který vytváří upozornění prostřednictvím Azure Portal:  
1. Svůj e-mail můžete zadat přímo nebo můžete zvolit vytvoření výstrah prostřednictvím skupin akcí.
2. Pokud se rozhodnete zadat přímo svůj e-mail, vytvoří se skupina akcí se skupinou **akcí název npm e-mail** a do této skupiny akcí se přidá ID e-mailu.
3. Pokud se rozhodnete použít skupiny akcí, budete muset vybrat dříve vytvořenou skupinu akcí. Informace o tom, jak vytvořit skupinu akcí, najdete [tady.](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. Po úspěšném vytvoření výstrahy můžete pomocí odkazu spravovat výstrahy spravovat výstrahy. 

Pokaždé, když vytvoříte upozornění, NPM vytvoří pravidlo výstrahy protokolu založené na dotazech v Azure Monitor. Tento dotaz se ve výchozím nastavení spouští každých 5 minut. Azure monitor neúčtuje za první vytvořená pravidla upozornění protokolu 250 a všechna pravidla výstrah nad limitem pravidel upozornění protokolů 250 se budou fakturovat podle ceny za výstrahy na [stránce Azure monitor Price](https://azure.microsoft.com/pricing/details/monitor/).
Oznámení se účtují samostatně podle [ceny za oznámení na stránce Azure monitor Price](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Ceny

Informace o cenách jsou k dispozici [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby 

* **UserVoice:** Můžete publikovat své nápady pro Network Performance Monitor funkce, na kterých chceme pracovat. Navštivte [stránku UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Připojte se k našemu kohorta:** Vždycky se zajímá, že noví zákazníci se k naší kohorta připojí. V rámci tohoto řešení získáte předběžný přístup k novým funkcím a příležitostí, které nám pomohou vylepšit Network Performance Monitor. Pokud se zajímáte o připojení, vyplňte tento [rychlý průzkum](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Další kroky 
Přečtěte si další informace o [monitorování výkonu](network-performance-monitor-performance-monitor.md), [monitorování připojení služby](network-performance-monitor-performance-monitor.md)a [monitorování ExpressRoute](network-performance-monitor-expressroute.md). 
