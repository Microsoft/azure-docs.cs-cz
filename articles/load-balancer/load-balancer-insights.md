---
title: Přehledy pro Azure Load Balancer
description: Pomocí přehledů nástroje pro vyrovnávání zatížení můžete dosáhnout rychlé lokalizace chyb a rozhodování o návrhu.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 88c287c4aa87b4df41bfe6bbc1992262b21d07aa
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232756"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Monitorování a konfigurace Azure Load Balancer pomocí přehledů

Prostřednictvím Azure Monitor pro sítě jste pro vaše nástroje pro vyrovnávání zatížení poskytovali funkční vizualizace závislosti a předem nakonfigurované řídicí panely metrik. Tyto vizuály vám pomůžou zajistit rozhodování o návrhu a rychle lokalizovat, diagnostikovat a vyřešit případné chyby.

>[!NOTE] 
>Všimněte si prosím, že tato funkce je ve verzi Preview a zobrazení funkční závislosti a předkonfigurovaný řídicí panel se může změnit, aby se zlepšilo toto prostředí.

>[!IMPORTANT]
>Pro zobrazení metrik z oboru názvů Load Balancer v předem nakonfigurovaném řídicím panelu metriky se vyžaduje Standard Load Balancer. Pořád budete moct zobrazit metriky z virtuálního počítače, sady škálování virtuálních počítačů a oborů názvů monitorování připojení, ale doporučujeme [upgradovat na standard](./upgrade-basic-standard.md) pro jakékoli provozní úlohy, abyste mohli využít robustní sadu Load Balancerch metrik.

## <a name="functional-dependency-view"></a>Zobrazení funkční závislosti

Zobrazení funkční závislosti vám umožní sestavovat i nejsložitější nastavení nástroje pro vyrovnávání zatížení. Díky vizuální zpětné vazbě na nejnovější konfiguraci Load Balancer můžete provádět aktualizace, a přitom si zapamatujte svou konfiguraci.

K tomuto zobrazení se dostanete tak, že navštívíte okno přehledy svého prostředku Load Balancer v Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition zobrazení funkční závislosti. Front-end nástroje pro vyrovnávání zatížení se může zobrazit připojením ke členům fondu back-endu prostřednictvím nakonfigurovaných pravidel. Pro Standard Load Balancer jsou řádky z pravidel vyrovnávání zatížení do instancí fondu back-endu barevně kódované na základě stavu sondy stavu." border="true":::

U standardních nástrojů pro vyrovnávání zatížení jsou prostředky vašeho fondu back-endu barevně kódované se stavem sondy stavu, což značí aktuální dostupnost vašeho back-endu pro poskytování provozu. Vedle výše uvedené topologie se zobrazí graf s časovým přehledem stavu, který poskytuje zobrazení snímku stavu vaší aplikace.

## <a name="metrics-dashboard"></a>Řídicí panel metrik

V okně přehledy Load Balancer můžete vybrat podrobnější metriky a zobrazit předem nakonfigurovaný [Azure monitor sešit](../azure-monitor/platform/workbooks-overview.md) obsahující vizuály metriky, které jsou relevantní pro konkrétní aspekty vašeho Load Balancer. Tento řídicí panel zobrazí stav Load Balancer a odkazy na příslušnou dokumentaci v horní části stránky.

Nejprve se zobrazí karta Přehled. Můžete procházet dostupné karty, z nichž každý obsahuje vizuály relevantní pro konkrétní aspekty vašeho Load Balancer. Explicitní doprovodné materiály pro každý z nich jsou k dispozici na řídicím panelu v dolní části každé karty.

Karty řídicího panelu, které jsou aktuálně k dispozici:
* Přehled
* Dostupnost front-endu a back-endu
* Propustnost dat
* Distribuce toků
* Monitorování připojení
* Definice metrik 

### <a name="overview-tab"></a>Karta Přehled
Karta Přehled obsahuje mřížku s možností vyhledávání s celkovým stavem dostupnosti a stavu sondy dat pro každou front-endové IP adresu připojenou k vašemu Load Balancer. Tyto metriky označují, jestli front-end adresa IP reaguje, a výpočetní instance ve fondu back-endu přireagují na příchozí připojení samostatně.

Můžete si také zobrazit celkovou propustnost dat pro každou front-endové IP adresu na této stránce, abyste získali představu o tom, jestli vytváříte a přijímáte očekávané úrovně provozu. Pokyny v dolní části stránky vás přesměrují na příslušnou kartu, aby se zobrazily případné nepravidelné hodnoty.

### <a name="frontend-and-backend-availability-tab"></a>Karta dostupnost front-endu a back-endu
Na kartách front-endu a dostupnosti back-endu se zobrazují metriky stavu propustnosti dat a sondy stavu, které se zobrazují v několika užitečných zobrazeních. První graf znázorňuje agregovanou hodnotu, abyste mohli určit, zda došlo k problému. Zbytek grafů zobrazuje tyto metriky rozdělené podle různých dimenzí, takže můžete řešit problémy a identifikovat zdroje potíží s příchozí dostupností.

Pracovní postup pro zobrazení těchto grafů je k dispozici v dolní části stránky s běžnými příčinami různých příznaků. 

### <a name="data-throughput-tab"></a>Karta propustnost dat
Karta propustnost dat vám umožní zkontrolovat příchozí a odchozí propustnost a zjistit, jestli jsou vaše modely přenosů očekávané. Zobrazí se tím propustnost příchozích a odchozích dat rozdělená na front-end IP a front-end port, abyste mohli zjistit, jestli se služba, kterou spouštíte, provádí samostatně.

### <a name="flow-distribution"></a>Distribuce toků
Karta distribuce toků vám pomůže vizualizovat a spravovat počet toků, které instance back-endu dostávají a produkují. Zobrazuje frekvenci vytváření toků a počet toků pro příchozí a odchozí provoz a také přenos síťových přenosů, které instance virtuálních počítačů a sady škálování virtuálních počítačů přijímají. 

Tato zobrazení vám můžou sdělit svůj názor na to, jestli konfigurace Load Balancer nebo vzorce provozu vedou k nevyváženému provozu. Například pokud máte nakonfigurované spřažení relace a jeden klient provádí neúměrný počet požadavků. Také vám poskytne informace o tom, jestli se při přístupu k velikosti vašeho počítače blíží [limit toku pro virtuální počítače](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) .

### <a name="connection-monitors"></a>Monitorování připojení
Na kartě monitorování připojení se zobrazí latence odezvy na globální mapě pro všechna nakonfigurovaná [monitorování připojení](../network-watcher/connection-monitor.md)  . Tyto vizuály poskytují užitečné informace pro služby s přísnými nároky na latenci. Abyste splnili Vaše požadavky, možná budete muset přidat další oblastní nasazení nebo přejít na model [Vyrovnávání zatížení pro různé regiony](./cross-region-overview.md) .

### <a name="metric-definitions"></a>Definice metrik
Karta definice metriky obsahuje všechny informace, které jsou uvedeny v [článku multidimenzionální metriky](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).

## <a name="next-steps"></a>Další kroky
* Pokud máte všechno, co se dá zlepšit, Projděte si řídicí panel a sdělte nám svůj názor pomocí níže uvedeného odkazu.
* [Přečtěte si dokumentaci k metrikám a ujistěte se, že rozumíte tomu, jak se každá metrika počítá.](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Vytvoření monitorování připojení pro Load Balancer](../network-watcher/connection-monitor.md)
* [Vytvořte si vlastní sešity](../azure-monitor/platform/workbooks-overview.md), můžete inspiraci kliknutím na tlačítko Upravit na podrobném řídicím panelu metriky.
