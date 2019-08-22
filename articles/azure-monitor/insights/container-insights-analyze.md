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
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 154848c33960cb78b10c58e7a39ddec669d4fae0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872994"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Porozumět výkonu cluster AKS pomocí Azure monitoru pro kontejnery
Díky Azure Monitor pro kontejnery můžete pomocí grafů výkonu a stavu monitorovat zatížení clusterů Azure Kubernetes Service (AKS) ze dvou perspektiv. Můžete monitorovat přímo z clusteru AKS nebo můžete monitorovat všechny clustery AKS v rámci předplatného z Azure Monitor. Zobrazení Azure Container Instances je také možné při monitorování konkrétního clusteru AKS.

Tento článek vám pomůže pochopit dvě perspektivy a jak Azure Monitor vám pomůže rychle posoudit, prozkoumat a vyřešit zjištěné problémy.

Informace o tom, jak povolit Azure Monitor pro kontejnery, najdete v tématu připojení [Azure monitor kontejnerů](container-insights-onboard.md).

Azure Monitor poskytuje zobrazení více clusterů, které zobrazuje stav všech monitorovaných clusterů AKS se systémem Linux a Windows Server 2019 nasazených ve skupinách prostředků ve vašich předplatných. Zobrazuje clustery AKS zjištěné, které řešení nesleduje. Stav clusteru můžete okamžitě pochopit a z tohoto místa můžete přejít k podrobnostem na stránce s výkonem uzlů a kontroléru nebo přejít k zobrazení grafů výkonu pro cluster. U clusterů AKS, které byly zjištěny a identifikovány jako nemonitorované, můžete kdykoli povolit monitorování. 

Hlavní rozdíly v monitorování clusteru Windows serveru s Azure Monitor pro kontejnery v porovnání s clusterem Linux jsou následující:

- Metrika RSS paměti není k dispozici pro uzly a kontejnery Windows.
- Informace o kapacitě diskového úložiště nejsou k dispozici pro uzly Windows.
- Podpora živých protokolů je k dispozici s výjimkou protokolů kontejnerů Windows.
- Monitoruje se jenom pod prostředími, nikoli Docká prostředí.
- Ve verzi Preview se podporuje maximálně 30 kontejnerů Windows serveru. Toto omezení se nevztahuje na kontejnery Linux. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusteru ze služby Azure Monitor

Pokud chcete zobrazit stav všech nasazených AKS clusterů, vyberte **monitor** v levém podokně Azure Portal. V části **Insights** vyberte **kontejnery**. 

![Ukázkový řídicí panel clusteru více služby Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Na kartě **monitorované clustery** se dozvíte následující:

- Počet clusterů v kritickém nebo nesprávném stavu, v jakém jsou v pořádku nebo nikoli hlášení (označované jako neznámý stav).
- Zda jsou všechna nasazení [modulu Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) v dobrém stavu.
- Kolik uzlů a systémových prostředí je nasazeno na cluster.
- Kolik místa na disku je k dispozici a pokud dojde k potížím s kapacitou.

Zahrnuté stavy stavu jsou: 

* **V pořádku**: Pro virtuální počítač se nezjistily žádné problémy a funguje podle potřeby. 
* **Kritické**: Zjistili jsme jeden nebo několik kritických problémů, které je potřeba vyřešit tak, aby se v normálním provozním stavu obnovila podle očekávání.
* **Upozornění**: Zjistil se jeden nebo víc problémů, které je potřeba vyřešit, nebo se může stát kritickým stavem.
* **Neznámý**: Pokud by služba nemohla vytvořit spojení s uzlem nebo pod, stav se změní na neznámý stav.
* Nenalezeno: Odstranil se pracovní prostor, skupina prostředků nebo předplatné obsahující pracovní prostor pro toto řešení.
* Neautorizováno: Uživatel nemá požadovaná oprávnění ke čtení dat v pracovním prostoru.
* **Chyba:** Při pokusu o čtení dat z pracovního prostoru došlo k chybě.
* **Nesprávně nakonfigurované**: V zadaném pracovním prostoru nebyla správně nakonfigurovaná Azure Monitor pro kontejnery.
* **Žádná data:** Data nebyla v pracovním prostoru hlášena za posledních 30 minut.

Služba Health (stav) počítá celkový stav clusteru jako *nejhorší ze* tří stavů s jednou výjimkou. Pokud některý ze tří stavů není znám, celkový stav clusteru zobrazuje **Neznámý**. 

Následující tabulka uvádí rozpis výpočtu, který řídí stav pro monitorovaný cluster v zobrazení více clusterů.

| |Stav |Dostupnost |  
|-------|-------|-----------------|  
|**Uživatel pod**| | |  
| |V pořádku |100% |  
| |Upozornění |90 - 99 % |  
| |Kritická |< 90 % |  
| |Neznámé |Pokud není uvedená v posledních 30 minut |  
|**Systém pod**| | |  
| |V pořádku |100% |
| |Upozornění |neuvedeno |
| |Kritická |< 100 % |
| |Neznámé |Pokud není uvedená v posledních 30 minut |
|**Node** | | |
| |V pořádku |> 85 % |
| |Upozornění |60 - 84 % |
| |Kritická |< 60 % |
| |Neznámé |Pokud není uvedená v posledních 30 minut |

V seznamu clusterů můžete přejít na stránku **clusteru** tak, že vyberete název clusteru. Pak přejdete na stránku výkon **uzlů** , a to tak, že vyberete souhrn uzlů ve sloupci **uzly** tohoto konkrétního clusteru. Případně můžete přejít k podrobnostem na stránce s výkonem **řadičů** , a to výběrem souhrnu sloupce **uživatelské lusky** nebo **Systémová lusky** .  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Výkon zobrazení přímo z clusteru AKS

Přístup k Azure Monitor pro kontejnery je k dispozici přímo z clusteru AKS, a to tak, že v levém podokně vyberete přehledy. Informace o clusteru AKS jsou uspořádány do čtyř perspektiv:

- Cluster
- Uzly 
- Kontrolery 
- Containers

Výchozí stránka se otevře, když vyberete**cluster** **Insights** > . Čtyři řádky grafu výkonu zobrazují klíčové metriky výkonu vašeho clusteru. 

![Příklad grafy výkonu na kartě clusteru](./media/container-insights-analyze/containers-cluster-perfview.png)

Grafy výkonu zobrazují čtyři metriky výkonu:

- **Využití&nbsp;procesoruuzlem:%** Agregovaná perspektiva využití procesoru pro celý cluster. Pokud chcete filtrovat výsledky pro časový rozsah, vyberte v selektoru percentilu nad grafem hodnotu **AVG**, **min**, **50**, **devadesát**, **95.** nebo **Max** . Filtry lze použít buď jednotlivě, nebo v kombinaci. 
- **Využití&nbsp;pamětiuzlu%** : Agregovaná perspektiva využití paměti pro celý cluster. Pokud chcete filtrovat výsledky pro časový rozsah, vyberte v selektoru percentilu nad grafem hodnotu **AVG**, **min**, **50**, **devadesát**, **95.** nebo **Max** . Filtry lze použít buď jednotlivě, nebo v kombinaci. 
- **Počet uzlů**: Počet uzlů a stav z Kubernetes. Stavy clusterů, které jsou reprezentovány, jsou celkem, připravené a nejsou připravené. Je možné je filtrovat individuálně nebo kombinovat v selektoru nad grafem. 
- **Počet aktivních pod**: Počet pod a stav z Kubernetes. Stavy, které jsou reprezentovány, jsou celkem, čeká, spuštěno, neznámý, úspěch nebo selhání. Je možné je filtrovat individuálně nebo kombinovat v selektoru nad grafem. 

Pomocí šipek vlevo a vpravo můžete cyklicky přepínat mezi jednotlivými datovými body v grafu. Pomocí šipek nahoru a dolů můžete cyklicky procházet řádky percentilu. Vyberte ikonu připnutí v pravém horním rohu libovolného grafu, abyste mohli vybraný graf připnout na poslední prohlížený řídicí panel Azure. Z řídicího panelu můžete změnit velikost grafu nebo změnit jeho umístění. Výběr grafu z řídicího panelu vás přesměruje na Azure Monitor pro kontejnery a načte správný rozsah a zobrazení.

Azure Monitor for Containers také podporuje [Průzkumníka metrik](../platform/metrics-getting-started.md)Azure monitor, kde můžete vytvářet vlastní diagramy, sladit a prozkoumat trendy a připnout je na řídicí panely. Z Průzkumníka metrik můžete také použít kritéria, která jste nastavili k vizualizaci metrik, jako základ pro [pravidlo upozornění založené na metrikách](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Zobrazit metriky kontejneru v Průzkumníkovi metrik

V Průzkumníku metrik můžete zobrazit agregované metriky využití uzlů a pod Azure Monitor pro kontejnery. Následující tabulka shrnuje podrobnosti, které vám pomůžou pochopit, jak používat grafy metrik k vizualizaci metrik kontejnerů.

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
| | PodCount |

Můžete [rozdělit](../platform/metrics-charts.md#apply-splitting-to-a-chart) metriku tak, aby se zobrazila podle dimenze, a vizualizovat, jak vzájemně porovnávají různé segmenty. Pro uzel můžete graf rozdělit podle dimenze *hostitele* . Z pod pod můžete segmentovat podle následujících dimenzí:

* Kontrolér
* Obor názvů Kubernetes
* Uzel
* Fáze

## <a name="analyze-nodes-controllers-and-container-health"></a>Analýza stavů uzlů, řadičů a kontejnerů

Po přepnutí na karty **uzly**, **řadiče**a **kontejnery** se automaticky zobrazí podokno vlastností na pravé straně stránky. Zobrazuje vlastnosti vybrané položky, včetně popisků, které jste definovali pro uspořádání objektů Kubernetes. Když je vybrán uzel Linux, v části **kapacita místního disku** se zobrazí také dostupné místo na disku a procento využité pro každý disk prezentovaný uzlu. **>>** Vyberte odkaz v podokně pro zobrazení nebo skrytí podokna.

![Příklad podoken vlastností perspektiv Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Při rozbalení objekty v hierarchii, aktualizace podokna vlastností založené na vybraný objekt. V podokně můžete také zobrazit události Kubernetes s předdefinovanými prohledáváním protokolů tak, že v horní části podokna vyberete odkaz **Zobrazit protokoly událostí Kubernetes** . Další informace o tom, jak zobrazit data protokolu Kubernetes, najdete v tématu [Analýza dat v protokolech hledání](container-insights-log-search.md). Při kontrole prostředků clusteru můžete zobrazit protokoly kontejnerů a události v reálném čase. Další informace o této funkci a konfiguraci vyžadované pro udělení a řízení přístupu najdete v tématu [zobrazení protokolů v reálném čase s Azure monitor pro kontejnery](container-insights-live-logs.md). 

Pomocí možnosti **+ Přidat filtr** v horní části stránky můžete filtrovat výsledky pro zobrazení podle **služby**, **uzlu**, **oboru názvů**nebo **fondu uzlů**. Po výběru rozsahu filtru vyberte jednu z hodnot zobrazených v poli **Vybrat hodnoty** . Po nakonfigurování filtru se použije globálně při prohlížení jakékoli perspektivy clusteru AKS. Vzorec podporuje pouze znaménko rovná se. Můžete přidat další filtry na první z nich můžete dále zúžit výsledky. Pokud například zadáte Filter by **Node**, můžete pro druhý filtr vybrat pouze **službu** nebo **obor názvů** .

![Příklad pomocí filtru, který můžete zúžit výsledky](./media/container-insights-analyze/add-filter-option-01.png)

Zadání filtru na jedné kartě bude nadále použito, pokud vyberete jiný. Po výběru symbolu **x** vedle zadaného filtru se odstraní. 

Přepněte na kartu **uzly** a hierarchie řádků se řídí objektovým modelem Kubernetes, který začíná uzlem v clusteru. Rozbalením uzlu zobrazíte jednu nebo více lusků spuštěných v uzlu. Pokud je více než jeden kontejner seskupen do seznamu pod, zobrazí se jako poslední řádek v hierarchii. Můžete také zobrazit, kolik úloh, které se nepoužívají, na hostiteli, pokud má hostitel tlak procesoru nebo paměti.

![Příklad hierarchie uzlu Kubernetes v zobrazení výkonu](./media/container-insights-analyze/containers-nodes-view.png)

Kontejnery Windows serveru, na kterých běží operační systém Windows Server 2019, se zobrazují po všech uzlech založených na systému Linux v seznamu. Když rozbalíte uzel systému Windows Server, můžete zobrazit jednu nebo více lusků a kontejnerů, které jsou spuštěny na uzlu. Po výběru uzlu se v podokně Vlastnosti zobrazí informace o verzi. Informace o agentech jsou vyloučené, protože uzly Windows serveru nemají nainstalovaného agenta. 

![Ukázková hierarchie uzlů s uvedenými uzly Windows serveru](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances virtuálních uzlů, které spouštějí operační systém Linux, se zobrazí za posledním uzlem clusteru AKS v seznamu. Když rozbalíte Container Instances virtuální uzel, můžete zobrazit jednu nebo více Container Instancesch lusků a kontejnerů, které jsou spuštěny na uzlu. Metriky nejsou shromažďovány a hlášeny pro uzly, pouze pro lusky.

![Příklad uzlu hierarchie se službou Container Instances uvedené](./media/container-insights-analyze/nodes-view-aci.png)

Z rozbaleného uzlu můžete procházet hierarchii pod nebo kontejnerem, který běží na uzlu, k zobrazení dat výkonu filtrovaných pro daný kontroler. Vyberte hodnotu pod sloupcem **kontroléru** pro konkrétní uzel.
 
![Příklad přechodu k podrobnostem z uzlu na kontroler v zobrazení výkonu](./media/container-insights-analyze/drill-down-node-controller.png)

V horní části stránky vyberte řadiče nebo kontejnery, abyste si mohli prohlédnout stav a využití prostředků u těchto objektů. Pokud chcete zkontrolovat využití paměti, vyberte v rozevíracím seznamu metrika možnost **paměť RSS** nebo **pracovní sada paměti**. **Paměť RSS** se podporuje jenom pro Kubernetes verze 1.8 a novější. V opačném případě zobrazení hodnot pro **Min&nbsp; %**  jako *NaN&nbsp;%* , má hodnotu číselný datový typ, který představuje Nedefinovaná nebo přičtení hodnotu.

**Pracovní sada paměti** zobrazuje jak rezidentní paměť, tak virtuální paměť (mezipaměť) a je celkový počet aplikací, které aplikace používá. **RSS paměti** zobrazuje pouze hlavní paměť, což je rezidentní paměť. Tato metrika zobrazuje skutečnou kapacitu dostupné paměti.

![Zobrazení výkonu uzly kontejneru](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Ve výchozím nastavení jsou data o výkonu založena na posledních šesti hodinách, ale můžete změnit okno pomocí možnosti **TimeRange** vlevo nahoře. Výsledky můžete filtrovat také v časovém rozsahu tak, že v selektoru percentilu vyberete **min**, **AVG**, **50**, **devadesát**, **95.** a **Max** . 

![Výběr percentilu pro filtrování dat](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Když najedete myší na pruhový graf ve sloupci **trend** , na každém řádku se zobrazí využití CPU nebo paměti v závislosti na tom, která metrika se vybrala, a to během vzorkovacího období 15 minut. Když vyberete graf trendu pomocí klávesnice, použijte klávesu ALT + PAGE UP nebo klávesu Alt + Page Down a procházejte jednotlivé panely samostatně. Budou se vám zobrazovat stejné podrobnosti, jako kdybyste najeďte na panel.

![Příklad přechodu ukazatele myši na pruhový graf trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

V dalším příkladu se pro první uzel v seznamu *AKS-nodepool1-* , hodnota pro **kontejnery** je 9. Tato hodnota je souhrnem celkového počtu nasazených kontejnerů.

![Souhrn kontejnerů – příklad pro uzel](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Tyto informace vám pomůžou rychle zjistit, jestli máte v clusteru správnou rovnováhu mezi uzly. 

Informace, které se zobrazí po zobrazení karty **uzly** , jsou popsány v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název hostitele. |
| Status | Kubernetes pohled na stav uzlu. |
| Minimální&nbsp;%, prům&nbsp;.%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, max.&nbsp;%  | Průměrné procento uzlů podle percentilu během vybrané doby trvání. |
| Min, AVG, 50, 90, 95., Max | Průměrná hodnota uzlů na základě hodnoty percentilu v době zvolené doby trvání. Průměrná hodnota se měří od nastaveného limitu CPU/paměti pro uzel. V případě lusků a kontejnerů je to průměrná hodnota uvedená v hostiteli. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, protože uzel spuštěn nebo byl restartován. |
| Kontrolér | Pouze pro kontejnery a tyto pody. Zobrazuje, ve kterém řadiči se nachází. Ne všechny podů jsou v kontroleru, takže některé se může zobrazit **není k dispozici**. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Pruhový graf trendu představuje průměrný percentilu metrik procento kontroleru. |

V modulu pro výběr, vyberte **řadiče**.

![Vybrat zobrazení řadičů](./media/container-insights-analyze/containers-controllers-tab.png)

Tady můžete zobrazit stav výkonu pro řadiče a Container Instances řadiče virtuálních uzlů nebo nepřipojené k řadiči virtuální uzel.

![\<Zobrazení výkonu názvů > řadičů](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchie řádků začíná na řadiči. Když rozbalíte kontroler, zobrazíte jednu nebo více lusků. Rozbalte uzel pod a poslední řádek zobrazí kontejner seskupený do pole pod. Z rozbaleného kontroleru můžete přejít k podrobnostem uzlu, na kterém je spuštěný, aby se zobrazila data výkonu filtrovaná pro tento uzel. Container Instances lusky nepřipojené k řadiči se v seznamu zobrazí jako poslední.

![Příklad hierarchie řadiče s uvedené podů Container Instances](./media/container-insights-analyze/controllers-view-aci.png)

Vyberte hodnotu ve sloupci **uzel** pro konkrétní kontroler.

![Příklad přechodu k podrobnostem z uzlu na kontroler v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení řadičů, jsou popsány v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Stav | Stav souhrnu kontejnerů po dokončení jeho spuštění se stavem, jako je například *OK*, *ukončeno*, *Chyba*, *Zastaveno*nebo pozastaveno. Pokud je kontejner spuštěný, ale stav buď nebyl správně zobrazen nebo nebyl vyzvednut agentem a nereagoval na více než 30 minut, je stav *Neznámý*. Další podrobnosti o ikoně stavu jsou uvedeny v následující tabulce.|
| Minimální&nbsp;%, prům&nbsp;.%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, max.&nbsp;%| Souhrn průměrem průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Min, AVG, 50, 90, 95., Max  | Souhrn průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartování | Souhrn počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Node | Pouze pro kontejnery a tyto pody. Zobrazuje, ve kterém řadiči se nachází. | 
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Pruhový graf trendu představuje metrika průměrné percentilu kontroleru. |

Ikony v poli Stav označují online stav kontejnerů.
 
| Ikona | Status | 
|--------|-------------|
| ![Připraveno spuštěné ikona stavu](./media/container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|
| ![Ikona stavu čekání nebo pozastavení](./media/container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|
| ![Naposledy hlásila systémem ikona stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední zpráva byla spuštěna, ale nereagovala po dobu delší než 30 minut.|
| ![Ikona úspěšný stav](./media/container-insights-analyze/containers-green-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|

Ikona stavu zobrazuje počet založené na co chcete pod poskytuje. Zobrazuje nejhorší dvou stavů, a když najedete myší stav, zobrazuje souhrn stavu ze všech podů v kontejneru. Pokud není k dispozici stavu Připraveno, hodnota stavu zobrazí **(0)** .

V modulu pro výběr, vyberte **kontejnery**.

![Vybrat zobrazení kontejnerů](./media/container-insights-analyze/containers-containers-tab.png)

Zde můžete zobrazit stav výkonu kontejnerů Kubernetes v Azure a Azure Container Instances. 

![\<Zobrazení výkonu názvů > kontejnerů](./media/container-insights-analyze/containers-containers-view.png)

Z kontejneru procházením hierarchie můžete pod nebo uzel k zobrazení dat výkonu filtrování pro daný objekt. Vyberte hodnotu pod sloupcem **pod** nebo **uzlu** pro konkrétní kontejner.

![Příklad přechodu k podrobnostem z uzlu na kontejnery v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení kontejnerů, jsou popsány v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav kontejnerů, pokud existuje. V další tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| Minimální&nbsp;%, prům&nbsp;.%,&nbsp;50%,&nbsp;90%,&nbsp;95.%, max.&nbsp;% | Souhrn průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Min, AVG, 50, 90, 95., Max | Souhrn průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Pod | Kontejner, ve které se nachází pod.| 
| Node |  Uzel, ve které se nachází kontejneru. | 
| Restartování | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, protože kontejneru se spustit nebo restartovat. |
| Trend min&nbsp;%, AVG&nbsp;%, 50&nbsp;%, 90&nbsp;%, 95.&nbsp;%, Max&nbsp;% | Pruhový graf trendu představuje průměrný percentilu metrik procento kontejneru. |

Ikony v poli Stav označují stav online lusků, jak je popsáno v následující tabulce.
 
| Ikona | Status |  
|--------|-------------|  
| ![Připraveno spuštěné ikona stavu](./media/container-insights-analyze/containers-ready-icon.png) | Systémem (připravená)|  
| ![Ikona stavu čekání nebo pozastavení](./media/container-insights-analyze/containers-waiting-icon.png) | Čeká se na nebo pozastavena|  
| ![Naposledy hlásila systémem ikona stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá za více než 30 minut|  
| ![Ikona stavu ukončení](./media/container-insights-analyze/containers-terminated-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|  
| ![Ikona stavu se nezdařilo](./media/container-insights-analyze/containers-failed-icon.png) | Chybovém stavu |  

## <a name="workbooks"></a>Sešity

Sešity kombinují text, [dotazy protokolů](../log-query/query-language.md), [metriky](../platform/data-platform-metrics.md)a parametry do propracovaných interaktivních sestav. Sešity mohou upravovat všichni ostatní členové týmu, kteří mají přístup ke stejným prostředkům Azure.

Azure Monitor pro kontejnery obsahují čtyři sešity, které vám pomohou začít:

- **Kapacita disku**: Prezentuje grafy použití interaktivního disku pro každý disk prezentovaný uzlu v rámci kontejneru pomocí následujících perspektiv:

    - Využití disku v procentech pro všechny disky.
    - Volné místo na disku pro všechny disky.
    - Mřížka zobrazující disk jednotlivých uzlů, její procento využitého místa, trend procenta využitého místa, volné místo na disku (GiB) a trend volného místa na disku (GiB). Po výběru řádku v tabulce se pod řádkem zobrazí procentuální hodnota využitého místa a volné místo na disku (GiB). 

- **Vstupně-výstupní operace disku**: Prezentuje grafy využití interaktivního disku pro každý disk prezentovaný uzlu v rámci kontejneru pomocí následujících perspektiv:

    - Počet vstupně-výstupních operací na disku shrnutých na všech discích, a to čtením bajtů za sekundu, zápisem bajtů/s a čtením a zápisem v bajtech/s.
    - Osm grafů výkonu ukazuje klíčové ukazatele výkonu, které umožňují měřit a identifikovat kritické body v/v disku.

- **Kubelet**: Obsahuje dvě mřížky, které zobrazují provozní statistiku klíčových uzlů:

    - Přehled podle mřížky uzlů shrnuje celkovou operaci, celkový počet chyb a úspěšné operace podle procenta a trendu pro každý uzel.
    - Přehled podle souhrnu typu operací pro každou operaci: celková operace, celkový počet chyb a úspěšné operace podle procenta a trendu.

- **Síť**: Prezentuje interaktivní grafy využití sítě pro každý síťový adaptér uzlu a Mřížka představuje klíčové ukazatele výkonu, které vám pomůžou změřit výkon síťových adaptérů.

Přístup k těmto sešitům získáte tak, že v rozevíracím seznamu **Zobrazit sešity** vyberete jednu z nich.

![Zobrazit rozevírací seznam sešitů](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [vytvoření výstrah výkonu pomocí Azure monitor pro kontejnery](container-insights-alerts.md) , kde se dozvíte, jak vytvořit výstrahy s vysokým využitím procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.
- V [příkladech dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) si můžete prohlédnout předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizaci nebo analýze clusterů.
