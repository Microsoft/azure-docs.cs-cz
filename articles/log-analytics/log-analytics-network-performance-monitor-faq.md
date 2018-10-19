---
title: Nejčastější dotazy – řešení Network Performance monitoru v Azure | Dokumentace Microsoftu
description: Tento článek zaznamená nejčastější dotazy o NPM v Azure. Network Performance Monitor (NPM) vám pomůže s monitorováním výkonu vaší sítě, téměř v reálném čase, ke zjišťování a vyhledejte síťové výkonnostní kritické body.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinynigam
ms.openlocfilehash: 2821f3fa07d8d9ada02da212084639c93e469d0b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408876"
---
# <a name="network-performance-monitor-solution-faq"></a>Network Performance Monitor řešení – nejčastější dotazy

![Symbol sledování výkonu sítě](media/log-analytics-network-performance-monitor/npm-symbol.png)

Tento článek zachycuje nejčastější dotazy (FAQ) o Network Performance Monitor (NPM) v Azure

[Network Performance Monitor](/azure/networking/network-monitoring-overview) je Cloudová [monitorování sítě hybridní](log-analytics-network-performance-monitor-performance-monitor.md) řešení usnadňující sledování výkonu sítě mezi různými body v síťové infrastruktuře. To zároveň pomáhá monitorovat síťové připojení k [koncové body služby a aplikace](log-analytics-network-performance-monitor-service-endpoint.md) a [monitorování výkonu Azure ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 

Network Performance Monitor zjistí problémy se sítí jako provoz blackholing směrování chyby a problémy, které nejsou schopna zjistit monitorování metody konvenční sítě. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Další informace o různých možnostech nepodporuje [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) je k dispozici online.

## <a name="set-up-and-configure-agents"></a>Nastavení a konfigurace agentů

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky na platformu pro uzly, které má být použit pro monitorování pomocí NPM?
Tady jsou požadavky na platformu pro NPM, a různé možnosti:

- Sledování výkonu a možnosti monitorování připojení služby NPM, a podporují i Windows server (2008 SP1 nebo novější) a Windows stolních počítačů nebo klientské operační systémy (Windows 10, Windows 8.1, Windows 8 a Windows 7). 
- Funkce monitorování ExpressRoute NPM, a podporuje pouze Windows server (2008 SP1 nebo novější) operačního systému.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Můžete použít počítače s Linuxem jako sledování uzlů v NPM?
Možnost monitorování sítí pomocí uzlů založených na Linuxu je v současnosti ve verzi private preview. Obraťte na vašeho Account manažera pro další informace. Po zadání ID pracovního prostoru, budeme pokračovat a povolit funkci. Agenti systému Linux poskytují možnost monitorování pouze pro NPM, a možnost sledování výkonu a nejsou k dispozici pro možnosti sledování ExpressRoute a připojení k monitorování služby

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky na velikost uzlů se použije pro monitorování, tak NPM?
Pro spuštění na uzlech virtuálních počítačů určených k monitorování sítě řešení NPM, uzly by měl mít aspoň 500 MB paměti a jedno jádro. Není potřeba používat samostatné uzly pro spouštění NPM. Řešení můžete spustit na uzly, které mají jiné úlohy v něm spuštěný. Má řešení 5 % využití procesoru umožňuje ukončit proces monitorování v případě, že využívá více než.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Pokud chcete použít NPM, připojovat své uzly jako přímý agent nebo pomocí System Center Operations Manager?
Sledování výkonu a možnosti monitorování připojení služby podporují uzlů [připojený jako přímí agenti](log-analytics-agent-windows.md) stejně jako [připojený prostřednictvím Operations managera](log-analytics-om-agents.md).

Pro funkce monitorování ExpressRoute, které musí být připojené uzlů Azure jako přímí agenti pouze. Azure uzly, které jsou propojené prostřednictvím nástroje Operations Manager se nepodporují. Pro uzly v místním uzly připojené jako přímí agenti, stejně jako prostřednictvím nástroje Operations Manager jsou podporovány pro monitorování okruhu ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Protokol TCP a ICMP, který je třeba zvolit pro monitorování?
Pokud monitorujete svoji síť pomocí služeb Windows na serveru uzly, doporučujeme že použít TCP jako protokol monitorování, protože nabízí vyšší přesnost. 

Windows stolní počítače nebo klienta na základě operačního systému uzlů ICMP se doporučuje, protože tato platforma neumožňuje TCP data odesílaná nezpracovaná sockets, která je nástroj NPM potřebné ke zjišťování síťové topologie.

Další podrobnosti získáte na relativní výhody každý protokol [tady](log-analytics-network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Konfiguraci uzlu pro podporu monitorování prostřednictvím protokolu TCP
Pro uzel k podpoře monitorování prostřednictvím protokolu TCP: 
* Ujistěte se, že uzel platformy Windows serveru (2008 SP1 nebo novější).
* Spustit [EnableRules.ps1](https://aka.ms/npmpowershellscript) skript prostředí Powershell na uzlu. Zobrazit [pokyny](log-analytics-network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) další podrobnosti.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak mohu změnit port TCP používán NPM pro monitorování?
Můžete změnit port TCP používaný NPM pro monitorování, spuštěním [EnableRules.ps1](https://aka.ms/npmpowershellscript) skriptu. Je nutné zadat číslo portu, který máte v úmyslu použít jako parametr. Například pokud chcete povolit TCP na portu 8060, spusťte `EnableRules.ps1 8060`. Ujistěte se, že používáte stejný port TCP na všech uzlech se používají pro monitorování.

Skript nakonfiguruje pouze Windows Firewall místně. Pokud máte síťová brána firewall nebo pravidlech skupiny zabezpečení sítě (NSG), ujistěte se, že umožňují provoz určený pro port TCP používaný systémem NPM.

### <a name="how-many-agents-should-i-use"></a>Kolik agentů mám použít?
Měli byste použít alespoň jednoho agenta pro každou podsíť, která chcete monitorovat.

## <a name="monitoring"></a>Monitorování

### <a name="how-are-loss-and-latency-calculated"></a>Jak se počítají ztrát a latence
Zdroj agenti odesílají buď TCP SYN požadavky (Pokud TCP se zvolí jako protokol pro monitorování) nebo žádostí o ODEZVU protokolu ICMP (Pokud je protokol ICMP se zvolí jako protokol pro monitorování) na IP adresu cílového v pravidelných intervalech a ujistěte se, že všechny cesty mezi zdroj cíl IP kombinace se vztahuje. Procento přijatých paketů a dobu odezvy paketů se měří k výpočtu ztrát a latence každé cesty. Tato data je agregované za interval cyklického dotazování a u všech cest k získání agregované hodnoty ztrát a latence pro kombinaci IP pro konkrétní interval dotazování.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Jak často zdrojovém agentovi pro odesílání paketů na cíl pro monitorování?
Zdroj pro funkce monitorování výkonu a monitorování ExpressRoute, odesílá pakety každých 5 sekund a zaznamenává měření sítě. Tato data je souhrn za interval cyklického dotazování 3minutové pro výpočet průměrné a špičku hodnoty ztrát a latence. Frekvenci odesílání paketů pro měření sítě pro funkce monitorování připojení služby, které je určeno frekvence zadané uživatelem pro konkrétní testovací při konfiguraci testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Kolik pakety odesílají pro monitorování?
Počet paketů odesílaných agenta zdroje do cíle v dotazování je adaptivní a rozhodnuto pomocí našeho vlastního algoritmu, který se může lišit pro jiné síťové topologie. Větší počet síťových cest mezi zdroj cíl IP kombinace, další je počet paketů, které se odesílají. Systém zajistí, že jsou zahrnuté všechny cesty mezi kombinaci IP zdroj cíl.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Jak NPM zjišťování topologie sítě mezi zdrojovým a cílovým?
NPM vlastního algoritmu podle Traceroute používá ke zjišťování cesty a segmentů směrování mezi zdrojem a cílem.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>NPM poskytuje informace o směrování a přepínání úrovni 
I když NPM může zjistit všechny možné trasy mezi agentem zdroj a cíl, neposkytuje viditelnost, do kterého byla získána směrování paketů odesílaných vaše konkrétní úlohy. Řešení můžete identifikovat cesty a základní segmentů směrování, které přidáváte další latenci, než jste očekávali.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Proč jsou některé z cest není v pořádku?
Různých cest v síti může existovat mezi zdrojovou a cílovou IP adres a jednotlivé cesty může mít jinou hodnotu ztrát a latence. NPM označí (označeny červenou barvou) tyto cesty k jako není v pořádku, pro kterého hodnoty ztrát a latence je větší než příslušné mezní hodnotu nastavenou v konfiguraci monitorování.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co místo směrování v červená barva v mapě topologie sítě?
Pokud segment směrování je červené, označuje, že se jedná o součást alespoň jednu cestu pro není v pořádku. NPM pouze označí cesty jako není v pořádku, není to oddělit stav každé cesty. K identifikaci problémových segmentů směrování, můžete zobrazit hop-by-hop latence a oddělit ty přidání více než očekávaná latence.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak funguje lokalizace chyb v nástroji Sledování výkonu?
NPM používá mechanismus pravděpodobnostní pravděpodobnosti selhání přiřadit každé síťové cestě segmentu sítě, a základní síťová směrování na základě počtu chybné cesty jsou součástí. Podle segmentů sítě a směrování se stanou součástí větší počet cest není v pořádku, zvyšuje se pravděpodobnost selhání, k nim má přiřazené. Tento algoritmus funguje nejlépe, když máte mnoho uzlů s NPM agent je připojený k sobě navzájem, jak se tím zvyšuje datových bodů pro výpočet pravděpodobnosti výskytu chyb.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak můžete vytvořit výstrahy v NPM?
Odkazovat na [výstrahy oddílu v dokumentaci k](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) podrobné pokyny.

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Můžete NPM monitorovat síťové směrovače a servery jako jednotlivá zařízení?
NPM identifikuje pouze IP adresy a hostitele název podkladové síťové směrování (přepínače, směrovače, servery atd.) mezi zdrojovým a cílovým IP adresy. Také identifikuje latence mezi tyto zjištěné segmenty směrování. Nesleduje jednotlivě tyto základní segmenty směrování.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>NPM umožňuje monitorovat síťové připojení mezi Azure a AWS?
Ano. Najdete v článku [monitorování Azure, AWS a místních sítí pomocí NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) podrobnosti.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Využití šířky pásma ExpressRoute je příchozí nebo odchozí?
Využití šířky pásma je celková příchozí a odchozí šířka pásma. Je vyjádřena v bitech za sekundu.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Můžeme získat informace příchozí a odchozí šířka pásma pro ExpressRoute?
Příchozí a odchozí hodnoty pro primární a sekundární šířka pásma se dají zachytit.

Vytvoření partnerského vztahu úrovni informace, použijte níže uvedených dotazu prohledávání protokolu

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Pro úroveň informací o okruhu, použijte níže uvedených dotazu 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Které oblasti jsou podporovány pro monitorování výkonu NPM?
NPM můžete monitorovat připojení mezi sítěmi v jakékoli části na světě, z pracovního prostoru, který je hostován v jednom z [podporované oblasti](log-analytics-network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Které oblasti jsou podporovány pro monitorování připojení služby NPM?
NPM můžete monitorovat připojení ke službám v jakékoli části na světě, z pracovního prostoru, který je hostován v jednom z [podporované oblasti](log-analytics-network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Které oblasti jsou podporovány pro monitorování NPM pro ExpressRoute?
NPM můžete monitorovat okruhů ExpressRoute, který je umístěný v libovolné oblasti Azure. Připojení k NPM, budete potřebovat pracovní prostor Log Analytics, která musí být hostovaný v jednom z [podporované oblasti](/azure/expressroute/how-to-npm#regions)

## <a name="troubleshoot"></a>Řešení potíží

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Proč nejsou některé segmenty směrování označená jako neznámá v zobrazení topologie sítě?
NPM používá upravenou verzi traceroute ke zjišťování topologie z agenta zdroje do cíle. Neidentifikovaný segment směrování představuje, že směrování sítě nereagovala na žádost traceroute zdrojového agenta. Pokud agenta traceroute Neodpovídat 3 po sobě jdoucích segmentů směrování, řešení označí reagovat segmentů směrování, jako jsou neidentifikované a nepokusí se zjistit další směrování.

Směrování nemusí odpovídat traceroute v jedné nebo více následující scénáře:

* Směrovače nastavené nezpřístupňovat svoji identitu.
* Síťová zařízení nejsou povolení ICMP_TTL_EXCEEDED provozu.
* Brána firewall blokuje ICMP_TTL_EXCEEDED odpovědi ze síťového zařízení.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Toto řešení ukazuje 100 % dojít ke ztrátě, ale jsou dostupné možnosti připojení mezi zdrojem a cílem
K tomu může dojít, pokud zprostředkující brány (síťová brána firewall nebo Azure NSG) nebo bránu firewall hostitele blokuje komunikaci mezi agentem zdroj a cíl přes port monitorování nástroj NPM (ve výchozím nastavení port je 8084, není-li Zákazník byl změněn to).

* Pokud chcete ověřit, že hostitelské brány firewall neblokuje komunikaci na požadovaný port, zobrazit stav zdrojových a cílových uzlů z následující zobrazení: Network Performance Monitor -> Konfigurace -> uzlů. 
  Pokud jsou v pořádku, zobrazení pokynů a provést opravné akce. Pokud jsou uzly v dobrém stavu, přejděte k kroku b. níže.
* Ověřte, že k zprostředkující síťová brána firewall nebo Azure NSG neblokuje komunikaci na požadovaný port, můžete pomocí nástroje třetích stran PsPing následujících pokynů:
  * psping nástroj je k dispozici ke stažení [zde](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Spusťte následující příkaz z zdrojový uzel.
    * psping -n 15 \<cílový uzel IPAddress\>: ČísloPortu NPM ve výchozím nastavení používá 8084 port. V případě můžete explicitně změnily to pomocí skriptu EnableRules.ps1, zadejte číslo port. Tento vlastní port, který používáte). Toto je odeslání příkazu ping z počítače Azure k místnímu
* Zkontrolujte, jestli jsou příkazy ping pro zjištění úspěšné. Pokud ne, pak určuje, že k zprostředkující síťová brána firewall nebo Azure NSG blokuje provoz na tomto portu.
* Nyní spusťte příkaz z cílového uzlu na zdrojová IP adresa uzlu.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Existuje ztráta z uzlu A, b, ale ne z uzel B a A. Proč?
Jak síťových cest mezi od A do B se může lišit od síťových cest mezi B do A, může být dodržen různé hodnoty ztrát a latence.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Proč nejsou všechny okruhy ExpressRoute a připojení s partnerským vztahem nemožnost zjistit?
To může nastat, pokud váš okruh a partnerské vztahy virtuálních sítí se distribuují napříč několika předplatnými. NPM zjišťuje pouze ty ExpressRoute privátní připojení s partnerským vztahem ve kterých se virtuální sítě připojené k ExpressRoute jsou ve stejném předplatném, jako je propojený s pracovním prostorem NPM. Kromě toho NPM zjistí tyto partnerské vztahy Microsoftu v tedy připojených okruh ExpressRoute v rámci stejného předplatného, jako je propojený s pracovním prostorem NPM. To může být vyjasněno z následujícím příkladu:

 Pokud máte 2 virtuálními SÍTĚMI - virtuální síť A v předplatném A a B virtuální sítě v předplatném B, připojené k ExpressRoute v rámci předplatného C. Kromě toho je v předplatném C. Další virtuálními SÍTĚMI C ER má také MS partnerského vztahu v rámci předplatného C. 

Potom

* Pokud NPM pracovní prostor je propojený s předplatným A, pak budete moct monitorovat připojení prostřednictvím ER do virtuální sítě A pouze.
* Pokud NPM pracovní prostor je propojený s předplatným B, pak budete moct sledovat připojením přes virtuální síť b ER pouze.
* Pokud NPM pracovní prostor je propojený s předplatným C, pak budete moct monitorovat připojení prostřednictvím ER virtuální sítí C, jakož i MS partnerský vztah.

Mezi předplatnými podpora bude brzy k dispozici. Potom bude možné monitorovat všechny privátní ExpressRoute a připojení s partnerským vztahem Microsoft v různých předplatných, z jednoho pracovního prostoru.
### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkce monitorování ER má diagnostickou zprávu "Přenos neprochází přes všechny okruh". Co to znamená?

Může být scénář, kde není v pořádku připojení mezi místní a uzlů Azure, ale provoz, nepůjde přes okruh ExpressRoute, byla konfigurována pro sledování nástroj NPM. 

To může dojít, pokud:

* Okruh ER je mimo provoz.
* Filtry tras konfigurují způsobem, že dát přednost k dalším postupům (například připojení k síti VPN nebo jiného okruhu ExpressRoute) přes zamýšlený okruh ExpressRoute. 
* Místní a uzlech Azure jste zvolili pro monitorování okruh ExpressRoute v konfiguraci monitorování, nemají připojení k sobě navzájem přes zamýšlený okruh ExpressRoute. Ujistěte se, že jste zvolili správné uzly, které mají připojení k sobě navzájem nad rámec okruhu ExpressRoute, kterou chcete monitorovat.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Při konfiguraci monitorování okruhu ExpressRoute, nebyly detekovány uzlů Azure.
To může nastat, když Azure uzly jsou propojené prostřednictvím nástroje Operations Manager. Funkce monitorování ExpressRoute podporuje pouze ty uzly Azure, které jsou připojené jako přímí agenti.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Můžu nelze zjistit pomocí okruhů ExpressRoute v portálu OMS
I když NPM můžete použít, jak z webu Azure portal, jakož i na portálu OMS, zjišťování okruh ve funkci monitorování ExpressRoute funguje pouze na webu Azure portal. Po zjištění obvody prostřednictvím webu Azure portal, pak můžete funkci v některém z těchto dvou portálech. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>V možnosti monitorování připojení služby dobu odezvy služby, ztráty v síti, jakož i latence jsou zobrazeny jako není k dispozici
Můžete k tomu dojít, pokud jeden nebo více:

* Služba je mimo provoz.
* Uzel použít pro kontrolu síťové připojení ke službě je mimo provoz.
* Cíl zadaný v konfiguraci testu je nesprávný.
* Uzel nemá žádné připojení k síti.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>V možnosti monitorování připojení služby doba odezvy platný služby se zobrazí, ale ztráty v síti, jakož i latenci, jsou uvedeny jako není k dispozici
 Můžete k tomu dojít, pokud jeden nebo více:

* Pokud je uzel použít pro kontrolu síťové připojení ke službě klientské počítače s Windows, Cílová služba neblokuje požadavky ICMP nebo síťová brána firewall neblokuje požadavky protokolu ICMP, které pocházejí z uzlu.
* Proveďte sítě měření zaškrtávací políčko je prázdný v konfiguraci testu.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>V možnosti monitorování připojení služby dobu odezvy služby není k dispozici, ale ztráty v síti, jakož i latence jsou platné
K tomu může dojít, pokud cílová služba není webovou aplikaci, ale test je konfigurován jako webový test. Upravte konfiguraci testu a zvolte typ testu jako síť místo Web.

## <a name="miscellaneous"></a>Různé

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Je nějaký dopad na výkon v uzlu monitorování?
Zastavit, pokud jej využívá více než 5 % prostředků procesoru hostitele je konfigurován proces NPM. Tím je zajištěno, že můžete používat uzly pro své obvyklé úlohy bez dopadu na výkon.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM upraví pravidla firewallu pro monitorování?
NPM pouze vytvoří pravidlo místní brány Windows Firewall na uzly, na kterých běží Powershellový skript EnableRules.ps1 umožňující agentů k vytvoření připojení TCP mezi sebou na zadaném portu. Řešení neprovede žádné změny libovolné síťová brána firewall nebo pravidlech skupiny zabezpečení sítě (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak můžu zkontrolovat stav uzlů monitorování?
Můžete zobrazit stav uzlů monitorování z následující zobrazení: Network Performance Monitor -> Konfigurace -> uzlů. Pokud uzel není v pořádku, můžete zobrazit podrobnosti o chybě a provést doporučenou akci.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Můžete nahlásit NPM latence v mikrosekundách?
NPM zaokrouhlí latence čísel v uživatelském rozhraní a v milisekundách. Stejná data uložená na vyšší členitost (někdy až čtyři desetinná místa).

## <a name="next-steps"></a>Další postup

- Další informace o Network Performance Monitor rekapitulací [řešení Network Performance monitoru v Azure](log-analytics-network-performance-monitor.md).
