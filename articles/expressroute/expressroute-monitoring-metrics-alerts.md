---
title: 'Azure ExpressRoute: monitorování, metriky a výstrahy'
description: Seznamte se s Azure ExpressRoute monitoring, metrikami a výstrahami pomocí Azure Monitor, což je jedna zastávka pro všechny metriky, výstrahy a diagnostické protokoly v Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725777"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorování, metriky a výstrahy služby ExpressRoute

Tento článek vám pomůže pochopit monitorování, metriky a výstrahy ExpressRoute pomocí Azure Monitor. Azure Monitor je jedno zastavení pro všechny metriky, výstrahy a diagnostické protokoly napříč všemi Azure.
 
>[!NOTE]
>Použití **klasických metrik** se nedoporučuje.
>

## <a name="expressroute-metrics"></a>ExpressRoute metriky

Chcete-li zobrazit **metriky**, přejděte na stránku *Azure monitor* a vyberte *metriky*. Pokud chcete zobrazit metriky **ExpressRoute** , filtrujte podle typu prostředku *ExpressRoute okruhy*. Pokud chcete zobrazit **Global REACH** metriky, filtrujte podle typu prostředku *ExpressRoute okruhy* a vyberte prostředek okruhu ExpressRoute, který má povolený Global REACH. Pokud chcete zobrazit **ExpressRoute přímé** metriky, filtrujte typ prostředku pomocí *portů ExpressRoute*. 

Po výběru metriky se použije výchozí agregace. Volitelně můžete použít rozdělení, které zobrazí metriku s různými dimenzemi.

### <a name="aggregation-types"></a>Typy agregace:

Průzkumník metrik podporuje SUM, MAX, MIN, AVG a COUNT jako [typy agregace](../azure-monitor/essentials/metrics-charts.md#aggregation). Při kontrole přehledů jednotlivých metrik ExpressRoute byste měli použít doporučený typ agregace.

* Sum: součet všech hodnot zachycených během intervalu agregace. 
* Count (počet): počet měření zachycených během intervalu agregace. 
* Average: průměr hodnot metriky zachycených během intervalu agregace. 
* Min: nejmenší hodnota zachycená během intervalu agregace. 
* Max: největší hodnota zachycená během intervalu agregace. 

### <a name="available-metrics"></a>Dostupné metriky

|**Metrika**|**Kategorie**|**Dimenze (y)**|**Funkce (y)**|
| --- | --- | --- | --- |
|Dostupnost protokolu ARP|Dostupnost|<ui><li>Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu (Private/Public/Microsoft)</ui></li>|ExpressRoute|
|Dostupnost protokolu BGP|Dostupnost|<ui><li> Partner (primární/sekundární ExpressRoute směrovač)</ui></li><ui><li> Typ partnerského vztahu</ui></li>|ExpressRoute|
|BitsInPerSecond|Provoz|<ui><li> Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li>Odkaz (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Připojení brány ExpressRoute</ui></li>|
|BitsOutPerSecond|Provoz| <ui><li>Typ partnerského vztahu (ExpressRoute)</ui></li><ui><li> Odkaz (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>Připojení brány ExpressRoute</ui></li>|
|Využití procesoru|Výkon| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Pakety za sekundu|Výkon| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet tras inzerovaných pro partnerský uzel |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet tras získaných z partnerského vztahu |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Četnost změn tras |Dostupnost| <ui><li>Instance</ui></li>|ExpressRoute Virtual Network bránu|
|Počet virtuálních počítačů v Virtual Network |Dostupnost| – |ExpressRoute Virtual Network bránu|
|GlobalReachBitsInPerSecond|Provoz|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Provoz|<ui><li>Skey okruhu s partnerským vztahem (klíč služby)</ui></li>|Global Reach|
|AdminState|Fyzické připojení|Odkaz|ExpressRoute Direct|
|LineProtocol|Fyzické připojení|Odkaz|ExpressRoute Direct|
|RxLightLevel|Fyzické připojení|<ui><li>Propojit</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fyzické připojení|<ui><li>Propojit</ui></li><ui><li>Pásu</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Použití *GlobalGlobalReachBitsInPerSecond* a *GlobalGlobalReachBitsOutPerSecond* se zobrazí jenom v případě, že se naváže aspoň jedno připojení Global REACH.
>

## <a name="circuits-metrics"></a>Metriky okruhů

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Služby a výstupní metriky v rámci všech partnerských vztahů

Typ agregace: *prům* .

Metriky můžete zobrazit ve všech partnerských vztazích na daném okruhu ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metriky okruhu":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity a výstupní metriky na partnerský vztah

Typ agregace: *prům* .

V bitech za sekundu si můžete zobrazit metriky pro privátní, veřejné a partnerské vztahy Microsoftu.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metriky na partnerský vztah":::

### <a name="bgp-availability---split-by-peer"></a>Dostupnost protokolu BGP – rozdělit podle partnerského vztahu  

Typ agregace: *prům* .

U partnerských vztahů a partnerských vztahů (primárních a sekundárních ExpressRoute směrovačů) se můžete podívat na blížící se k dostupnosti protokolu BGP (připojení vrstvy 3) v reálném čase. Tento řídicí panel zobrazuje primární stav relace protokolu BGP pro privátní partnerské vztahy a druhý stav relace protokolu BGP je mimo provoz privátního partnerského vztahu. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Dostupnost protokolu BGP na partnerský uzel":::

### <a name="arp-availability---split-by-peering"></a>Dostupnost protokolu ARP – rozdělení podle partnerského vztahu  

Typ agregace: *prům* .

U partnerských vztahů a partnerských vztahů (primárních a sekundárních ExpressRoute směrovačů) se můžete podívat na blížící se k dostupnosti [protokolu ARP](./expressroute-troubleshooting-arp-resource-manager.md) (konektivita vrstvy 3) v reálném čase. Tento řídicí panel zobrazuje stav relace protokolu ARP privátního partnerského vztahu v obou partnerských uzlech, ale v případě partnerského vztahu Microsoftu pro oba partnerské vztahy. Výchozí agregace (průměr) byla využívána v obou partnerských uzlech.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Dostupnost protokolu ARP na partnerský uzel":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute přímé metriky

### <a name="admin-state---split-by-link"></a>Stav Správce – rozdělit podle odkazu

Typ agregace: *prům* .

Můžete zobrazit stav správce pro každý odkaz dvojice portů ExpressRoute Direct. Stav Správce představuje, zda je fyzický port zapnut nebo vypnut. Tento stav je nutný k předávání provozu napříč ExpressRoute přímým připojením.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Stav Správce ER Direct":::

### <a name="bits-in-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení

Typ agregace: *prům* .

V obou propojeních dvojice portů ExpressRoute můžete zobrazit bity za sekundu. Monitorujte tento řídicí panel a porovnejte příchozí šířku pásma pro oba odkazy.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Přímých bitů ER za sekundu":::

### <a name="bits-out-per-second---split-by-link"></a>Bity za sekundu – rozdělit podle propojení

Typ agregace: *prům* .

Můžete také zobrazit bity za sekundu v obou odkazech dvojice portů ExpressRoute Direct. Monitorujte tento řídicí panel a porovnejte odchozí šířku pásma pro oba odkazy.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER – přímé bity za sekundu":::

### <a name="line-protocol---split-by-link"></a>Protokol řádku – rozdělit podle odkazu

Typ agregace: *prům* .

Protokol linky můžete zobrazit přes každý odkaz dvojice portů ExpressRoute Direct. Protokol linky označuje, zda je fyzické propojení funkční a funguje přes ExpressRoute Direct. Sledujte tento řídicí panel a nastavte výstrahy na informace o tom, kdy došlo k ukončení fyzického připojení.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Přímý spojnicový protokol ER":::

### <a name="rx-light-level---split-by-link"></a>Úroveň Light pro příjem – rozdělení podle propojení

Typ agregace: *prům* .

Můžete zobrazit úroveň indikátoru příjmu (úroveň světla, kterou port pro přímý přenos ExpressRoute **přijímá**) pro každý port. V pořádku úrovně nízké úrovně příjmu obvykle spadají do rozsahu-10 přepálených dat na 0 dBm. Nastavte upozornění, aby se zobrazovala oznámení v případě, že úroveň nízké úrovně příjmu spadá mimo dosah v pořádku.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Úroveň nízké linky pro příjem na ER":::

### <a name="tx-light-level---split-by-link"></a>Úroveň Light tx – rozdělení podle propojení

Typ agregace: *prům* .

Úroveň síla pro odesílání můžete zobrazit (úroveň světla, kterou port ExpressRoute Direct **odesílá) pro** každý port. Nízké úrovně světla v pořádku jsou obecně v rozsahu od-10 přepálených dat na 0 dBm. Nastavte upozornění, aby se zobrazila výstraha v případě, že úroveň nenáročného vysílání spadá mimo dosah v pořádku.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Světlá úroveň TX přímého řádku ER":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network metriky brány

Typ agregace: *prům* .

Když nasadíte bránu ExpressRoute, Azure spravuje výpočty a funkce vaší brány. K dispozici je šest metrik brány, abyste lépe porozuměli výkonu brány:

* Využití procesoru
* Počet paketů za sekundu
* Počet tras inzerovaných pro partnerské uzly
* Počet tras získaných od partnerských uzlů
* Četnost změněných tras
* Počet virtuálních počítačů ve virtuální síti  

Důrazně doporučujeme nastavit výstrahy pro každou z těchto metrik, abyste se dozvěděli, že by vaše brána mohla zobrazit problémy s výkonem.

### <a name="cpu-utilization---split-instance"></a>Využití CPU – dělená instance

Typ agregace: *prům* .

Můžete zobrazit využití CPU v každé instanci brány. Využití CPU může krátce během údržby rutiny, ale prodloužit vysoké využití procesoru, znamenat, že vaše brána dosahuje nekritického výkonu. Zvýšení velikosti brány ExpressRoute může vyřešit tento problém. Nastavte výstrahu, jak často využití CPU překročí určitou prahovou hodnotu.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Snímek obrazovky využití CPU – rozdělte metriky.":::

### <a name="packets-per-second---split-by-instance"></a>Počet paketů za sekundu – rozdělení podle instance

Typ agregace: *prům* .

Tato metrika zachycuje počet příchozích paketů procházejících bránou ExpressRoute. Pokud brána přijímá provoz z vaší místní sítě, měli byste se podívat na konzistentní datový proud dat. Nastavte upozornění, když počet paketů za sekundu klesne pod prahovou hodnotu, což znamená, že brána už nepřijímá provoz.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Snímek obrazovky s pakety za sekundu – rozdělí metriky.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Počet tras inzerovaných pro rozdělení peer-Split podle instance

Typ agregace: *počet*

Tato metrika je počet tras, které brána ExpressRoute Gateway do okruhu inzeruje. Adresní prostory můžou zahrnovat virtuální sítě, které jsou připojené pomocí partnerského vztahu virtuálních sítí a využívají vzdálenou bránu ExpressRoute. Měli byste očekávat, že počet tras zůstane konzistentní, pokud nedochází k častým změnám adresních prostorů virtuální sítě. Nastavte upozornění, když počet inzerovaných tras přetáhne pod prahovou hodnotu pro počet adresních prostorů ve virtuální síti, na které víte.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Snímek obrazovky počtu tras inzerovaných pro partnerský uzel":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Počet tras získaných z instance peer-Split podle instance

Typ agregace: *Max*

Tato metrika znázorňuje počet tras, které brána ExpressRoute Gateway učí od partnerských uzlů připojených k okruhu ExpressRoute. Tyto trasy můžou být buď z jiné virtuální sítě připojené ke stejnému okruhu, nebo se z místního prostředí dozvěděly. Nastavte upozornění, když počet zjištěných tras přetáhne pod určitou prahovou hodnotu. To může znamenat, že brána zobrazuje problém s výkonem nebo že vzdálení partneři již Neinzerují trasy k okruhu ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Snímek obrazovky počtu tras získaných z partnerského vztahu":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frekvence změny – rozdělení podle instance

Typ agregace: *součet*

Tato metrika znázorňuje četnost tras, které se z nich dozvěděly nebo které jsou inzerovány vzdáleným partnerům. Nejdřív byste měli prozkoumat vaše místní zařízení, abyste zjistili, proč se síť často mění. Vysoká frekvence změny tras může znamenat problém s výkonem v bráně ExpressRoute, ve které se může problém vyřešit škálováním SKU brány. Nastavte výstrahu pro prahovou hodnotu četnosti, která bude vědět, kdy brána ExpressRoute zobrazuje neobvyklé změny směrování.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Snímek obrazovky frekvence změněné metriky":::

### <a name="number-of-vms-in-the-virtual-network"></a>Počet virtuálních počítačů v Virtual Network

Typ agregace: *Max*

Tato metrika zobrazuje počet virtuálních počítačů, které používají bránu ExpressRoute. Počet virtuálních počítačů může zahrnovat virtuální počítače z partnerských virtuálních sítí, které používají stejnou bránu ExpressRoute. Nastavte upozornění pro tuto metriku, pokud počet virtuálních počítačů překročí určitou prahovou hodnotu, která by mohla ovlivnit výkon brány. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Snímek obrazovky počtu virtuálních počítačů v metrikě virtuální sítě":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Připojení brány ExpressRoute v bitech za sekundu

Typ agregace: *prům* .

Tato metrika ukazuje využití šířky pásma pro konkrétní připojení k okruhu ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Snímek obrazovky metriky využití šířky pásma připojení brány":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Výstrahy pro připojení brány ExpressRoute

1. Pokud chcete nakonfigurovat výstrahy, přejděte na **Azure monitor** a pak vyberte **výstrahy**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="výstrahy":::
2. Vyberte **+ vybrat cíl** a vyberte prostředek připojení brány ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cílové":::
3. Zadejte podrobnosti výstrahy.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="Skupina akcí":::
4. Definujte a přidejte skupinu akcí.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Přidat skupinu akcí":::

## <a name="alerts-based-on-each-peering"></a>Výstrahy na základě jednotlivých partnerských vztahů

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="Každý partnerský vztah":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurace výstrah pro protokoly aktivit na okruhech

V části **kritéria výstrahy** můžete pro typ signálu vybrat **protokol aktivity** a vybrat signál.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="protokoly aktivit":::

## <a name="more-metrics-in-log-analytics"></a>Další metriky v Log Analytics

Metriky ExpressRoute můžete zobrazit také tak, že přejdete do svého prostředku okruhu ExpressRoute a vyberete kartu *protokoly* . Pro všechny metriky, které dotazují, bude výstup obsahovat následující sloupce.

|**Sloupec**|**Typ**|**Popis**|
| --- | --- | --- |
|TimeGrain|řetězec|PT1M (hodnoty metriky se posunou každou minutu)|
|Počet|real|Obvykle se rovná 2 (každý MSEE po každou minutu nahraje jednu hodnotu metriky)|
|Minimum|real|Minimum dvou hodnot metrik nabízených dvěma směrovači msee|
|Maximum|real|Maximální počet dvou hodnot metrik nabízených dvěma směrovači mseey|
|Průměr|real|Rovná se (minimálně + maximum)/2|
|Celkem|real|Součet dvou hodnot metriky z obou směrovači msee (hlavní hodnota, která se má zaměřit na dotazování metriky)|
  
## <a name="next-steps"></a>Další kroky

Nakonfigurujte připojení ExpressRoute.
  
* [Vytvoření a změny okruhu](expressroute-howto-circuit-arm.md)
* [Vytvoření a změny konfigurace partnerského vztahu](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
