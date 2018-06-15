---
title: Síťová řešení pro sledování výkonu v Azure Log Analytics | Microsoft Docs
description: Ke sledování začátku do konce připojení a výkon mezi pobočkami a Azure, přes Azure ExpressRoute použijte možnost ExpressRoute správce v nástroji Sledování výkonu sítě.
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
ms.openlocfilehash: 9610a8b37ead976cfdfa2fed81d4d3932055ddcc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30237779"
---
# <a name="expressroute-manager"></a>Správce ExpressRoute

Můžete použít funkce Azure ExpressRoute Manager v [sledování výkonu sítě](log-analytics-network-performance-monitor.md) ke sledování začátku do konce připojení a výkonu mezi pobočkami a Azure, přes Azure ExpressRoute. Jsou klíčové výhody: 

- Automatickou detekcí ExpressRoute okruhy spojené s vaším předplatným.
- Sledování využití šířky pásma, ztráta a latence v okruhu, vztahy a úroveň Azure Virtual Network pro ExpressRoute.
- Zjišťování topologie sítě vaší okruhy ExpressRoute.

![Monitorování ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfigurace 
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení pro sledování výkonu sítě](log-analytics-network-performance-monitor.md) a vyberte **konfigurace**.

### <a name="configure-network-security-group-rules"></a>Konfigurace pravidel skupiny zabezpečení sítě 
Pro servery v Azure, které se používají pro monitorování pomocí sledování výkonu sítě nakonfigurujte pravidla skupiny (NSG) zabezpečení sítě tak, aby umožnila přenosy TCP na portu, používá nástroj Sledování výkonu sítě pro syntetické transakce. Výchozí port je 8084. Tato konfigurace umožňuje nainstalován na virtuálních počítačích Azure ke komunikaci s místní agent Operations Management Suite monitoring agent. 

Další informace o NSG najdete v tématu [skupin zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Předtím, než budete pokračovat v tomto kroku, nainstalujte agenta na místní server a Azure server agenta a spusťte skript prostředí PowerShell EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Zjistit připojení partnerského vztahu ExpressRoute 
 
1. Vyberte **partnerských vztahů ExpressRoute** zobrazení.
2. Vyberte **zjistit nyní** ke zjišťování všech ExpressRoute soukromé partnerské vztahy, které jsou připojené k virtuálním sítím v rámci předplatného Azure spojena se tento pracovní prostor analýzy protokolů Azure.

    >[!NOTE]
    > Řešení aktuálně zjišťuje pouze soukromé partnerské vztahy ExpressRoute. 

    >[!NOTE]
    > Pouze zjištění soukromé partnerské vztahy připojené k virtuálním sítím, které jsou přidružené k předplatnému spojena se tento pracovní prostor analýzy protokolů. Pokud ExpressRoute je připojen k virtuálním sítím mimo předplatné propojené do tohoto pracovního prostoru, vytvořte pracovní prostor analýzy protokolů v těchto předplatných. Pak použijte nástroj Sledování výkonu sítě k monitorování těchto partnerských vztahů. 

    ![Konfigurace ExpressRoute monitoru](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Po dokončení zjišťování zjištěných soukromého partnerského vztahu připojení jsou uvedeny v tabulce. Monitorování pro tyto partnerské vztahy zpočátku je v zakázaném stavu. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Povolit monitorování připojení partnerského vztahu ExpressRoute 

1. Vyberte soukromého partnerského vztahu připojení, které chcete monitorovat.
2. V podokně na pravé straně, vyberte **sledovat tento partnerský vztah** zaškrtávací políčko. 
3. Pokud máte v úmyslu vytvořit události stavu pro toto připojení, vyberte **povolit sledování stavu pro tento partnerský vztah**. 
4. Vyberte sledování podmínek. Vlastní prahové hodnoty pro generování událostí stavu můžete nastavit tak, že zadáte prahové hodnoty. Vždy, když je hodnota stavu překročí jeho vybraná prahová hodnota pro připojení k partnerského vztahu, je generována událost stavu. 
5. Vyberte **přidat agenty** zvolit sledování agentů máte v úmyslu použít pro sledování tohoto partnerského vztahu připojení. Ujistěte se, abyste přidali agentů na obou koncích připojení. Musíte mít alespoň jeden agent ve virtuální síti připojené k tohoto partnerského vztahu. Je také nutné mít alespoň jeden místní agent připojené k tohoto partnerského vztahu. 
6. Vyberte **Uložit** konfiguraci uložíte. 

   ![Monitorování konfigurace ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Po povolení pravidla a vyberte hodnoty a agenty, počkejte 30 až 60 minut pro hodnoty k naplnění a **ExpressRoute monitorování** dlaždice se objeví. Až uvidíte monitorování dlaždice, okruhy ExpressRoute a připojení prostředky jsou nyní monitorován pomocí sledování výkonu sítě. 

>[!NOTE]
> Tato funkce funguje spolehlivě na pracovní prostory, které jste upgradovali na nový dotazovací jazyk.

## <a name="walkthrough"></a>Názorný postup 

Řídicí panel monitorování výkonu sítě zobrazuje přehled stavu okruhy ExpressRoute a připojení partnerského vztahu. 

![Řídicí panel monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Okruhy seznamu 

Chcete-li zobrazit seznam všech monitorovaných okruhy ExpressRoute, vyberte dlaždici okruhů ExpressRoute. Můžete vybrat okruhu a zobrazit její stav, trend grafy pro ztráta paketů, využití šířky pásma a latence. Grafy, jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafy. Přetáhněte myši nad oblast grafu přiblížení a zobrazit podrobné datových bodů. 

![Seznam okruhy ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy ztrátě, latence a propustnosti 

Grafy využití, latence a ztráta šířky pásma, jsou interaktivní. Na všechny části tyto grafy se můžete přiblížit pomocí ovládacích prvků myši. Taky uvidíte šířku pásma, latence a ztráta dat pro další intervalech. V levém horním pod **akce** tlačítko, vyberte **datum a čas**. 

![Latence ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Seznam partnerských vztahů 

Chcete-li vyvolat seznam všech připojení k virtuálním sítím přes soukromého partnerského vztahu, vyberte **soukromé partnerské vztahy** dlaždici na řídicím panelu. Tady můžete vybrat virtuální síťové připojení a zobrazit její stav, trend grafy pro ztráta paketů, využití šířky pásma a latence. 

![Partnerské vztahy ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Okruh topologie 

Chcete-li zobrazit okruh topologie, vyberte **topologie** dlaždici. Tím přejdete na zobrazení topologie vybrané okruhu nebo partnerský vztah. Diagram topologie poskytuje latence pro každý segment v síti, a každé směrování vrstvy 3 je reprezentována uzlu diagramu. Výběr směrování, zobrazí se další podrobnosti o směrování. Pokud chcete zvýšit úroveň viditelnosti zahrnout místní směrování, nastavte polohu jezdce pod **FILTRY**. Přechod jezdce doleva nebo doprava zvyšuje nebo snižuje počet skoků v grafu topologie. Latence v každém segmentu je viditelná, což umožňuje rychlejší izolace s vysokou latencí segmentů ve vaší síti. 

![Topologie ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Zobrazení podrobné topologie okruhu 

Toto zobrazení uvádí připojení virtuální sítě. 

![Připojení ExpressRoute virtuální sítě](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostika 

Sledování výkonu sítě umožňuje diagnostikovat problémy s připojením k několika okruh. Některé problémy, jsou zde uvedeny. 

**Okruh je mimo provoz.** Sledování výkonu sítě vás upozorní, jakmile dojde ke ztrátě připojení mezi místními prostředky a virtuální sítě Azure. Toto oznámení umožňuje udělat proaktivní dřív, než se zobrazí uživatelské eskalací a zkrátit dobu prostojů.

![Okruh ExpressRoute je mimo provoz](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Provoz v není určený okruh.** Sledování výkonu sítě vás upozorní, vždy, když není provoz předávaných mezi určený okruh ExpressRoute. Tento problém může dojít, pokud je okruh dolů a provoz je předávaných mezi zálohování trasy. Je také může dojít, pokud nastane problém s směrování. Tyto informace pomáhají aktivně spravovat problémy s konfigurací v vaše zásady směrování a ujistěte se, že se používá trasy, která nejvíce optimální a zabezpečení. 

 

**Přenosy dat není předávaných mezi primární okruh.** Sledování výkonu sítě vás upozorní, když je provoz předávaných mezi sekundární okruh ExpressRoute. I když v tomto případě, proaktivně nedochází žádné problémy s připojením k řešení potíží s primární okruh umožňuje vám lépe připravené. 

 
![Tok přenosů ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Snížení výkonu z důvodu využití ve špičce.** Mohou korelovat trend využití šířky pásma s trendu latence a určete, jestli úloha Azure snížení je z důvodu ve špičce ve využití šířky pásma, nebo ne. Potom můžete provést akci odpovídajícím způsobem.

![Využití šířky pásma ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Další postup
[V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné sítě záznamů dat výkonu.
