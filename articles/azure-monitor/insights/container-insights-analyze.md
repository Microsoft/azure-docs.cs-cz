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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: f0f929e7caece9bea10dbe09e237bc987ad93d44
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159651"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Porozumět výkonu cluster AKS pomocí Azure monitoru pro kontejnery 
Díky Azure monitoru pro kontejnery můžete použít grafy výkonu a stavu ke sledování těchto úloh své clustery Azure Kubernetes Service (AKS) za dvou hledisek, přímo z clusteru AKS nebo všechny AKS clusterů v rámci předplatného Azure Monitorování. Zobrazení služby Azure Container Instances (ACI) je také možné, při sledování konkrétní clusteru AKS.

Tento článek vám pomůže pochopit prostředí mezi dvěma perspektivy a jak vám může pomoct rychle posoudit, prozkoumat a vyřešit problémy zjištěné.

Informace o povolení monitorování Azure pro kontejnery, naleznete v tématu [připojení Azure Monitor pro kontejnery](container-insights-onboard.md).

Azure Monitor nabízí více clusteru zobrazení, které zobrazí stav všech monitorovaných AKS clusterech nasazených napříč skupinami prostředků v rámci vašich předplatných.  Zobrazuje se v clusteru AKS zjistí, která nemonitoruje řešení. Okamžitě můžete porozumět stavu clusteru a z tohoto místa můžete potom přejít na stránku výkonu uzlu a řadič nebo přejděte na Zobrazit grafy výkonu pro cluster.  Pro zjištění a identifikuje jako nemonitorované AKS clustery můžete povolit monitorování pro tento cluster v každém okamžiku.  

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusteru ze služby Azure Monitor 
Chcete-li zobrazit stav všech clusterech AKS nasadit, vyberte **monitorování** z podokna vlevo na webu Azure Portal.  V části **Insights** vyberte **kontejnery**.  

![Ukázkový řídicí panel clusteru více služby Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview-1018.png)

Na **monitorovat clustery** kartu, je možné získat následující:

1. Kolik clustery jsou ve stavu kritický nebo není v pořádku, a kolik jsou v pořádku, či ne vytváření sestav (označované jako neznámém stavu)?
1. Jsou všechny moje [modul Azure Kubernetes (AKS-engine)](https://github.com/Azure/aks-engine) nasazení v pořádku?
1. Kolik uzlů, uživatele a podů systému jsou nasazené na clusteru.  

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

Když přejdete na **uzly**, **řadiče**, a **kontejnery** kartu, automaticky zobrazí na pravé straně stránky se podokno vlastností.  Zobrazuje vlastnosti položky vybrané, včetně popisků definujete uspořádat objekty Kubernetes. Klikněte na **>>** propojit v podokně view\hide podokna.  

![Podokno vlastností perspektivy příklad Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Při rozbalení objekty v hierarchii, aktualizace podokna vlastností založené na vybraný objekt. V podokně můžete také zobrazit události Kubernetes pomocí prohledávání protokolu předem definovaných kliknutím na **protokoly událostí Kubernetes zobrazení** odkazu v horní části podokna. Další informace o zobrazování dat protokolu Kubernetes najdete v tématu [vyhledávání protokolů pro analýzu dat](#search-logs-to-analyze-data). Když kontrolujete kontejnery v **kontejnery** zobrazení, můžete zobrazit protokoly kontejneru v reálném čase. Další informace o této funkci a požadované konfigurace a řízení přístupu najdete v tématu [zobrazení kontejneru protokoly reálném čase pomocí Azure monitoru pro kontejnery](container-insights-live-logs.md). 

Použití **+ přidat filtr** možnost z horní části stránky filtrovat výsledky zobrazit podle **služby**, **uzel**, nebo **Namespace** a po provedení Výběr oboru filtru, potom vyberete jednu z hodnoty zobrazené v **vyberte hodnoty** pole.  Po dokončení konfigurace filtru se globálně použije při zobrazování všechny perspektivy clusteru AKS.  Vzorec podporuje pouze znaménko rovná se.  Můžete přidat další filtry na první z nich můžete dále zúžit výsledky.  Například, pokud jste zadali filtrovat podle **uzel**, druhý filtr by pouze umožňuje zvolit **služby** nebo **Namespace**.  

![Příklad pomocí filtru, který můžete zúžit výsledky](./media/container-insights-analyze/add-filter-option-01.png)

Zadáním filtru v jedné karty se i dál používá při vyberte jiný a odstraní se po kliknutí na **x** symbol vedle zadaný filtr.   

Přepněte **uzly** kartu a hierarchii řádek následuje objektový model Kubernetes, počínaje uzlu v clusteru. Rozbalte uzel a můžete zobrazit jeden nebo více podů se spuštěnou na uzlu. Pokud více než jednoho kontejneru je seskupení pod, se zobrazí jako poslední řádek v hierarchii. Můžete také zobrazit, kolik souvisejících úlohách bez pod běží na hostiteli, pokud má hostitel procesoru nebo tlaku na paměť.

![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/container-insights-analyze/containers-nodes-view.png)

Azure Container instance virtuální uzly spuštěnými operačního systému Linux jsou uvedeny po poslední uzel clusteru AKS v seznamu.  Při rozšiřování virtuálního uzlu ACI můžete zobrazit jednu nebo více podů v ACI a kontejnery běží na uzlu.  Metriky nejsou shromážděných a nahlášených pro uzly pouze tyto pody.

![Příklad uzlu hierarchie se službou Container Instances uvedené](./media/container-insights-analyze/nodes-view-aci.png)

Z rozbalené uzly můžete procházet hierarchii z pod nebo kontejnerů běží na uzlu kontroleru zobrazení dat výkonu filtrování pro tento kontroler. Klikněte na hodnoty v rámci **řadič** sloupec pro konkrétní uzel.   
![Příklad procházení z uzlu do kontroleru v zobrazení výkonu](./media/container-insights-analyze/drill-down-node-controller.png)

Můžete vybrat, řadiče nebo kontejnery v horní části stránky a zkontrolujte stav a využití prostředků týkajících se těchto objektů.  Pokud místo toho chcete zkontrolovat využití paměti v **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **pracovní sada paměti**. **Paměť RSS** se podporuje jenom pro Kubernetes verze 1.8 a novější. V opačném případě zobrazení hodnot pro **Min&nbsp; %**  jako *NaN&nbsp;%*, má hodnotu číselný datový typ, který představuje Nedefinovaná nebo přičtení hodnotu. 

![Zobrazení výkonu uzly kontejneru](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Ve výchozím nastavení, údaje o výkonu podle posledních 6 hodin, ale můžete změnit v okně pomocí **TimeRange** možnost v levém horním rohu. Můžete také filtrovat výsledky v rámci časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentil. 

![Výběr percentilu pro filtrování dat](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Když najedete myší pruhový graf pod položkou **Trend** sloupce, každý panel ukazuje využití procesoru nebo paměti, podle toho, která je vybraná Metrika ukázka lhůtě 15 minut. Když vyberete graf trendu pomocí klávesnice, vám pomůže kláves Alt + Page Up nebo Alt + PageDown cyklicky procházet všechny jednotlivě a získat stejné informace, jako byste to zvládli mouseover.

![Trend panelu příklad graf při přechodu myší](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

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
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Souhrn průměrem průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Souhrn průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartování | Souhrn počtu restartování z kontejnerů. |
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

![Analýza dat v Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

Výstup protokoly kontejneru, který se předávají do Log Analytics jsou STDOUT a STDERR. Protože Azure Monitor je monitorování Azure managed Kubernetes (AKS), Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "cpuUsageNanoCores" &#124; shrnout AvgCPUUsageNanoCores = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **Vyberte možnosti spojnicový graf zobrazení**:<br> Výkonu<br> &#124;kde ObjectName == "K8SContainer" a hodnota CounterName == "memoryRssBytes" &#124; shrnout AvgUsedRssMemoryBytes = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |

## <a name="alerting"></a>Zobrazení výstrah
Azure Monitor pro kontejnery nezahrnuje předdefinovanou sadu výstrah, které můžete zkopírovat a upravit podle vašich podpůrné procesy a postupy. Do té doby, podívejte se na [vytvoření upozornění protokolu službou Azure Monitor](../../azure-monitor/platform/alerts-log.md?toc=/azure/azure-monitor/toc.json) a zjistěte, jak vytvořit vlastní sadu výstrah.  
