---
title: Monitorování Kubernetes pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete zobrazit a analyzovat výkon clusteru Kubernetes pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298378"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Sledování výkonu clusteru Kubernetes pomocí Azure Monitoru pro kontejnery

Pomocí Azure Monitor pro kontejnery můžete použít grafy výkonu a stav stavu ke sledování úloh clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), Azure Stack nebo jiném prostředí ze dvou hledisek. Můžete sledovat přímo z clusteru nebo můžete zobrazit všechny clustery v předplatném z Azure Monitoru. Zobrazení azure kontejneru instance je také možné při sledování konkrétního clusteru AKS.

Tento článek vám pomůže pochopit dvě perspektivy a jak Azure Monitor pomáhá rychle vyhodnotit, prozkoumat a vyřešit zjištěné problémy.

Informace o tom, jak povolit Azure Monitor pro kontejnery, najdete [v tématu Onboard Azure Monitor pro kontejnery](container-insights-onboard.md).

Azure Monitor poskytuje zobrazení s více clustery, které zobrazuje stav všech monitorovaných clusterů Kubernetes se systémem Linux a Windows Server 2019 nasazených napříč skupinami prostředků ve vašich předplatných. Zobrazuje clustery zjištěné ve všech prostředích, které nejsou sledovány řešením. Můžete okamžitě pochopit stav clusteru a odtud můžete přejít k podrobnostem na stránce výkonu uzlu a řadiče nebo přejít k zobrazení grafů výkonu pro cluster. Pro clustery AKS, které byly zjištěny a identifikovány jako nemonitorované, můžete kdykoli povolit monitorování. 

Hlavní rozdíly v monitorování clusteru Windows Server s Azure Monitor pro kontejnery ve srovnání s clusterem Linux jsou popsány [zde](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) v článku s přehledem.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusterů z Azure Monitoru

Pokud chcete zobrazit stav všech nasazených kubernetesových clusterů, vyberte **Monitor ovat** v levém podokně na webu Azure Portal. V části **Přehledy** vyberte **Kontejnery**. 

![Příklad řídicího panelu Azure Monitor s více clustery](./media/container-insights-analyze/azmon-containers-multiview.png)

Výsledky zobrazené v mřížce můžete zobrazit tak, aby zobrazovaly clustery, které jsou:

* **Azure** – clustery AKS a AKS-Engine hostované ve službě Azure Kubernetes Service
* **Azure Stack (Preview)** – clustery AKS-Engine hostované v Azure Stacku
* **Jiné než Azure (Preview)** – clustery Kubernetes hostované místně
* **Vše** – zobrazte všechny clustery Kubernetes hostované v Azure, Azure Stacku a místních prostředích, která jsou na palubě Azure Monitoru pro kontejnery.

Chcete-li zobrazit clustery z určitého prostředí, vyberte je z **pilulky Prostředí** v levém horním rohu stránky.

![Příklad voliče pilulek pro životní prostředí](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na kartě **Sledované clustery** se dozvíte následující:

- Kolik clusterů jsou v kritickém nebo stavu není v pořádku, oproti kolik jsou v pořádku nebo není hlášení (označované jako neznámý stav).
- Zda jsou všechna nasazení [Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine) v pořádku.
- Kolik uzlů a uživatelských a systémových podů se nasazuje na cluster.
- Kolik místa na disku je k dispozici a pokud došlo k problému s kapacitou.

Zahrnuty jsou zdravotní stavy: 

* **V pořádku**: Pro virtuální ho dispoziční systém nejsou zjištěny žádné problémy a funguje podle potřeby. 
* **Kritické**: Jsou zjištěny jeden nebo více kritických problémů, které je třeba vyřešit, aby bylo možné obnovit normální provozní stav podle očekávání.
* **Upozornění**: Je zjištěn jeden nebo více problémů, které je třeba vyřešit nebo může být stav kritický.
* **Neznámý:** Pokud služba nebyla schopna navázat připojení k uzlu nebo podu, stav se změní na neznámý stav.
* **Nebyl nalezen:** Byl odstraněn pracovní prostor, skupina prostředků nebo odběr, který obsahuje pracovní prostor pro toto řešení.
* **Neautorizováno**: Uživatel nemá požadovaná oprávnění ke čtení dat v pracovním prostoru.
* **Chyba**: Při pokusu o čtení dat z pracovního prostoru došlo k chybě.
* **Chybně nakonfigurované:** Azure Monitor pro kontejnery nebyl správně nakonfigurován v zadaném pracovním prostoru.
* **Žádná data**: Data nebyla hlášena do pracovního prostoru za posledních 30 minut.

Stav vypočítá celkový stav clusteru jako *nejhorší ze* tří stavů s jednou výjimkou. Pokud některý ze tří stavů je Neznámý, celkový stav clusteru zobrazí **Neznámý**. 

Následující tabulka obsahuje rozpis výpočtu, který řídí stavy monitorovaného clusteru v zobrazení s více clustery.

| |Status |Dostupnost |  
|-------|-------|-----------------|  
|**Pod uživatele**| | |  
| |V pořádku |100 % |  
| |Upozornění |90 - 99% |  
| |Kritická |<90 % |  
| |Není známo |Pokud není hlášena za posledních 30 minut |  
|**Systémový pod**| | |  
| |V pořádku |100 % |
| |Upozornění |Není dostupné. |
| |Kritická |<100% |
| |Není známo |Pokud není hlášena za posledních 30 minut |
|**Node** | | |
| |V pořádku |>85 % |
| |Upozornění |60 - 84% |
| |Kritická |<60% |
| |Není známo |Pokud není hlášena za posledních 30 minut |

Ze seznamu clusterů můžete přejít k podrobnostem stránky **Clusteru** výběrem názvu clusteru. Potom přejděte na stránku výkonu **uzly** výběrem souhrnu uzlů ve sloupci **Uzly** pro daný cluster. Nebo můžete přejít k podrobnostem na stránce výkonu **řadiče** výběrem **souhrnu uživatelských podů** nebo **systémových podů** sloupce.

## <a name="view-performance-directly-from-a-cluster"></a>Zobrazení výkonu přímo z clusteru

Přístup k Azure Monitor pro kontejnery je k dispozici přímo z clusteru AKS výběrem**clusteru** **Insights** > z levého podokna nebo když jste vybrali cluster ze zobrazení více clusterů. Informace o clusteru jsou uspořádány do čtyř hledisek:

- Cluster
- Uzly 
- Kontrolery 
- Kontejnery

>[!NOTE]
>Zkušenosti popsané ve zbývající části tohoto článku jsou také použitelné pro zobrazení výkonu a stavu clusterů Kubernetes hostovaných v Azure Stack nebo v jiném prostředí při výběru ze zobrazení více clusterů. 

Otevře se výchozí stránka a zobrazí čtyři grafy výkonu řádků, které zobrazují klíčové metriky výkonu vašeho clusteru. 

![Příklad grafů výkonu na kartě Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Grafy výkonu zobrazují čtyři metriky výkonu:

- **Využití&nbsp;procesoru uzlu**: Agregovaná perspektiva využití procesoru pro celý cluster. Chcete-li filtrovat výsledky pro časový rozsah, vyberte **možnost Průměr**, **95th** **Min**, **Max** **50.** **90th** Filtry lze použít samostatně nebo v kombinaci. 
- **Využití&nbsp;paměti uzlu**: Agregovaná perspektiva využití paměti pro celý cluster. Chcete-li filtrovat výsledky pro časový rozsah, vyberte **možnost Průměr**, **95th** **Min**, **Max** **50.** **90th** Filtry lze použít samostatně nebo v kombinaci. 
- **Počet uzlů**: Počet uzlů a stav z Kubernetes. Stavy reprezentovanéu uzlů clusteru jsou Celkem, Připraveno a Není připraveno. Mohou být filtrovány jednotlivě nebo kombinovány v selektoru nad grafem. 
- **Počet aktivních modulů**: Počet podů a stav z Kubernetes. Stavy reprezentovaných podů jsou Total, Pending, Running, Unknown, Succeeded nebo Failed. Mohou být filtrovány jednotlivě nebo kombinovány v selektoru nad grafem. 

Pomocí kláves se šipkami vlevo a vpravo můžete procházet každý datový bod v grafu. Pomocí kláves se šipkami nahoru a dolů můžete cykonožovat čáry percentilu. Vyberte ikonu pinu v pravém horním rohu některého z grafů, chcete-li připnout vybraný graf k poslednímu řídicímu panelu Azure, který jste si prohlíželi. Na řídicím panelu můžete změnit velikost a umístění grafu. Výběr grafu z řídicího panelu přesměruje na Azure Monitor pro kontejnery a načte správný obor a zobrazení.

Azure Monitor pro kontejnery také podporuje [Průzkumník metrik](../platform/metrics-getting-started.md)Azure Monitoru , kde můžete vytvářet vlastní grafy vykreslování, korelovat a zkoumat trendy a připnout na řídicí panely. V průzkumníku metrik můžete také použít kritéria, která jste nastavili k vizualizaci metrik jako základ [pravidla výstrahy založenéna metrikami](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Zobrazení metrik kontejneru v průzkumníku metrik

V průzkumníku metrik můžete zobrazit agregované metriky využití uzlů a podů z Azure Monitoru pro kontejnery. Následující tabulka shrnuje podrobnosti, které vám pomohou pochopit, jak pomocí grafů metrik vizualizovat metriky kontejneru.

|Obor názvů | Metrika | Popis | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Agregované měření využití procesoru v celém clusteru. Jedná se o jádro PROCESORU rozdělené na 1000 jednotek (milli = 1000). Slouží k určení využití jader v kontejneru, kde mnoho aplikací může používat jedno jádro.| 
| | cpuUsagePercentage | Agregované průměrné využití procesoru měřené v procentech v celém clusteru.|
| | memoryRssBytes | Kontejner RSS paměti používané v bajtů.| 
| | memoryRssPercentage | Kontejner RSS paměti používané v procentech.|
| | memoryWorkingSetBytes | Použitý paměť pracovní sady kontejneru.| 
| | paměťWorkingSetPercentage | Kontejner pracovní sada paměti používané v procentech. | 
| | nodesCount | Počet uzlů z Kubernetes.|
| insights.container/pods | |
| | PodCount | Počet modulů z Kubernetes.|

Metriku můžete [rozdělit](../platform/metrics-charts.md#apply-splitting-to-a-chart) a zobrazit ji podle dimenze a vizualizovat, jak se různé segmenty vzájemně porovnávají. U uzlu můžete graf segmentovat podle *dimenze hostitele.* Z podu jej můžete segmentovat podle následujících rozměrů:

* Řadič
* Obor názvů Kubernetes
* Node
* Fáze

## <a name="analyze-nodes-controllers-and-container-health"></a>Analýza uzlů, řadičů a stavu kontejneru

Když přepnete na karty **Uzly**, **Řadiče**a **Kontejnery,** podokno vlastností se automaticky zobrazí na pravé straně stránky. Zobrazuje vlastnosti vybrané položky, která zahrnuje popisky, které jste definovali pro uspořádání objektů Kubernetes. Pokud je vybrán uzel Linuxu, část **Místní kapacita disku** také zobrazuje dostupné místo na disku a procento použité pro každý disk prezentovaný uzlu. Vyberte **>>** odkaz v podokně, chcete-li podokno zobrazit nebo skrýt.

Při rozbalení objektů v hierarchii se podokno vlastností aktualizuje na základě vybraného objektu. V podokně můžete také zobrazit protokoly kontejnerů Kubernetes (stdout/stderror), události a metriky podu výběrem odkazu **Zobrazit živá data (náhled)** v horní části podokna. Další informace o konfiguraci potřebné k udělení a řízení přístupu k zobrazení těchto dat naleznete [v tématu Nastavení živých dat (náhled)](container-insights-livedata-setup.md). Při kontrole prostředků clusteru, můžete vidět tato data z kontejneru v reálném čase. Další informace o této funkci naleznete [v tématu Zobrazení kubernetes protokolů, událostí a pod metriky v reálném čase](container-insights-livedata-overview.md). Chcete-li zobrazit data protokolu Kubernetes uložená ve vašem pracovním prostoru na základě předdefinovaných hledání protokolů, vyberte **zobrazit protokoly kontejnerů** z rozevíracího seznamu **Zobrazení v analýze.** Další informace o tomto tématu naleznete v [tématu Search logs to analyze data](container-insights-log-search.md#search-logs-to-analyze-data).

Pomocí možnosti **+ Přidat filtr** v horní části stránky můžete filtrovat výsledky zobrazení podle **služby**, **uzlu**, **oboru názvů**nebo **fondu uzlů**. Po výběru oboru filtru vyberte jednu z hodnot zobrazených v poli **Vybrat hodnotu.** Po konfiguraci filtru se použije globálně při zobrazení libovolné perspektivy clusteru AKS. Vzorec podporuje pouze znaménko rovná se. Můžete přidat další filtry na první z nich dále zúžit výsledky. Pokud například zadáte filtr podle **uzlu**, můžete vybrat pouze **službu** nebo **obor názvů** pro druhý filtr.

Zadání filtru na jedné kartě se použije i při výběru jiné karty. Odstraní se po výběru symbolu **x** vedle zadaného filtru. 

Přepněte na kartu **Uzly** a hierarchie řádků následuje objektový model Kubernetes, který začíná uzlem ve vašem clusteru. Rozbalte uzel a zobrazte jeden nebo více podů spuštěných na uzlu. Pokud je do podu seskupeno více než jeden kontejner, zobrazí se jako poslední řádek v hierarchii. Můžete také zobrazit, kolik úloh, které nejsou související s podem, jsou spuštěny na hostiteli, pokud má hostitel tlak na procesor nebo paměť.

![Příklad hierarchie uzlů Kubernetes v zobrazení výkonu](./media/container-insights-analyze/containers-nodes-view.png)

Kontejnery Windows Serveru, které používají operační systém Windows Server 2019, se zobrazují po všech uzlech založených na Linuxu v seznamu. Když rozbalíte uzel Windows Server, můžete zobrazit jeden nebo více podů a kontejnerů spuštěných na uzlu. Po výběru uzlu se v podokně vlastností zobrazí informace o verzi. Informace o agentovi jsou vyloučeny, protože uzly systému Windows Server nemají nainstalovaného agenta. 

![Příklad hierarchie uzlů s uvedenými uzly systému Windows Server](./media/container-insights-analyze/nodes-view-windows.png) 

Virtuální uzly Azure Container Instances, které spouštějí operační systém Linux, se zobrazují za posledním uzlem clusteru AKS v seznamu. Když rozbalíte virtuální uzel Instance kontejneru, můžete zobrazit jeden nebo více podů instancí kontejneru a kontejnerů, které běží na uzlu. Metriky nejsou shromažďovány a hlášeny pro uzly, pouze pro pody.

![Příklad hierarchie uzlů s uvedenými instancemi kontejneru](./media/container-insights-analyze/nodes-view-aci.png)

Z rozbaleného uzlu můžete přejít k podrobnostem z podu nebo kontejneru, který běží na uzlu do řadiče, a zobrazit tak data o výkonu filtrovaná pro tento řadič. Vyberte hodnotu ve sloupci **Řadič** pro konkrétní uzel.
 
![Příklad přechodu k podrobnostem z uzlu k řadiči v zobrazení výkonu](./media/container-insights-analyze/drill-down-node-controller.png)

Vyberte řadiče nebo kontejnery v horní části stránky ke kontrole stavu a využití prostředků pro tyto objekty. Chcete-li zkontrolovat využití paměti, vyberte v rozevíracím seznamu **Metrika** **pracovní sadu**Paměť **RSS** nebo Paměť . **Paměťový RSS** je podporován pouze pro Kubernetes verze 1.8 a novější. V opačném případě zobrazíte hodnoty pro **Min&nbsp; ** jako *NaN&nbsp;*, což je číselná hodnota datového typu, která představuje nedefinovanou nebo nereprezentovat hodnotu.

![Zobrazení výkonu uzlů kontejneru](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Pracovní sada paměti** zobrazuje zahrnutou rezidentní paměť i virtuální paměť (mezipaměť) a je součtem toho, co aplikace používá. **Paměť RSS** zobrazuje pouze hlavní paměť (což není nic jiného než rezidentní paměti jinými slovy). Tato metrika zobrazuje skutečnou kapacitu dostupné paměti. Jaký je rozdíl mezi rezidentní pamětí a virtuální pamětí?

- Rezidentní paměť nebo hlavní paměť je skutečné množství paměti počítače, které je k dispozici pro uzly clusteru.

- Virtuální paměť je vyhrazeno místo na pevném disku (cache) používá operační systém pro výměnu dat z paměti na disk, když pod tlakem paměti a pak načíst zpět do paměti v případě potřeby.

Ve výchozím nastavení jsou data o výkonu založena na posledních šesti hodinách, ale okno můžete změnit pomocí možnosti **Časový rozsah** v levém horním rohu. Výsledky můžete také filtrovat v časovém rozsahu výběrem **min** **95th**, **Max** **výčnělku**, **50.** **90th** 

![Výběr percentilu pro filtrování dat](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Když najedete ukazatel na pruhový graf ve sloupci **Trend,** každý pruh zobrazuje využití procesoru nebo paměti v závislosti na tom, která metrika je vybrána, během ukázkového období 15 minut. Po výběru grafu trendů pomocí klávesnice můžete pomocí klávesy Alt+Page up nebo Alt+Page down procházet každý pruh jednotlivě. Dostanete stejné detaily, které byste udělali, kdybyste se vznášeli nad barem.

![Příklad najetí přes ukazatel trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

V dalším příkladu pro první uzel v seznamu *aks-nodepool1-*, hodnota **kontejnerů** je 9. Tato hodnota je souhrn celkového počtu nasazených kontejnerů.

![Souhrn kontejnerů na uzel příklad](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Tyto informace vám mohou pomoci rychle určit, zda máte správnou rovnováhu kontejnerů mezi uzly v clusteru. 

Informace, které jsou uvedeny při zobrazení karty **Uzly,** jsou popsány v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Name (Název) | Název hostitele |
| Status | Kubernetes zobrazení stavu uzlu. |
| Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;%  | Průměrné procento uzlu na základě percentilu během vybrané doby trvání. |
| Min, Avg, 50., 90., 95., Max | Průměrná skutečná hodnota uzlů na základě percentilu během vybrané doby trvání. Průměrná hodnota se měří z limitu procesoru/paměti nastaveného pro uzel. Pro pody a kontejnery je průměrná hodnota hlášená hostitelem. |
| Kontejnery | Počet kontejnerů. |
| Uptime | Představuje čas od spuštění uzlu nebo restartování počítače. |
| Řadič | Pouze pro kontejnery a lusky. Zobrazuje, ve kterém řadiči je umístěn. Ne všechny pody jsou v řadiči, takže některé mohou zobrazit **Není k zapisováno**. | 
| Trend Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;% | Trend pruhového grafu představuje průměrné procento percentilu v hodnotě regulátoru. |

Můžete si všimnout pracovního vytížení po rozšíření uzlu s názvem **Jiný proces**. Představuje nekontejnerizované procesy, které běží na vašem uzlu a zahrnuje:

* Samořízené nebo spravované nekontejnerizované procesy Kubernetes

* Procesy za běhu kontejneru  

* Kubelet  

* Systémové procesy spuštěné na vašem uzlu

* Jiné úlohy bez Kubernetes spuštěné na hardwaru uzlu nebo virtuálním počítači

Vypočítá se podle: *Celkové využití z využití CAdvisor* - z*kontejnerizovaného procesu*.  

V voliči vyberte **Ovladače**.

![Zobrazení Vybrat ovladače](./media/container-insights-analyze/containers-controllers-tab.png)

Zde můžete zobrazit stav výkonu řadičů a řadičů virtuálních uzlů instance kontejneru nebo pody virtuálních uzlů, které nejsou připojeny k řadiči.

![\<Zobrazení výkonu řadiče> názvů](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchie řádků začíná řadičem. Při rozbalení řadiče zobrazíte jeden nebo více podů. Rozbalte pod a poslední řádek zobrazí kontejner seskupený do podu. Z rozbaleného řadiče můžete přejít k uzlu, na který běží, a zobrazit tak data o výkonu filtrovaná pro tento uzel. Pody instancí kontejnerů, které nejsou připojeny k řadiči, jsou v seznamu uvedeny jako poslední.

![Příklad hierarchie řadičů s uvedenými pody instancí kontejnerů](./media/container-insights-analyze/controllers-view-aci.png)

Vyberte hodnotu ve sloupci **Uzel** pro konkrétní řadič.

![Příklad přechodu k podrobnostem z uzlu k řadiči v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení řadičů, jsou popsány v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Name (Název) | Název řadiče.|
| Status | Stav souhrnu kontejnerů po dokončení spuštění se stavem *NApříklad OK*, *Ukončeno*, *Nezdařilo se*, *Zastaveno*nebo *Pozastaveno*. Pokud je kontejner spuštěn, ale stav nebyl správně zobrazen nebo nebyl agentem vyzvednut a neodpověděl déle než 30 minut, je stav *Neznámý*. Další podrobnosti o ikoně stavu jsou uvedeny v následující tabulce.|
| Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;%| Souhrnná průměr průměrného procenta každé entity pro vybranou metriku a percentil. |
| Min, Avg, 50., 90., 95., Max  | Kumulativní průměrné procesorové milijádrnebo výkon paměti kontejneru pro vybraný percentil. Průměrná hodnota se měří z limitu procesoru/paměti nastaveného pro pod. |
| Kontejnery | Celkový počet kontejnerů pro řadič nebo pod. |
| Restartuje | Kumulativní počet restartování z kontejnerů. |
| Uptime | Představuje čas od spuštění kontejneru. |
| Node | Pouze pro kontejnery a lusky. Zobrazuje, ve kterém řadiči je umístěn. | 
| Trend Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;% | Trend pruhového grafu představuje průměrnou percentilovou metriku regulátoru. |

Ikony ve stavovém poli označují stav kontejnerů online.
 
| Ikona | Status | 
|--------|-------------|
| ![Ikona stavu spuštění](./media/container-insights-analyze/containers-ready-icon.png) | Běh (připraveno)|
| ![Ikona stavu Čekání nebo Pozastavený stav](./media/container-insights-analyze/containers-waiting-icon.png) | Čekání nebo pozastaveno|
| ![Ikona naposledy hlášeného stavu spuštění](./media/container-insights-analyze/containers-grey-icon.png) | Poslední nahlášený běh, ale neodpověděl více než 30 minut|
| ![Ikona úspěšného stavu](./media/container-insights-analyze/containers-green-icon.png) | Úspěšně zastaveno nebo se nepodařilo zastavit.|

Ikona stavu zobrazuje počet na základě toho, co pod poskytuje. Zobrazuje nejhorší dva stavy a když najedete přes stav, zobrazí souhrnný stav ze všech podů v kontejneru. Pokud není k dispozici stav připravenosti, zobrazí se hodnota stavu **(0).**

Ve voliči vyberte **Kontejnery**.

![Vybrat zobrazení Kontejnery](./media/container-insights-analyze/containers-containers-tab.png)

Tady si můžete zobrazit stav výkonu kontejnerů Azure Kubernetes a Azure Container Instances. 

![\<Zobrazení výkonu> kontejnerů](./media/container-insights-analyze/containers-containers-view.png)

Z kontejneru můžete přejít k podrobnostem podu nebo uzlu a zobrazit tak data o výkonu filtrovaná pro tento objekt. Vyberte hodnotu ve sloupci **Pod** nebo **Uzel** pro konkrétní kontejner.

![Příklad přechodu k podrobnostem z uzlu do kontejnerů v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení kontejnerů je popsán v následující tabulce.

| Sloupec | Popis | 
|--------|-------------|
| Name (Název) | Název řadiče.|
| Status | Stav kontejnerů, pokud existuje. Další podrobnosti o ikoně stavu jsou uvedeny v následující tabulce.|
| Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;% | Souhrn průměrného procenta každé entity pro vybranou metriku a percentil. |
| Min, Avg, 50., 90., 95., Max | Souhrn průměrného procesoru milicore nebo výkon paměti kontejneru pro vybraný percentil. Průměrná hodnota se měří z limitu procesoru/paměti nastaveného pro pod. |
| Pod | Kontejner, kde se nachází pod.| 
| Node |  Uzel, kde se nachází kontejner. | 
| Restartuje | Představuje čas od spuštění kontejneru. |
| Uptime | Představuje čas od spuštění nebo restartování kontejneru. |
| Trend Min&nbsp;%, Avg&nbsp;%, 50th&nbsp;%, 90th&nbsp;%, 95th&nbsp;%, Max&nbsp;% | Trend pruhového grafu představuje průměrné procento percentilu kontejneru. |

Ikony ve stavovém poli označují online stavy podů, jak je popsáno v následující tabulce.
 
| Ikona | Status |  
|--------|-------------|  
| ![Ikona stavu spuštění](./media/container-insights-analyze/containers-ready-icon.png) | Běh (připraveno)|  
| ![Ikona stavu Čekání nebo Pozastavený stav](./media/container-insights-analyze/containers-waiting-icon.png) | Čekání nebo pozastaveno|  
| ![Ikona naposledy hlášeného stavu spuštění](./media/container-insights-analyze/containers-grey-icon.png) | Poslední nahlášený běh, ale neodpověděl více než 30 minut|  
| ![Ikona ukončeného stavu](./media/container-insights-analyze/containers-terminated-icon.png) | Úspěšně zastaveno nebo se nepodařilo zastavit.|  
| ![Ikona stavu se nezdařilo](./media/container-insights-analyze/containers-failed-icon.png) | Stav se nezdařilo. |  

## <a name="workbooks"></a>Workbooks

Sešity kombinují text, [dotazy protokolu](../log-query/query-language.md), [metriky](../platform/data-platform-metrics.md)a parametry do bohatých interaktivních sestav. Sešity jsou upravitelné všemi ostatními členy týmu, kteří mají přístup ke stejným prostředkům Azure.

Azure Monitor pro kontejnery obsahuje čtyři sešity, které vám pomohou začít:

- **Kapacita disku**: Představuje interaktivní grafy využití disku pro každý disk prezentovaný uzlu v kontejneru podle následujících perspektiv:

    - Procento využití disku pro všechny disky.
    - Uvolněte místo na disku pro všechny disky.
    - Mřížka, která zobrazuje disk každého uzlu, jeho procento využitého místa, trend procenta využitého místa, volné místo na disku (GiB) a trend volného místa na disku (GiB). Pokud je v tabulce vybrán řádek, zobrazí se pod řádkem procento využitého místa a volného místa na disku (GiB). 

- **IO disku**: Představuje interaktivní grafy využití disku pro každý disk prezentovaný uzlu v kontejneru podle následujících perspektiv:

    - Vstupně-out disku shrnutý na všech discích podle čtení bajtů/s, zápisů bajtů/s a čtení a zápisu bajtů/s trendů.
    - Osm grafů výkonu zobrazuje klíčové ukazatele výkonu, které pomáhají měřit a identifikovat kritická místa vstupně-videa disku.

- **Kubelet**: Zahrnuje dvě mřížky, které zobrazují provozní statistiky klíčových uzlů:

    - Přehled podle mřížky uzlu shrnuje celkovou operaci, celkové chyby a úspěšné operace podle procent a trendu pro každý uzel.
    - Přehled podle typu operace shrnuje pro každou operaci celkovou operaci, celkové chyby a úspěšné operace podle procent a trendu.

- **Síť**: Představuje interaktivní grafy využití sítě pro síťový adaptér každého uzlu a mřížka představuje klíčové ukazatele výkonu, které pomáhají měřit výkon síťových adaptérů.

K těmto sešitům se dostanete výběrem všech z rozevíracího seznamu **Zobrazit sešity.**

![Zobrazit rozevírací seznam Sešitů](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Další kroky

- Zkontrolujte Vytvoření výstrah výkonu pomocí Azure Monitor u kontejnerů se [dozvíte,](container-insights-alerts.md) jak vytvářet výstrahy pro vysoké využití procesoru a paměti pro podporu devOps nebo provozní procesy a postupy.

- Chcete-li zobrazit předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení tak, aby bylo možné zobrazit, vizualizovat nebo analyzovat clustery, zobrazit [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.

- Zobrazit [stav clusteru monitoru](container-insights-health.md) se dozvíte o zobrazení stavu clusteru Kubernetes.
