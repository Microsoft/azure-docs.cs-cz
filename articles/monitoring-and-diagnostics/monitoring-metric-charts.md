---
title: Azure Průzkumníku metrik monitorování
description: Další informace o nových funkcích v Průzkumníku metrik Azure monitorování
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: a2611f89d9eef1ec6bac34389fa4db833aecc087
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264084"
---
# <a name="azure-monitor-metrics-explorer"></a>Průzkumníku metrik Azure monitorování

Tento postup popisuje další generace Azure monitorování metriky vytváření grafů práci se aktuálně ve verzi public preview. Nové prostředí podporuje vykreslování grafů pro multidimenzionální metriky i základní metriky s žádné dimenze. Můžete zobrazit grafy překrytí metriky z různých typů prostředků, více skupin prostředků a odběry. Grafy vícerozměrných metriky můžete přizpůsobit použití filtrů dimenze a také seskupení. Jakéhokoli grafu, včetně přizpůsobené grafů můžete připnuli na řídicí panely.

Pokud hledáte informace o původní prostředí, které podporuje pouze s dimenzemi v žádné základní metriky, najdete v části s názvem "Přístup k metriky prostřednictvím portálu" v [metriky přehledem průvodce](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>Co je Azure Průzkumníku metrik monitorování?

Azure Průzkumníku metrik monitorování je součástí portálu Microsoft Azure, který umožňuje vykreslení grafy, vizuálně korelace trendy a příčin špičky a klesne služba metriky hodnoty. Průzkumníku metrik je nezbytné počáteční bod pro zkoumání různé výkonu a dostupnosti problémy s vašimi aplikacemi a infrastruktury sledovat službami Azure monitorování nebo hostované v Azure. 

## <a name="what-are-metrics-in-azure"></a>Jaké jsou metriky v Azure?

Metriky v Microsoft Azure jsou řady měřená hodnot a počty, které jsou shromážděných a uložených v čase. Existují metriky standard (nebo "platformy") a vlastní metriky. Standardní metriky jsou pro vás poskytovaný samotné platformě Azure. Standardní metriky projeví statistiky stavu a využití vašich prostředků Azure. Zatímco vlastní metriky zasílá do Azure vaší aplikace s použitím [Application Insights API pro vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Vlastní metriky jsou uloženy v prostředcích Application Insights společně s další metriky pro konkrétní aplikace.

## <a name="what-are-multi-dimensional-metrics"></a>Jaké jsou vícerozměrných metriky?

Mnoho prostředků Azure a nyní vystavit vícerozměrných metriky. Tyto metriky sledovat více řad hodnot pro jednu s názvem dimenze. Metrika "dostupné místo na disku" může mít například dimenze nazvaná "Jednotka" s hodnotami "C:", "D:", který by umožnil zobrazení buď volného místa na disku na všech jednotkách nebo pro každou jednotku jednotlivě. 

Následující příklad ukazuje dvě datové sady pro hypotetické metriku názvem "Propustnost sítě". První datovou sadu nemá žádné dimenze. Druhý datová sada zobrazuje hodnoty se dvěma rozměry "IP adresa" a "Směr":

### <a name="network-throughput"></a>Propustnost sítě
(Tato metrika nemá žádné dimenze)

 |Časové razítko        | Hodnota metriky | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kb/s | 
   | 8/9/2017 8:15 | 1,141.4 kb/s |
   | 8/9/2017 8:16 | 1,110.2 kb/s |

Tato metrika dimenzí může obsahovat jenom jako odpověď základní otázku "co se Moje propustnost sítě v daném okamžiku?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Propustnost sítě + dvěma rozměry ("IP adresy" a "Směr")

| Časové razítko          | Dimenze "IP adresy" | Dimenze "Směr" | Hodnota metriky| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Odeslat"    | 646.5 kb/s |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Směr = "Zobrazit" | 420.1 kB/s |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 150.0 kb/s | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 115,2 kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Odeslat"    | 515.2 kb/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Směr = "Zobrazit" | 371.1 kB/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Odeslat"    | 155.0 kb/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Směr = "Zobrazit" | 100.1 kB/s |

Tato metrika dokáže odpovědět na otázky, jako je například "co se propustnost sítě pro každou IP adresu?" a "kolik data byla odeslána a přijala?" Multidimenzionální metriky provádění další hodnoty pro analýzu a diagnostiky ve srovnání s-dimenzí metriky. 

## <a name="how-do-i-create-a-new-chart"></a>Vytvoření nového grafu

   > [!NOTE]
   > Některé funkce staré metriky prostředí ještě nejsou k dispozici v nové Průzkumníku metrik. Zatímco nové prostředí je ve verzi preview, můžete pokračovat pomocí původní (bez prostorové) metriky zobrazení monitorování Azure. 

1. Otevřete portál Azure
2. Přejděte do nové **monitorování** a pak vyberte **metriky (preview)**.

   ![Obrázek náhledu metriky](./media/monitoring-metric-charts/001.png)

3. **Metriky selektor** budou automaticky otevřené pro vás. Zvolte prostředku ze seznamu zobrazíte její přidružené metriky. V seznamu jsou uvedeny pouze prostředky s metriky.

   ![Obrázek náhledu metriky](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Pokud máte více než jedno předplatné, si Průzkumníku metrik se prostředky ve všech předplatných, které jsou vybrány v nastavení portálu pomocí seznamu odběrů -> filtru. Chcete-li ji změnit, klikněte na ikonu portálu nastavení zařízení na obrazovce a vyberte odběry, které chcete použít.

4. Pro některé typy prostředků (tj. účty úložiště a virtuálních počítačů), před výběrem metriky, musíte zvolit **Sub služby**. Každá služba dílčí představuje vlastní sadu metriky, které se vztahují k této službě dílčí pouze a ne k dalším službám sub.

   Každý Azure Storage má například metriky pro subservices "Objekty BLOB", "Files", "Fronty" a "Tabulky", které jsou všechny části účtu úložiště. Metrika "počet zpráv fronty" je však přirozeně použít subservice "Fronty" a nikoli na všechny ostatní subservices účet úložiště.

   ![Obrázek náhledu metriky](./media/monitoring-metric-charts/003.png)

5. Vyberte ze seznamu metriky. Pokud znáte částečný název metriky, které chcete, můžete spustit zadáním v zobrazíte filtrovaný seznam dostupné metriky:

   ![Obrázek náhledu metriky](./media/monitoring-metric-charts/004.png)

6. Po výběru metriky, bude graf vykreslen s výchozí agregace pro metriku vybrané. V tomto okamžiku můžete jen kliknout na směrem od **metriky selektor** a tím ho zavřete. Graf můžete volitelně také můžete přepnout do jiného agregačního. Pro některé metriky přepínání agregace vám umožní vybrat hodnotu, která chcete zobrazit v grafu. Například můžete přepínat mezi průměrné, minimální a maximální hodnoty. 

7. Kliknutím na ikonu Přidat metrika ![Ikona metriky](./media/monitoring-metric-charts/icon001.png) a s opakováním kroků 3 až 6 můžete přidat další metriky na stejném grafu.

   > [!NOTE]
   > Nechcete obvykle mají metriky s různými jednotkami množství (tj. "milisekundách" a "kilobajtů") nebo s výrazně odlišné škálování na jeden graf. Místo toho zvažte použití více grafů. Klikněte na tlačítko přidat graf vytvořit více grafů v Průzkumníku metrik.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Jak používat filtry k grafy?

Nastavte filtry pro grafy, které se zobrazí metriky s dimenzí. Například pokud metrika "Počet transakcí" dimenzi, "Odpověď typu", který určuje, zda odpověď z transakce byla úspěšná nebo neúspěšná, pak filtrování v této dimenzi by vykreslení grafu řádek pro pouze úspěšné (nebo jenom se nezdařilo) transakce. 

### <a name="to-add-a-filter"></a>Přidání filtru:

1. Klikněte na ikonu Přidat filtr ![ikonu filtru](./media/monitoring-metric-charts/icon002.png) výše grafu

2. Vyberte dimenzi, která (vlastnost), který chcete filtrovat

   ![metriky bitové kopie](./media/monitoring-metric-charts/006.png)

3. Vyberte dimenze hodnot, které chcete zahrnout při vykreslení grafu (Tento příklad ukazuje, filtrování transakce úspěšné úložiště):

   ![metriky bitové kopie](./media/monitoring-metric-charts/007.png)

4. Po výběru hodnot filtru, klikněte na tlačítko mimo selektor filtru, a tím ho zavřete. Teď graf zobrazuje, kolik transakcí úložiště se nezdařilo:

   ![metriky bitové kopie](./media/monitoring-metric-charts/008.png)

5. Kroky 1 – 4 platí více filtrů pro stejné grafy, můžete opakovat.

## <a name="how-do-i-segment-a-chart"></a>Jak segmentovat graf?

Můžete rozdělit dimenze, která bude vizualizovat jak různých segmentů metriky porovnání proti sobě metriky a identifikovat odlehlé segmenty dimenze. 

### <a name="to-segment-a-chart"></a>Segmentu graf:

1. Klikněte na ikonu Přidat seskupení  ![metriky bitové kopie](./media/monitoring-metric-charts/icon003.png) výše grafu.
 
   > [!NOTE]
   > Můžete mít více filtrů, ale pouze jedno seskupení na jednoho grafu.

2. Vyberte dimenze, na kterém chcete segmentovat grafu: 

   ![metriky bitové kopie](./media/monitoring-metric-charts/010.png)

   Teď grafu zobrazí více řádků, jednu pro každý segment dimenze:

   ![metriky bitové kopie](./media/monitoring-metric-charts/012.png)

3. Klikněte na tlačítko směrem od **seskupení selektor** a tím ho zavřete.

   > [!NOTE]
   > Pomocí filtrování a seskupení na stejné dimenze skrýt segmentů, které jsou důležité pro váš scénář a usnadňují grafy.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Jak připnout grafy, aby řídicí panely?

Po dokončení konfigurace grafy, můžete ho přidat do řídicí panely, abyste mohli znovu zobrazit, pravděpodobně v kontextu jiných monitorování telemetrických dat, nebo sdílet se svým týmem. 

Chcete-li připnout nakonfigurované graf na řídicí panel:

Po dokončení konfigurace grafu, klikněte na **grafu akce** nabídky v pravém horním rohu grafu top a klikněte na **připnout na řídicí panel**.

   ![metriky bitové kopie](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Další postup

  Čtení [vytváření vlastní řídicí panely klíčového ukazatele výkonu](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) Další informace o osvědčené postupy pro vytváření řešitelné řídicích panelů s metriky.