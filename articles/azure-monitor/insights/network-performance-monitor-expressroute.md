---
title: Řešení Network Performance Monitor v Azure Log Analytics | Microsoft Docs
description: Pomocí možnosti monitorování ExpressRoute v Network Performance Monitor můžete monitorovat komplexní připojení a výkon mezi vašimi pobočkami a Azure, a to prostřednictvím Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 477512c0b1de8548e2aa1e2971a5d831e84d2139
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578370"
---
# <a name="expressroute-monitor"></a>Monitorování ExpressRoute

> [!IMPORTANT]
> Od 1. července 2021 nebudete moci přidat nové testy do existujícího pracovního prostoru nebo povolit nový pracovní prostor v Network Performance Monitor. Můžete pokračovat v používání testů vytvořených před 1. července 2021. Pokud chcete minimalizovat přerušení služby na vaše aktuální úlohy, [migrujte testy z Network Performance Monitor na nové monitorování připojení](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) v Azure Network Watcher před 29. února 2024.

Funkce Azure ExpressRoute monitor [Network Performance Monitor](network-performance-monitor.md) v nástroji můžete použít k monitorování komplexního připojení a výkonu mezi firemními pobočkami a Azure, a to prostřednictvím Azure ExpressRoute. Klíčové výhody: 

- Automatickou detekci okruhů ExpressRoute přidružených k vašemu předplatnému.
- Sledování využití šířky pásma, ztrát a latence v okruhu, partnerského vztahu a úrovně Azure Virtual Network pro ExpressRoute.
- Zjišťování síťové topologie okruhů ExpressRoute.

![Monitorování ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfigurace 
Chcete-li otevřít konfiguraci pro Network Performance Monitor, otevřete [řešení Network Performance Monitor](network-performance-monitor.md) a vyberte **Konfigurovat**.

### <a name="configure-network-security-group-rules"></a>Konfigurovat pravidla skupiny zabezpečení sítě 
Pro servery v Azure, které se používají pro monitorování prostřednictvím Network Performance Monitor, nakonfigurujte pravidla skupiny zabezpečení sítě (NSG) tak, aby umožňovala přenosy TCP na portu používaném Network Performance Monitor pro syntetické transakce. Výchozí port je 8084. Tato konfigurace umožňuje, aby byl agent Log Analytics nainstalovaný na virtuálních počítačích Azure ke komunikaci s místním agentem monitorování. 

Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Než budete pokračovat v tomto kroku, nainstalujte agenta místního serveru a agenta serveru Azure a spusťte skript EnableRules.ps1 PowerShellu. 

 
### <a name="discover-expressroute-peering-connections"></a>Zjistit připojení partnerských vztahů ExpressRoute 
 
1. Vyberte zobrazení **partnerské vztahy ExpressRoute** .
2. Výběrem možnosti **vyhledat nyní** zjistíte všechny soukromé partnerské vztahy ExpressRoute, které jsou připojené k virtuálním sítím v předplatném Azure, které jsou propojené s tímto pracovním prostorem Azure Log Analytics.

    >[!NOTE]
    > Řešení aktuálně zjišťuje pouze soukromé partnerské vztahy ExpressRoute. 

    >[!NOTE]
    > Zjišťují se jenom privátní partnerské vztahy připojené k virtuálním sítím přidruženým k předplatnému, které jsou propojené s tímto Log Analytics pracovním prostorem. Pokud je ExpressRoute připojený k virtuálním sítím mimo předplatné propojené s tímto pracovním prostorem, vytvořte v těchto předplatných Log Analytics pracovní prostor. Pak použijte Network Performance Monitor k monitorování těchto partnerských vztahů. 

    ![Konfigurace monitoru ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Po dokončení zjišťování budou zjištěná připojení privátního partnerského vztahu uvedena v tabulce. Monitorování těchto partnerských vztahů je zpočátku v zakázaném stavu. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Povolit monitorování připojení partnerských vztahů ExpressRoute 

1. Vyberte připojení privátního partnerského vztahu, které chcete monitorovat.
2. V pravém podokně zaškrtněte políčko **monitorovat tento partnerský vztah** . 
3. Pokud máte v úmyslu vytvořit události stavu pro toto připojení, vyberte **Povolit monitorování stavu pro tento partnerský vztah**. 
4. Vyberte možnost podmínky monitorování. Můžete nastavit vlastní prahové hodnoty pro generování události stavu zadáním prahových hodnot. Pokaždé, když hodnota podmínky překročí zvolenou prahovou hodnotu pro připojení partnerského vztahu, bude vygenerována událost stavu. 
5. Vyberte možnost **přidat agenty** a zvolte agenty monitorování, které chcete použít pro monitorování tohoto připojení partnerského vztahu. Ujistěte se, že jste přidali agenty na obou koncích připojení. Potřebujete aspoň jednoho agenta ve virtuální síti připojené k tomuto partnerskému vztahu. K tomuto partnerskému vztahu musíte taky připojit aspoň jeden místní agent. 
6. Kliknutím na **Uložit** uložte konfiguraci. 

   ![Konfigurace monitorování ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Po povolení pravidel a výběru hodnot a agentů Počkejte 30 až 60 minut, než se hodnoty naplní, a zobrazí se dlaždice **monitorování ExpressRoute** . Po zobrazení dlaždic monitorování jsou nyní vaše okruhy ExpressRoute a prostředky připojení monitorovány pomocí Network Performance Monitor. 

>[!NOTE]
> Tato funkce funguje spolehlivě v pracovních prostorech, které byly upgradovány na nový dotazovací jazyk.

## <a name="walkthrough"></a>Názorný postup 

Řídicí panel Network Performance Monitor zobrazuje přehled stavu okruhů ExpressRoute a připojení partnerských vztahů. 

![Řídicí panel Network Performance Monitor](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Seznam okruhů 

Pokud chcete zobrazit seznam všech sledovaných okruhů ExpressRoute, vyberte dlaždici okruhy ExpressRoute. Můžete vybrat okruh a zobrazit jeho stav, diagramy trendů pro ztrátu paketů, využití šířky pásma a latenci. Grafy jsou interaktivní. Můžete vybrat vlastní časový interval pro vykreslení grafů. Přetažením myši na oblast v grafu se můžete přiblížit a zobrazit jemně odstupňované datové body. 

![Seznam okruhů ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy ztráty, latence a propustnosti 

Grafy využití šířky pásma, latence a ztrát jsou interaktivní. Pomocí ovládacích prvků myši můžete přiblížit libovolný oddíl těchto grafů. Také se můžete podívat na data o šířce pásma, latenci a ztrátě pro jiné intervaly. V levém horním rohu pod tlačítkem **Akce** vyberte  **Datum a čas**. 

![Latence ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Seznam partnerských vztahů 

Pokud chcete v rámci privátního partnerského vztahu zobrazit seznam všech připojení k virtuálním sítím, vyberte na řídicím panelu dlaždici **privátních partnerských vztahů** . Tady můžete vybrat připojení k virtuální síti a zobrazit jeho stav, diagramy trendů pro ztrátu paketů, využití šířky pásma a latenci. 

![Partnerské vztahy ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologie okruhů 

Pokud chcete zobrazit topologii okruhů, vyberte dlaždici **topologie** . Tato akce přejde k zobrazení topologie vybraného okruhu nebo partnerského vztahu. Diagram topologie poskytuje latenci pro jednotlivé segmenty v síti a všechny segmenty vrstvy 3 jsou reprezentovány uzlem diagramu. Výběr směrování odhalí další podrobnosti o směrování. Pokud chcete zvýšit úroveň viditelnosti tak, aby zahrnovala místní směrování, přesuňte posuvník v části **filtry**. Přesunutí posuvníku doleva nebo doprava se zvýší nebo sníží počet segmentů směrování v grafu topologie. Latence napříč každým segmentem je viditelná, což umožňuje rychlejší izolaci segmentů s vysokou latencí ve vaší síti.

![Topologie ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Detailní zobrazení topologie okruhu 

Toto zobrazení ukazuje připojení k virtuální síti. 

![Připojení k virtuální síti ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostika 

Network Performance Monitor pomáhá diagnostikovat několik problémů s připojením okruhu. Některé problémy, které vidíte, jsou uvedeny níže.

Můžete zobrazit kódy oznámení a nastavit výstrahy na nich prostřednictvím **LogAnalytics**. Na stránce **Diagnostika npm** uvidíte popisy každé aktivované zprávy diagnostiky.

| Kód oznámení (protokoly) | Description |
| --- | --- |
| 5501 | Nejde procházet sekundárním připojením okruhu ExpressRoute. |
| 5502 | Primární připojení okruhu ExpressRoute se nedá Procházet. |
| 5503 | Pro předplatné propojené s pracovním prostorem se nenašel žádný okruh. | 
| 5508 | Nepovedlo se určit, jestli provoz projde všemi okruhy pro cestu. |
| 5510 | Přenos nepřechází přes zamýšlený okruh. | 
| 5511 | Přenos nepřechází přes zamýšlenou virtuální síť. | 

**Okruh je mimo provoz.** Network Performance Monitor vás upozorní, jakmile dojde ke ztrátě spojení mezi místními prostředky a virtuálními sítěmi Azure. Toto oznámení vám pomůže proniknout aktivní akci před přijetím eskalace uživatelů a snížením prostojů.

![Okruh ExpressRoute je mimo provoz.](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Provoz, který nepřechází přes zamýšlený okruh.** Network Performance Monitor vás upozorní, když přenos neprojde zamýšleným okruhem ExpressRoute. K tomuto problému může dojít, pokud je okruh mimo provoz a přenos prochází přes trasu zálohování. Může k tomu také dojít, pokud dojde k potížím s směrováním. Tyto informace vám pomůžou aktivně spravovat všechny problémy s konfigurací v zásadách směrování a zajistit, aby se používala optimální a zabezpečená trasa. 

 

**Provoz nevedený přes primární okruh.** Network Performance Monitor vás upozorní, když přenos provádí tok přes sekundární okruh ExpressRoute. I když v takovém případě nebudete mít problémy s připojením, budete moct aktivně řešit problémy s primárním okruhem a lépe tak připravit. 

 
![Tok provozu ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Snížení výkonu z důvodu využití špičky** Trend využití šířky pásma s trendem latence můžete korelovat a zjistit, jestli je snížení zátěže úlohy Azure způsobené špičkou využití šířky pásma nebo ne. Pak můžete provést akci odpovídajícím způsobem.

![Využití šířky pásma ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Další kroky
V [protokolech hledání](../logs/log-query-overview.md) zobrazíte podrobné záznamy dat o výkonu sítě.

