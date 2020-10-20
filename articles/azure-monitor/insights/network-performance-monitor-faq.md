---
title: Nejčastější dotazy – řešení Network Performance Monitor v Azure | Microsoft Docs
description: Tento článek zachycuje Nejčastější dotazy týkající se Network Performance Monitor v Azure. Network Performance Monitor (NPM) vám pomůže monitorovat výkon sítí prakticky v reálném čase a zjišťovat a vyhledávat kritické body výkonu sítě.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: b8c8e7539b888141a22e92378d78282edd1ce6ff
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208018"
---
# <a name="network-performance-monitor-solution-faq"></a>Nejčastější dotazy k řešení Network Performance Monitor

![Symbol Network Performance Monitor](media/network-performance-monitor-faq/npm-symbol.png)

Tento článek zachycuje Nejčastější dotazy týkající se Network Performance Monitor (NPM) v Azure.

[Network Performance Monitor](../../networking/network-monitoring-overview.md) je cloudové řešení [monitorování hybridní sítě](./network-performance-monitor-performance-monitor.md) , které vám pomůže monitorovat výkon sítě mezi různými body v infrastruktuře vaší sítě. Pomůže vám taky monitorovat síťové připojení ke [koncovým bodům služby a aplikace](./network-performance-monitor-service-connectivity.md) a [monitorovat výkon Azure ExpressRoute](./network-performance-monitor-expressroute.md). 

Network Performance Monitor detekuje problémy se sítí, jako jsou přenosy dat blackholing, chyby směrování a problémy, které nedokáže detekovat konvenční metody monitorování sítě. Toto řešení generuje výstrahy a upozorní vás, když u síťové linky dojde k překročení prahové hodnoty. Zajišťuje také včasné zjištění problémů s výkonem sítě a lokalizuje příčinu problému na konkrétní segment sítě nebo zařízení. 

Další informace o různých funkcích podporovaných nástrojem [Network Performance Monitor](../../networking/network-monitoring-overview.md) jsou k dispozici online.

## <a name="set-up-and-configure-agents"></a>Nastavení a konfigurace agentů

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky na platformu pro uzly, které se mají použít k monitorování pomocí NPM?
Níže jsou uvedené požadavky na platformu pro různé možnosti NPM:

- Funkce monitorování výkonu a monitorování výkonu služby NPM podporují desktopové a klientské operační systémy Windows Server i Windows. Podporované verze operačního systému Windows Server jsou 2008 SP1 nebo novější. Podporovány jsou stolní počítače a verze klientů Windows ve Windows 10, Windows 8.1, Windows 8 a Windows 7. 
- Funkce monitorování ExpressRoute pro NPM podporuje jenom operační systém Windows Server (2008 SP1 nebo novější).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Můžu počítače se systémem Linux použít jako uzly monitorování v NPM?
Možnost monitorování sítí pomocí uzlů se systémem Linux je aktuálně ve verzi Preview. Obraťte se na svého správce účtu a získejte další informace. Agenti systému Linux poskytují možnost monitorování pouze pro funkci sledování výkonu NPM a nejsou k dispozici pro monitorování připojení služby a možnosti monitorování ExpressRoute.

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jaké jsou požadavky na velikost uzlů, které se mají použít k monitorování pomocí NPM?
Aby bylo možné spustit řešení NPM na virtuálních počítačích uzlů pro monitorování sítí, musí mít uzly alespoň 500 MB paměti a jednu jádro. Nemusíte používat samostatné uzly pro používání NPM. Řešení může běžet na uzlech, na kterých běží jiné úlohy. Řešení má možnost zastavit proces monitorování, pokud používá více než 5% CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Pokud chcete použít NPM, mám uzly připojit jako přímý agent nebo prostřednictvím System Center Operations Manager?
Monitorování výkonu i možnosti monitorování připojení služby podporují uzly [připojené jako přímí agenti](../platform/agent-windows.md) a [připojení prostřednictvím Operations Manager](../platform/om-agents.md).

Pro funkci monitorování ExpressRoute by uzly Azure měly být připojené pouze jako přímí agenti. Uzly Azure, které jsou připojené prostřednictvím Operations Manager, se nepodporují. U místních uzlů se pro monitorování okruhu ExpressRoute podporují uzly připojené jako přímí agenti a prostřednictvím Operations Manager.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Který protokol mezi TCP a ICMP by měl být zvolen pro monitorování?
Pokud sledujete síť pomocí uzlů založených na Windows serveru, doporučujeme, abyste jako monitorovací protokol používali TCP, protože poskytuje lepší přesnost. 

Protokol ICMP se doporučuje u klientských počítačů s operačním systémem Windows nebo klientských uzlů. Tato platforma neumožňuje odesílat data TCP přes nezpracované sokety, které NPM používá ke zjišťování síťové topologie.

Můžete získat další podrobnosti o relativních výhodách [jednotlivých protokolů.](./network-performance-monitor-performance-monitor.md#choose-the-protocol)

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Jak můžu nakonfigurovat uzel pro podporu monitorování pomocí protokolu TCP?
Pro uzel, který podporuje monitorování pomocí protokolu TCP: 
* Ujistěte se, že platforma Node je Windows Server (2008 SP1 nebo novější).
* Spusťte [EnableRules.ps1](https://aka.ms/npmpowershellscript) skriptu PowerShellu na uzlu. Další podrobnosti najdete v [pokynech](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) .


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak můžu změnit port TCP používaný službou NPM ke sledování?
Port TCP, který používá NPM k monitorování, můžete změnit spuštěním skriptu [EnableRules.ps1](https://aka.ms/npmpowershellscript) . Je nutné zadat číslo portu, který chcete použít jako parametr. Chcete-li například povolit TCP na portu 8060, spusťte příkaz `EnableRules.ps1 8060` . Ujistěte se, že používáte stejný port TCP na všech uzlech, které jsou používány pro monitorování.

Skript nakonfiguruje pouze místní bránu firewall systému Windows. Pokud máte pravidla brány firewall sítě nebo skupiny zabezpečení sítě (NSG), ujistěte se, že povolují provoz určený pro port TCP používaný v NPM.

### <a name="how-many-agents-should-i-use"></a>Kolik agentů mám použít?
Pro každou podsíť, kterou chcete monitorovat, byste měli použít aspoň jednoho agenta.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Jaký je maximální počet agentů, které můžu použít, nebo se zobrazuje chyba... dosáhli jste limitu konfigurace?
NPM omezuje počet IP adres na jeden pracovní prostor na 5000. Pokud má uzel adresy IPv4 i IPv6, bude se tento uzel počítat jako 2 IP adresy. Proto tento limit 5000 IP adres určí horní limit počtu agentů. Neaktivních agentů můžete odstranit na kartě uzly v NPM >> nakonfigurovat. NPM také udržuje historii všech IP adres, které byly někdy přiřazeny k virtuálnímu počítači, který je hostitelem agenta, a každá z nich se počítá jako samostatná IP adresa přispívající k tomuto hornímu limitu 5000 IP adres. Pokud chcete pro svůj pracovní prostor uvolnit IP adresy, můžete pomocí stránky uzly odstranit IP adresy, které se nepoužívají.

## <a name="monitoring"></a>Monitorování

### <a name="how-are-loss-and-latency-calculated"></a>Jak se počítají ztráty a latence
Zdrojové agenti odesílají žádosti TCP SYN (Pokud je zvolen protokol TCP jako protokol pro monitorování) nebo požadavky na ODEZVu ICMP (Pokud se protokol ICMP vybere jako protokol pro monitorování) do cílové IP adresy v pravidelných intervalech, aby se zajistilo, že se pokryje všechny cesty mezi kombinací IP adresy zdroje a cíle. Procento přijatých paketů a doba odezvy přenosu paketů se měří k výpočtu ztráty a latence každé cesty. Tato data se agreguje v intervalu cyklického dotazování a přes všechny cesty, aby se získaly agregované hodnoty ztráty a latence pro danou kombinaci IP adres pro konkrétní interval dotazování.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>S jakou frekvencí zdrojový Agent odesílá pakety do cíle pro monitorování?
Pro sledování výkonu a možnosti monitorování ExpressRoute zdroj odesílá pakety každých 5 sekund a zaznamenává měření sítě. Tato data jsou agregována v průběhu 3 minut intervalu dotazování pro výpočet průměrné hodnoty a hodnot špičky ztráty a latence. V případě schopnosti monitorování připojení služby je četnost odesílání paketů pro měření sítě určena frekvencí zadanou uživatelem pro konkrétní test při konfiguraci testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Kolik paketů bylo odesláno pro monitorování?
Počet paketů odeslaných zdrojovým agentem do cílového umístění při cyklickém dotazování je adaptivní a je určen podle našeho vlastního algoritmu, který se může lišit pro různé topologie sítě. Větší počet síťových cest mezi kombinací IP adresy zdroje a cíle je počet odeslaných paketů. Systém zajišťuje, aby se pokryly všechny cesty mezi touto kombinací IP adresy zdroje a cíle.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Jak NPM zjistí síťovou topologii mezi zdrojem a cílem?
NPM používá proprietární algoritmus založený na traceroute ke zjištění všech cest a segmentů směrování mezi zdrojem a cílem.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Poskytuje NPM informace o směrování a přepínání úrovně 
I když NPM dokáže detekovat všechny možné trasy mezi zdrojovým agentem a cílem, neposkytuje tak přehled o tom, které trase provedly pakety odesílané konkrétními úlohami. Řešení vám může pomáhat identifikovat cesty a podkladové segmenty sítě, které přidávají větší latenci, než jste očekávali.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Proč některé z cest nejsou v pořádku?
Mezi zdrojovou a cílovou IP adresou můžou existovat různé síťové cesty a každá cesta může mít jinou hodnotu ztráty a latence. NPM označí tyto cesty jako chybné (označené červenou barvou), pro které jsou hodnoty ztráty a/nebo latence větší než odpovídající prahová hodnota nastavená v konfiguraci monitorování.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co v mapě topologie sítě značí směrování červenou barvou?
Pokud je směrování červené, znamená to, že je součástí nejméně jedné cesty, která není v pořádku. NPM označí pouze cesty jako chybné, nedělí stav jednotlivých cest. Chcete-li identifikovat komplikované směrování, můžete zobrazit latenci směrování po směrování a oddělit ty přidáním více než očekávané latence.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak funkce lokalizace chyb v nástroji Sledování výkonu funguje?
NPM používá mechanismus pravděpodobnostní pro přiřazení pravděpodobnosti chyby každé síťové cestě, segmentu sítě a směrování sítě v závislosti na počtu nezdravých cest, které jsou součástí. Protože segmenty sítě a směrování se stanou součástí většího počtu špatných cest, zvyšují se pravděpodobnost selhání, která jsou k nim přidružená. Tento algoritmus funguje nejlépe tehdy, když máte spoustu uzlů s NPM agentem, který se navzájem připojuje. tím se zvyšuje počet datových bodů pro výpočet pravděpodobnosti selhání.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak můžu vytvářet upozornění v NPM?
V současné době se vytváření výstrah z uživatelského rozhraní NPM nedaří kvůli známému problému. [Vytvořte prosím výstrahy ručně](../platform/alerts-log.md).

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Jaké jsou výchozí Log Analytics dotazy na výstrahy
Dotaz na sledování výkonu

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Dotaz na monitorování připojení služby

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

ExpressRoute monitorovat dotazy: dotaz na okruhy

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Soukromý partnerský vztah

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Partnerský vztah Microsoftu

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Společný dotaz

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Můžou NPM monitorovat směrovače a servery jako jednotlivá zařízení?
NPM identifikuje jenom IP adresu a název hostitele pro základní síťové směrování (přepínače, směrovače, servery atd.) mezi zdrojovou a cílovou IP adresou. Také identifikuje latenci mezi těmito identifikovanými segmenty směrování. Nemonitoruje jednotlivě tyto segmenty směrování.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Dá se NPM použít k monitorování připojení k síti mezi Azure a AWS?
Yes. Podrobnosti najdete v článku [monitorování Azure, AWS a místních sítí pomocí npm](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) .

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Je využití šířky pásma ExpressRoute příchozí nebo odchozí?
Využití šířky pásma je celkovým počtem příchozích a odchozích šířek pásma. Je vyjádřena v bitech/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Můžeme získat informace o příchozích a odchozích šířkách šířky pásma pro ExpressRoute?
Je možné zachytit příchozí a odchozí hodnoty pro primární i sekundární šířku pásma.

Pro informace o úrovni partnerského vztahu MS použijte níže uvedený dotaz v hledání v protokolu.

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

Informace o úrovni privátního partnerského vztahu najdete níže uvedeným dotazem v hledání v protokolu.

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

Pro informace o úrovni okruhu použijte níže uvedený dotaz v hledání v protokolu

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Které oblasti jsou podporovány pro monitor výkonu NPM?
NPM může monitorovat propojení mezi sítěmi v jakékoli části světa, od pracovního prostoru hostovaného v některé z [podporovaných oblastí](./network-performance-monitor.md#supported-regions) .

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Které oblasti jsou podporovány pro monitorování připojení služby NPM?
NPM může monitorovat připojení ke službám v jakékoli části světa, od pracovního prostoru hostovaného v některé z [podporovaných oblastí](./network-performance-monitor.md#supported-regions) .

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Které oblasti se podporují pro monitor ExpressRoute v NPM?
NPM dokáže monitorovat vaše ExpressRoute okruhy, které jsou umístěné v libovolné oblasti Azure. K připojení do NPM budete potřebovat pracovní prostor Log Analytics, který musí být hostovaný v některé z [podporovaných oblastí](../../expressroute/how-to-npm.md) .

## <a name="troubleshoot"></a>Řešení potíží

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Proč jsou některé z směrování označeny jako neidentifikované v zobrazení síťové topologie?
NPM používá upravenou verzi traceroute ke zjištění topologie ze zdrojového agenta do cíle. Neidentifikovaný skok znamená, že směrování sítě nereagovalo na žádost traceroute zdrojového agenta. Pokud tři po sobě jdoucí síťové segmenty nereagují na traceroute agenta, toto řešení označí nereagující chmel jako neidentifikovaný a nepokusí se zjistit další segmenty směrování.

Segment směrování nemusí reagovat na traceroute v jednom nebo několika následujících scénářích:

* Směrovače byly nakonfigurovány tak, aby neodhalily svoji identitu.
* Síťová zařízení nepovolují ICMP_TTL_EXCEEDED provoz.
* Brána firewall blokuje ICMP_TTL_EXCEEDED reakci ze síťového zařízení.

Když některý z koncových bodů leží v Azure, traceroute zobrazí neidentifikované směrování, protože infrastruktura Azure neodhalí identitu pro traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Zobrazují se upozornění na špatné testy, ale v grafu ztrát a latence se nezobrazuje vysoké hodnoty NPM. Návody zjistit, co není v pořádku?
NPM vygeneruje výstrahu v případě, že koncová latence mezi zdrojem a cílem překračuje prahovou hodnotu pro libovolnou cestu mezi nimi. Některé sítě mají více cest připojujících se ke stejnému zdroji a cíli. NPM vyvolá výstrahu, že některá cesta není v pořádku. Ztráta a latence zaznamenané v grafech jsou průměrnou hodnotou všech cest, takže se nemusí zobrazit přesnou hodnotu jedné cesty. Chcete-li zjistit, kde došlo k porušení prahové hodnoty, vyhledejte ve výstraze sloupec "podtyp". Pokud je problém způsoben cestou, hodnota podtypu bude Síťovácesta (pro testy sledování výkonu), EndpointPath (pro testy monitorování dostupnosti služby) a ExpressRoutePath (pro testy ExpressRotue monitor). 

Vzorový dotaz, který se má najít, je cesta není v pořádku:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Proč můj test zobrazuje stav není v pořádku, ale topologie 
NPM monitoruje koncovou ztrátu, latenci a topologii v různých intervalech. Ztráta a latence se měří každých 5 sekund a agreguje se každé tři minuty (pro monitorování výkonu a sledování tras Express), zatímco topologie se počítá pomocí traceroute každých 10 minut. Například mezi 3:44 a 4:04 může být topologie aktualizována třikrát (3:44, 3:54, 4:04), ale ztráta a latence se aktualizují přibližně na sedm časů (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologie vygenerovaná v 3:54 se vykreslí pro ztrátu a latenci vypočítanou v 3:56, 3:59 a 4:02. Předpokládejme, že jste obdrželi výstrahu, že váš okruh ER nebyl v pořádku při 3:59. Přihlásíte se k NPM a pokusíte se nastavit čas topologie na 3:59. NPM vykreslí topologii vygenerovanou v 3:54. Pro pochopení poslední známé topologie vaší sítě Porovnejte pole TimeProcessed (čas, kdy byla vypočítána ztráta a latence) a TracerouteCompletedTime (čas výpočtu topologie). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Jaký je rozdíl mezi poli E2EMedianLatency a AvgHopLatencyList v tabulce NetworkMonitoring
E2EMedianLatency je latence aktualizována každé tři minuty po agregaci výsledků testů TCP testu TCP, zatímco AvgHopLatencyList se aktualizuje každých 10 minut na základě traceroute. Chcete-li pochopit přesný čas, kdy byl E2EMedianLatency vypočítán, použijte pole TimeProcessed. Chcete-li pochopit přesný čas, kdy se traceroute dokončil a aktualizuje AvgHopLatencyList, použijte pole TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Proč se čísla latence směrování mezi segmenty liší od HopLatencyValues 
HopLatencyValues jsou zdrojem do koncového bodu.
Příklad: směrování-A, B, C. AvgHopLatency – 10, 15, 20. To znamená, že zdroj k latenci = 10, latenci pro zdroj B = 15 a latenci zdrojového kódu C je 20. Uživatelské rozhraní vypočítá latenci směrování A-B jako 5 v topologii.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Toto řešení ukazuje 100% ztrátu, ale mezi zdrojem a cílem je připojení.
K tomu může dojít, pokud hostitelská brána firewall nebo zprostředkující brána firewall (síťová brána firewall nebo Azure NSG) blokuje komunikaci mezi zdrojovým agentem a cílem přes port, který se používá pro monitorování nástrojem NPM (ve výchozím nastavení je port 8084, pokud ho zákazník nezměnil).

* Chcete-li ověřit, zda hostitelská brána firewall neblokuje komunikaci na požadovaném portu, zobrazte stav zdrojových a cílových uzlů z následujícího zobrazení: Network Performance Monitor-> konfigurace > uzlů. 
  Pokud nejsou v pořádku, podívejte se na pokyny a proveďte nápravné opatření. Pokud jsou uzly v pořádku, přejděte ke kroku b. níže.
* Pokud chcete ověřit, jestli zprostředkující síťová brána firewall nebo Azure NSG neblokuje komunikaci na požadovaném portu, použijte nástroj PsPing třetí strany pomocí následujících pokynů:
  * Nástroj psping je k dispozici ke stažení [zde](/sysinternals/downloads/psping) . 
  * Spusťte následující příkaz ze zdrojového uzlu.
    * psping-n 15 \<destination node IPAddress\> :P Ortnumber ve výchozím nastavení npm používá port 8084. V případě, že jste to explicitně změnili pomocí skriptu EnableRules.ps1, zadejte vlastní číslo portu, které používáte). Toto je příkaz k odeslání z Azure Machine do místního počítače.
* Ověřte, zda jsou příkazy if-Tests úspěšné. Pokud ne, znamená to, že zprostředkující síťová brána firewall nebo Azure NSG blokuje provoz na tomto portu.
* Nyní spusťte příkaz z cílového uzlu na IP adresu zdrojového uzlu.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Dojde ke ztrátě z uzlu A na B, ale nikoli z uzlu B do A. Proč?
Jelikož síťové cesty mezi A a B můžou být odlišné od síťových cest mezi B a a, může být zjištěna jiná hodnota pro ztrátu a latenci.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Proč se všechny moje okruhy ExpressRoute a připojení partnerských vztahů neobjevují?
NPM nyní zjišťuje okruhy ExpressRoute a připojení partnerských vztahů ve všech předplatných, ke kterým má uživatel přístup. Vyberte všechna předplatná, kde jsou připojené prostředky Express Route, a povolte monitorování každého zjištěného prostředku. NPM vyhledá objekty připojení při zjišťování privátního partnerského vztahu, zkontrolujte prosím, jestli je virtuální síť přidružená k partnerskému vztahu. NPM nedetekuje okruhy a partnerské vztahy, které jsou v jiném tenantovi, z pracovního prostoru Log Analytics.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkce monitoru ER má diagnostickou zprávu "přenos neprojde žádným okruhem". Co to znamená?

Může se jednat o scénář, ve kterém se nachází v pořádku mezi místními a uzly Azure, ale přenos nepřekračuje okruh ExpressRoute nakonfigurovaný pro monitorování pomocí NPM. 

K tomu může dojít v následujícím případě:

* Okruh ER je mimo provoz.
* Filtry tras jsou nakonfigurovány tak, aby měly přednost jiným trasám (například připojení VPN nebo jinému okruhu ExpressRoute) prostřednictvím zamýšleného okruhu ExpressRoute. 
* Místní a uzly Azure zvolené pro monitorování okruhu ExpressRoute v konfiguraci monitorování, neexistují vzájemně propojeny přes zamýšlený okruh ExpressRoute. Ujistěte se, že jste vybrali správné uzly, které mají vzájemnou konektivitu přes okruh ExpressRoute, který chcete monitorovat.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Proč nástroj ExpressRoute monitor hlásí, že je můj okruh/partnerský vztah ve stavu není v pořádku, pokud je k dispozici a předávání dat.
ExpressRoute monitor porovnává hodnoty výkonu sítě (ztráty, latence a využití šířky pásma) hlášené agenty/službou a hodnotami prahové hodnoty nastavené během konfigurace. U okruhu, pokud je nahlášená využití šířky pásma větší než prahová hodnota nastavená v konfiguraci, je okruh označený jako není v pořádku. U partnerských vztahů, pokud je nahlášená ztráta, latence nebo využití šířky pásma větší než prahová hodnota nastavená v konfiguraci, je partnerský vztah označen jako není v pořádku. NPM nevyužívá metriky ani žádnou jinou formu dat do deicde stavu.

### <a name="why-does-expressroute-monitorbandwidth-utilisation-report-a-value-differrent-from-metrics-bits-inout"></a>Proč služba využití ExpressRoute Monitor'bandwidth nahlásí hodnotu jinou z metriky v/v.
V případě monitorování ExpressRoute je šířka pásma utiliation průměrem příchozí a odchozí šířky pásma za posledních 20 minut, které se vyjadřují v bitech za sekundu. V případě metriky Express Route je bitová/výstupní data za minutu datových bodů. Vnitřně použitá datová sada pro obojí je stejná, ale agregace valies mezi NPM a ER metrikami. Pro podrobné a rychlé výstrahy monitorování a rychlých výstrah doporučujeme nastavit výstrahy přímo na metrikách ER.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Při konfiguraci monitorování okruhu ExpressRoute se uzly Azure nezjišťují.
K tomu může dojít, pokud jsou uzly Azure připojené prostřednictvím Operations Manager. Funkce monitorování ExpressRoute podporuje jenom uzly Azure, které jsou připojené jako přímí agenti.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Na portálu OMS nemůžu zjistit ExpressRoute okruhy
I když je možné NPM použít jak z Azure Portal, tak i z portálu OMS, zjišťování okruhů ve schopnosti monitorování ExpressRoute funguje jenom přes Azure Portal. Jakmile budou okruhy zjištěny prostřednictvím Azure Portal, můžete použít možnost v jednom ze dvou portálů. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>V možnosti monitorování připojení služby se doba odezvy služby, ztráta sítě a latence zobrazují jako NA.
K tomu může dojít, pokud je splněna jedna nebo více:

* Služba je mimo provoz.
* Uzel, který se používá pro kontrolu připojení k síti ke službě, je mimo provoz.
* Cíl zadaný v konfiguraci testu není správný.
* Uzel nemá žádné síťové připojení.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>V rámci možnosti monitorování připojení služby se zobrazuje platná doba odezvy služby, ale také latence sítě a latence.
 K tomu může dojít, pokud je splněna jedna nebo více:

* Pokud uzel použitý k ověření síťového připojení ke službě je klientský počítač Windows, znamená to, že cílová služba blokuje žádosti ICMP, nebo síťová brána firewall blokuje žádosti ICMP, které pocházejí z uzlu.
* Zaškrtávací políčko provádět měření sítě je v konfiguraci testu prázdné.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>V závislosti na možnosti monitorování připojení služby je doba odezvy služby NA hodnotu, ale i latence sítě a latence jsou platné.
K tomu může dojít, pokud cílová služba není webová aplikace, ale test je nakonfigurován jako webový test. Upravte konfiguraci testu a jako místo webu vyberte typ testu síť.

## <a name="miscellaneous"></a>Různé

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Existuje dopad na výkon uzlu, který se používá k monitorování?
Proces NPM je nakonfigurován tak, aby se zastavil, pokud využívá více než 5% prostředků hostitelského procesoru. K tomu je potřeba zajistit, aby se uzly používaly pro běžné úlohy, aniž by to mělo vliv na výkon.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Upravuje NPM pravidla brány firewall pro monitorování?
NPM na uzlech EnableRules.ps1, na kterých je spuštěný skript PowerShellu, vytvoří jenom místní pravidlo brány Windows Firewall, které agentům umožní vytvářet připojení TCP mezi sebou na zadaném portu. Řešení nemění pravidla brány firewall sítě ani skupiny zabezpečení sítě (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak můžu ověřit stav uzlů používaných k monitorování?
Stav uzlů používaných k monitorování můžete zobrazit z následujícího zobrazení: Network Performance Monitor-> konfigurace > uzlů. Pokud uzel není v pořádku, můžete zobrazit podrobnosti o chybě a provést navrhovanou akci.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Může NPM zprávy o latenci v mikrosekundách?
NPM zaokrouhlí čísla latence v uživatelském rozhraní a v milisekundách. Stejná data se ukládají s větší členitosti (někdy až na čtyři desetinná místa).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o Network Performance Monitor odkazem na [řešení Network Performance Monitor v Azure](./network-performance-monitor.md).

