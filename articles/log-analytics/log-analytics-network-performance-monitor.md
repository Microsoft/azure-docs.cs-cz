---
title: Síťová řešení pro sledování výkonu v Azure | Microsoft Docs
description: Sledování výkonu v Azure sítě vám pomůže s monitorováním výkonu vaší sítě-in téměř skutečné čas – chcete zjišťovat a vyhledejte kritická místa výkonu sítě.
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
ms.openlocfilehash: 90fd9b09f90d5b209e0eb8cc6996ef7fb1e7e0b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Síťová řešení pro sledování výkonu v Azure

![Symbol sledování výkonu sítě](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Monitorování výkonu v síti (NPM) je síť hybridní cloudové řešení monitorování, které umožňuje sledovat výkon sítě mezi různými body v síťové infrastruktuře, monitorování síťové připojení ke koncovým bodům služby nebo aplikace a monitorování výkonu Azure ExpressRoute.  

NPM zjistí problémy se síťovým jako blackholing provoz, směrování chyb a problémů, které metody monitorování konvenční sítě nejsou schopna zjistit. Řešení generuje výstrahy, upozorní, když prahové hodnoty porušení zabezpečení nebyla pro síťové propojení, zajišťuje možno včas zjistit problémy s výkonem sítě a lokalizováno zdroj problému na určitém segmentu sítě nebo zařízení. 

NPM nabízí tři obecné možnosti: 

[Sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md): sledovat připojení k síti v rámci cloudu nasazení a místní umístění, několika datových centrech a firemní pobočky, služba mission kritické vícevrstvé aplikace a micro služby. Pomocí tohoto nástroje můžete zjistit problémy se síťovým před stížnost vaši uživatelé.  

[Monitorování koncového bodu služby](log-analytics-network-performance-monitor-service-endpoint.md): můžete sledovat připojení od uživatelů ke službám zajímají, určit, jakou infrastrukturu je v cestě a kde sítě dochází k kritická místa. Vědět o výpadků před uživatelům a zobrazit je přesné umístění problémy podél síťovou cestu. 

Tato funkce slouží k provádění http, HTTPS, TCP a ICMP na základě testy ke sledování v téměř v reálném čase nebo v minulosti dostupnosti a doba odezvy služby a příspěvku sítě v ztráta paketů a latence. S mapy topologie sítě můžete izolovat zpomalení sítě tím, že určíte body problém, ke kterým došlo v síťové cestě z uzlu službu s data latence jednotlivých směrování. Pomocí předdefinovaných testů monitorování síťové připojení k Office 365 a Dynamics CRM bez jakékoli předběžné konfigurace. Díky této funkci můžete sledovat síťové připojení k žádný TCP podporující koncový bod, jako jsou například weby, SaaS, PaaS aplikace, databáze SQL, atd.  

[Monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): sledování začátku do konce připojení a výkonu mezi pobočkami a Azure, přes Azure ExpressRoute.  

Další informace o různých možnostech nepodporuje [sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) je k dispozici online.
 

## <a name="set-up-and-configure"></a>Nastavení a konfigurace

### <a name="install-and-configure-agents"></a>Instalace a konfigurace agentů 

Použijte k instalaci agentů na základní procesy [počítače se systémem Windows se připojit k analýze protokolů](log-analytics-windows-agents.md) a [připojení nástroje Operations Manager k analýze protokolů](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Umístění instalace agentů 

**Sledování výkonu:** nainstalovat agenty OMS na nejméně jeden uzel připojení pro každou podsíť, ze kterého chcete monitorovat síťové připojení k jiné podsítě.  

Ke sledování síťového propojení, je nutné nainstalovat agenty na oba koncové body tento odkaz.  Pokud si nejste jistí o topologii sítě, nainstalujte agenty na serverech s kritickým úlohám, mezi kterými chcete monitorovat výkon sítě. Například pokud chcete monitorovat síťové připojení mezi webový server a server se systémem SQL, nainstalujte agenta na obou serverech. Agenti monitorují připojení k síti (odkazy) mezi hostiteli není hostitelů sami. 

**Monitorování koncového bodu služby:** nainstalovat agenta OMS na každém uzlu, ze kterého chcete monitorovat síťové připojení ke koncovému bodu služby. Například pokud máte v úmyslu monitorování připojení k síti pro Office 365 z webu office O1, O2 a O3, pak nainstalujte agenta OMS na nejméně jeden uzel každý O1, O2 a O3. 

**Monitorování ExpressRoute:** nainstalovat alespoň jeden agent OMS ve vaší virtuální síti Azure a alespoň jeden agent v místní podsítí, které je připojené prostřednictvím ExpressRoute privátní partnerský vztah.  

### <a name="configure-oms-agents-for-monitoring"></a>Konfigurace OMS agentů pro monitorování  

NPM používá k monitorování výkonu sítě mezi zdrojovým a cílovým agenty syntetické transakce. Toto řešení umožňuje výběr ze TCP a ICMP jako protokol pro monitorování v nástroji Sledování výkonu a monitorování koncového bodu služby funkce, zatímco TCP se používá pro monitorování ExpressRoute. Ujistěte se, že brána firewall umožňuje komunikaci mezi agenty OMS používá pro monitorování na protokol, na který jste zvolili pro monitorování.  

**Protokolu TCP:** Pokud TCP jste vybrali jako protokol pro monitorování, otevřete port brány Firewall na agentech používá pro sledování výkonu a ExpressRoute monitorování možnosti zajistit, že agentů můžete připojit k sobě navzájem. K tomu, spusťte skript prostředí PowerShell EnableRules.ps1 bez parametrů v okně power shell s oprávněními správce.  

Tento skript vytvoří klíčům registru požadovaným řešení a vytvoří pravidla brány firewall systému Windows povolit agentů k vytvoření připojení TCP mezi sebou. Klíče registru vytvořený skript také určete, zda protokoly pro ladění a cesta k souboru protokoly protokolu. Definuje také agent TCP port používaný pro komunikaci. Hodnoty pro tyto klíče se nastaví automaticky ve skriptu, takže byste neměli měnit ručně tyto klíče. Port otevřít ve výchozím nastavení je 8084. Můžete vytvořit vlastní port tím, že poskytuje číslo_portu parametr skriptu. Však stejný port by měl použít na všech počítačích, kde je skript spuštěn. 

>[!NOTE]
> Skript nakonfiguruje jenom brány windows firewall místně. Pokud máte síťovou bránu firewall, měli byste si ověřit, že ho umožňuje provoz určený pro port TCP používán NPM 

>[!NOTE]
> Není potřeba spustit skript prostředí PowerShell EnableRules.ps1 pro monitorování koncového bodu služby 

 

**Protokol ICMP** – Pokud jste zvolili ICMP jako protokol pro monitorování, povolte následující pravidla brány firewall pro spolehlivě využitím ICMP: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Konfigurace řešení 

1. Přidat řešení monitorování výkonu sítě do pracovního prostoru z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md). 
2. Otevřete pracovní prostor analýzy protokolů a klikněte na **přehled** dlaždici.  
3. Klikněte na dlaždici s názvem **sledování výkonu sítě** se zprávou *řešení vyžaduje další konfiguraci*.

    ![Dlaždice NPM](media/log-analytics-network-performance-monitor/npm-config.png)

3. Na **instalace** stránce se zobrazí možnost instalace agentů OMS a konfigurace agentů pro monitorování v **společná nastavení** zobrazení. Jak je popsáno výše, pokud jste již nainstalovali a nakonfigurovali OMS agenty, klikněte na **instalace** zobrazení pro konfiguraci funkce, které vás zajímají pomocí.  

    **Zobrazení monitorování výkonu** – vyberte, jaký protokol pro by měla použít pro syntetické transakce ve výchozí pravidlo pro sledování výkonu a klikněte na Uložit a pokračovat. Tento protokol výběr obsahuje pouze pro generována výchozí pravidlo a je třeba vybrat protokol pokaždé, když chcete vytvořit pravidlo, sledování výkonu explicitně. Vždy můžete přesunout do výchozího nastavení pravidla na kartě Sledování výkonu (zobrazí se po dokončení konfiguraci den-0) a protokol později změnit. V případě, že nejste zájem o rPerfomance funkce monitorování, můžete zakázat výchozí pravidlo od výchozího nastavení pravidla na kartě Sledování výkonu. 

    ![Konfigurace NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Zobrazení monitorování koncový bod služby** -možnost poskytuje integrované předkonfigurované testy monitorování síťové připojení k Office 365 a Dynamcis365 z agenty. Zvolte služby Office 365 a Dynamcis365, které vás zajímají monitorování zaškrtnutím políčka vedle je. Zvolte agentů, z nichž chcete sledovat, kliknutím na tlačítko přidat agenty. Pokud nechcete použít tuto funkci, nebo chcete je nastavit později, můžete přeskočit to a přímo klikněte na **Uložit** a **pokračovat** bez nic výběr.  

    ![Konfigurace NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Zobrazení monitorování ExpressRoute** – klikněte na **zjistit teď** tlačítko vyhledat všechny ExpressRoute soukromé partnerské vztahy, které jsou připojené k virtuálním sítím v rámci předplatného Azure spojena se tento pracovní prostor analýzy protokolů.  


    >[!NOTE] 
    > Řešení aktuálně zjišťuje pouze soukromé partnerské vztahy ExpressRoute. 

    >[!NOTE] 
    > Pouze ty soukromé partnerské vztahy jsou zjišťovány, které jsou připojené k virtuální sítě přidružený k odběru spojena se tento pracovní prostor analýzy protokolů. Pokud vaše ExpressRoute se připojit k virtuálním sítím mimo předplatné propojené do tohoto pracovního prostoru, budete muset vytvořit pracovní prostor analýzy protokolů v těchto předplatných a monitorování těchto partnerských vztahů pomocí NPM. 

    ![Konfigurace NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Po dokončení zjišťování zjištěných soukromé partnerské vztahy jsou uvedené v tabulce.  

    ![Konfigurace NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    Monitorování pro tyto partnerské vztahy jsou původně v zakázaném stavu. Klikněte na každý partnerský vztah zájem o monitorování a konfigurace monitorování pro ně z pravé straně (RHS) podrobností zobrazení.  Klikněte na tlačítko Uložit konfiguraci uložit. V tématu [monitorování konfigurace ExpressRoute]() Další informace.  

    Po dokončení instalace trvá hodinu k naplnění dat 30 minut. Při řešení je agregování dat z vaší sítě, uvidíte *řešení vyžaduje další konfiguraci* na dlaždici s přehledem NPM. Jakmile data se shromažďují a indexované, přehledu dlaždici změny a informuje souhrnné informace o stavu vaší sítě. Potom můžete upravit monitorování uzlů, ve kterých jsou nainstalováni agenti OMS, a také podsítě zjištěných z prostředí 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Upravte nastavení monitorování pro podsítě a uzly 

Na kartě podsítě na stránce konfigurace jsou uvedeny všechny podsítě alespoň jeden instalaci agenta. 


Chcete povolit nebo zakázat monitorování konkrétní podsítě 

1. Vyberte nebo zaškrtněte políčko vedle položky **podsítí ID** a potom ověřte, že **použijte pro monitorování** je vybrané nebo nezaškrtnuté, podle potřeby. Můžete zaškrtněte nebo zrušte více podsítí. Pokud je zakázáno, nejsou monitorována podsítě jako agenti se aktualizují na zastavení otestováním jiné agenty. 
2. Vyberte uzly, které chcete monitorovat v konkrétní podsíť podsítí výběrem ze seznamu a přesun požadované uzly mezi seznamy obsahující sledována a sledované uzly. Můžete přidat **vlastní popis** podsítě. 
3. Klikněte na tlačítko **Uložit** konfiguraci uložíte. 

#### <a name="choose-nodes-to-monitor"></a>Zvolte uzlů k monitorování

Všechny uzly, které nemá agenta nainstalovaného na jejich jsou uvedeny v **uzly** kartě. 

1. Vyberte nebo zrušte uzly, které chcete sledovat nebo zastavení monitorování. 
2. Klikněte na tlačítko **použijte pro monitorování**, nebo vymazat, podle potřeby. 
3. Klikněte na **Uložit**. 


Konfigurace capability(s), které vás zajímají: 
- Konfigurace [sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Konfigurace [monitorování koncového bodu služby](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Konfigurace [monitorování ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Podrobnosti kolekce dat
Sledování výkonu sítě používá TCP SYN. SYNACK ACK handshake paketů při výběru TCP a odpověď odezvy ICMP ODEZVU protokolu ICMP při výběru jako protokol ICMP ke shromažďování informací ztrátě a latenci. Příkaz Traceroute slouží také k získání informací o topologii.

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se údaje pro sledování výkonu sítě.

| Platforma | Přímé agenta | Agenta nástroje SCOM | Azure Storage | SCOM vyžaduje? | Data agenta SCOM odeslána prostřednictvím skupiny pro správu | Frekvence kolekce |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP metodou handshake/ODEZVU protokolu ICMP každých 5 sekund, data odeslána každé 3 minuty |
 

 
Toto řešení využívá k posouzení stavu sítě syntetické transakce. Agenti OMS nainstalovaný na různých místech v síťových paketů TCP exchange nebo odezvu protokolu ICMP (v závislosti na protokol vybraná pro monitorování) sebou. V procesu agenty další operace round-trip čas a paket ztrátě případných. Každý agent pravidelně provádí taky trasování cesty a jiné agenty pro najít všechny různé trasy v síti, která musí být testována. Na základě těchto dat můžete agenty odvodit latence sítě a obrázků ke ztrátě paketů. Testy jsou opakovat každých pět sekund a data se shromažďují pro dobu 3 minut pomocí agentů před nahráním do služby analýzy protokolů. 



>[!NOTE]
> I když agenti komunikaci mezi sebou často, se negenerují významné síťových přenosů při provádění testů. Agenti spoléhat jenom na pakety handshake TCP SYN. SYNACK ACK k určení ztrátě a čekací doba – žádná data, které se vyměňují paketů. Během tohoto procesu agenty vzájemně komunikovat pouze v případě potřeby a komunikační topologie agenta je optimalizována pro snížení síťový provoz.

## <a name="using-the-solution"></a>Použití řešení 

### <a name="npm-overview-tile"></a>Dlaždice přehledu NPM 

Poté, co jste povolili nástroj Sledování výkonu sítě řešení, dlaždice řešení na stránce Přehled poskytuje rychlý přehled o stavu sítě. 

 ![Dlaždice přehledu NPM](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Řídicí panel monitorování výkonu sítě 

**Události stavu sítě horní** stránka obsahuje seznam nejaktuálnějších událostí, stavu a výstrahy v systému a čas, vzhledem k tomu, že událost byla aktivní. Událost stavu nebo výstraha je vygenerována vždy, když hodnota vybrané metriky (ztrátě, latenci, doby odezvy nebo využití šířky pásma) pro monitorování pravidlo překročí prahovou hodnotu. 

 **Sledování výkonu** stránku obsahuje, shrnutí stavu síťová propojení a podsítí propojuje monitorovaných řešení. Na dlaždici topologie informuje o počtu síťových cest, které jsou monitorovány ve vaší síti. Kliknutím na tuto dlaždici přímo můžete přejde do zobrazení topologie. 

 **Monitorování koncového bodu služby** stránku obsahuje, shrnutí stavu různých testy, které jste vytvořili. Na dlaždici topologie informuje počet koncových bodů, které jsou monitorovány. Kliknutím na tuto dlaždici přímo můžete přejde do zobrazení topologie.

 **ExpressRoute monitorování** stránka poskytuje souhrn stavu různých připojení partnerského vztahu ExpressRoute monitorovaných řešení. Na dlaždici topologie informuje o počtu síťových cest prostřednictvím circuit(s) ExpressRoute monitorovaných ve vaší síti. Kliknutím na tuto dlaždici přímo můžete přejde do zobrazení topologie.

 **Běžné dotazy** stránka obsahuje sadu vyhledávací dotazy, které načíst monitorování dat přímo nezpracovaná sítě. Tyto dotazy můžete použít jako východisko pro vytvoření vlastní dotazy pro vytváření přizpůsobených sestav. 

![Řídicí panel NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>procházení hloubka 

Můžete kliknout na různých odkazy na řídicí panel řešení přejdete dolů hlubší do všech oblastí zájmu. Například pokud se zobrazí výstraha nebo není v pořádku síťové propojení se zobrazují na řídicím panelu, můžete můžete kliknutím na prošetřily. Přejdete na stránku, která uvádí všechna propojení podsítí pro konkrétní síťové propojení. Budete moci zobrazit stav, latence a ztráta stav každé propojení podsítí a rychle zjistěte jaké propojení podsítí jsou příčinou problému. Poté můžete kliknout na **zobrazení uzlu odkazy** zobrazíte všechny odkazy na uzlu odkazu podsíť není v pořádku. Potom můžete zobrazit jednotlivé uzly – odkazy a odkazy uzlů ve špatném stavu. 

Můžete kliknout na **zobrazení topologie** zobrazíte topologii směrování směrování trasy mezi zdrojovým a cílovým uzly. Není v pořádku trasy jsou zobrazené červeně a lze je zobrazit latence přispěli každého směrování, aby mohli rychle identifikovat problém pro konkrétní části sítě. 

 

### <a name="network-state-recorder"></a>Zapisovač stavu sítě 

Každé zobrazení zobrazí snímek stavu vaší sítě na určitém místě v čase. Ve výchozím nastavení se zobrazí poslední stav. Na panelu v horní části stránky zobrazí bod v čase, pro který se zobrazuje stav. Můžete přejít zpět v čase a kliknutím na panelu na akce zobrazit snímek stavu vaší sítě. Také můžete k povolení nebo zakázání automatického obnovení pro všechny stránky, zatímco zobrazit nejnovější stav. 

 ![Zapisovač stavu sítě](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafy trendů 

Na každé úrovni, který můžete procházet uvidíte trend použít metrika – ztráta, latenci, doby odezvy, využití šířky pásma. Časový interval trendu můžete změnit pomocí ovládacího prvku čas v horní části grafu. 

Grafy trendů zobrazit Historický přehled výkonu metriky výkonu. Některé problémy s sítě jsou přechodné ve své podstatě a bude obtížné zachytit pouze na základě aktuálního stavu sítě. Je to proto, že problémy můžete rychle surface a zmizí před nikým oznámení, jenom na objevit znova později v čase. Tyto přechodné problémy může být složité pro správce aplikace také protože ty problémy často prostor jako nevysvětlitelné zvyšování doba odezvy aplikace, i když plynulejší se zobrazují všechny součásti aplikace. 

Tyto druhy problémů můžete snadno zjistit prohlížením graf trendů, kde problém se zobrazí jako nečekané Špička v latence sítě nebo ke ztrátě paketů. Potom můžete prozkoumat problém pomocí zapisovače stavu sítě zobrazit sítě snímku a topologie pro tento bod v čase, pokud měl k problému došlo. 

 
![Grafy trendů](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapy topologie 

NPM ukazuje topologie směrování směrování tras mezi zdrojovým a cílovým koncového bodu, na mapě interaktivní topologie. Mapy topologie můžete zobrazit kliknutím na **topologie** dlaždici na řídicím panelu řešení nebo kliknutím na **zobrazení topologie** odkaz na stránkách procházení.  

Topologie mapa zobrazuje, kolik trasy jsou mezi zdrojové a cílové a co cesty trvat datových paketů. Latence přispěli každého směrování sítě je také viditelné. Všechny cesty, pro které latence celkový cesta je nad prahovou hodnotou (v odpovídajících monitorování pravidle nastavit) se zobrazí červeně.  

Když kliknete na uzel nebo hover nad ním na mapě topologie, zobrazí se vlastnosti uzlu jako plně kvalifikovaný název domény a IP adresa. Klikněte na tlačítko směrování zobrazíte jeho IP adresu. Směrování problémových sítě lze určit podle vašeho povšimnutí latence se přispěli. Můžete filtrovat pomocí filtrů v podokně Akce sbalitelné konkrétní trasy. Topologie sítě také zjednodušíte skrytí střední směrování pomocí posuvníku v podokně Akce. Můžete zvětšení nebo mimo mapy topologie pomocí kolečko myši. 

Upozorňujeme, že topologie, zobrazí se v mapě je topologie vrstvy 3 a neobsahuje vrstvy 2 zařízení a připojení. 

 
![Mapy topologie](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Hledání analýzy protokolů 

Všechna data, která je graficky zveřejněné přes NPM řídicí panel a procházení stránek je také k dispozici nativně [analýzy protokolů hledání](log-analytics-log-search-new.md). Můžete provádět interaktivní analýzu dat v úložišti, korelovat data z různých zdrojů, vytvářet vlastní výstrahy, vytvořte vlastní zobrazení a export dat do aplikace Excel, PowerBI nebo ke sdílení odkaz. Oblasti běžné dotazy na řídicím panelu má některá užitečná dotazy, které můžete použít jako výchozí bod pro vytvoření vlastní dotazy a sestavy. 

 

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby 

**UserVoice** -účtováním pro sledování výkonu sítě funkce, které chcete, abychom mohli pracovat na vašich nápadů. Navštivte naše [stránku UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Připojení k naší kohorty** -zajímají nás vždy nutnosti připojení k naší kohorty nové zákazníky. V rámci ho využívat nové funkce a Pomozte nám vylepšit sledování výkonu sítě. Pokud vás zajímá připojení, vyplnění to [rychlé průzkum](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Další postup 
- Další informace o [sledování výkonu](log-analytics-network-performance-monitor-performance-monitor.md), [monitorování koncového bodu služby](log-analytics-network-performance-monitor-performance-monitor.md), a [ExpressRoute monitorování](log-analytics-network-performance-monitor-expressroute.md). 
