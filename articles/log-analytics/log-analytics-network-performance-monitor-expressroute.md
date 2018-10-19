---
title: Network Performance Monitor řešení v Azure Log Analytics | Dokumentace Microsoftu
description: Pomocí funkce monitorování ExpressRoute v Network Performance Monitor monitorovat začátku do konce připojení a výkon mezi vašimi pobočkami a Azure, přes Azure ExpressRoute.
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
ms.openlocfilehash: 73978609d018eb43ab8031dc6e8261861e1ee3bf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402526"
---
# <a name="expressroute-monitor"></a>ExpressRoute Monitor

Můžete použít funkce monitorování Azure ExpressRoute v [Network Performance Monitor](log-analytics-network-performance-monitor.md) k monitorování připojení začátku do konce a výkon mezi vašimi pobočkami a Azure, přes Azure ExpressRoute. Jsou klíčové výhody: 

- Okruhy ExpressRoute automatickou detekcí spojených s vaším předplatným.
- Sledování využití šířky pásma, ztrát a latence v okruhu, partnerského vztahu a úrovně Azure Virtual Network pro ExpressRoute.
- Zjišťování síťové topologie okruhů ExpressRoute.

![ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Konfigurace 
Chcete-li spustit nástroj konfigurace pro sledování výkonu sítě, otevřete [řešení Network Performance Monitor](log-analytics-network-performance-monitor.md) a vyberte **konfigurovat**.

### <a name="configure-network-security-group-rules"></a>Konfigurace pravidla skupiny zabezpečení sítě 
Pro servery v Azure, které se používají pro monitorování prostřednictvím služby Network Performance Monitor nakonfigurujte skupiny (NSG) pravidla zabezpečení sítě umožňující provoz TCP na portu, používaný Network Performance Monitor pro syntetické transakce. Výchozí port je 8084. Tato konfigurace umožňuje agenta Log Analytics, který je nainstalován na virtuálních počítačích Azure ke komunikaci s místní agent monitorování. 

Další informace o skupinách Nsg najdete v tématu [skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Než budete pokračovat s tímto krokem, nainstalujte místního serveru agenta a agenta Azure server a spusťte Powershellový skript EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Zjistit partnerské vztahy ExpressRoute 
 
1. Vyberte **partnerské vztahy ExpressRoute** zobrazení.
2. Vyberte **zjistit nyní** ke zjišťování všech ExpressRoute privátní partnerské vztahy, které jsou připojené k virtuálním sítím v předplatném Azure, propojené s tímto pracovním prostorem Azure Log Analytics.

    >[!NOTE]
    > Řešení aktuálně zjišťuje pouze privátní partnerské vztahy ExpressRoute. 

    >[!NOTE]
    > Zjištění pouze privátní partnerské vztahy připojené k virtuálním sítím přidružené předplatné propojené s tímto pracovním prostorem Log Analytics. Pokud ExpressRoute je připojená k virtuálním sítím mimo předplatné propojené s Tento pracovní prostor, vytvořte pracovní prostor Log Analytics v těchto předplatných. Pak pomocí Network Performance Monitor monitorovat tyto partnerské vztahy. 

    ![Konfigurace ExpressRoute monitoru](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Po dokončení zjišťování zjištěné privátní připojení s partnerským vztahem jsou uvedeny v tabulce. Monitorování pro tyto partnerské vztahy zpočátku je v zakázaném stavu. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Povolit monitorování partnerské vztahy ExpressRoute 

1. Vyberte privátní připojení s partnerským vztahem, kterou chcete monitorovat.
2. V podokně na pravé straně vyberte **monitorovat tento partnerský vztah** zaškrtávací políčko. 
3. Pokud máte v úmyslu vytvořit stavu události pro toto připojení, vyberte **povolit monitorování stavu pro tento partnerský vztah**. 
4. Vyberte sledování podmínek. Můžete nastavit vlastní prahové hodnoty pro generování události stavu tak, že zadáte prahové hodnoty. Pokaždé, když se hodnota podmínka překročí jeho zvolená prahová hodnota pro připojení s partnerským vztahem, vygeneruje událost stavu. 
5. Vyberte **přidat agenty** zvolit agentů monitorování, které chcete využít pro monitorování tohoto připojení s partnerským vztahem. Ujistěte se, že přidáte agenty na obou koncích připojení. Budete potřebovat alespoň jednoho agenta do virtuální sítě připojené k tohoto partnerského vztahu. Budete také potřebovat aspoň jeden místní agent je připojený k tohoto partnerského vztahu. 
6. Vyberte **Uložit** uložte konfiguraci. 

   ![Konfigurace monitorování ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Po povolení pravidla a vyberte hodnoty a agenty, Počkejte přibližně 30 – 60 minut, než se hodnoty, které mají naplnit a **monitorování ExpressRoute** dlaždic, které se zobrazí. Když se zobrazí dlaždice monitorování, služba Network Performance Monitor monitorovat nyní okruhy ExpressRoute a připojení prostředků. 

>[!NOTE]
> Tato funkce funguje spolehlivě na pracovní prostory, které se upgradovaly na nový dotazovací jazyk.

## <a name="walkthrough"></a>Názorný postup 

Network Performance Monitor řídicího panelu ukazuje přehled stavu okruhů ExpressRoute a připojení s partnerským vztahem. 

![Řídicí panel monitorování výkonu sítě](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Seznam okruhy 

Pokud chcete zobrazit seznam všech monitorované okruhy ExpressRoute, vyberte dlaždici okruhů ExpressRoute. Můžete vybrat okruh a zobrazit její stav, grafy trendů využití šířky pásma, latence a ztráta paketů. Grafy jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafy. Přetažením myši přes oblast na graf můžete přiblížit a zobrazit podrobné datových bodů. 

![Seznam okruhy ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy ztráty, latence a propustnosti 

Grafy využití, latence a ztráta šířky pásma jsou interaktivní. Do libovolné části tyto grafy můžete přiblížit pomocí myši ovládacích prvků. Také můžete zobrazit na šířku pásma, latence a ztráta dat jiné intervaly. V levém horním rohu pod **akce** tlačítka, vyberte **data a času**. 

![Latence ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Seznam partnerských vztahů 

Zobrazíte seznam všech připojení k virtuálním sítím přes privátní partnerský vztah, vyberte **privátní partnerské vztahy** dlaždici na řídicím panelu. Tady můžete vybrat virtuální síťové připojení a zobrazit její stav, grafy trendů využití šířky pásma, latence a ztráta paketů. 

![Partnerské vztahy ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologie okruh 

Chcete-li zobrazit topologie okruhů, vyberte **topologie** dlaždici. Tím přejdete na zobrazení topologie zvolené okruhu nebo partnerského vztahu. Diagram topologie poskytuje latence pro každý segment v síti a každého směrování vrstvy 3 je reprezentována uzlu diagramu. Výběr hop zobrazí další podrobnosti o směrování. Pokud chcete zvýšit úroveň viditelnosti zahrnout segmenty směrování místní, přesuňte posuvník v části **FILTRY**. Přesunutí posuvník doleva nebo doprava zvyšuje nebo snižuje počet segmentů směrování v grafu topologie. Latence v každém segmentu je viditelná, který umožňuje rychlejší izolace vysokou latencí segmentů ve vaší síti. 

![Topologie ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Zobrazení topologie podrobné okruhu 

Toto zobrazení uvádí připojení virtuální sítě. 

![Připojení virtuální sítě ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostika 

Network Performance Monitor pomáhá diagnostikovat problémy s připojením několik okruhu. Některé problémy, jsou zde uvedeny. 

**Okruh je mimo provoz.** Network Performance Monitor vás upozorní, jakmile dojde ke ztrátě připojení mezi virtuálními sítěmi Azure a místních prostředků. Toto oznámení vám pomůže přijmout proaktivní opatření předtím, než se zobrazí uživateli eskalací a snížit prostoje.

![Okruh ExpressRoute je mimo provoz](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Provoz není přes zamýšlený okruh.** Network Performance Monitor upozornění vždy, když neprobíhá provoz přes zamýšlený okruh ExpressRoute. Tento problém může dojít, pokud je okruh je mimo provoz a provoz se přenášejí prostřednictvím zálohování trasy. Je také může dojít, pokud nastane problém s směrování. Tyto informace pomáhají aktivně spravovat problémy s konfigurací v zásadách pro směrování a ujistěte se, že se používá směrování nejvíce optimální a zabezpečené. 

 

**Provoz není primární okruh.** Network Performance Monitor vás upozorní, když provoz se přenášejí prostřednictvím sekundární okruh ExpressRoute. I když nebude zaznamenáte jakékoli problémy s připojením v tomto případě, proaktivně řešení potíží s primární okruh vám umožňuje lépe připravený. 

 
![Tok provozu ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Snížení kvůli využití ve špičce.** Můžete porovnat trend využití šířky pásma s trend latence a zjistěte, jestli snížení sady funkcí Azure je z důvodu špičky ve využití šířky pásma, nebo ne. Potom můžete provést akci odpovídajícím způsobem.

![Využití šířky pásma ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Další postup
[Hledání protokolů](log-analytics-log-searches.md) zobrazíte podrobné sítě výkonu datových záznamů.
