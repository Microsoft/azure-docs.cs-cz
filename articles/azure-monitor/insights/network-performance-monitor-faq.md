---
title: Časté otázky – řešení sledování výkonu sítě v Azure | Dokumenty společnosti Microsoft
description: Tento článek zachycuje nejčastější dotazy týkající se sledování výkonu sítě v Azure. Sledování výkonu sítě (NPM) pomáhá sledovat výkon sítí téměř v reálném čase a zjišťovat a vyhledávat kritická místa výkonu sítě.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096229"
---
# <a name="network-performance-monitor-solution-faq"></a>Nejčastější dotazy týkající se řešení sledování výkonu sítě

![Symbol sledování výkonu sítě](media/network-performance-monitor-faq/npm-symbol.png)

Tento článek zachycuje nejčastější dotazy týkající se sledování výkonu sítě (NPM) v Azure

[Nástroj Sledování výkonu sítě](/azure/networking/network-monitoring-overview) je cloudové [hybridní řešení monitorování sítě,](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) které pomáhá sledovat výkon sítě mezi různými body síťové infrastruktury. Pomáhá také monitorovat připojení k síti ke [koncovým bodům služeb a aplikací a](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) sledovat výkon [služby Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Nástroj Sledování výkonu sítě detekuje problémy se sítí, jako je blackholing provozu, chyby směrování a problémy, které konvenční metody monitorování sítě nejsou schopny zjistit. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Další informace o různých funkcích podporovaných [programem Sledování výkonu sítě](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jsou k dispozici online.

## <a name="set-up-and-configure-agents"></a>Nastavení a konfigurace agentů

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky platformy pro uzly, které mají být použity pro monitorování NPM?
Níže jsou uvedeny požadavky na platformu pro různé možnosti NPM:

- Funkce sledování výkonu a sledování připojení služby npm podporují stolní počítače/klientské systémy Windows i windows. Podporované verze operačního systému Windows server jsou 2008 SP1 nebo novější. Podporované desktopy/klientské verze Windows jsou Windows 10, Windows 8.1, Windows 8 a Windows 7. 
- Funkce sledování ExpressRoute monitoru npm podporuje pouze operační systém Windows server (2008 SP1 nebo novější).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Mohu používat linuxové počítače jako monitorovací uzly v NPM?
Schopnost monitorovat sítě pomocí uzlů založených na Linuxu je aktuálně ve verzi preview. Obraťte se na správce účtu, abyste se dozvěděli více. Agenti Linuxu poskytují možnosti monitorování pouze pro funkci sledování výkonu služby NPM a nejsou k dispozici pro funkce Sledování připojení služby a Monitor ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky na velikost uzlů, které mají být použity pro monitorování npm?
Pro spuštění řešení NPM na virtuálních počítačích uzlů pro monitorování sítí by uzly měly mít alespoň 500 MB paměti a jedno jádro. Není nutné používat samostatné uzly pro spuštění NPM. Řešení lze spustit na uzlech, které mají jiné úlohy spuštěné na něm. Řešení má schopnost zastavit proces monitorování, pokud používá více než 5% CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Chcete-li používat npm, mám připojit své uzly jako přímý agent nebo prostřednictvím nástroje System Center Operations Manager?
Sledování výkonu i funkce Sledování připojení služby podporují uzly [připojené jako přímí agenti](../../azure-monitor/platform/agent-windows.md) a připojené [prostřednictvím nástroje Operations Manager](../../azure-monitor/platform/om-agents.md).

Pro funkci ExpressRoute Monitor by uzly Azure měly být připojeny pouze jako přímí agenti. Uzly Azure, které jsou připojené prostřednictvím nástroje Operations Manager, nejsou podporovány. Pro místní uzly jsou podporovány uzly připojené jako přímí agenti a prostřednictvím nástroje Operations Manager pro monitorování okruhu ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Který protokol mezi protokoly TCP a ICMP by měl být zvolen pro monitorování?
Pokud sledujete síť pomocí uzlů založených na serveru Windows, doporučujeme použít protokol TCP jako monitorovací protokol, protože poskytuje lepší přesnost. 

IcMP se doporučuje pro desktopy Windows/klientské uzly založené na operačním systému. Tato platforma neumožňuje přenos dat TCP přes nezpracované sokety, které NPM používá ke zjišťování topologie sítě.

Můžete získat více informací o relativní výhody každého protokolu [zde](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Jak lze nakonfigurovat uzel pro podporu monitorování pomocí protokolu TCP?
Pro uzel pro podporu monitorování pomocí protokolu TCP: 
* Ujistěte se, že platforma uzlu je Windows Server (2008 SP1 nebo novější).
* Spusťte v uzlu skript [PowerRules.ps1](https://aka.ms/npmpowershellscript) Powershell. Další [podrobnosti](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) najdete v pokynech.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak lze změnit port TCP používaný npm pro monitorování?
Spuštěním skriptu [EnableRules.ps1](https://aka.ms/npmpowershellscript) můžete změnit port TCP používaný protokolem NPM pro monitorování. Musíte zadat číslo portu, které chcete použít jako parametr. Chcete-li například povolit protokol TCP `EnableRules.ps1 8060`na portu 8060, spusťte . Ujistěte se, že používáte stejný port TCP na všech uzlech používaných pro monitorování.

Skript konfiguruje pouze bránu Windows Firewall místně. Pokud máte pravidla síťové brány firewall nebo skupiny zabezpečení sítě (NSG), ujistěte se, že umožňují přenos y určené pro port TCP používaný protokolem NPM.

### <a name="how-many-agents-should-i-use"></a>Kolik agentů mám použít?
Pro každou podsíť, kterou chcete sledovat, byste měli použít alespoň jednoho agenta.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Jaký je maximální počet agentů, které mohu použít, nebo vidím chybu ".... dosáhli jste limitu konfigurace"?
NPM omezuje počet IP adresy na 5000 IP na pracovní prostor. Pokud uzel má adresy IPv4 i IPv6, bude se to pro tento uzel počítat jako 2 IP adresy. Tento limit 5000 IP by proto rozhodl o horní hranici počtu agentů. Neaktivní agenty můžete odstranit z karty Uzly v aplikaci NPM >> Konfigurace. NPM také udržuje historii všech IP adres, které byly někdy přiřazeny k virtuálnímu virtuálnímu počítačům hostujícímu agenta a každý se počítá jako samostatná IP adresa přispívající k této horní hranici 5000 IP adres. Chcete-li uvolnit IP adresy pro pracovní prostor, můžete pomocí stránky Uzly odstranit IP adresy, které se nepoužívají.

## <a name="monitoring"></a>Monitorování

### <a name="how-are-loss-and-latency-calculated"></a>Jak se počítá ztráta a latence
Zdrojoví agenti odesílají požadavky TCP SYN (pokud je protokol TCP vybrán jako protokol pro monitorování) nebo požadavky ICMP ECHO (pokud je icmp vybrán jako protokol pro monitorování) do cílové IP adresy v pravidelných intervalech, aby bylo zajištěno, že všechny cesty mezi adresou IP původu a cílovou kombinace jsou pokryty. Procento přijatých paketů a doba odezvy paketů se měří pro výpočet ztráty a latence každé cesty. Tato data jsou agregována v intervalu dotazování a přes všechny cesty získat agregované hodnoty ztráty a latence pro kombinaci IP pro konkrétní interval dotazování.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>S jakou frekvencí odesílají zdrojové agenty pakety do cíle pro sledování?
Pro funkce Sledování výkonu a ExpressRoute Monitor zdroj odesílá pakety každých 5 sekund a zaznamenává měření v síti. Tato data jsou agregována v intervalu dotazování 3 minuty pro výpočet průměrné a maximální hodnoty ztráty a latence. U funkce Sledování připojení služby je frekvence odesílání paketů pro měření sítě určena frekvencí zadanou uživatelem pro konkrétní test při konfiguraci testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Kolik paketů je odesláno k monitorování?
Počet paketů odeslaných zdrojovým agentem do cíle v dotazování je adaptivní a rozhoduje o tom náš proprietární algoritmus, který se může lišit pro různé topologie sítě. Více počtu síťových cest mezi kombinací ip adresy zdroj-cíl, více je počet odeslaných paketů. Systém zajišťuje, že jsou pokryty všechny cesty mezi kombinací IP zdroj-cíl.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Jak NPM zjišťuje síťovou topologii mezi zdrojem a cílem?
NPM používá proprietární algoritmus založený na Traceroute zjistit všechny cesty a směrování mezi zdrojem a cílem.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Poskytuje npm informace o úrovni směrování a přepínání 
Přestože NPM může zjistit všechny možné trasy mezi zdrojového agenta a cíl, neposkytuje přehled o tom, které trasy byly pořízeny pakety odeslané konkrétní úlohy. Řešení vám může pomoci identifikovat cesty a základní síťové směrování, které přidávají více latence, než jste očekávali.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Proč jsou některé cesty nezdravé?
Mezi zdrojovými a cílovými IP adresami mohou existovat různé síťové cesty a každá cesta může mít jinou hodnotu ztráty a latence. NPM označí tyto cesty jako není v pořádku (označené červenou barvou), pro které je hodnota ztráty nebo latence větší než příslušná prahová hodnota nastavená v konfiguraci monitorování.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co znamená skok v červené barvě v mapě topologie sítě?
Pokud směrování je červená, znamená to, že je součástí alespoň jednu cestu není v pořádku. NPM pouze označí cesty jako není v pořádku, není oddělit stav každé cesty. Chcete-li identifikovat problematické směrování, můžete zobrazit latenci směrování a oddělit ty, které přidávají více než očekávanou latenci.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak funguje lokalizace chyb v monitoru výkonu?
NPM používá pravděpodobnostní mechanismus přiřadit pravděpodobnosti chyb y každé síťové cestě, segmentu sítě a základní sítě směrování na základě počtu cest není v pořádku, které jsou součástí. Jako síťové segmenty a směrování se stanou součástí většího počtu cest není v pořádku, zvyšuje se pravděpodobnost chyb, která je s nimi spojena. Tento algoritmus funguje nejlépe, pokud máte mnoho uzlů s agentem NPM vzájemně propojeny, protože to zvyšuje datové body pro výpočet pravděpodobnosti chyb.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak mohu vytvořit výstrahy v NPM?
Podrobné pokyny naleznete [v části výstrahy v dokumentaci.](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts)

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Jaké jsou výchozí dotazy analýzy protokolů pro výstrahy
Dotaz na sledování výkonu

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Dotaz na monitorování připojení služby

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
Dotazy na monitorování ExpressRoute: Dotaz na obvody

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Soukromý partnerský vztah

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Partnerský vztah Microsoftu

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Běžný dotaz   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Může NPM monitorovat směrovače a servery jako jednotlivá zařízení?
NPM identifikuje pouze název IP a hostitele základního síťového směrování (přepínače, směrovače, servery atd.) mezi zdrojovými a cílovými IP adresami. Také identifikuje latenci mezi těmito identifikovanými směrováními. Nesleduje jednotlivě tyto základní směrování.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM lze použít ke sledování připojení k síti mezi Azure a AWS?
Ano. Podrobnosti najdete v článku [Monitorování Azure, AWS a místních sítí pomocí NPM.](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Je využití šířky pásma ExpressRoute příchozí nebo odchozí?
Využití šířky pásma je celková příchozí a odchozí šířka pásma. Vyjadřuje se v bitech/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Můžeme získat informace o šířce pásma pro ExpressRoute?
Lze zachytit příchozí a odchozí hodnoty pro primární i sekundární šířku pásma.

Pro informace o úrovni partnerského vztahu MS použijte níže uvedený dotaz ve vyhledávání protokolů

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Informace o úrovni soukromého partnerského vztahu použijte níže uvedený dotaz ve vyhledávání protokolů.

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Informace o úrovni okruhu získáte v protokolu.

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Které oblasti jsou podporovány pro sledování výkonu npm?
NPM může monitorovat připojení mezi sítěmi v jakékoli části světa, z pracovního prostoru, který je hostován v jedné z [podporovaných oblastí](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Které oblasti jsou podporovány pro sledování připojení služby NPM?
NPM může monitorovat připojení ke službám v jakékoli části světa, z pracovního prostoru, který je hostován v jedné z [podporovaných oblastí](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Které oblasti jsou podporovány pro monitor ExpressRoute monitoru NPM?
NPM můžete sledovat vaše ExpressRoute okruhy umístěné v libovolné oblasti Azure. Chcete-li na palubě npm, budete potřebovat pracovní prostor Log Analytics, který musí být hostované v jedné z [podporovaných oblastí](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Proč jsou některé chmelové chmely označeny jako neidentifikované v zobrazení topologie sítě?
NPM používá upravenou verzi traceroute ke zjištění topologie ze zdrojového agenta do cíle. Neidentifikovaný směrování představuje, že síťový směrování neodpověděl na požadavek traceroute zdrojového agenta. Pokud tři po sobě jdoucí sítě směrování nereagují na trasování agenta, řešení označí nereagující směrování jako neidentifikované a nepokouší se zjistit další směrování.

Směrování nemusí reagovat na traceroute v jednom nebo více z níže uvedených scénářů:

* Směrovače byly nakonfigurovány tak, aby neodhalily svou identitu.
* Síťová zařízení neumožňují ICMP_TTL_EXCEEDED provoz.
* Brána firewall blokuje ICMP_TTL_EXCEEDED odezvu ze síťového zařízení.

Když některý z koncových bodů leží v Azure, traceroute zobrazí neidentifikovaný směrování jako Azure Infrastructure neodhalí identitu traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Dostávám upozornění pro testy není v pořádku, ale nevidím vysoké hodnoty v grafu ztráty a latence NPM. Jak mohu zkontrolovat, co je nezdravé?
NPM vyvolá výstrahu, pokud end-to-end latence mezi zdrojem a cílem překročí prahovou hodnotu pro libovolnou cestu mezi nimi. Některé sítě mají více cest spojujících stejný zdroj a cíl. NPM vyvolá výstrahu je libovolná cesta není v pořádku. Ztráta a latence vidět v grafech je průměrná hodnota pro všechny cesty, proto nemusí zobrazit přesnou hodnotu jedné cesty. Chcete-li pochopit, kde byla překročena prahová hodnota, vyhledejte sloupec "Podtyp" ve výstraze. Pokud je problém způsoben cestou, bude hodnotou SubType NetworkPath (pro testy sledování výkonu), EndpointPath (pro testy sledování připojení služby) a ExpressRoutePath (pro testy ExpressRotue Monitor). 

Ukázkový dotaz, který má být vyhledání, není v pořádku:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Proč můj test ukazuje nezdravé, ale topologie není 
NPM monitoruje end-to-end ztráty, latence a topologie v různých intervalech. Ztráta a latence se měří jednou za 5 sekund a agregovány každé tři minuty (pro sledování výkonu a sledování expresní trasy), zatímco topologie se počítá pomocí traceroute jednou za 10 minut. Například mezi 3:44 a 4:04 topologie může být aktualizován třikrát (3:44, 3:54, 4:04), ale ztráta a latence jsou aktualizovány přibližně sedmkrát (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologie generované v 3:54 budou vykresleny pro ztrátu a latenci, která se vypočítá v 3:56, 3:59 a 4:02. Předpokládejme, že se zobrazí upozornění, že váš okruh ER není v pořádku v 3:59. Přihlásíte se k npm a pokusíte se nastavit čas topologie na 3:59. NPM vykreslí topologii vygenerovanou v 3:54. Chcete-li porozumět poslední známé topologii sítě, porovnejte pole TimeProcessed (čas, kdy byla vypočtena ztráta a latence) a TracerouteCompletedTime(čas, kdy byla vypočtena topologie). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Jaký je rozdíl mezi poli E2EMedianLatency a AvgHopLatencyList v tabulce NetworkMonitoring
E2EMedianLatency je latence aktualizována každé tři minuty po agregaci výsledků testů ping protokolu TCP, zatímco AvgHopLatencyList je aktualizován každých 10 minut na základě traceroute. Chcete-li pochopit přesný čas, kdy byl vypočítán E2EMedianLatency, použijte pole TimeProcessed. Chcete-li porozumět přesnému času, kdy trasování bylo dokončeno a aktualizováno, použijte pole TracerouteCompletedTime.

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Proč se čísla latence směrování liší od hodnot HopLatencyValues 
HopLatencyValues jsou zdrojem ke koncovému bodu.
Například: Chmel - A,B,C. AvgHopLatency - 10,15,20. To znamená, že zdroj latence = 10, zdroj do latence B = 15 a zdroj c latence je 20. UI vypočítá latenci chmele A-B jako 5 v topologii

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Řešení vykazuje 100% ztrátu, ale existuje konektivita mezi zdrojem a cílem
K tomu může dojít, pokud brána firewall hostitele nebo zprostředkující brána firewall (síťová brána firewall nebo síť Azure NSG) blokuje komunikaci mezi zdrojovým agentem a cílem přes port používaný pro monitorování službou NPM (ve výchozím nastavení je port 8084, pokud zákazník to změnil).

* Chcete-li ověřit, zda brána firewall hostitele neblokuje komunikaci na požadovaném portu, zobrazte stav zdrojových a cílových uzlů z následujícího zobrazení: Sledování výkonu sítě -> Konfigurace -> uzly. 
  Pokud nejsou v pořádku, zobrazte pokyny a podnikejte nápravná opatření. Pokud jsou uzly v pořádku, přesuňte se do kroku b. níže.
* Chcete-li ověřit, zda zprostředkující síťová brána firewall nebo síť Azure NSG neblokuje komunikaci na požadovaném portu, použijte nástroj PsPing jiného výrobce pomocí následujících pokynů:
  * psping nástroj je k dispozici ke stažení [zde](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Spusťte následující příkaz ze zdrojového uzlu.
    * psping -n \<15 cílový\>uzel IPAddress :portNumber Ve výchozím nastavení NPM používá port 8084. V případě, že jste to explicitně změnili pomocí skriptu EnableRules.ps1, zadejte vlastní číslo portu, které používáte). Toto je ping z počítače Azure na místní
* Zkontrolujte, zda jsou příkazy ping úspěšné. Pokud ne, znamená to, že zprostředkující síťová brána firewall nebo azure nsg blokuje provoz na tomto portu.
* Nyní spusťte příkaz z cílového uzlu do adresy IP zdrojového uzlu.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Je ztráta z uzlu A do B, ale ne z uzlu B do A. Proč?
Vzhledem k tomu, že síťové cesty mezi A až B se mohou lišit od síťových cest mezi B a A, lze pozorovat různé hodnoty ztráty a latence.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Proč nejsou zjištěny všechny okruhy ExpressRoute a připojení partnerského vztahu?
NPM nyní zjišťuje ExpressRoute okruhy a partnerských připojení ve všech předplatných, ke kterým má uživatel přístup. Zvolte všechna předplatná, kde jsou propojeny prostředky expresní trasy a povolte monitorování pro každý zjištěný prostředek. NPM hledá objekty připojení při zjišťování soukromého partnerského vztahu, proto zkontrolujte, jestli je virtuální síť přidružená k vašemu partnerského vztahu.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkce monitorování ER má diagnostickou zprávu "Provoz neprochází žádným obvodem". Co to znamená?

Může existovat scénář, kde je v pořádku připojení mezi místní a Uzly Azure, ale provoz neprochází přes okruh ExpressRoute nakonfigurované pro monitorování NPM. 

K tomu může dojít v následujícím případě:

* Obvod pohotovosti nezbyl.
* Filtry tras jsou konfigurovány tak, aby upřednostňovaly jiné trasy (například připojení VPN nebo jiný okruh ExpressRoute) před zamýšleným okruhem ExpressRoute. 
* Místní a Azure uzly vybrané pro monitorování okruhu ExpressRoute v konfiguraci monitorování nemají připojení k sobě přes zamýšlený okruh ExpressRoute. Ujistěte se, že jste vybrali správné uzly, které mají připojení k sobě navzájem přes okruh ExpressRoute, který chcete sledovat.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Při konfiguraci monitorování okruhu ExpressRoute nejsou zjištěny uzly Azure.
K tomu může dojít, pokud jsou uzly Azure připojeny prostřednictvím nástroje Operations Manager. Funkce ExpressRoute Monitor podporuje pouze ty uzly Azure, které jsou připojeny jako přímí agenti.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nelze zjistit pomocí okruhů ExpressRoute na portálu OMS
I když npm lze použít jak z portálu Azure, stejně jako portál OMS, zjišťování okruhu ve funkci ExpressRoute Monitor funguje pouze prostřednictvím portálu Azure. Jakmile jsou obvody zjištěny prostřednictvím portálu Azure, můžete pak použít funkci v jednom ze dvou portálů. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Ve funkci Sledování připojení služby se doba odezvy služby, ztráta sítě a latence zobrazují jako
K tomu může dojít, pokud je splněna jedna nebo více skutečností:

* Služba neklesla.
* Uzel používaný pro kontrolu připojení k síti ke službě je vypnutý.
* Cíl zadaný v konfiguraci testu je nesprávný.
* Uzel nemá žádné připojení k síti.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>Ve funkci Sledování připojení služby je zobrazena platná doba odezvy služby, ale ztráta sítě a latence jsou zobrazeny jako NA
 K tomu může dojít, pokud je splněna jedna nebo více skutečností:

* Pokud je uzel používaný ke kontrole síťového připojení ke službě klientským počítačem se systémem Windows, cílová služba blokuje požadavky ICMP nebo síťová brána firewall blokuje požadavky ICMP, které pocházejí z uzlu.
* Zaškrtávací políčko Provést měření sítě je v testovací konfiguraci prázdné.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Ve funkci Sledování připojení služby je doba odezvy služby NA, ale ztráta sítě a latence jsou platné
K tomu může dojít, pokud cílová služba není webová aplikace, ale test je nakonfigurován jako webový test. Upravte konfiguraci testu a zvolte typ testu jako síť místo jako web.

## <a name="miscellaneous"></a>Různé

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Má vliv na výkon uzlu, který se používá pro monitorování?
Proces NPM je nakonfigurován tak, aby se zastavil, pokud využívá více než 5 % prostředků hostitelského procesoru. Tím zajistíte, že můžete nadále používat uzly pro jejich obvyklé úlohy bez dopadu na výkon.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Upravuje npm pravidla brány firewall pro monitorování?
Služba NPM vytvoří pouze místní pravidlo brány Windows Firewall na uzlech, na kterých je spuštěn skript EnableRules.ps1 Powershell, aby agenti mohli vzájemně vytvářet připojení TCP na určeném portu. Řešení nemění žádná pravidla síťové brány firewall ani skupiny zabezpečení sítě (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak lze zkontrolovat stav uzlů používaných pro monitorování?
Stav uzlů používaných pro monitorování můžete zobrazit v následujícím zobrazení: Sledování výkonu sítě -> Konfigurace -> uzly. Pokud uzel není v pořádku, můžete zobrazit podrobnosti o chybě a provést navrhovanou akci.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Může NPM hlásit čísla latence v mikrosekundách?
NPM zaokrouhluje čísla latence v ui a v milisekundách. Stejná data jsou uložena s vyšší rozlišovací schopností (někdy až čtyři desetinná místa).

## <a name="next-steps"></a>Další kroky

- Další informace o sledování výkonu sítě s odkazem na [řešení Sledování výkonu sítě v Azure](../../azure-monitor/insights/network-performance-monitor.md).
