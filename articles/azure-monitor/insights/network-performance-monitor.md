---
title: Řešení sledování výkonu sítě v Azure | Dokumenty společnosti Microsoft
description: Sledování výkonu sítě v Azure vám pomůže sledovat výkon vašich sítí téměř v reálném čase, abyste mohli zjišťovat a vyhledávat kritická místa výkonu sítě.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480874"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Řešení sledování výkonu sítě v Azure

![Symbol sledování výkonu sítě](./media/network-performance-monitor/npm-symbol.png)


Nástroj Sledování výkonu sítě je cloudové hybridní řešení monitorování sítě, které pomáhá sledovat výkon sítě mezi různými body síťové infrastruktury. Umožňuje rovněž monitorovat síťové připojení ke službě a koncovým bodům aplikace a monitorovat výkon Azure ExpressRoute. 

Nástroj Sledování výkonu sítě detekuje problémy se sítí, jako je blackholing provozu, chyby směrování a problémy, které konvenční metody monitorování sítě nejsou schopny zjistit. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Nástroj Sledování výkonu sítě nabízí tři široké možnosti: 

* [Sledování výkonu](network-performance-monitor-performance-monitor.md): Můžete monitorovat připojení k síti napříč cloudovými nasazeními a místními umístěními, více datovými centry a pobočkami a důležitými víceúrovňové aplikace nebo mikroslužbami. Pomocí funkce Sledování výkonu můžete zjistit problémy se sítí dříve, než si uživatelé stěžují.

* [Sledování připojení služby](network-performance-monitor-service-connectivity.md): Můžete sledovat připojení uživatelů ke službám, které vás zajímají, určit, jaká infrastruktura je v cestě, a určit, kde dochází k problematickám místům v síti. Můžete vědět o výpadcích před uživateli a zobrazit přesné umístění problémů na cestě k síti. 

    Tato funkce vám pomůže provádět testy založené na protokolu HTTP, HTTPS, TCP a ICMP, které budou monitorovat téměř v reálném čase nebo historicky dostupnost a dobu odezvy vaší služby. Můžete také sledovat příspěvek sítě ve ztrátě paketů a latenci. Pomocí mapy topologie sítě můžete izolovat zpomalení sítě. Můžete identifikovat problémové body, ke kterým dochází podél síťové cesty z uzlu do služby, s daty latence na každém směrování. Díky integrovaným testům můžete monitorovat připojení k síti k Office 365 a Dynamics CRM bez jakékoli předkonfigurace. Díky této funkci můžete sledovat připojení k síti k libovolnému koncovému bodu podporujícímu protokol TCP, jako jsou weby, aplikace SaaS, aplikace PaaS a databáze SQL.

* [Monitor ExpressRoute](network-performance-monitor-expressroute.md): Monitorujte připojení a výkon mezi koncovými soubory mezi pobočkami a Azure přes Azure ExpressRoute.  

Další informace o různých funkcích podporovaných [programem Sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jsou k dispozici online.
 
## <a name="supported-regions"></a>Podporované oblasti
NPM může monitorovat připojení mezi sítěmi a aplikacemi v jakékoli části světa z pracovního prostoru, který je hostován v jedné z následujících oblastí:
* Severní Evropa
* Západní Evropa
* Francie – střed
* Střední Kanada
* USA – západ
* USA – středozápad
* USA – středosever
* USA – středojih
* USA – střed
* USA – východ
* USA – východ 2
* USA – západ 2
* Východní Japonsko
* Jihovýchodní Asie
* Jihovýchodní Austrálie
* Austrálie – střed
* Austrálie – východ
* Jižní Velká Británie
* Východní Asie
* Jižní Korea – střed
* Indie – střed
* Americká vláda Virginie
* Čína východ 2


Seznam podporovaných oblastí pro ExpressRoute Monitor je k dispozici v [dokumentaci](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Nastavení a konfigurace

### <a name="install-and-configure-agents"></a>Instalace a konfigurace agentů 

Základní procesy slouží k instalaci agentů v [počítačích Connect Windows k Azure Monitor](../platform/agent-windows.md) a Connect Operations Manager k Azure [Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Kam nainstalovat agenty 

* **Sledování výkonu**: Nainstalujte agenty Log Analytics alespoň do jednoho uzlu připojeného ke každé podsíti, ze které chcete monitorovat připojení k síti k jiným podsítím.

    Chcete-li sledovat síťové propojení, nainstalujte agenty na oba koncové body tohoto propojení. Pokud si nejste jisti topologii sítě, nainstalujte agenty na servery s kritickými úlohami, mezi nimiž chcete sledovat výkon sítě. Chcete-li například sledovat síťové připojení mezi webovým serverem a serverem se systémem SQL, nainstalujte agenta na oba servery. Agenti monitorují připojení k síti (propojení) mezi hostiteli, nikoli samotnými hostiteli. 

* **Sledování připojení služby**: Nainstalujte agenta Analýzy protokolů do každého uzlu, ze kterého chcete sledovat připojení k síti ke koncovému bodu služby. Příkladem je, že chcete sledovat připojení k síti k Office 365 z kancelářských webů označených O1, O2 a O3. Nainstalujte agenta Log Analytics alespoň do jednoho uzlu v O1, O2 a O3. 

* **ExpressRoute Monitor:** Nainstalujte do virtuální sítě Azure alespoň jednoho agenta Analýzy protokolů. Nainstalujte také alespoň jednoho agenta do místní podsítě, která je propojena prostřednictvím soukromého partnerského vztahu ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů analýzy protokolů pro monitorování 

Sledování výkonu sítě používá syntetické transakce ke sledování výkonu sítě mezi zdrojovými a cílovými agenty. Můžete si vybrat mezi protokolem TCP a ICMP jako protokol pro monitorování funkcí sledování výkonu a sledování připojení služby. Jako monitorovací protokol pro monitor ExpressRoute je k dispozici pouze protokol TCP. Ujistěte se, že brána firewall umožňuje komunikaci mezi agenty Log Analytics používanými pro monitorování zvoleného protokolu. 

* **Protokol TCP**: Pokud zvolíte protokol TCP pro monitorování, otevřete port brány firewall u agentů používaných pro sledování výkonu sítě a monitor ExpressRoute, abyste se ujistili, že se agenti mohou vzájemně připojovat. Chcete-li otevřít port, spusťte skript Prostředí PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript) bez jakýchkoli parametrů v okně prostředí PowerShell s oprávněními správce.

    Skript vytvoří klíče registru vyžadované řešením. Vytváří také pravidla brány Windows Firewall, která agentům umožňují vytvářet vzájemně připojení TCP. Klíče registru vytvořené skriptem určují, zda mají protokolovat protokoly ladění a cestu k souboru protokolů. Skript také definuje agentta TCP port používaný pro komunikaci. Hodnoty těchto klíčů jsou automaticky nastaveny skriptem. Tyto klíče neměňte ručně. Port otevřený ve výchozím nastavení je 8084. Vlastní port můžete použít poskytnutím parametru portNumber skriptu. Stejný port použijte ve všech počítačích, ve kterých je skript spuštěn. 

    >[!NOTE]
    > Skript konfiguruje pouze bránu Windows Firewall místně. Pokud máte síťovou bránu firewall, ujistěte se, že umožňuje přenosy určené pro port TCP používaný programem Sledování výkonu sítě.

    >[!NOTE]
    > Není nutné spouštět skript Prostředí PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript ) pro sledování připojení služby.

    

* **Protokol ICMP**: Pokud zvolíte protokol ICMP pro monitorování, povolte spolehlivé využití protokolu ICMP následujícími pravidly brány firewall:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurace řešení 

1. Přidejte do svého pracovního prostoru řešení Sledování výkonu sítě z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Můžete také použít proces popsaný v [přidat řešení Azure Monitor z Galerie řešení](../../azure-monitor/insights/solutions.md). 
2. Otevřete pracovní prostor Log Analytics a vyberte dlaždici **Přehled.** 
3. Vyberte dlaždici **Sledování výkonu sítě** se zprávou Řešení vyžaduje další *konfiguraci*.

   ![Dlaždice Sledování výkonu sítě](media/network-performance-monitor/npm-config.png)

4. Na stránce **Instalace** se zobrazí možnost instalace agentů Analýzy protokolů a konfigurace agentů pro monitorování v zobrazení **Společné nastavení.** Jak již bylo vysvětleno, pokud jste nainstalovali a nakonfigurovali agenty Analýzy **protokolů,** vyberte zobrazení instalace a nakonfigurujte funkci, kterou chcete použít. 

   **Sledování výkonu**: Zvolte protokol, který chcete použít pro syntetické transakce v **pravidle Výchozí** monitor výkonu, a vyberte **uložit & pokračovat**. Tento výběr protokolu platí pouze pro výchozí pravidlo generované systémem. Je třeba zvolit protokol při každém explicitním vytvoření pravidla sledování výkonu. Vždy můžete přejít na **výchozí** nastavení pravidla na kartě **Sledování výkonu** (zobrazí se po dokončení konfigurace den-0) a později změnit protokol. Pokud nechcete funkci Sledování výkonu, můžete zakázat výchozí pravidlo z **výchozího** nastavení pravidla na kartě **Sledování výkonu.**

   ![Zobrazení sledování výkonu](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Sledování připojení služby**: Tato funkce poskytuje integrované předkonfigurované testy pro monitorování síťového připojení k Office 365 a Dynamics 365 od vašich agentů. Vyberte služby Office 365 a Dynamics 365, které chcete sledovat, zaškrtnutím políček vedle nich. Chcete-li vybrat agenty, od kterých chcete sledovat, vyberte **přidat agenty**. Pokud tuto funkci nechcete použít nebo ji chcete později nastavit, nevybírejte nic a vyberte **Uložit & Pokračovat**.

   ![Zobrazení Sledování připojení služby](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor ExpressRoute**: Vyberte **Zjistit nyní** a zjišťujte všechny soukromé partnerské partnerské servery ExpressRoute, které jsou připojené k virtuálním sítím v předplatném Azure propojeném s tímto pracovním prostorem Log Analytics. 

   ![Zobrazení expressroute monitoru](media/network-performance-monitor/npm-express-route.png)

   Po dokončení zjišťování jsou zjištěné obvody a partnerské partnerské společnosti uvedeny v tabulce. 

   ![Stránka Konfigurace monitoru výkonu sítě](media/network-performance-monitor/npm-private-peerings.png)
    
Monitorování těchto obvodů a partnerských vrstev je zpočátku v zakázaném stavu. Vyberte každý prostředek, který chcete sledovat, a nakonfigurujte pro ně monitorování ze zobrazení podrobností vpravo. Chcete-li uložit konfiguraci, vyberte **uložit.** Další informace naleznete v článku Konfigurace monitorování expressroute. 

Po dokončení instalace trvá 30 minut až hodinu, než se data naplní. Zatímco řešení agreguje data z vaší sítě, zobrazí se zpráva *Řešení vyžaduje další konfiguraci* na dlaždici **Přehled** sledování výkonu sítě. Po shromažďování a indexování dat se dlaždice **Přehled** změní a v souhrnu vás informuje o stavu sítě. Potom můžete upravit monitorování uzlů, na kterých jsou nainstalováni agenti Analýzy protokolů, stejně jako podsítě zjištěné z vašeho prostředí.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Úprava nastavení monitorování pro podsítě a uzly 

Všechny podsítě s nainstalovaným alespoň jedním agentem jsou uvedeny na kartě **Podsítě** na konfigurační stránce. 


Povolení nebo zakázání monitorování určitých podsítí:

1. Zaškrtněte nebo zrušte zaškrtnutí políčka vedle **ID podsítě**. Pak se ujistěte, že **použití pro monitorování** je vybrána nebo vymazána, podle potřeby. Můžete vybrat nebo vymazat více podsítí. Pokud je zakázáno, podsítě nejsou sledovány a agenti jsou aktualizovány zastavit ping ostatní agenty. 
2. Zvolte uzly, které chcete sledovat v určité podsíti. Vyberte podsíť ze seznamu a přesuňte požadované uzly mezi seznamy, které obsahují nemonitorované a monitorované uzly. Do podsítě můžete přidat vlastní popis.
3. Chcete-li uložit konfiguraci, vyberte **uložit.** 

#### <a name="choose-nodes-to-monitor"></a>Výběr uzlů, které chcete sledovat

Všechny uzly, které mají agenta nainstalované na nich jsou uvedeny na kartě **Uzly.** 

1. Vyberte nebo zrušte zaškrtnutí uzlů, které chcete sledovat nebo ukončit monitorování. 
2. Vyberte **použít pro monitorování**, nebo zrušte, podle potřeby. 
3. Vyberte **Uložit**. 


Nakonfigurujte požadované funkce:

- [Monitorování výkonu](network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování připojení služby](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Podrobnosti o shromažďování dat
Chcete-li shromažďovat informace o ztrátě a latenci, nástroj Sledování výkonu sítě používá pakety handshake TCP SYN-SYNACK-ACK, pokud jako protokol zvolíte protokol TCP. Monitor výkonu sítě používá ICMP ECHO ICMP ECHO REPLY, když jako protokol zvolíte ICMP. Trasa trasy se také používá k získání informací o topologii.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak jsou data shromažďována pro sledování výkonu sítě.

| Platforma | Přímý agent | Agent provozního manažera systémového centra | Azure Storage | Vyžaduje se provozní manažer? | Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP handshakes/ICMP ECHO zprávy každých 5 sekund, data odeslaná každé 3 minuty |
 

 
Řešení používá syntetické transakce k posouzení stavu sítě. Agenti Log Analytics nainstalované na různých místech v síťových paketech TCP nebo ICMP Echo mezi sebou. To, zda agenti používají pakety TCP nebo ICMP Echo, závisí na protokolu, který jste vybrali pro monitorování. V tomto procesu se agenti učí čas odezvy a ztrátu paketů, pokud existuje. Pravidelně každý agent také provádí trasovací trasu k jiným agentům najít všechny různé trasy v síti, které musí být testovány. Pomocí těchto dat mohou agenti odvodit údaje o latenci sítě a ztrátě paketů. Testy se opakují každých pět sekund. Data jsou agregována po dobu asi tří minut agenty před jejich odesláním do pracovního prostoru Log Analytics v Azure Monitoru.



>[!NOTE]
> Přestože agenti komunikují mezi sebou často, nevytvářejí významné síťové přenosy při provádění testů. Agenti spoléhají pouze na pakety tcp SYN-SYNACK-ACK handshake k určení ztráty a latence. Nejsou vyměňovány žádné datové pakety. Během tohoto procesu agenti komunikovat mezi sebou pouze v případě potřeby. Topologie komunikace agenta je optimalizována pro snížení síťového provozu.

## <a name="use-the-solution"></a>Použijte řešení 

### <a name="network-performance-monitor-overview-tile"></a>Dlaždice Sledování výkonu sítě 

Po povolení řešení Sledování výkonu sítě poskytuje dlaždice řešení na stránce **Přehled** rychlý přehled stavu sítě. 

 ![Dlaždice Sledování výkonu sítě](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Řídicí panel Sledování výkonu sítě 

* **Nejčastější události stavu sítě**: Tato stránka obsahuje seznam nejnovějších událostí stavu a výstrah v systému a doby od jejich aktivní hodu. Událost stavu nebo výstraha je generována vždy, když hodnota zvolené metriky (ztráta, latence, doba odezvy nebo využití šířky pásma) pro pravidlo monitorování překročí prahovou hodnotu. 

* **Monitor ExpressRoute**: Tato stránka poskytuje souhrny stavu pro různá připojení partnerského vztahu ExpressRoute, která řešení monitoruje. Na dlaždici **Topologie** je zobrazen počet síťových cest prostřednictvím okruhů ExpressRoute, které jsou monitorovány v síti. Tuto dlaždici vyberte, chcete-li přejít do zobrazení **Topologie.**

* **Sledování připojení služby**: Tato stránka obsahuje souhrny stavu pro různé testy, které jste vytvořili. Dlaždice **Topologie** zobrazuje počet sledovaných koncových bodů. Tuto dlaždici vyberte, chcete-li přejít do zobrazení **Topologie.**

* **Sledování výkonu**: Tato stránka obsahuje souhrny stavu pro **propojení sítě** a **podsítě,** které řešení monitoruje. Na dlaždici **Topologie** je zobrazen počet síťových cest, které jsou monitorovány v síti. Tuto dlaždici vyberte, chcete-li přejít do zobrazení **Topologie.** 

* **Běžné dotazy**: Tato stránka obsahuje sadu vyhledávacích dotazů, které přímo načítají nezpracovaná data monitorování sítě. Tyto dotazy můžete použít jako výchozí bod k vytvoření vlastních dotazů pro vlastní vytváření sestav. 

   ![Řídicí panel Sledování výkonu sítě](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Hloubka přechodu k podrobnostem 

Můžete vybrat různé odkazy na řídicím panelu řešení a přejít hlouběji do jakékoli oblasti zájmu. Pokud se například na řídicím panelu zobrazí výstraha nebo síťové propojení není v pořádku, vyberte ji, abyste ji dále prozkoumali. Na stránce jsou uvedeny všechny odkazy na podsíť pro konkrétní síťovou vazbu. Můžete zobrazit ztrátu, latenci a stav každého propojení podsítě. Můžete rychle zjistit, které propojení podsítě způsobuje problémy. Vyberte **Zobrazit odkazy uzlů,** chcete-li zobrazit všechny odkazy uzlů pro propojení podsítě Není v pořádku. Potom můžete zobrazit jednotlivé odkazy mezi uznami a najít odkazy uzlu není v pořádku. 

Výběrem **možnosti Zobrazit topologii** zobrazíte topologii směrování tras mezi zdrojovými a cílovými uzly. Cesty není v pořádku se zobrazí červeně. Můžete zobrazit latenci, kterou jednotlivé směrování přispívají, abyste mohli problém rychle identifikovat v určité části sítě.

 

### <a name="network-state-recorder-control"></a>Řízení síťového záznamníku stavu

Každé zobrazení zobrazuje snímek stavu sítě v určitém okamžiku. Ve výchozím nastavení je zobrazen nejnovější stav. Pruh v horní části stránky zobrazuje bod v čase, pro který je zobrazen stav. Chcete-li zobrazit snímek stavu sítě v předchozím čase, vyberte **možnost Akce**. Můžete také povolit nebo zakázat automatické aktualizace pro libovolnou stránku při zobrazení nejnovějšího stavu. 

 ![Zapisovač stavu sítě](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafy trendů 

Na každé úrovni, kterou přejdete k podrobnostem, můžete zobrazit trend příslušné metriky. Může to být ztráta, latence, doba odezvy nebo využití šířky pásma. Chcete-li změnit časový interval trendu, použijte ovládací prvek čas v horní části grafu. 

Grafy trendů zobrazují historickou perspektivu výkonu metriky výkonu. Některé problémy se sítí jsou přechodné povahy a je obtížné zachytit pouze na aktuální stav sítě. Problémy se mohou rychle objevit a zmizet dříve, než si toho někdo všimne, jen aby se později znovu objevil. Tyto přechodné problémy také může být obtížné pro správce aplikací. Problémy se často zobrazují jako nevysvětlitelné zvýšení doby odezvy aplikace, i když se zdá, že všechny součásti aplikace běží hladce. 

Tyto druhy problémů můžete snadno zjistit pohledem na graf trendů. Problém se zobrazí jako náhlý nárůst latence sítě nebo ztráty paketů. Chcete-li tento problém prozkoumat, použijte ovládací prvek Zapisovač stavu sítě k zobrazení síťového snímku a topologie pro tento bod v čase, kdy došlo k problému.

 
![Grafy trendů](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa topologie 

Sledování výkonu sítě zobrazuje topologii směrování tras mezi zdrojovým a cílovým koncovým bodem na interaktivní mapě topologie. Chcete-li zobrazit mapu topologie, vyberte dlaždici **Topologie** na řídicím panelu řešení. Můžete také vybrat odkaz **Zobrazit topologii** na stránkách přechodu k podrobnostem. 

Mapa topologie zobrazuje, kolik tras je mezi zdrojem a cílem a jaké cesty datové pakety vedou. Latence, kterou přispívá každý síťový směrování, je také viditelná. Všechny cesty, pro které je celková latence cesty nad prahovou hodnotou (nastavenou v odpovídajícím pravidle monitorování), jsou zobrazeny červeně. 

Když vyberete uzel nebo najeďte na něj na mapě topologie, zobrazí se vlastnosti uzlu, například FQDN a IP adresa. Výběrem směrování zobrazíte jeho IP adresu. Problematickou síťovou směrování můžete identifikovat tak, že si vyjádíte latenci, kterou přispívá. Chcete-li filtrovat určité trasy, použijte filtry v podokně sbalitelných akcí. Chcete-li zjednodušit topologii sítě, skryjte zprostředkující směrování pomocí posuvníku v podokně akcí. Mapu topologie můžete přiblížit nebo oddálit pomocí kolečka myši. 

Topologie zobrazená na mapě je topologie vrstvy 3 a neobsahuje zařízení a připojení vrstvy 2. 

 
![Mapa topologie](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Protokolovat dotazy ve službě Azure Monitor

Všechna data, která jsou graficky vystavena prostřednictvím řídicího panelu sledování výkonu sítě a stránek podrobností o podrobnostech, jsou také k dispozici nativně v [dotazech protokolu](../log-query/log-query-overview.md). Můžete provádět interaktivní analýzu dat v úložišti a korelovat data z různých zdrojů. Můžete taky vytvořit vlastní výstrahy a zobrazení a exportovat data do Excelu, Power BI nebo odkazu ke sdílení. Oblast **Běžné dotazy** v řídicím panelu má některé užitečné dotazy, které můžete použít jako výchozí bod k vytvoření vlastních dotazů a sestav. 

## <a name="alerts"></a>Výstrahy

Sledování výkonu sítě používá možnosti výstrah azure [monitoru](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

To znamená, že všechna oznámení jsou spravována pomocí [skupin akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Pokud jste uživatel NPM, který vytváří výstrahu prostřednictvím analýzy protokolů: 
1. Zobrazí se odkaz, který vás přesměruje na portál Azure. Klikněte na něj pro přístup k portálu.
2. Klikněte na dlaždici řešení Sledování výkonu sítě. 
3. Přejděte na Konfigurovat.  
4. Vyberte test, na který chcete vytvořit výstrahu, a postupujte podle níže uvedených kroků.

Pokud jste uživatel NPM, který vytváří výstrahu prostřednictvím portálu Azure:  
1. Můžete se rozhodnout zadat svůj e-mail přímo nebo můžete vytvářet upozornění prostřednictvím skupin akcí.
2. Pokud se rozhodnete zadat svůj e-mail přímo, vytvoří se skupina akcí s názvem **NPM Email ActionGroup** a id e-mailu se přidá do této skupiny akcí.
3. Pokud se rozhodnete použít skupiny akcí, budete muset vybrat dříve vytvořenou skupinu akcí. Zde se dozvíte, jak vytvořit skupinu [akcí.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Po úspěšném vytvoření výstrahy můžete ke správě výstrah použít odkaz Spravovat výstrahy. 

Pokaždé, když vytvoříte výstrahu, NPM vytvoří pravidlo výstrahy protokolu založené na dotazu v Azure Monitoru. Tento dotaz se ve výchozím nastavení aktivuje každých 5 minut. Azure Monitor se neúčtuje za prvních 250 pravidel upozornění protokolu vytvořené a všechna pravidla výstrah nad 250 protokolu omezení pravidel výstrahy se budou účtovat podle [upozornění ceny na stránce cen Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
Oznámení se účtují zvlášť podle [cen oznámení na stránce s cenami Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Ceny

Informace o cenách jsou k dispozici [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby 

* **UserVoice:** Můžete zveřejnit své nápady pro funkce sledování výkonu sítě, na kterých chcete, abychom pracovali. Navštivte [stránku UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Připojte se k naší kohortě:** Vždy máme zájem o to, aby se k naší kohortě připojili noví zákazníci. V rámci toho získáte včasný přístup k novým funkcím a příležitost, která nám pomůže zlepšit sledování výkonu sítě. Máte-li zájem o vstup, vyplňte tento [rychlý průzkum](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Další kroky 
Další informace o [sledování výkonu](network-performance-monitor-performance-monitor.md), sledování [připojení služby](network-performance-monitor-performance-monitor.md)a [monitoru ExpressRoute](network-performance-monitor-expressroute.md). 
