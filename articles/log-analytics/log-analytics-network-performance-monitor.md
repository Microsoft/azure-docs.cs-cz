---
title: Síťová řešení pro sledování výkonu v Azure | Microsoft Docs
description: Sledování výkonu sítě v Azure umožňuje sledovat výkon sítě, skoro v reálném čase, na zjištění a najít kritické body sítě.
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
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 1d12df7c37c4c96198865479326851040b46986a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Síťová řešení pro sledování výkonu v Azure

![Symbol sledování výkonu sítě](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Sledování výkonu sítě je síť hybridní cloudové řešení monitorování, které umožňuje sledovat výkon sítě mezi různými body v síťové infrastruktuře. To zároveň pomáhá monitorovat síťové připojení k služby a aplikace koncových bodů a monitorování výkonu Azure ExpressRoute. 

Sledování výkonu sítě zjistí problémy se síťovým jako blackholing provoz, směrování chyb a problémů, které metody monitorování konvenční sítě nejsou schopna zjistit. Řešení generuje výstrahy a upozorní vás, když prahové hodnoty je nedodržení pro síťové propojení. Také zajistí možno včas zjistit problémy s výkonem sítě a lokalizováno zdroj problému na určitém segmentu sítě nebo zařízení. 

Sledování výkonu sítě nabízí tři obecné možnosti: 

* [Sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md): připojení k síti můžete sledovat v rámci nasazení cloudu a místní umístění více datových centrech a pobočky a kritické vícevrstvé aplikace nebo mikroslužeb. Pomocí tohoto nástroje můžete zjistit problémy se síťovým před stížnost uživatele.

* [Monitorování koncového bodu služby](log-analytics-network-performance-monitor-service-endpoint.md): můžete sledovat připojení od uživatelů ke službám zajímají, určit, jakou infrastrukturu je v cestě a identifikovat, kdy dojde k problémových míst v síti. Můžete vědět o výpadcích před uživatelům a najdete přesné umístění problémy podél síťovou cestu. 

    Tato funkce umožňuje provádět testy na základě protokolu HTTP, HTTPS, TCP a ICMP sledování v téměř v reálném čase nebo v minulosti dostupnosti a doba odezvy služby. Také můžete monitorovat příspěvku sítě v ztráta paketů a latenci. S mapy topologie sítě můžete izolovat zpomalení sítě. Můžete identifikovat problém body, ke kterým došlo v síťové cestě z uzlu službu s data latence jednotlivých směrování. Pomocí předdefinovaných testů můžete sledovat síťové připojení k Office 365 a Dynamics CRM bez jakékoli přednastavení. Díky této funkci můžete sledovat síťové připojení ke koncovému bodu žádné podporující TCP/IP, například weby, aplikace SaaS, PaaS aplikací a databází SQL.

* [Monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): sledování začátku do konce připojení a výkonu mezi pobočkami a Azure, přes Azure ExpressRoute.  

Další informace o různých možnostech nepodporuje [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) je k dispozici online.
 
## <a name="supported-regions"></a>Podporované oblasti
NPM můžete monitorovat připojení mezi sítěmi a aplikací na celém světě, z pracovního prostoru, který je hostován v jednom z následujících oblastí:
* Západní Evropa
* Západní střed USA
* Východ USA
* Jihovýchodní Asie
* Východ Jižní Austrálie
* Spojené království – jih
* Virginia vláda USA

Seznam podporované oblasti pro monitorování ExpressRoute je k dispozici v [dokumentaci](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Nastavení a konfigurace

### <a name="install-and-configure-agents"></a>Instalace a konfigurace agentů 

Použijte k instalaci agentů na základní procesy [počítače se systémem Windows se připojit k Azure Log Analytics](log-analytics-windows-agents.md) a [připojení nástroje Operations Manager k analýze protokolů](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Umístění instalace agentů 

* **Sledování výkonu**: instalace služby Operations Management Suite agenty na nejméně jeden uzel připojení pro každou podsíť, ze kterého chcete monitorovat síťové připojení k jiné podsítě.

    Pokud chcete monitorovat síťové propojení, nainstalujte agenty na oba koncové body tento odkaz. Pokud si nejste jisti o topologii sítě, nainstalujte agenty na serverech s kritickým úlohám, mezi kterými chcete monitorovat výkon sítě. Například pokud chcete monitorovat síťové připojení mezi webový server a server se systémem SQL, nainstalujte agenta na obou serverech. Agenti monitorují připojení k síti (odkazy) mezi hostiteli, není hostitelů sami. 

* **Monitorování koncového bodu služby**: nainstalujte agenta Operations Management Suite na každý uzel, ze kterého chcete monitorovat síťové připojení ke koncovému bodu služby. Příkladem je, pokud chcete monitorovat síťové připojení k Office 365 z office lokalit s O1, O2 a O3 názvem bez přípony. Nainstalujte agenta Operations Management Suite na nejméně jeden uzel každý O1, O2 a O3. 

* **Monitorování ExpressRoute**: nainstalujte alespoň jeden agenta Operations Management Suite ve vaší virtuální síti Azure. Alespoň jeden agenta nainstalujte také v místní podsíť, která je připojena prostřednictvím soukromého partnerského vztahu ExpressRoute.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurace služby Operations Management Suite agentů pro monitorování 

Sledování výkonu sítě používá k monitorování výkonu sítě mezi zdrojovým a cílovým agenty syntetické transakce. Můžete zvolit TCP a ICMP jako protokol pro monitorování v nástroji Sledování výkonu a monitorování koncového bodu služby. TCP se používá pro monitorování ExpressRoute. Ujistěte se, že brána firewall umožňuje komunikaci mezi agenty Operations Management Suite použít pro monitorování na protokol, který zvolíte. 

* **Protokolu TCP**: Pokud si zvolíte TCP jako protokol pro monitorování, otevřete port brány firewall týkající se agentů, použít pro sledování výkonu sítě a monitorování ExpressRoute a ujistěte se, že agentů můžete připojit k sobě navzájem. Otevřete port, spusťte skript prostředí PowerShell EnableRules.ps1 bez parametrů v okně prostředí PowerShell s oprávněními správce.

    Tento skript vytvoří klíčům registru požadovaným řešení. Vytvoří také pravidla brány Windows Firewall povolit agentů k vytvoření připojení TCP mezi sebou. Klíče registru vytvořený skript určete, zda chcete protokoly pro ladění a cesta k souboru protokoly protokolu. Skript také definuje agenta TCP port používaný pro komunikaci. Hodnoty pro tyto klíče se nastaví automaticky skriptem. Tyto klíče nemusíte ručně změnit. Port otevřít ve výchozím nastavení je 8084. Můžete vytvořit vlastní port tím, že poskytuje číslo_portu parametr skriptu. Použijte stejný port na všech počítačích, kde je skript spuštěn. 

    >[!NOTE]
    > Skript nakonfiguruje jenom brány Windows Firewall místně. Pokud máte síťovou bránu firewall, ujistěte se, že umožňuje, aby provoz určený pro TCP port je používán nástroj Sledování výkonu sítě.

    >[!NOTE]
    > Nemusíte spouštění skriptu EnableRules.ps1 PowerShell pro monitorování koncového bodu služby.

    

* **Protokol ICMP**: Pokud si zvolíte ICMP jako protokol pro monitorování, povolte následující pravidla brány firewall ICMP spolehlivě využívat:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurace řešení 

1. Přidat do pracovního prostoru z řešení monitorování výkonu sítě [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Můžete taky použít proces popsaný v [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md). 
2. Otevřete pracovní prostor analýzy protokolů a vyberte **přehled** dlaždici. 
3. Vyberte **sledování výkonu sítě** dlaždici se zprávou *řešení vyžaduje další konfiguraci*.

   ![Dlaždice monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-config.png)

4. Na **instalace** stránce se zobrazí možnost instalace agentů Operations Management Suite a konfigurace agentů pro monitorování v **společná nastavení** zobrazení. Jak už bylo vysvětleno, pokud jste nainstalovali a nakonfigurovali agenty Operations Management Suite, vyberte **instalace** zobrazení nakonfigurovat možnosti, kterou chcete použít. 

   **Sledování výkonu**: Zvolte protokol bude použit pro syntetické transakce v **výchozí** pravidlo pro sledování výkonu a vyberte **Uložit & Pokračovat**. Tento protokol výběr obsahuje pouze pro generována výchozí pravidlo. Je třeba vybrat protokol pokaždé, když chcete vytvořit pravidlo, sledování výkonu explicitně. Můžete vždy přesunout **výchozí** pravidla nastavení u **sledování výkonu** karta (zobrazí se po dokončení konfiguraci den-0) a změňte protokol později. Pokud nechcete, aby rPerfomance funkce monitorování, můžete zakázat výchozí pravidlo z **výchozí** pravidla nastavení u **sledování výkonu** kartě.

   ![Zobrazení nástroje Sledování výkonu](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitorování koncového bodu služby**: možnosti poskytuje integrované předkonfigurované testy monitorování připojení k síti a Office 365, Dynamics 365 z agenty. Vyberte služby Office 365 a Dynamics 365, které chcete monitorovat zaškrtnutím políčka vedle je. Chcete-li zvolit agentů, z nichž chcete sledovat, vyberte **přidat agenty**. Pokud nechcete, aby pomocí této funkce nebo chcete je nastavit později, nemusíte nic zvolte a vyberte **Uložit & Pokračovat**.

   ![Zobrazení monitorování koncového bodu služby](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitorování ExpressRoute**: vyberte **zjistit nyní** ke zjišťování všech ExpressRoute soukromé partnerské vztahy, které jsou připojené k virtuálním sítím v rámci předplatného Azure spojena se tento pracovní prostor analýzy protokolů. 

   >[!NOTE] 
   > Řešení aktuálně zjišťuje pouze soukromé partnerské vztahy ExpressRoute. 

   >[!NOTE] 
   > Pouze zjištění soukromé partnerské vztahy, které jsou připojené k virtuálním sítím, které jsou přidružené k předplatnému spojena se tento pracovní prostor analýzy protokolů. Pokud ExpressRoute je připojen k virtuálním sítím mimo předplatné propojené do tohoto pracovního prostoru, vytvořte pracovní prostor analýzy protokolů v těchto předplatných. Ke sledování těchto partnerských vztahů použijte nástroj Sledování výkonu sítě.

   ![Zobrazení monitorování ExpressRoute](media/log-analytics-network-performance-monitor/npm-express-route.png)

   Po dokončení zjišťování zjištěných soukromé partnerské vztahy jsou uvedeny v tabulce. 

   ![Stránka Konfigurace monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
Monitorování pro tyto partnerské vztahy zpočátku je v zakázaném stavu. Vyberte každý partnerský vztah, kterou chcete monitorovat a nakonfigurovat monitorování pro ně ze zobrazení podrobností na pravé straně. Vyberte **Uložit** konfiguraci uložíte. Další informace najdete v článku "Konfigurace ExpressRoute monitorování". 

Po dokončení instalace trvá hodinu k naplnění dat 30 minut. Při řešení agreguje data z vaší sítě, zobrazí se zpráva *řešení vyžaduje další konfiguraci* na sledování výkonu sítě **přehled** dlaždici. Po data se shromažďují a indexované, **přehled** dlaždice se změní a informace o stavu vaší sítě v souhrnu. Potom můžete upravit monitorování uzlů, ve které Operations Management Suite jsou nainstalováni agenti, stejně jako podsítě zjištěných z prostředí.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Upravte nastavení monitorování pro podsítě a uzly 

Všechny podsítě s nainstalovaným agentem alespoň jeden jsou uvedeny na **podsítě** kartě na stránce konfigurace. 


Chcete povolit nebo zakázat monitorování konkrétní podsítě:

1. Vyberte nebo zrušte zaškrtnutí políčka vedle **podsítí ID**. Potom zkontrolujte, zda **použijte pro monitorování** je vybrané nebo nezaškrtnuté, podle potřeby. Můžete zaškrtněte nebo zrušte více podsítí. Pokud je zakázáno, nejsou monitorovány podsítě a agenti se aktualizují na zastavení otestováním jiné agenty. 
2. Vyberte uzly, které chcete monitorovat v konkrétní podsítí. Vyberte ze seznamu podsítí a přesunout požadované uzly mezi seznamy, které obsahují sledována a sledované uzly. Přidáte vlastní popis do podsítě.
3. Vyberte **Uložit** konfiguraci uložíte. 

#### <a name="choose-nodes-to-monitor"></a>Zvolte uzlů k monitorování

Všechny uzly, které nemá agenta nainstalovaného na jejich jsou uvedeny na **uzly** kartě. 

1. Vyberte nebo zrušte uzly, které chcete sledovat nebo zastavení monitorování. 
2. Vyberte **použijte pro monitorování**, nebo vymazat, podle potřeby. 
3. Vyberte **Uložit**. 


Nakonfigurujte možnosti, které chcete:

- [Sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování koncového bodu služby](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Podrobnosti kolekce dat
Ke shromažďování informací ztrátě a latence, sledování výkonu sítě používá TCP SYN. SYNACK ACK handshake pakety když zvolíte jako protokol TCP. Sledování výkonu sítě používá odpověď odezvy ICMP ODEZVU protokolu ICMP, když zvolíte jako protokol ICMP. Trasování cesty slouží také k získání informací o topologii.

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se údaje pro sledování výkonu sítě.

| Platforma | Přímé agenta | Agenta System Center Operations Manager | Azure Storage | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP metodou handshake/ODEZVU protokolu ICMP každých 5 sekund, data odeslána každé 3 minuty |
 

 
Toto řešení využívá k posouzení stavu sítě syntetické transakce. Agenti nástroje Operations Management Suite nainstalovaný na různých místech v síťových paketů TCP exchange nebo odezvu protokolu ICMP sebou. Jestli agenti použít pakety TCP nebo odezvu protokolu ICMP, závisí na protokol, na který jste vybrali pro monitorování. V procesu agenty další operace round-trip čas a paket ztrátě případných. Každý agent pravidelně provádí taky trasování cesty a jiné agenty pro najít všechny různé trasy v síti, která musí být testována. Na základě těchto dat můžete agenty odvodit latence sítě a obrázků ke ztrátě paketů. Testy jsou opakovat každých pět sekund. Data se shromažďují pro přibližně tři minuty pomocí agentů před odesláním do služby analýzy protokolů.



>[!NOTE]
> I když agenti komunikaci mezi sebou často, že při provádění testů negenerovat výrazné navýšení provozu sítě. Agenti spoléhat jenom na pakety handshake TCP SYN. SYNACK ACK k určení ztrátě a latence. Výměny žádné datových paketů. Během tohoto procesu agenty vzájemně komunikovat pouze v případě potřeby. Topologie komunikace agenta je optimalizovaná pro omezení provozu v síti.

## <a name="use-the-solution"></a>Použít řešení 

### <a name="network-performance-monitor-overview-tile"></a>Dlaždice přehledu monitorování výkonu sítě 

Po povolení řešení monitorování výkonu sítě na dlaždici řešení **přehled** stránka poskytuje rychlý přehled o stavu sítě. 

 ![Dlaždice přehledu monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Řídicí panel monitorování výkonu sítě 

* **TOP události stavu sítě**: Tato stránka obsahuje seznam nejaktuálnějších událostí, stavu a výstrahy v systému a čas, vzhledem k tomu, že události byla aktivní. Událost stavu nebo výstraha je vygenerována vždy, když hodnota vybrané metriky (ztrátě, latenci, doby odezvy nebo využití šířky pásma) pro monitorování pravidlo překročí prahovou hodnotu. 

* **Monitorování ExpressRoute**: Tato stránka obsahuje souhrny stavu pro různé připojení partnerského vztahu ExpressRoute řešení monitorování. **Topologie** dlaždice zobrazuje počet síťových cest prostřednictvím okruhy ExpressRoute, které jsou monitorovány ve vaší síti. Vyberte tuto dlaždici přejít na **topologie** zobrazení.

* **Monitorování koncového bodu služby**: Tato stránka obsahuje souhrny stavu pro různé testy, které jste vytvořili. **Topologie** dlaždice ukazuje počet koncových bodů, které jsou monitorovány. Vyberte tuto dlaždici přejít na **topologie** zobrazení.

* **Sledování výkonu**: Tato stránka obsahuje souhrny stavu pro **sítě** odkazy a **podsítí** odkazy, které monitoruje řešení. **Topologie** dlaždice zobrazuje počet síťových cest, které jsou monitorovány ve vaší síti. Vyberte tuto dlaždici přejít na **topologie** zobrazení. 

* **Běžné dotazy**: Tato stránka obsahuje sadu vyhledávací dotazy, které načíst monitorování dat přímo nezpracovaná sítě. Tyto dotazy jako výchozí bod vám pomůže vytvořit vlastní dotazy pro vytváření přizpůsobených sestav. 

   ![Řídicí panel monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>procházení hloubka 

Můžete vybrat různé odkazy na řídicí panel řešení přejdete dolů hlubší do všech oblastí zájmu. Například pokud se zobrazí výstraha nebo není v pořádku síťové propojení se zobrazují na řídicím panelu, vyberte ho k prozkoumání Další. Na stránce uvádí všechna propojení podsítí pro konkrétní síťové propojení. Uvidíte ztrátě, latence a stav každého odkazu podsítí. Můžete rychle zjistit způsobuje problémy, které propojení podsítí. Vyberte **zobrazení uzlu odkazy** zobrazíte všechny odkazy na uzlu odkazu podsíť není v pořádku. Potom můžete zobrazit jednotlivé uzly – odkazy a odkazy uzlů ve špatném stavu. 

Vyberte **zobrazení topologie** zobrazíte topologii směrování směrování trasy mezi zdrojovým a cílovým uzly. Není v pořádku trasy se zobrazí červeně. Můžete zobrazit latence přispěli každého směrování, aby mohli rychle identifikovat problém pro konkrétní části sítě.

 

### <a name="network-state-recorder-control"></a>Zapisovač řízení stavu sítě

Každé zobrazení zobrazí snímek stavu vaší sítě na určitém místě v čase. Ve výchozím nastavení se zobrazí poslední stav. Na panelu v horní části stránky zobrazí bod v čase, pro který se zobrazí stav. Pokud chcete zobrazit snímek stavu vaší sítě předchozí najednou, vyberte **akce**. Také můžete povolit nebo zakázat automatické aktualizace pro všechny stránky, zatímco zobrazit nejnovější stav. 

 ![Zapisovač stavu sítě](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafy trendů 

Na každé úrovni, který můžete procházet uvidíte trend použít metriku. Může být ztrátě, latenci, doby odezvy nebo využití šířky pásma. Časový interval trendu, můžete změnit čas ovládací prvek v horní části grafu. 

Grafy trendů zobrazit Historický přehled výkonu metriky výkonu. Některé problémy se síťovým se přechodný ve své podstatě a jsou těžko catch pohledem na aktuálním stavu sítě. Problémy můžete rychle surface a zmizí před nikým oznámení, jenom na objevit znova později v čase. Tyto přechodné problémy také může být složité pro správce aplikace. Problémy často zobrazují jako nevysvětlitelné zvyšování doba odezvy aplikace, i když všechny součásti aplikace zobrazí plynulejší. 

Podobné typy potíží můžete snadno zjistit prohlížením graf trendů. Problém se zobrazí jako nečekané Špička v latence sítě nebo ke ztrátě paketů. Chcete-li prozkoumat problém, použijte ovládací prvek zapisovač stavu sítě k zobrazení sítě snímku a topologie pro tento bod v čase, kdy došlo k problému.

 
![Grafy trendů](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapy topologie 

Sledování výkonu sítě zobrazuje topologii směrování směrování tras mezi zdrojovým a cílovým koncový bod na mapě interaktivní topologie. Chcete-li zobrazit topologickou mapu, vyberte **topologie** dlaždici na řídicím panelu řešení. Také můžete vybrat **zobrazení topologie** odkaz na stránkách procházení. 

Topologie mapa zobrazuje, kolik trasy jsou mezi zdrojové a cílové a co cesty trvat datových paketů. Latence přispěli každého směrování sítě je také viditelné. Všechny cesty, pro které latence celkový cesta je nad prahovou hodnotou (v odpovídajících monitorování pravidle nastavit) se zobrazí červeně. 

Když vyberete uzel nebo hover se na mapě topologie, zobrazí vlastnosti uzlu, jako je plně kvalifikovaný název domény a IP adresu. Vyberte směrování zobrazíte jeho IP adresu. Směrování problémových sítě lze určit podle vašeho povšimnutí latenci, které přispívá. Chcete-li filtrovat konkrétní trasy, pomocí filtrů v podokně sbalitelné akce. Pro zjednodušení síťové topologie, skryjte střední směrování pomocí posuvníku v podokně Akce. Můžete zvětšit nebo zmenšit mapy topologie pomocí kolečko myši. 

Topologie, zobrazí se v mapě je topologie vrstvy 3 a neobsahuje vrstvy 2 zařízení a připojení. 

 
![Mapy topologie](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Hledání analýzy protokolů 

Všechna data, která je graficky zveřejněné prostřednictvím řídicího panelu monitorování výkonu sítě a procházení stránek je také k dispozici nativně [analýzy protokolů hledání](log-analytics-log-search-new.md). Můžete provádět interaktivní analýzu dat v úložišti a korelovat data z různých zdrojů. Také můžete vytvořit vlastní výstrahy a zobrazení a export dat do aplikace Excel, Power BI nebo ke sdílení odkaz.  **Běžné dotazy** oblasti v řídicím panelu má některá užitečná dotazy, které můžete použít jako výchozí bod k vytvoření vlastních dotazů a sestav. 

## <a name="alerts"></a>Výstrahy

Sledování výkonu sítě používá výstrahy možnosti [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

To znamená, že všechny výstrahy je spravovat pomocí [skupiny akcí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview).  

Pokud jste uživatelem NPM vytváření výstrahu prostřednictvím OMS: 
1. Zobrazí se odkaz, který vás přesměruje na portál Azure. Klikněte na něj získat přístup k portálu.
2. Klikněte na dlaždici řešení pro sledování výkonu sítě. 
3. Přejděte ke konfiguraci.  
4. Vyberte testovací, kterou chcete vytvořit upozornění na a postupujte podle níže uvedené kroky.

Pokud jste uživatelem NPM vytváření výstrahu prostřednictvím portálu Azure:  
1. Můžete přímo zadat e-mailu nebo můžete vytvořit výstrah pomocí akce skupiny.
2. Pokud zvolíte možnost přímo zadat e-mailu, akce skupiny s názvem **NPM e-mailu ActionGroup** je vytvořena a id e-mailu se přidá do této skupiny akce.
3. Pokud se rozhodnete použít skupiny akce, je nutné vybrat skupinu dříve vytvořená akce. Zjistěte, jak vytvořit skupinu akcí [sem.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Po úspěšném vytvoření výstrahy můžete spravovat výstrahy odkaz ke správě upozornění. 

##<a name="pricing"></a>Ceny

Informace o cenách jsou k dispozici [online](log-analytics-network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby 

* **UserVoice:** můžete účtovat pro sledování výkonu sítě funkce, které chcete, abychom mohli pracovat na vašich nápadů. Přejděte [stránku UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Připojení k naší kohorty:** zajímají nás vždy nutnosti připojení k naší kohorty nové zákazníky. V rámci ji můžete využívat nové funkce a možnost Pomozte nám vylepšit sledování výkonu sítě. Pokud vás zajímá připojení, vyplňte to [rychlé průzkum](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Další postup 
Další informace o [sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md), [monitorování koncového bodu služby](log-analytics-network-performance-monitor-performance-monitor.md), a [ExpressRoute monitorování](log-analytics-network-performance-monitor-expressroute.md). 
