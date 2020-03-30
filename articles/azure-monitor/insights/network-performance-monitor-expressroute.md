---
title: Řešení sledování výkonu sítě v Azure Log Analytics | Dokumenty společnosti Microsoft
description: Pomocí funkce ExpressRoute Monitor v programu Sledování výkonu sítě můžete monitorovat připojení a výkon mezi koncovými soubory mezi pobočkami a Azure přes Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660746"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

Funkci Azure ExpressRoute Monitor můžete použít v [programu Sledování výkonu sítě](network-performance-monitor.md) ke sledování připojení a výkonu mezi koncovými soubory mezi pobočkami a Azure přes Azure ExpressRoute. Hlavní výhody jsou: 

- Automatická detekce obvodů ExpressRoute přidružených k vašemu předplatnému.
- Sledování využití šířky pásma, ztráty a latence na úrovni okruhu, partnerského vztahu a virtuální sítě Azure pro ExpressRoute.
- Zjišťování síťové topologie okruhů ExpressRoute.

![ExpressRoute Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfigurace 
Chcete-li otevřít konfiguraci nástroje Sledování výkonu sítě, otevřete [řešení Sledování výkonu sítě](network-performance-monitor.md) a vyberte **konfigurovat**.

### <a name="configure-network-security-group-rules"></a>Konfigurace pravidel skupiny zabezpečení sítě 
Pro servery v Azure, které se používají pro monitorování prostřednictvím sledování výkonu sítě, nakonfigurujte pravidla skupiny zabezpečení sítě (NSG) tak, aby přenosy TCP na portu používaném sledováním výkonu sítě pro syntetické transakce povolily provoz TCP na portu používaném sledováním výkonu sítě. Výchozí port je 8084. Tato konfigurace umožňuje agentovi Analýzy protokolů nainstalovanému na virtuálních počítačích Azure komunikovat s místním agentem monitorování. 

Další informace o skupinách zabezpečení sítě naleznete v tématu [Skupiny zabezpečení sítě](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Než budete pokračovat v tomto kroku, nainstalujte místního agenta serveru a agenta serveru Azure a spusťte skript EnableRules.ps1 PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Zjišťování připojení partnerského vztahu ExpressRoute 
 
1. Vyberte zobrazení **Partnerského vztahu ExpressRoute.**
2. Vyberte **Zjistit nyní,** chcete-li zjistit všechny soukromé partnerské partnerské servery ExpressRoute, které jsou připojené k virtuálním sítím v předplatném Azure propojeném s tímto pracovním prostorem Azure Log Analytics.

    >[!NOTE]
    > Řešení aktuálně zjišťuje pouze soukromé partnerské partnerské vztahy ExpressRoute. 

    >[!NOTE]
    > Jsou zjištěny pouze soukromé partnerské partnerské společnosti připojené k virtuálním sítím přidruženým k předplatnému propojenému s tímto pracovním prostorem Analýzy protokolů. Pokud je ExpressRoute připojen k virtuálním sítím mimo předplatné propojené s tímto pracovním prostorem, vytvořte v těchto předplatných pracovní prostor Analýzy protokolů. Potom pomocí sledování výkonu sítě sledujte tyto partnerské partnerské partnerské společnosti. 

    ![Konfigurace monitoru ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Po dokončení zjišťování jsou zjištěná soukromá připojení partnerského vztahu uvedena v tabulce. Monitorování těchto partnerských stran je zpočátku v zakázaném stavu. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Povolení monitorování připojení partnerského vztahu ExpressRoute 

1. Vyberte soukromé připojení partnerského vztahu, které chcete sledovat.
2. V podokně vpravo zaškrtněte políčko **Sledovat tento partnerský vztah.** 
3. Pokud chcete pro toto připojení vytvořit události stavu, vyberte **možnost Povolit monitorování stavu pro tento partnerský vztah**. 
4. Zvolte podmínky monitorování. Vlastní prahové hodnoty pro generování událostí stavu můžete nastavit zadáním prahových hodnot. Vždy, když hodnota podmínky překročí vybranou prahovou hodnotu pro připojení partnerského vztahu, je generována událost stavu. 
5. Vyberte **Přidat agenty** a vyberte agenty monitorování, které chcete použít pro sledování tohoto připojení partnerského vztahu. Ujistěte se, že přidáte agenty na obou koncích připojení. Potřebujete alespoň jednoho agenta ve virtuální síti připojenék tomuto partnerském vztahu. Potřebujete také alespoň jednoho místního agenta připojeného k tomuto partnerského vztahu. 
6. Chcete-li uložit konfiguraci, vyberte **uložit.** 

   ![Konfigurace monitorování ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Po povolení pravidel a výběru hodnot a agentů počkejte 30 až 60 minut, než se hodnoty naplní a zobrazí se dlaždice **Sledování ExpressRoute.** Když se zobrazí dlaždice monitorování, vaše expressroute obvody a prostředky připojení jsou nyní sledovány sledování výkonu sítě. 

>[!NOTE]
> Tato funkce funguje spolehlivě v pracovních prostorech, které upgradovaly na nový dotazovací jazyk.

## <a name="walkthrough"></a>Názorný postup 

Řídicí panel Sledování výkonu sítě zobrazuje přehled stavu okruhů ExpressRoute a připojení partnerského vztahu. 

![Řídicí panel Sledování výkonu sítě](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Seznam obvodů 

Chcete-li zobrazit seznam všech sledovaných okruhů ExpressRoute, vyberte dlaždici okruhů ExpressRoute. Můžete vybrat okruh a zobrazit jeho stav, grafy trendů pro ztrátu paketů, využití šířky pásma a latenci. Grafy jsou interaktivní. Můžete vybrat vlastní časové okno pro vykreslení grafů. Přetažením myši přes oblast grafu zobrazení přiblížíte a zobrazíte jemnozrnné datové body. 

![Seznam okruhů ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy ztráty, latence a propustnosti 

Využití šířky pásma, latence a ztráty grafy jsou interaktivní. Můžete přiblížit libovolnou část těchto grafů pomocí ovládacích prvků myši. Můžete také zobrazit data šířky pásma, latence a ztráty pro jiné intervaly. V levém horním rohu pod tlačítkem **Akce** vyberte **Datum a čas**. 

![Latence ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Seznam partnerských stran 

Chcete-li vyvolat seznam všech připojení k virtuálním sítím přes privátní partnerský vztah, vyberte dlaždici **Soukromé partnerské vztahy** na řídicím panelu. Zde můžete vybrat připojení k virtuální síti a zobrazit její stav, grafy trendů pro ztrátu paketů, využití šířky pásma a latenci. 

![Partnerský vztah ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologie obvodu 

Chcete-li zobrazit topologii obvodu, vyberte dlaždici **Topologie.** Tato akce vás přenese do zobrazení topologie vybraného okruhu nebo partnerského vztahu. Diagram topologie poskytuje latenci pro každý segment v síti a každý směrování vrstvy 3 je reprezentováno uzem diagramu. Výběr směrování odhalí další podrobnosti o směrování. Chcete-li zvýšit úroveň viditelnosti tak, aby zahrnovala místní směrování, přesuňte posuvník pod **filtry**. Přesunutím posuvníku doleva nebo doprava se zvýší nebo sníží počet směrování v grafu topologie. Latence v každém segmentu je viditelná, což umožňuje rychlejší izolaci segmentů s vysokou latencí v síti.

![Topologie ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Detailní pohled na topologickou část obvodu 

Toto zobrazení zobrazuje připojení k virtuální síti. 

![Připojení virtuálnísítě ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostika 

Nástroj Sledování výkonu sítě pomáhá diagnostikovat několik problémů s připojením okruhu. Některé z problémů, které můžete vidět, jsou uvedeny níže.

Můžete vidět kódy oznámení a nastavit upozornění na ně přes **LogAnalytics**. Na stránce **Diagnostika NPM** můžete zobrazit popisy každé aktivované diagnostické zprávy.

| Kód oznámení (protokoly) | Popis |
| --- | --- |
| 5501 | Nelze procházet sekundárním připojením okruhu ExpressRoute. |
| 5502 | Nelze procházet primárním připojením okruhu ExpressRoute. |
| 5503 | Pro předplatné propojené s pracovním prostorem nebyl nalezen žádný okruh. | 
| 5508 | Není schopen určit, zda provoz prochází libovolným okruhem (okruhy) pro trasu |
| 5510 | Provoz neprochází zamýšleným okruhem | 
| 5511 | Provoz neprochází zamýšlenou virtuální sítí. | 

**Obvod neubíhá.** Sledování výkonu sítě vás upozorní, jakmile dojde ke ztrátě připojení mezi místními prostředky a virtuálními sítěmi Azure. Toto oznámení vám pomůže provést proaktivní akci před přijetím eskalace uživatelů a snížit prostoje.

![Okruh ExpressRoute je vypnutý](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Provoz neprotéká zamýšleným okruhem.** Sledování výkonu sítě vás upozorní vždy, když provoz není tok přes zamýšlený okruh ExpressRoute. K tomuto problému může dojít, pokud okruh je dole a provoz protéká záložní trasy. Může se také stát, pokud dojde k problému se směrováním. Tyto informace vám pomohou proaktivně spravovat všechny problémy s konfigurací v zásadách směrování a zajistit, aby byla použita nejoptimálnější a nejbezpečnější trasa. 

 

**Provoz neprotéká primárním okruhem.** Sledování výkonu sítě vás upozorní, když provoz protéká sekundárním okruhem ExpressRoute. I když v tomto případě nebudete mít žádné problémy s připojením, proaktivní řešení problémů s primárním okruhem vám pomůže lépe připravit. 

 
![Tok provozu ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradace v důsledku využití píků.** Můžete korelovat trend využití šířky pásma s trendem latence a zjistit, zda je snížení zatížení Azure způsobeno špičkou využití šířky pásma nebo ne. Pak můžete podniknout kroky odpovídajícím způsobem.

![Využití šířky pásma ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Další kroky
[Hledání protokolů](../../azure-monitor/log-query/log-query-overview.md) pro zobrazení podrobných záznamů dat o výkonu sítě.
