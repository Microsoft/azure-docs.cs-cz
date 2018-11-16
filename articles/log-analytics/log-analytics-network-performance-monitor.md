---
title: Network Performance Monitor řešení v Azure | Dokumentace Microsoftu
description: Sledování výkonu sítě v Azure umožňuje sledovat výkon vaší sítě, téměř v reálném čase, ke zjišťování a najít kritické body výkonu sítě.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: ''
ms.openlocfilehash: 78d081fe3ba2bfa4b598dfb0cc62c03d66d3d356
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712309"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor řešení v Azure

![Symbol sledování výkonu sítě](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor je hybridní cloudové řešení usnadňující sledování výkonu sítě mezi různými body v síťové infrastruktuře pro monitorování sítě. Umožňuje rovněž monitorovat síťové připojení ke službě a koncovým bodům aplikace a monitorovat výkon Azure ExpressRoute. 

Network Performance Monitor zjistí problémy se sítí jako provoz blackholing směrování chyby a problémy, které nejsou schopna zjistit monitorování metody konvenční sítě. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Network Performance Monitor nabízí tři různé možnosti: 

* [Sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md): můžete monitorovat připojení k síti napříč nasazeními v cloudu a místními umístěními, více datových center a firemní pobočky a nejdůležitější vícevrstvé aplikace nebo mikroslužeb. Pomocí sledování výkonu dokáže detekovat problémy se sítí, předtím, než si zákazníci začnou stěžovat.

* [Monitorování připojení služby](log-analytics-network-performance-monitor-service-endpoint.md): od uživatelů můžete monitorovat připojení ke službám, o kterého vám jde, zjistit, jakou infrastrukturu je na cestě a určit, kde dojde k problémových míst v síti. Můžete vědět o výpadcích dříve než vaši uživatelé a zobrazit je přesné umístění problémy síťové cestě. 

    Tato funkce umožňuje provádět testy založené na protokolu HTTP, HTTPS, TCP a ICMP monitorování v téměř reálném čase nebo v minulosti dostupnost a dobu odezvy služby. Také můžete monitorovat příspěvku v ztrátu paketů a latenci sítě. Pomocí mapy topologie sítě můžete izolovat zpomalení sítě. Můžete identifikovat problém míst, ke kterým dochází na síťové cestě z uzlu do služby s daty čekací doba na každého směrování. Pomocí integrovaných testů můžete monitorovat síťové připojení k Office 365 a Dynamics CRM, bez jakékoli přednastavení. Díky této funkci můžete monitorovat připojení k síti do koncového bodu TCP podporuje, například z webů, aplikací SaaS, PaaS aplikací nebo databází SQL.

* [Monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): monitorování připojení začátku do konce a výkon mezi vašimi pobočkami a Azure, přes Azure ExpressRoute.  

Další informace o různých možnostech nepodporuje [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) je k dispozici online.
 
## <a name="supported-regions"></a>Podporované oblasti
NPM můžete monitorovat připojení mezi sítěmi a aplikacemi v jakékoli části na světě, z pracovního prostoru, který je hostován v jednom z následujících oblastí:
* Západní Evropa
* Západní střed USA
* USA – východ
* Východní Japonsko
* Jihovýchodní Asie
* Jižní Austrálie – východ
* Velká Británie – jih
* Virginie státní správy USA

Seznam podporovaných oblastí pro monitorování ExpressRoute je k dispozici v [dokumentaci](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Nastavení a konfigurace

### <a name="install-and-configure-agents"></a>Instalace a konfigurace agentů 

Můžete nainstalovat agenty na základních procesů [počítače Windows se připojit ke službě Azure Log Analytics](log-analytics-windows-agents.md) a [připojení Operations Manageru k Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Kam chcete nainstalovat agenty 

* **Sledování výkonu**: Log Analytics nainstalovat agenty na alespoň jednom uzlu připojeném ke všem podsítím, ze kterého chcete monitorovat síťové připojení k jiné podsítě.

    Pokud chcete monitorovat síťové propojení, nainstalujte agenty na oba koncové body tento odkaz. Pokud si nejste jisti o topologii vaší sítě, nainstalujte agenty na servery s důležitými úlohami, mezi kterými chcete monitorovat výkon sítě. Například pokud chcete monitorovat síťové připojení mezi webový server a server se systémem SQL, nainstalujte agenta na obou serverech. Agenti monitorují připojení k síti (odkazy) mezi hostiteli, ne hostitele sami. 

* **Monitorování připojení služby**: nainstalujte agenta Log Analytics na každý uzel, ze kterého chcete monitorovat síťové připojení ke koncovému bodu služby. Příkladem je, pokud chcete monitorovat síťové připojení k Office 365 z webů office s popisky O1, O2 a O3. Nainstalujte agenta Log Analytics na alespoň jeden uzel v O1, O2 a O3. 

* **Monitorování ExpressRoute**: nainstalujte alespoň jednoho agenta Log Analytics ve službě Azure virtual network. Také nainstalujte alespoň jednoho agenta do místní podsítě, která je připojená přes privátní partnerský vztah ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurace agentů Log Analytics pro monitorování 

Network Performance Monitor používá k monitorování výkonu sítě mezi zdrojovým a cílovým agenty syntetické transakce. Můžete zvolit TCP a ICMP jako protokol pro monitorování v možnosti sledování výkonu a monitorování připojení služby. Jen protokol TCP je k dispozici jako protokol monitorování pro monitorování ExpressRoute. Ujistěte se, že brána firewall umožňuje komunikaci mezi agentů Log Analytics pro monitorování na protokol, který zvolíte. 

* **Protokol TCP**: Pokud se rozhodnete TCP jako protokol pro monitorování, otevřete port brány firewall na agenty použité pro sledování výkonu sítě a monitorování ExpressRoute, abyste měli jistotu, že agenti může připojit k sobě navzájem. Chcete-li otevřít port, spusťte [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershellový skript bez parametrů v okně Powershellu s oprávněními správce.

    Tento skript vytvoří klíčům registru požadovaným řešení. Vytvoří také pravidla brány Windows Firewall, která povolí agentů k vytvoření připojení TCP mezi sebou. Klíče registru, který je vytvořen skriptem určete, zda protokoly ladění a cestu k souboru protokolů protokolu. Skript také definuje port TCP agent používá pro komunikaci. Hodnoty pro tyto klíče se automaticky nastaví skript. Neměnit ručně tyto klíče. Otevřený ve výchozím nastavení port je 8084. Můžete použít vlastní port zadáním parametru číslo_portu skriptu. Použijte stejný port, na všech počítačích, ve kterém se skript spouští. 

    >[!NOTE]
    > Skript nakonfiguruje pouze Windows Firewall místně. Pokud máte síťovou bránu firewall, ujistěte se, že umožňuje provoz určený pro TCP port je používán nástroj Sledování výkonu sítě.

    >[!NOTE]
    > Není nutné ke spuštění [EnableRules.ps1](https://aka.ms/npmpowershellscript ) skript prostředí PowerShell pro monitorování koncového bodu služby.

    

* **Protokol ICMP**: Pokud se rozhodnete ICMP jako protokol pro monitorování, povolte následující pravidla brány firewall spolehlivě využívat protokol ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurovat řešení 

1. Přidat do pracovního prostoru z řešení Network Performance Monitor [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Také můžete použít postup v [přidání řešení Log Analytics z Galerie řešení](../azure-monitor/insights/solutions.md). 
2. Otevřete váš pracovní prostor Log Analytics a vyberte **přehled** dlaždici. 
3. Vyberte **Network Performance Monitor** dlaždici s touto zprávou *řešení vyžaduje další konfiguraci*.

   ![Dlaždice monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-config.png)

4. Na **nastavení** stránce zobrazí možnost instalace agentů Log Analytics a konfigurace agentů pro monitorování v **obecná nastavení** zobrazení. Jak bylo vysvětleno dříve, pokud jste nainstalovali a nakonfigurovali agentů Log Analytics, vyberte **nastavení** zobrazení ke konfiguraci možností, kterou chcete použít. 

   **Sledování výkonu**: Vyberte protokol pro syntetické transakce ve **výchozí** pravidlo monitorování výkonu a vyberte **uložit a pokračovat**. Tento výběr protokolu obsahuje pouze pro systémem generované výchozí pravidlo. Musíte zvolit protokol pokaždé, když explicitně vytvořit pravidlo monitorování výkonu. Můžete vždycky přesunout **výchozí** pravidla nastavení u **sledování výkonu** kartu (zobrazí se po dokončení konfigurace den-0) a později změnit protokol. Pokud nechcete, aby rPerfomance funkce monitorování, můžete zakázat výchozí pravidlo z **výchozí** pravidla nastavení u **sledování výkonu** kartu.

   ![Zobrazení monitorování výkonu](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitorování připojení služby**: možnost poskytuje integrované předkonfigurované testy k monitorování síťových připojení k Office 365 a Dynamics 365 z agentů. Zvolte, které chcete monitorovat tak, že vyberete zaškrtávací políčka vedle jejich služeb Office 365 a Dynamics 365. Vyberte agenty, ze kterých chcete monitorovat, vyberte **přidat agenty**. Pokud už nechcete používat tuto funkci nebo chcete později nastavit, nemusíte nic zvolte a vyberte **uložit a pokračovat**.

   ![Zobrazení monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitorování ExpressRoute**: vyberte **zjistit nyní** ke zjišťování všech ExpressRoute privátní partnerské vztahy, které jsou připojené k virtuálním sítím v Azure předplatné propojené s tímto pracovním prostorem Log Analytics. 

   >[!NOTE] 
   > Řešení aktuálně zjišťuje pouze privátní partnerské vztahy ExpressRoute. 

   >[!NOTE] 
   > Zjištění pouze privátní partnerské vztahy, které jsou připojené k virtuálním sítím přidružené předplatné propojené s tímto pracovním prostorem Log Analytics. Pokud ExpressRoute je připojená k virtuálním sítím mimo předplatné propojené s Tento pracovní prostor, vytvořte pracovní prostor Log Analytics v těchto předplatných. Pomocí Network Performance Monitor monitorovat tyto partnerské vztahy.

   ![Zobrazení monitorování ExpressRoute](media/log-analytics-network-performance-monitor/npm-express-route.png)

   Po dokončení zjišťování zjištěných privátní partnerské vztahy jsou uvedeny v tabulce. 

   ![Stránka konfigurace sledování výkonu sítě](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
Monitorování pro tyto partnerské vztahy zpočátku je v zakázaném stavu. Vyberte každý partnerský vztah, který chcete monitorovat a konfigurace monitorování pro ně ze zobrazení podrobností na pravé straně. Vyberte **Uložit** uložte konfiguraci. Další informace najdete v článku "Monitorování konfigurace ExpressRoute". 

Po dokončení instalace trvá 30 minut až jednu hodinu pro naplní agregovanými daty. Při řešení agreguje data z vaší sítě, zobrazí se zpráva *řešení vyžaduje další konfiguraci* na Network Performance Monitor **přehled** dlaždici. Jakmile se data shromažďují a indexovat, **přehled** dlaždice se změní a informuje o stavu vašeho sítě v souhrnu. Potom můžete upravit monitorování uzly, na které Log Analytics jsou nainstalovaní agenti, stejně jako podsítě prohledány z vašeho prostředí.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Úprava nastavení monitorování pro podsítě a uzlů 

Všechny podsítě s nainstalovaným agentem alespoň jeden jsou uvedeny na **podsítě** karty na stránce konfigurace. 


K povolení nebo zakázání monitorování konkrétní podsítě:

1. Zaškrtněte nebo zrušte zaškrtnutí políčka vedle položky **ID podsítě**. Ujistěte se, že **použijte pro monitorování** je vybrané nebo nezaškrtnuté, podle potřeby. Můžete vybrat nebo zrušte více podsítí. Zakázán, nejsou monitorovány podsítě a agenti se aktualizují se zastavit příkazy ping jiní agenti. 
2. Vyberte uzly, které chcete monitorovat v konkrétní podsíti. Vyberte ze seznamu podsítě a požadované uzly přesouvat mezi seznamy, které obsahují zůstávají nemonitorovaná a monitorované uzly. Přidáte vlastní popis do podsítě.
3. Vyberte **Uložit** uložte konfiguraci. 

#### <a name="choose-nodes-to-monitor"></a>Zvolte uzly k monitorování

Všechny uzly, kterých je agent nainstalovaný na nich jsou uvedeny na **uzly** kartu. 

1. Zaškrtněte nebo zrušte uzly, které chcete sledovat nebo zastavení sledování. 
2. Vyberte **použijte pro monitorování**, nebo zaškrtnutí políčka zrušte, podle potřeby. 
3. Vyberte **Uložit**. 


Konfigurace možností, které chcete, aby:

- [Sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování koncového bodu služby](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Podrobné informace o shromažďování dat
Ke shromažďování informací ztrát a latence, Network Performance Monitor používá TCP SYN SYNACK potvrzení handshake pakety, pokud zvolíte protokol TCP. Network Performance Monitor používá odpověď odezvy ICMP ODEZVU protokolu ICMP, při výběru jako protokol ICMP. Trasování tras slouží také k získání informací o topologii.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o jak shromažďuje data pro sledování výkonu sítě.

| Platforma | Přímý agent | Agenta System Center Operations Manageru | Azure Storage | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP metodou handshake/ODEZVU protokolu ICMP každých 5 sekund dat odeslaných každé 3 minuty |
 

 
Toto řešení využívá k vyhodnocení stavu sítě syntetické transakce. Log Analytics agenty nainstalované v různých fázích síťových paketů protokolu TCP serveru exchange nebo odezvu ICMP mezi sebou. Určuje, zda agenti použít pakety TCP nebo ICMP Echo závisí na protokol, který jste vybrali pro sledování. V procesu další agenty round-trip čas a paketů ztráty případné. Každý agent pravidelně provádí trasování cesty a jiní agenti pro vyhledání různých cest v síti, která musí být testována. Na základě těchto dat můžete odvodit agenty, latence sítě a ztrát paketů. Testy jsou opakovat každých pět sekund. Data je agregovat informace o tři minuty agenti před nahráním do služby Log Analytics.



>[!NOTE]
> I když se agenti komunikovat mezi sebou často, že při provádění testů negenerovat následek výrazné navýšení provozu sítě. Agenti pouze využívají pakety handshake TCP SYN SYNACK potvrzení k určení ztrát a latence. Žádné datové pakety se vyměňují. Během tohoto procesu agenti komunikovat mezi sebou jenom v případě potřeby. Komunikační topologie agenta je optimalizována pro snížení síťový provoz.

## <a name="use-the-solution"></a>Použití řešení 

### <a name="network-performance-monitor-overview-tile"></a>Dlaždice přehledu monitorování výkonu sítě 

Po povolení řešení Network Performance Monitor řešení na dlaždici **přehled** stránce najdete rychlý přehled o stavu sítě. 

 ![Dlaždice přehledu monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Řídicí panel monitorování výkonu sítě 

* **Nejčastější události stavu sítě**: Tato stránka obsahuje seznam nejaktuálnějších událostí stavu a výstrahy v systému a čas, protože byla aktivní události. Událost stavu nebo výstraha je vygenerována vždy, když hodnotu vybrané metriky (ztráty, latence, dobu odezvy nebo využití šířky pásma) pro pravidlo monitorování překročí prahovou hodnotu. 

* **Monitorování ExpressRoute**: Tato stránka obsahuje souhrny stavů pro různá připojení s partnerským vztahem ExpressRoute řešení monitorování. **Topologie** dlaždici se zobrazuje počet síťových cest prostřednictvím okruhů ExpressRoute, které jsou monitorovány ve vaší síti. Vyberte tuto dlaždici a přejděte tak **topologie** zobrazení.

* **Monitorování připojení služby**: Tato stránka obsahuje souhrny stavů pro různé testy, které jste vytvořili. **Topologie** dlaždice ukazuje počet koncových bodů, které jsou monitorovány. Vyberte tuto dlaždici a přejděte tak **topologie** zobrazení.

* **Sledování výkonu**: Tato stránka obsahuje souhrny stavů pro **sítě** odkazy a **podsítě** odkazy, které monitoruje řešení. **Topologie** dlaždici se zobrazuje počet síťových cest, které jsou monitorovány ve vaší síti. Vyberte tuto dlaždici a přejděte tak **topologie** zobrazení. 

* **Běžné dotazy**: Tato stránka obsahuje sadu vyhledávacích dotazů, které načítají nezpracovaná data monitorování přímo sítě. Tyto dotazy můžete použít jako výchozí bod k vytvoření své vlastní dotazy pro vytváření přizpůsobených sestav. 

   ![Řídicí panel monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Přejít na nižší úroveň hloubky 

Můžete vybrat různé odkazy na řídicím panelu řešení přejít dolů hlouběji do libovolné oblasti zájmu. Například když se zobrazí výstraha nebo na řídicím panelu se zobrazí odkaz na není v pořádku, sítě, vyberte ho dále prozkoumat. Stránce uvedeny všechna propojení podsítě pro konkrétní síťové propojení. Zobrazí se ztrátou, latenci a stav každého odkaz na podsíť. Můžete rychle zjistit způsobuje problémy, které odkaz na podsíť. Vyberte **zobrazit odkazy na uzly** zobrazíte všechna propojení uzlů pro odkaz na podsíť není v pořádku. Pak můžete zobrazit jednotlivá propojení mezi uzly a odkazy uzlů ve špatném stavu. 

Vyberte **zobrazení topologie** zobrazíte hop-by-hop topologii tras mezi zdrojový a cílový uzel. Není v pořádku tras se zobrazí červeně. Můžete zobrazit latence z každého směrování, aby mohli rychle identifikovat problém, který chcete konkrétní části sítě.

 

### <a name="network-state-recorder-control"></a>Ovládací prvek záznam stavu sítě

Každé zobrazení se zobrazí snímek stavu vašeho sítě v určitém místě v čase. Ve výchozím nastavení aktuální stav je zobrazen. Na panelu v horní části stránky zobrazí bod v čase, pro který se zobrazí stav. Chcete-li zobrazit snímek stavu vašeho sítě předchozí najednou, vyberte **akce**. Také můžete povolit nebo zakázat automatické aktualizace pro všechny stránky, zatímco zobrazit nejnovější stav. 

 ![Záznam stavu sítě](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafy trendů 

Na všech úrovních, která můžete procházet hierarchii zobrazí se trend použít metriky. Může být ztráta, latence, dobu odezvy nebo využití šířky pásma. Chcete-li změnit časový interval pro vývoj, pomocí ovládacího prvku čas v horní části grafu. 

Grafy trendů zobrazit Historický přehled o výkonu metriky výkonu. Některé problémy se sítí jsou přechodné ze své podstaty a je těžké catch zobrazením aktuálního stavu sítě. Problémy můžete rychle plochu a zmizí před kýmkoli oznámení pouze pro znovu později v čase. Tyto přechodné problémy také může být obtížné pro správce aplikace. Problémy se často zobrazují jako nevysvětlitelné zvýšení doby odezvy aplikací, i když všechny součásti aplikace zdánlivě běží plynule. 

Tyto druhy problémů můžete snadno zjistit zobrazením graf trendů. Problém se zobrazí jako náhlá Špička latence sítě nebo ztráta paketů. K prošetření problému, pomocí ovládacího prvku záznam stavu sítě chcete-li zobrazit snímek sítě a topologie pro tento bod v čase, kdy došlo k problému.

 
![Grafy trendů](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapu 

Network Performance Monitor ukazuje hop-by-hop topologii tras mezi zdrojovým a cílovým koncový bod na mapu interaktivní topologie. Chcete-li zobrazit mapu, vyberte **topologie** dlaždici na řídicím panelu řešení. Také můžete vybrat **zobrazení topologie** odkaz na stránkách procházení. 

Mapa topologie zobrazuje, kolik trasy jsou v rozmezí od zdrojové a cílové a co cesty trvat datových paketů. Latence z každého směrování sítě je také viditelné. Všechny cesty, pro které latence celkový cesta je nad prahovou hodnotou (nastavená v odpovídající pravidlo monitorování) se zobrazí červeně. 

Když vyberete uzel nebo při najetí myší nad ním na mapě topologie, se zobrazí vlastnosti uzlu, jako je například plně kvalifikovaný název domény a IP adresu. Vyberte směrování zobrazíte jeho IP adresu. Směrování problémových sítě může identifikovat podle vašeho povšimnutí latenci, která přispívá. Filtrovat konkrétní trasy, pomocí filtrů v podokně sbalitelné panely akcí. Pro zjednodušení síťové topologie, skryjte zprostředkující segmenty směrování pomocí jezdce v podokně Akce. Můžete přiblížit nebo oddálit mapy topologie pomocí kolečko myši. 

Topologie zobrazené na mapě se topologie vrstvy 3 a neobsahuje vrstvy 2 zařízení a připojení. 

 
![Mapu](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Hledání log Analytics 

Všechna data, která je graficky vystavené prostřednictvím řídicího panelu monitorování výkonu sítě a její procházení stránek je taky dostupný nativně v [hledání Log Analytics](log-analytics-queries.md). Můžete provádět interaktivní analýzu dat v úložišti a korelovat data z různých zdrojů. Můžete také vytvořit vlastní výstrahy a zobrazení a export dat do Excelu, Power BI nebo ke sdílení odkazu.  **Běžné dotazy** oblasti na řídicím panelu má některé užitečné dotazy, které můžete použít jako výchozí bod k vytvoření vlastních dotazů a sestav. 

## <a name="alerts"></a>Výstrahy

Network Performance Monitor využívá výstrah možnosti [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

To znamená, že všechna oznámení se spravují pomocí [skupiny akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview).  

Pokud jste uživatelem NPM vytvořením výstrahy prostřednictvím Log Analytics: 
1. Zobrazí se odkaz, který vás přesměrují na webu Azure Portal. Klikněte na něj pro přístup k portálu.
2. Kliknutím na dlaždici řešení Network Performance monitoru. 
3. Přejděte ke konfiguraci.  
4. Vyberte test, který chcete vytvořit upozornění na a postupujte níže uvedených kroků.

Pokud jste uživatelem NPM vytvořením výstrahy prostřednictvím webu Azure Portal:  
1. Můžete také přímo zadat e-mailu nebo můžete vytvářet upozornění přes skupiny akcí.
2. Pokud budete chtít přímo, zadejte svůj e-mail, akce skupiny s názvem **NPM e-mailu ActionGroup** se vytvoří a id e-mailu se přidá do této skupiny akcí.
3. Pokud se rozhodnete použít skupiny akcí, budete muset vybrat skupinu dříve vytvořená akce. Zjistěte, jak vytvořit skupinu akcí [tady.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Po úspěšném vytvoření výstrahy můžete spravovat výstrahy odkaz jak spravovat upozornění. 

Pokaždé, když vytvoříte upozornění, NPM vytvoří pravidlo upozornění na základě dotazu protokolu ve službě Azure Monitor. Tento dotaz je triggerred ve výchozím nastavení každých 5 minut. Azure monitor není účtovat prvních 250 pravidel upozornění, které jsou vytvořené protokolů a všechna pravidla upozornění výše uvedené omezení 250 pravidel upozornění protokolů se budou účtovat podle jako [výstrahy ceny ve službě Azure Monitor stránce s cenami](https://azure.microsoft.com/en-us/pricing/details/monitor/).
Oznámení se účtují samostatně podle [oznámení ceny na stránce Azure Monitor pricig](https://azure.microsoft.com/en-us/pricing/details/monitor/).


## <a name="pricing"></a>Ceny

Informace o cenách jsou k dispozici [online](log-analytics-network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby 

* **UserVoice:** publikujete Network Performance Monitor funkce, které chcete pracovat na vašich názorů. Přejděte [stránku UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Připojte se k naší kohorta:** nás zajímá vždy s novým zákazníkům, připojte se k naší kohorty. Jako část získejte dřívější přístup k novým funkcím a příležitosti a Pomozte nám vylepšit nástroj Sledování výkonu sítě. Pokud vás zajímá propojení, vyplňte tento [rychlého průzkumu](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Další postup 
Další informace o [sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md), [monitorování připojení služby](log-analytics-network-performance-monitor-performance-monitor.md), a [monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
