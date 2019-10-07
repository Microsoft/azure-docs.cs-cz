---
title: Network Watcher Azure | Microsoft Docs
description: Přečtěte si o možnostech monitorování, diagnostiky, metriky a protokolování v Azure Network Watcher pro prostředky ve virtuální síti.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
Customer intent: As someone with basic Azure network experience, I want to understand how Azure Network Watcher can help me resolve some of the network-related problems I've encountered and provide insight into how I use Azure networking.
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: e6ff3e6798fadca89455790efa28891c02e9e459
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996958"
---
# <a name="what-is-azure-network-watcher"></a>Co je Azure Network Watcher?

Azure Network Watcher poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network. Network Watcher je navržený tak, aby sledoval a opravil stav sítě IaaS (infrastruktura jako služba), které zahrnují Virtual Machines, virtuální sítě, aplikační brány, nástroje pro vyrovnávání zatížení atd. Poznámka: není určena pro a nebude fungovat pro PaaS monitoring nebo Web Analytics. 

## <a name="monitoring"></a>Sledovaný

### <a name = "connection-monitor"></a>Monitorování komunikace mezi virtuálním počítačem a koncovým bodem

Koncovými body může být jiný virtuální počítač (VM), plně kvalifikovaný název domény (FQDN), identifikátor URI (Uniform Resource Identifier) nebo adresa IPv4. Funkce *monitorování připojení* monitoruje komunikaci v pravidelných intervalech a informuje o změnách dostupnosti, latence a síťové topologie mezi virtuálním počítačem a koncovým bodem. Můžete mít například virtuální počítač s webovým serverem, který komunikuje s virtuálním počítačem s databázovým serverem. Někdo ve vaší organizaci může být od vás neznámý, ale použít vlastní trasu nebo pravidlo zabezpečení sítě na virtuálním počítači nebo virtuálním počítači databázového serveru nebo podsíti.

Pokud dojde k nedosažitelnému koncovému bodu, bude řešení potíží s připojením informovat o příčině. Možnou příčinou jsou potíže s překladem názvů DNS, procesor, paměť nebo brána firewall v operačním systému virtuálního počítače nebo typ směrování vlastní trasy nebo pravidlo zabezpečení pro virtuální počítač nebo podsíť odchozího připojení. Přečtěte si další informace o [pravidlech zabezpečení](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#security-rules) a [typech směrování](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) směrování v Azure.

Monitor připojení také poskytuje minimální, průměrnou a maximální latenci zjištěnou v průběhu času. Po zjištění latence připojení můžete zjistit, že je možné snížit latenci přesunutím prostředků Azure do různých oblastí Azure. Přečtěte si další informace o určování [relativních latencí mezi oblastmi Azure a poskytovateli internetových služeb](#determine-relative-latencies-between-azure-regions-and-internet-service-providers) a o tom, jak monitorovat komunikaci mezi virtuálním počítačem a koncovým bodem pomocí [monitorování připojení](connection-monitor.md). Pokud místo toho chcete otestovat připojení v určitém časovém okamžiku, ale nechcete monitorovat připojení v průběhu času, jako je například monitorování připojení, použijte možnost [řešení potíží s připojením](#connection-troubleshoot) .

Sledování výkonu sítě je cloudové řešení monitorování hybridní sítě, které pomáhá monitorovat výkon sítě mezi různými body v infrastruktuře sítě. Pomůže vám taky monitorovat síťové připojení ke koncovým bodům služby a aplikace a monitorovat výkon Azure ExpressRoute. Nástroj Sledování výkonu sítě detekuje problémy se sítí, jako jsou blackholing provozu, chyby směrování a problémy, které nedokáže detekovat konvenční metody monitorování sítě. Řešení vygeneruje výstrahy a upozorní vás, když dojde k porušení prahové hodnoty pro síťové propojení. Zároveň zajišťuje včasné zjištění problémů s výkonem sítě a lokalizaci zdroje problému do konkrétního segmentu sítě nebo zařízení. Přečtěte si další informace o [nástroji Sledování výkonu sítě](../azure-monitor/insights/network-performance-monitor.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

### <a name="view-resources-in-a-virtual-network-and-their-relationships"></a>Zobrazení prostředků ve virtuální síti a jejich vztazích

Vzhledem k tomu, že se prostředky přidávají do virtuální sítě, může být obtížné pochopit, jaké prostředky jsou ve virtuální síti, a jak vzájemně souvisí. Schopnost *topologie* umožňuje generovat vizuální diagram prostředků ve virtuální síti a vztahy mezi prostředky. Následující obrázek znázorňuje příklad diagramu topologie pro virtuální síť, která má tři podsítě, dva virtuální počítače, síťová rozhraní, veřejné IP adresy, skupiny zabezpečení sítě, směrovací tabulky a vztahy mezi prostředky:

![Zobrazení topologie](./media/network-watcher-monitoring-overview/topology.png)

Upravitelnou verzi obrázku můžete stáhnout ve formátu SVG. Přečtěte si další informace o [zobrazení topologie](view-network-topology.md).

## <a name="diagnostics"></a>Diagnostika

### <a name="diagnose-network-traffic-filtering-problems-to-or-from-a-vm"></a>Diagnostika problémů s filtrováním síťového provozu do nebo z virtuálního počítače

Když nasadíte virtuální počítač, Azure použije několik výchozích pravidel zabezpečení pro virtuální počítač, který povoluje nebo zamítá provoz do nebo z virtuálního počítače. Můžete přepsat výchozí pravidla Azure nebo vytvořit další pravidla. V některých případech se může stát, že virtuální počítač nebude moci komunikovat s jinými prostředky z důvodu pravidla zabezpečení. Schopnost *ověřit tok protokolu IP* umožňuje zadat zdrojovou a cílovou IPv4 adresu, port, protokol (TCP nebo UDP) a směr přenosu (příchozí nebo odchozí). Ověření toku IP pak otestuje komunikaci a informuje vás, pokud je připojení úspěšné nebo neúspěšné. Pokud se připojení nepovede, ověření toku protokolu IP vám oznámí, které pravidlo zabezpečení povoluje nebo zakázalo komunikaci, abyste mohli problém vyřešit. Další informace o ověřování toků IP adres najdete v kurzu [Diagnostika problému s filtrováním síťového provozu virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md) .

### <a name="diagnose-network-routing-problems-from-a-vm"></a>Diagnostika problémů se síťovým směrováním z virtuálního počítače

Když vytváříte virtuální síť, Azure vytvoří několik výchozích odchozích tras pro síťový provoz. Odchozí provoz ze všech prostředků, jako jsou virtuální počítače nasazené ve virtuální síti, se směruje na základě výchozích tras Azure. Můžete přepsat výchozí trasy Azure nebo vytvořit další trasy. Může se stát, že virtuální počítač už nebude moci komunikovat s jinými prostředky z důvodu konkrétní trasy. Schopnost *dalšího směrování* umožňuje zadat zdrojovou a cílovou IPv4 adresu. Další směrování pak otestuje komunikaci a informuje vás o tom, jaký typ dalšího segmentu směrování se používá ke směrování provozu. Pak můžete odebrat, změnit nebo přidat trasu a vyřešit tak problém směrování. Přečtěte si další informace o schopnostech [dalšího směrování](diagnose-vm-network-routing-problem.md) .

### <a name="connection-troubleshoot"></a>Diagnostika odchozích připojení z virtuálního počítače

Funkce *řešení potíží s připojením* umožňuje testovat připojení mezi virtuálním počítačem a jiným virtuálním počítačem, plně kvalifikovaným názvem domény, identifikátorem URI nebo adresou IPv4. Test vrátí podobné informace, které byly vráceny při použití možnosti [monitorování připojení](#connection-monitor) , ale testuje připojení v určitém časovém okamžiku, nikoli v průběhu času, protože monitorování připojení. Přečtěte si další informace o řešení potíží s připojením pomocí [připojení – řešení potíží](network-watcher-connectivity-overview.md).

### <a name="capture-packets-to-and-from-a-vm"></a>Zachytávání paketů do a z virtuálního počítače

Možnosti rozšířeného filtrování a jemně vyladěné ovládací prvky, jako je možnost nastavení omezení času a velikosti, poskytují univerzálnost. Záznam může být uložený v Azure Storage, na disku virtuálního počítače nebo v obou. Potom můžete analyzovat zachytávací soubor pomocí několika standardních nástrojů pro analýzu síťového zachycení. Přečtěte si další informace o [zachytávání paketů](network-watcher-packet-capture-overview.md).

### <a name="diagnose-problems-with-an-azure-virtual-network-gateway-and-connections"></a>Diagnostikujte problémy s bránou virtuální sítě Azure a připojením

Brány virtuální sítě poskytují konektivitu mezi místními prostředky a virtuálními sítěmi Azure. Monitorování bran a jejich připojení je důležité pro zajištění, že komunikace není přerušena. Funkce *diagnostiky sítě VPN* nabízí možnost diagnostikovat brány a připojení. Diagnostika sítě VPN diagnostikuje stav brány nebo připojení brány a informuje o tom, jestli jsou k dispozici brány a připojení brány. Pokud brána nebo připojení není k dispozici, Diagnostika sítě VPN vás upozorní, abyste mohli problém vyřešit. Další informace o diagnostice sítě VPN najdete v kurzu [Diagnostika potíží s komunikací mezi sítěmi](diagnose-communication-problem-between-networks.md) .

### <a name="determine-relative-latencies-between-azure-regions-and-internet-service-providers"></a>Určení relativních latencí mezi oblastmi Azure a poskytovateli internetových služeb

Pro informace o latenci mezi oblastmi Azure a mezi poskytovateli internetových služeb můžete zadávat dotazy Network Watcher. Pokud znáte latence mezi oblastmi Azure a mezi poskytovateli internetových služeb, můžete nasadit prostředky Azure pro optimalizaci doby odezvy sítě. Přečtěte si další informace o [relativních latencích](view-relative-latencies.md).

### <a name="view-security-rules-for-a-network-interface"></a>Zobrazení pravidel zabezpečení pro síťové rozhraní

Platná pravidla zabezpečení pro síťové rozhraní jsou kombinací všech pravidel zabezpečení použitých pro síťové rozhraní a podsítě, ve které se síťové rozhraní nachází.  Možnost *zobrazení skupiny zabezpečení* zobrazuje všechna pravidla zabezpečení použitá pro síťové rozhraní, podsíť, ve které je síťové rozhraní, a agregaci obou. S porozuměním pravidlům, která se aplikují na síťové rozhraní, můžete přidat, odebrat nebo změnit pravidla, pokud povolíte nebo odepřete přenosy, které chcete změnit. Přečtěte si další informace o [zobrazení skupiny zabezpečení](network-watcher-security-group-view-overview.md).

## <a name="metrics"></a>Metriky

Existují [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#azure-resource-manager-virtual-networking-limits) počtu síťových prostředků, které můžete vytvořit v rámci předplatného Azure a oblasti. Pokud tato omezení splňujete, nemůžete v rámci předplatného nebo oblasti vytvořit další prostředky. Funkce *limitu počtu síťových předplatných* poskytuje souhrn toho, kolik všech síťových prostředků jste nasadili v rámci předplatného a oblasti a kolik je pro tento prostředek limit. Následující obrázek ukazuje částečný výstup pro síťové prostředky nasazené v Východní USA oblasti pro příklad předplatného:

![Omezení předplatného](./media/network-watcher-monitoring-overview/subscription-limit.png)

Tyto informace jsou užitečné při plánování budoucích nasazení prostředků.

## <a name="logs"></a>Protokolování

### <a name="analyze-traffic-to-or-from-a-network-security-group"></a>Analýza provozu do nebo ze skupiny zabezpečení sítě

Skupiny zabezpečení sítě (NSG) povolují nebo odmítají příchozí nebo odchozí přenosy na síťové rozhraní ve virtuálním počítači. Možnost *protokolu toku NSG* vám umožňuje protokolovat zdrojovou a cílovou IP adresu, port, protokol a to, jestli byl provoz povolený nebo zakázaný NSG. Protokoly můžete analyzovat pomocí nejrůznějších nástrojů, jako je PowerBI a funkce *Analýza provozu* . Analýza provozu poskytuje bohatou vizualizaci dat zapsaných do protokolů toku NSG. Následující obrázek ukazuje některé z informací a vizualizací, které Analýza provozu prezentuje z dat protokolu toku NSG:

![Analýza provozu](./media/network-watcher-monitoring-overview/traffic-analytics.png)

Přečtěte si další informace o protokolech toku NSG, a to tak, že vyplníte kurz [síťového provozu do a z virtuálního počítače](network-watcher-nsg-flow-logging-portal.md) a implementujete [analýzu provozu](traffic-analytics.md).

### <a name="view-diagnostic-logs-for-network-resources"></a>Zobrazení diagnostických protokolů pro síťové prostředky

Můžete povolit protokolování diagnostiky pro síťové prostředky Azure, jako jsou skupiny zabezpečení sítě, veřejné IP adresy, nástroje pro vyrovnávání zatížení, brány virtuální sítě a aplikační brány. Funkce *diagnostických protokolů* poskytuje jedno rozhraní pro povolení a zakázání diagnostických protokolů síťových prostředků pro všechny stávající síťové prostředky, které generují diagnostický protokol. Diagnostické protokoly můžete zobrazit pomocí nástrojů, jako jsou protokoly Microsoft Power BI a Azure Monitor. Další informace o analýze protokolů diagnostiky sítě Azure najdete [v tématu síťová řešení Azure v protokolu Azure monitor](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="network-watcher-automatic-enablement"></a>Network Watcher automatické povolení
Když ve svém předplatném vytvoříte nebo aktualizujete virtuální síť, Network Watcher se automaticky povolí v oblasti vašeho Virtual Network. Pro automatické povolování Network Watcher neexistuje žádný vliv na vaše prostředky ani související poplatky. Další informace najdete v tématu [Network Watcher Create](network-watcher-create.md).

## <a name="next-steps"></a>Další kroky

Nyní máte přehled o službě Azure Network Watcher. Pokud chcete začít používat Network Watcher, Diagnostikujte běžný problém s komunikací do a z virtuálního počítače pomocí ověření toku IP. Informace o tom, jak se dozvědět, najdete v článku [Diagnostika problému s filtrováním síťového provozu virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md) .
