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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: 6df7d42bc291713a815cac9f719f53136ed35b19
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956651"
---
## <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Porozumět výkonu cluster AKS pomocí Azure monitoru pro kontejnery
Zobrazení výkonu z vaší Azure Kubernetes Service (AKS) clustery mohou být dodržen za dvou hledisek službou Azure Monitor pro kontejnery, přímo z clusteru AKS nebo zobrazení všech clusterech AKS v rámci předplatného Azure monitor. 

Tento článek vám pomůže pochopit prostředí mezi dvěma perspektivy a tom, jak rychle posoudit, prozkoumat a vyřešit problémy zjištěné.

Informace o povolení monitorování Azure pro kontejnery, naleznete v tématu [připojení Azure Monitor pro kontejnery](monitoring-container-insights-onboard.md).

Azure Monitor nabízí více clusteru zobrazení, které zobrazí stav všech monitorovaných AKS clusterech nasazených napříč skupinami prostředků ve vašem předplatném a AKS clustery nemonitorováno řešení.  Okamžitě můžete porozumět stavu clusteru a odsud můžete přejít na stránce výkon uzlu a kontroler, nebo můžete přejít na další grafy výkonu pro cluster.  Pro zjištění a identifikuje jako nemonitorované AKS clustery můžete zvolit, pokud chcete povolit monitorování pro tento cluster v každém okamžiku.  

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusteru ze služby Azure Monitor 
Chcete-li zobrazit stav všech clusterech AKS nasadit, vyberte **monitorování** z podokna vlevo na webu Azure Portal.  V části **Insights** vyberte **kontejnery (preview)**.  

![Příklad řídicího panelu více clusteru Azure Monitor](./media/monitoring-container-insights-analyze/azmon-containers-multiview.png)

Na **monitorovat clustery** kartu, je možné získat následující:

1. Kolik clustery jsou ve stavu kritický nebo není v pořádku, a kolik jsou v pořádku, či ne vytváření sestav (označované jako neznámém stavu)?
2. Kolik uzlů, uživatele a podů systému jsou nasazené na clusteru.  

Stavy definované jsou: 

1. **V pořádku** – virtuální počítač se nedetekovaly žádné problémy a funguje podle potřeby. 
2. **Kritické** – jeden nebo více problémů se zjistí, které je třeba řešit, aby bylo možné obnovit normální provozní stav podle očekávání.
3. **Upozornění** -zjištění jeden nebo více problémů, které je třeba řešit nebo podmínku stavu může být důležité.
4. **Neznámý** – Pokud službu nebylo možné navázat připojení s uzel nebo pod, stav se změní na neznámém stavu.

Stav vypočítá celkový stav clusteru jako *nejhorší*"tři stavy s jednou výjimkou – Pokud je některá ze tří stavů *neznámý*, se zobrazí celkový stav clusteru **neznámý**.  

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

Ze seznamu clustery, můžete můžete přejít k **clusteru** stránku kliknutím na název clusteru, do **uzly** stránka výkonu po kliknutí na kumulativního počtu uzlů v **uzly** sloupec pro tento konkrétní cluster nebo procházení k **řadiče** výkonu stránku kliknutím na kumulativní **uživatele podů** nebo **systému podů**sloupce.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Výkon zobrazení přímo z clusteru AKS
Přístup k Azure Monitor pro kontejnery je k dispozici přímo z clusteru AKS tak, že vyberete **Insights (preview)** v levém podokně. Zobrazení informací o clusteru AKS je uspořádaný do čtyř perspektiv:

- Cluster
- Uzly 
- Kontrolery  
- Containers

Výchozí stránka se otevře po kliknutí na **Insights (preview)** je **clusteru**, a zahrnuje čtyři spojnicové grafy výkonu zobrazující klíčové metriky výkonu ve vašem clusteru. 

![Příklad grafy výkonu na kartě clusteru](./media/monitoring-container-insights-analyze/containers-cluster-perfview.png)

Graf výkonu zobrazí čtyři metriky výkonu:

- **Využití procesoru uzlu&nbsp;%**: agregovaná perspektivy využití výkonu procesoru pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Využití paměti v aplikaci node&nbsp;%**: agregovaná perspektivy využití paměti pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Počet uzlů**: počet uzlů a stav ze Kubernetes. Stavy uzlů clusteru, který je reprezentován *všechny*, *připravené*, a *Nepřipraveno* a můžete filtrovat jednotlivě nebo kombinaci v modulu pro výběr nad grafem. 
- **Počet podů aktivit**: počet podů a stav ze Kubernetes. Stavy podů reprezentované *všechny*, *čekající*, *systémem*, a *neznámý* a můžete filtrovat jednotlivě nebo v kombinaci selektor nad grafem. 

Když přejdete na **uzly**, **řadiče**, a **kontejnery** kartu, automaticky zobrazí na pravé straně stránky se podokno vlastností.  Zobrazuje vlastnosti položky vybrané, včetně popisků definujete uspořádat objekty Kubernetes. Klikněte na **>>** propojit v podokně view\hide podokna.  

![Podokno vlastností perspektivy příklad Kubernetes](./media/monitoring-container-insights-analyze/perspectives-preview-pane-01.png)

Při rozbalení objekty v hierarchii, aktualizace podokna vlastností založené na vybraný objekt. V podokně můžete také zobrazit události Kubernetes pomocí prohledávání protokolu předem definovaných kliknutím na **protokoly událostí Kubernetes zobrazení** odkazu v horní části podokna. Další informace o zobrazování dat protokolu Kubernetes najdete v tématu [vyhledávání protokolů pro analýzu dat](#search-logs-to-analyze-data).

Použití **+ přidat filtr** možnost z horní části stránky filtrovat výsledky zobrazit podle **služby**, **uzel**, nebo **Namespace** a po provedení výběru obor filtru se pak vyberete jednu z hodnot je znázorněno **vyberte hodnoty.** pole.  Po dokončení konfigurace filtru se globálně použije při zobrazování všechny perspektivy clusteru AKS.  Vzorec podporuje pouze znaménko rovná se.  Můžete přidat další filtry na první z nich můžete dále zúžit výsledky.  Například, pokud jste zadali filtrovat podle **uzel**, druhý filtr by pouze umožňuje zvolit **služby** nebo **Namespace**.  

![Příklad pomocí filtru, který můžete zúžit výsledky](./media/monitoring-container-insights-analyze/add-filter-option-01.png)

Zadáním filtru v jedné karty se i dál používá při vyberte jiný a odstraní se po kliknutí na **x** symbol vedle zadaný filtr.   

Přepněte **uzly** kartu a hierarchii řádek následuje objektový model Kubernetes, počínaje uzlu v clusteru. Rozbalte uzel a můžete zobrazit jeden nebo více podů se spuštěnou na uzlu. Pokud více než jednoho kontejneru je seskupení pod, se zobrazí jako poslední řádek v hierarchii. Můžete také zobrazit, kolik souvisejících úlohách bez pod běží na hostiteli, pokud má hostitel procesoru nebo tlaku na paměť.

![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/monitoring-container-insights-analyze/containers-nodes-view.png)

Můžete vybrat, řadiče nebo kontejnery v horní části stránky a zkontrolujte stav a využití prostředků týkajících se těchto objektů.  Pokud místo toho chcete zkontrolovat využití paměti v **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **pracovní sada paměti**. **Paměť RSS** se podporuje jenom pro Kubernetes verze 1.8 a novější. V opačném případě zobrazení hodnot pro **Min&nbsp; %**  jako *NaN&nbsp;%*, má hodnotu číselný datový typ, který představuje Nedefinovaná nebo přičtení hodnotu. 

![Zobrazení výkonu uzly kontejneru](./media/monitoring-container-insights-analyze/containers-node-metric-dropdown.png)

Ve výchozím nastavení, údaje o výkonu podle posledních 6 hodin, ale můžete změnit v okně pomocí **TimeRange** možnost v levém horním rohu. Můžete také filtrovat výsledky v rámci časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentil. 

![Výběr percentilu pro filtrování dat](./media/monitoring-container-insights-analyze/containers-metric-percentile-filter.png)

V následujícím příkladu si všimněte pro uzel *aks-nodepool1 -*, hodnota **kontejnery** je 14, což je souhrnné celkový počet kontejnerů.

![Souhrn kontejnerů na příklad uzlu](./media/monitoring-container-insights-analyze/containers-nodes-containerstotal.png)

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
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Pruhový graf trendu nabízí ten samý percentilu metrik procento kontroleru. |

V modulu pro výběr, vyberte **řadiče**.

![Vyberte kontrolerů zobrazení](./media/monitoring-container-insights-analyze/containers-controllers-tab.png)

Zde můžete zobrazit stav výkonu řadiče.

![zobrazení výkonu řadiče < název >](./media/monitoring-container-insights-analyze/containers-controllers-view.png)

Hierarchie řádek začíná kontroleru a rozbalí kontroleru. Můžete zobrazit jeden nebo více kontejnerů. Rozbalte pod a poslední řádek zobrazuje seskupené pod celému kontejneru.  

Informace, které se zobrazí, když zobrazujete řadiče je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Shrnutí stavu kontejnerů po dokončení spuštění se stavem, jako například *OK*, *ukončeno*, *neúspěšné* *Zastaveno*, nebo *Pozastaveno*. Pokud je kontejner spuštěný, ale stav byl buď není správně zobrazeny nebo nebyl vyzvednou agenta a neodpověděl více než 30 minut, stav je *neznámý*. V následující tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Souhrn průměrem průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Shrnující průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartování | Souhrn počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Node | Pouze pro kontejnery a tyto pody. Ukazuje kontroler, který je umístěný. | 
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;%| Pruhový graf trendu představující percentilu metrik kontroleru. |

Ikony v poli Stav označují online stav kontejnerů:
 
| Ikona | Status | 
|--------|-------------|
| ![Připraveno spuštěné ikona stavu](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|
| ![Ikona čekání nebo pozastaveném stavu](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|
| ![Naposledy hlásila systémem ikona stavu](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá více než 30 minut|
| ![Ikona úspěšný stav](./media/monitoring-container-insights-analyze/containers-green-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|

Ikona stavu zobrazuje počet založené na co chcete pod poskytuje. Zobrazuje nejhorší dvou stavů, a když najedete myší stav, zobrazí shrnutí stavu ze všech podů v kontejneru. Pokud není k dispozici stavu Připraveno, hodnota stavu zobrazí **(0)**. 

V modulu pro výběr, vyberte **kontejnery**.

![Vyberte kontejnery zobrazení](./media/monitoring-container-insights-analyze/containers-containers-tab.png)

Zde můžete zobrazit stav výkonu kontejnerů Kubernetes v Azure.  

![zobrazení výkonu řadiče < název >](./media/monitoring-container-insights-analyze/containers-containers-view.png)

Informace, které se zobrazí, když zobrazujete kontejnery je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav kontejnerů, pokud existuje. V další tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Kumulativní z průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Kumulativní průměrné využití procesoru millicore nebo paměti výkonu kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Pod | Kontejner, ve které se nachází pod.| 
| Node |  Uzel, ve které se nachází kontejneru. | 
| Restartování | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, protože kontejneru se spustit nebo restartovat. |
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Trend pruhový graf, který představuje průměrnou procentuální hodnotu metriky kontejneru. |

Ikony v poli Stav označují online stavy podů, jak je popsáno v následující tabulce:
 
| Ikona | Status |  
|--------|-------------|  
| ![Připraveno spuštěné ikona stavu](./media/monitoring-container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|  
| ![Ikona čekání nebo pozastaveném stavu](./media/monitoring-container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|  
| ![Naposledy hlásila systémem ikona stavu](./media/monitoring-container-insights-analyze/containers-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá za více než 30 minut|  
| ![Ikona stavu ukončení](./media/monitoring-container-insights-analyze/containers-terminated-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|  
| ![Ikona stavu se nezdařilo](./media/monitoring-container-insights-analyze/containers-failed-icon.png) | Chybovém stavu |  

## <a name="container-data-collection-details"></a>Podrobnosti o kontejneru shromažďování dat
Přehledy o kontejnerech různých metrik a protokolů shromažďuje údaje o výkonu z hostitelů kontejnerů a kontejnery. Data jsou shromažďována každé 3 minuty.

### <a name="container-records"></a>Záznamy kontejneru

Příklady záznamů, které byly shromážděny sadou monitorování Azure pro kontejnery a datové typy, které se zobrazí ve výsledcích hledání protokolů, které jsou zobrazeny v následující tabulce:

| Typ dat | Datový typ v prohledávání protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítače, název_objektu, CounterName &#40;% času procesoru, disku přečte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, síť posílat bajtů, procesor doby využití, sítě&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ukončovací kód, EnvironmentVar, příkaz, čas vytvoření, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Inventář imagí kontejnerů | `ContainerImageInventory` | TimeGenerated, počítače, Image, ImageTag, ImageSize, VirtualSize, spuštění, pozastavení, zastavení, se nezdařilo, SourceSystem, ID obrázku, TotalContainer |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource LogEntry, SourceSystem, identifikátor ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, počítače, TimeOfCommand, Image, příkaz, SourceSystem, identifikátor ContainerID |
| Inventář kontejnerových uzlů | `ContainerNodeInventory_CL`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kontejnerový proces | `ContainerProcess_CL` | TimeGenerated, počítače, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventář podů v clusteru Kubernetes | `KubePodInventory` | TimeGenerated, počítače, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, parametr ControllerName, ContainerStatus, Identifikátor ContainerID, ContainerName, název, PodLabel, Namespace, PodStatus, název clusteru, PodIp, SourceSystem |
| Seznam uzlů nepatří do clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, počítače, název clusteru, ClusterId, LastTransitionTimeReady, popisky, stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | `KubeEvents_CL` | TimeGenerated, počítače, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zprávy, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SNode" | Počítače, název_objektu, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec cpuAllocatableNanoCores, memoryAllocatableBytes cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem | 
| Metriky výkonu pro kontejnery jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SContainer" | Hodnota counterName &#40;cpuUsageNanoCores memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, Cesta_k_čítači, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Hledání protokolů pro analýzu dat
Log Analytics můžete hledat trendy, diagnostikovat problémových míst, předpovědi nebo korelovat data, která vám může pomoct určit, zda je aktuální konfiguraci clusteru optimální výkon. Prohledávání protokolů předem definovaných jsou k dispozici pro vás okamžitě začít využívat nebo přizpůsobit vrátit informace způsobem, jaký požadujete. 

Můžete provádět interaktivní analýzu dat v pracovním prostoru tak, že vyberete **protokoly událostí Kubernetes zobrazení** nebo **zobrazit protokoly kontejneru** možnost v podokně náhledu. **Prohledávání protokolů** pravé části stránky Azure portal, který jste byli, zobrazí se stránka.

![Analýza dat v Log Analytics](./media/monitoring-container-insights-analyze/container-health-log-search-example.png)   

Výstup protokoly kontejneru, který se předávají do Log Analytics jsou STDOUT a STDERR. Protože Azure Monitor je monitorování Azure managed Kubernetes (AKS), Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Perf<br> &#124;kde ObjectName == "Kontejneru" a hodnota CounterName == "čas procesoru v %"<br> &#124;shrnutí AvgCPUPercent = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Perf &#124; kde ObjectName == "Kontejneru" a hodnota CounterName == "MB využití paměti"<br> &#124;shrnutí AvgUsedMemory = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |
