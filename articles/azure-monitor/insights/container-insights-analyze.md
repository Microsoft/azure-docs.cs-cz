---
title: Monitorování výkonu clusteru AKS pomocí Azure monitoru pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete zobrazit a analyzovat data výkonu a protokolů pomocí Azure monitoru pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2019
ms.author: magoedte
ms.openlocfilehash: 531e51fbddb99ebba11284d5291b4cca26559bc1
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65906766"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Porozumět výkonu cluster AKS pomocí Azure monitoru pro kontejnery 
Díky Azure monitoru pro kontejnery můžete použít grafy výkonu a stavu ke sledování těchto úloh své clustery Azure Kubernetes Service (AKS) za dvou hledisek, přímo z clusteru AKS nebo všechny AKS clusterů v rámci předplatného Azure Monitorování. Zobrazení služby Azure Container Instances (ACI) je také možné, při sledování konkrétní clusteru AKS.

Tento článek vám pomůže pochopit prostředí mezi dvěma perspektivy a jak vám může pomoct rychle posoudit, prozkoumat a vyřešit problémy zjištěné.

Informace o povolení monitorování Azure pro kontejnery, naleznete v tématu [připojení Azure Monitor pro kontejnery](container-insights-onboard.md).

Azure Monitor nabízí více clusteru zobrazení, které zobrazí stav všech monitorovaných AKS clustery se systémem Linux a Windows Server. 2019 nasazení napříč skupinami prostředků v rámci vašich předplatných.  Zobrazuje se v clusteru AKS zjistí, která nemonitoruje řešení. Okamžitě můžete porozumět stavu clusteru a z tohoto místa můžete potom přejít na stránku výkonu uzlu a řadič nebo přejděte na Zobrazit grafy výkonu pro cluster.  Pro zjištění a identifikuje jako nemonitorované AKS clustery můžete povolit monitorování pro tento cluster v každém okamžiku.  

Hlavní rozdíly monitorování clusteru s Windows serverem pomocí Azure monitoru pro kontejnery ve srovnání s cluster s Linuxem jsou následující:

- Metrika RSS paměti není k dispozici pro uzel Windows a kontejnery 
- Informace o kapacitě úložiště disku není k dispozici pro uzly Windows
- Živé protokoly podpora je k dispozici s výjimkou protokoly kontejneru Windows.
- Pouze pod, které jsou monitorovány prostředí, nikoli prostředí Dockeru.
- S verzí preview jsou podporované maximálně 30 kontejnery Windows serveru. Toto omezení se nevztahuje na Linuxové kontejnery.  

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusteru ze služby Azure Monitor 
Chcete-li zobrazit stav všech clusterech AKS nasadit, vyberte **monitorování** z podokna vlevo na webu Azure Portal.  V části **Insights** vyberte **kontejnery**.  

![Ukázkový řídicí panel clusteru více služby Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na **monitorovat clustery** kartu, je možné získat následující:

1. Kolik clustery jsou ve stavu kritický nebo není v pořádku, a kolik jsou v pořádku, či ne vytváření sestav (označované jako neznámém stavu)?
2. Jsou všechny moje [modul Azure Kubernetes (AKS-engine)](https://github.com/Azure/aks-engine) nasazení v pořádku?
3. Kolik uzlů, uživatele a podů systému jsou nasazené na clusteru?
4. Kolik místa je k dispozici a je nějaký problém s kapacity?

Zahrnuté stavy stavu jsou: 

* **V pořádku** – virtuální počítač se nedetekovaly žádné problémy a funguje podle potřeby. 
* **Kritické** – jeden nebo více problémů se zjistí, které je třeba řešit, aby bylo možné obnovit normální provozní stav podle očekávání.
* **Upozornění** -zjištění jeden nebo více problémů, které je třeba řešit nebo podmínku stavu může být důležité.
* **Neznámý** – Pokud službu nebylo možné navázat připojení s uzel nebo pod, stav se změní na neznámém stavu.
* **Nebyl nalezen** – jedna ze dvou pracovní prostor, skupinu prostředků nebo předplatného, které obsahuje pracovní prostor pro toto řešení je Odstraněná.
* **Neoprávněné** – uživatel nemá potřebná oprávnění ke čtení dat v pracovním prostoru.
* **Chyba** -došlo k chybě při pokusu o čtení dat z pracovního prostoru.
* **MIS nakonfigurované** – monitorování Azure pro kontejnery nebyla správně nakonfigurovaná v zadaný pracovní prostor.
* **Žádná data** -Data neohlásil do pracovního prostoru v posledních 30 minut.

Stav vypočítá celkový stav clusteru jako *nejhorší* tři stavy s jednou výjimkou – Pokud je některá ze tří stavů *neznámý*, se zobrazí celkový stav clusteru **neznámý**.  

Následující tabulka uvádí výčet výpočtu řízení stavů pro cluster monitorovaných pro zobrazení více clusteru.

| |Status |Dostupnost |  
|-------|-------|-----------------|  
|**Pod uživatele**| | |  
| |V pořádku |100% |  
| |Upozornění |90 - 99 % |  
| |Kritická |< 90 % |  
| |Neznámé |Pokud není uvedená v posledních 30 minut |  
|**Systém Pod**| | |  
| |V pořádku |100% |
| |Upozornění |neuvedeno |
| |Kritická |< 100 % |
| |Neznámé |Pokud není uvedená v posledních 30 minut |
|**Node** | | |
| |V pořádku |> 85 % |
| |Upozornění |60 - 84 % |
| |Kritická |< 60 % |
| |Neznámé |Pokud není uvedená v posledních 30 minut |

Ze seznamu clustery, můžete můžete přejít k podrobnostem **clusteru** stránku kliknutím na název clusteru, do **uzly** stránka výkonu po kliknutí na kumulativního počtu uzlů v **uzly** sloupce pro tento konkrétní cluster, nebo si zobrazte podrobné dolů na **řadiče** výkonu stránku kliknutím na kumulativní **uživatele podů** nebo **systému podů**sloupce.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Výkon zobrazení přímo z clusteru AKS
Přístup k Azure Monitor pro kontejnery je k dispozici přímo z clusteru AKS tak, že vyberete **Insights** v levém podokně. Zobrazení informací o clusteru AKS je uspořádaný do čtyř perspektiv:

- Cluster
- Uzly 
- Kontrolery  
- Containers

Výchozí stránka se otevře po kliknutí na **Insights** je **clusteru**, a zahrnuje čtyři spojnicové grafy výkonu zobrazující klíčové metriky výkonu ve vašem clusteru. 

![Příklad grafy výkonu na kartě clusteru](./media/container-insights-analyze/containers-cluster-perfview.png)

Graf výkonu zobrazí čtyři metriky výkonu:

- **Využití procesoru uzlu&nbsp;%**: Agregované perspektivy využití výkonu procesoru pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Využití paměti v aplikaci node&nbsp;%**: Agregované perspektivy využití paměti pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Počet uzlů**: Počet uzlů a stav ze Kubernetes. Stavy uzlů clusteru, který je reprezentován *všechny*, *připravené*, a *Nepřipraveno* a můžete filtrovat jednotlivě nebo kombinaci v modulu pro výběr nad grafem. 
- **Počet podů aktivit**: Počet podů a stav ze Kubernetes. Stavy podů reprezentované *všechny*, *čekající*, *systémem*, a *neznámý* a můžete filtrovat jednotlivě nebo v kombinaci selektor nad grafem. 

Klávesy šipka vlevo/vpravo můžete cyklicky procházet každý datový bod na grafu a šipka nahoru/dolů k cyklování skrze percentilu řádky.

Azure Monitor pro kontejnery také podporuje Azure Monitor [Průzkumníka metrik](../platform/metrics-getting-started.md), kde můžete vytvářet vlastní grafy diagram, korelovat a prozkoumat trendy a připínat na řídicí panely. Z Průzkumníka metrik, můžete použít také kritéria jste nastavili jako základ pro vizualizovat metriky [metriky na základě pravidel upozornění](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Kontejner zobrazení metrik v Průzkumníku metrik
V Průzkumníku metrik můžete zobrazit agregované uzlu a pod metriky využití ze služby Azure Monitor pro kontejnery. Následující tabulka shrnuje údaje, které vám pomohou pochopit, jak vizualizovat metriky kontejnerů pomocí grafy metrik.

|Obor názvů | Metrika |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | podCount |

Můžete použít [rozdělení](../platform/metrics-charts.md#apply-splitting-to-a-chart) metriky pro zobrazení podle dimenzí a vizualizaci různých segmentů porovnání k sobě navzájem. Pro uzel, můžete rozdělit graf *hostitele* dimenze, a z podu můžete segmentovat ho pomocí následujících dimenzí:

* Kontrolér
* Obor názvů Kubernetes
* Node
* Fáze

## <a name="analyze-nodes-controllers-and-container-health"></a>Analýza uzly, kontrolerů a stavu kontejneru

Když přejdete na **uzly**, **řadiče**, a **kontejnery** kartu, automaticky zobrazí na pravé straně stránky se podokno vlastností. Zobrazuje vlastnosti položky vybraná, včetně popisků definujete uspořádat objekty Kubernetes. Když je vybrán uzel systému Linux, také ukazuje části **místní kapacita disku** volného místa na disku a % využití pro všechny disky uvedené k uzlu. Klikněte na **>>** propojit v podokně view\hide podokna. 

![Podokno vlastností perspektivy příklad Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Při rozbalení objekty v hierarchii, aktualizace podokna vlastností založené na vybraný objekt. V podokně můžete také zobrazit události Kubernetes pomocí prohledávání protokolu předem definovaných kliknutím na **protokoly událostí Kubernetes zobrazení** odkazu v horní části podokna. Další informace o zobrazování dat protokolu Kubernetes najdete v tématu [vyhledávání protokolů pro analýzu dat](container-insights-log-search.md). Když kontrolujete prostředky clusteru, můžete zobrazit protokoly kontejneru a událostí v reálném čase. Další informace o této funkci a požadované konfigurace a řízení přístupu najdete v tématu [jak chcete-li zobrazit protokoly reálném čase pomocí Azure monitoru pro kontejnery](container-insights-live-logs.md). 

Použití **+ přidat filtr** možnost z horní části stránky filtrovat výsledky zobrazit podle **služby**, **uzel**, **Namespace**, nebo  **Fond uzlů** a po výběru rozsah filtru, můžete pak vyberte jednu z hodnoty zobrazené **vyberte hodnoty** pole.  Po dokončení konfigurace filtru se globálně použije při zobrazování všechny perspektivy clusteru AKS.  Vzorec podporuje pouze znaménko rovná se.  Můžete přidat další filtry na první z nich můžete dále zúžit výsledky.  Například, pokud jste zadali filtrovat podle **uzel**, druhý filtr by pouze umožňuje zvolit **služby** nebo **Namespace**.  

![Příklad pomocí filtru, který můžete zúžit výsledky](./media/container-insights-analyze/add-filter-option-01.png)

Zadáním filtru v jedné karty se i dál používá při vyberte jiný a odstraní se po kliknutí na **x** symbol vedle zadaný filtr.   

Přepněte **uzly** kartu a hierarchii řádek následuje objektový model Kubernetes, počínaje uzlu v clusteru. Rozbalte uzel a můžete zobrazit jeden nebo více podů se spuštěnou na uzlu. Pokud více než jednoho kontejneru je seskupení pod, se zobrazí jako poslední řádek v hierarchii. Můžete také zobrazit, kolik souvisejících úlohách bez pod běží na hostiteli, pokud má hostitel procesoru nebo tlaku na paměť.

![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/container-insights-analyze/containers-nodes-view.png)

Operační systém Windows Server. 2019 kontejnery Windows serveru se zobrazí po všechny uzly založené na Linuxu v seznamu. Po rozbalení uzlu Windows serveru se zobrazí jeden nebo více podů a kontejnery běží na uzlu. Když je vybrán uzel, v podokně vlastností se zobrazí informace o verzi, s výjimkou informace o agentovi, protože uzly Windows serveru nemají nainstalovaný agent.  

![Příklad uzlů hierarchie s uvedené uzly Windows serveru](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container instance virtuální uzly spuštěnými operačního systému Linux jsou uvedeny po poslední uzel clusteru AKS v seznamu.  Při rozšiřování virtuálního uzlu ACI můžete zobrazit jednu nebo více podů v ACI a kontejnery běží na uzlu.  Metriky nejsou shromážděných a nahlášených pro uzly pouze tyto pody.

![Příklad uzlu hierarchie se službou Container Instances uvedené](./media/container-insights-analyze/nodes-view-aci.png)

Z rozbalené uzly můžete procházet hierarchii z pod nebo kontejnerů běží na uzlu kontroleru zobrazení dat výkonu filtrování pro tento kontroler. Klikněte na hodnoty v rámci **řadič** sloupec pro konkrétní uzel.   
![Příklad procházení z uzlu do kontroleru v zobrazení výkonu](./media/container-insights-analyze/drill-down-node-controller.png)

Můžete vybrat, řadiče nebo kontejnery v horní části stránky a zkontrolujte stav a využití prostředků týkajících se těchto objektů.  Pokud místo toho chcete zkontrolovat využití paměti v **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **pracovní sada paměti**. **Paměť RSS** se podporuje jenom pro Kubernetes verze 1.8 a novější. V opačném případě zobrazení hodnot pro **Min&nbsp; %**  jako *NaN&nbsp;%*, má hodnotu číselný datový typ, který představuje Nedefinovaná nebo přičtení hodnotu. 

![Zobrazení výkonu uzly kontejneru](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Ve výchozím nastavení, údaje o výkonu podle posledních 6 hodin, ale můžete změnit v okně pomocí **TimeRange** možnost v levém horním rohu. Můžete také filtrovat výsledky v rámci časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentil. 

![Výběr percentilu pro filtrování dat](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Když najedete myší pruhový graf pod položkou **Trend** sloupce, každý panel ukazuje využití procesoru nebo paměti, podle toho, která je vybraná Metrika ukázka lhůtě 15 minut. Když vyberete graf trendu pomocí klávesnice, vám pomůže kláves Alt + Page Up nebo Alt + PageDown cyklicky procházet všechny jednotlivě a získat stejné informace, jako byste to zvládli mouseover.

![Trend pruhový graf při přechodu myší nad příklad](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

V následujícím příkladu, mějte na paměti pro první v seznamu - uzel *aks-nodepool1 -*, hodnota **kontejnery** je 9, což je kumulativní počet kontejnerů.

![Souhrn kontejnerů na příklad uzlu](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Pomůže vám rychle zjistit, jestli máte správnou rovnováhu mezi uzly v clusteru a kontejnerů. 

Informace, které se zobrazí při zobrazení uzlů je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název hostitele. |
| Status | Kubernetes pohled na stav uzlu. |
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Průměrné procento uzlů podle percentilu během vybrané doby trvání. |
| Avg, Min, Max, 50, 90 | Průměrná uzlů podle percentilu během tuto dobu trvání vybrané skutečnou hodnotu. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro uzel; pro podů a kontejnery je průměrná hodnotu hlášenou síťovým hostitele. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, protože uzel spuštěn nebo byl restartován. |
| Kontrolery | Pouze pro kontejnery a tyto pody. Zobrazuje kontroleru, který je umístěný v. Ne všechny podů jsou v kontroleru, takže některé se může zobrazit **není k dispozici**. | 
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Pruhový graf trendu představuje průměrný percentilu metrik procento kontroleru. |

V modulu pro výběr, vyberte **řadiče**.

![Vyberte kontrolerů zobrazení](./media/container-insights-analyze/containers-controllers-tab.png)

Zde můžete zobrazit stav výkonu řadiče a řadiče ACI virtuálního uzlu nebo podů virtuální uzel není připojen k řadiči.

![zobrazení výkonu řadiče < název >](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchie řádek začíná s řadičem a když rozšiřujete kontroleru, zobrazení podů jeden nebo více.  Rozbalte pod a poslední řádek zobrazuje seskupené pod celému kontejneru. Z rozšířené řadiče můžete přejít na uzlu, na který je spuštěn na zobrazení dat výkonu filtrování pro tento uzel. ACI podů bez připojení k řadiči poslední uvedených v seznamu.

![Příklad hierarchie řadiče s uvedené podů Container Instances](./media/container-insights-analyze/controllers-view-aci.png)

Klikněte na hodnoty v rámci **uzel** sloupec pro konkrétní ovladač.   

![Příklad podrobnostem z uzlu do kontroleru v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí, když zobrazujete řadiče je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Souhrn stavu kontejnerů po dokončení spuštění se stavem, jako například *OK*, *ukončeno*, *neúspěšné* *Zastaveno*, nebo *Pozastaveno*. Pokud je kontejner spuštěný, ale stav byl buď není správně zobrazeny nebo nebyl vyzvednou agenta a neodpověděl více než 30 minut, stav je *neznámý*. V následující tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Souhrnné průměrem průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Shrnutí průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartuje se | Shrnutí počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Node | Pouze pro kontejnery a tyto pody. Ukazuje kontroler, který je umístěný. | 
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;%| Pruhový graf trendu představuje metrika průměrné percentilu kontroleru. |

Ikony v poli Stav označují online stav kontejnerů:
 
| Ikona | Status | 
|--------|-------------|
| ![Připraveno spuštěné ikona stavu](./media/container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|
| ![Ikona čekání nebo pozastaveném stavu](./media/container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|
| ![Naposledy hlásila systémem ikona stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá více než 30 minut|
| ![Ikona úspěšný stav](./media/container-insights-analyze/containers-green-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|

Ikona stavu zobrazuje počet založené na co chcete pod poskytuje. Zobrazuje nejhorší dvou stavů, a když najedete myší stav, zobrazuje souhrn stavu ze všech podů v kontejneru. Pokud není k dispozici stavu Připraveno, hodnota stavu zobrazí **(0)**. 

V modulu pro výběr, vyberte **kontejnery**.

![Vyberte kontejnery zobrazení](./media/container-insights-analyze/containers-containers-tab.png)

Zde můžete zobrazit stav výkonu kontejnerů Kubernetes v Azure a Azure Container Instances.  

![zobrazení výkonu řadiče < název >](./media/container-insights-analyze/containers-containers-view.png)

Z kontejneru procházením hierarchie můžete pod nebo uzel k zobrazení dat výkonu filtrování pro daný objekt. Klikněte na hodnoty v rámci **Pod** nebo **uzel** sloupec pro konkrétní kontejner.   

![Příklad podrobnostem z uzlu do kontroleru v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí, když zobrazujete kontejnery je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav kontejnerů, pokud existuje. V další tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Souhrn průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Souhrn průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Pod | Kontejner, ve které se nachází pod.| 
| Node |  Uzel, ve které se nachází kontejneru. | 
| Restartování | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, protože kontejneru se spustit nebo restartovat. |
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Pruhový graf trendu představuje průměrný percentilu metrik procento kontejneru. |

Ikony v poli Stav označují online stavy podů, jak je popsáno v následující tabulce:
 
| Ikona | Status |  
|--------|-------------|  
| ![Připraveno spuštěné ikona stavu](./media/container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|  
| ![Ikona čekání nebo pozastaveném stavu](./media/container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|  
| ![Naposledy hlásila systémem ikona stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá za více než 30 minut|  
| ![Ikona stavu ukončení](./media/container-insights-analyze/containers-terminated-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|  
| ![Ikona stavu se nezdařilo](./media/container-insights-analyze/containers-failed-icon.png) | Chybovém stavu |  

## <a name="next-steps"></a>Další postup
- Zkontrolujte [vytvoření výstrahy související s výkonem pomocí Azure monitoru pro kontejnery](container-insights-alerts.md) se naučíte vytvářet upozornění na vysoké využití procesoru a paměti pro podporu DevOps nebo provozní procesy a postupy. 
- Zobrazení [protokolu Příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) předem definovaných dotazů a příklady, které vyhodnotí nebo přizpůsobení pro výstrahy vizualizace a analýza vašich clusterů.
