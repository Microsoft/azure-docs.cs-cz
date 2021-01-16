---
title: Kubernetes monitorování pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete zobrazit a analyzovat výkon clusteru Kubernetes s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 68d0ac03ae0f6029e0f984e296a89048536f4eb7
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251280"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorování výkonu clusteru Kubernetes s využitím Azure Monitor pro kontejnery

Díky Azure Monitor pro kontejnery můžete pomocí grafů výkonu a stavu monitorovat úlohy Kubernetes clusterů hostovaných ve službě Azure Kubernetes Service (AKS), Azure Stack nebo jiném prostředí ze dvou perspektiv. Můžete monitorovat přímo z clusteru nebo můžete zobrazit všechny clustery v rámci předplatného Azure Monitor. Zobrazení Azure Container Instances je také možné při monitorování konkrétního clusteru AKS.

Tento článek vám pomůže pochopit dvě perspektivy a jak Azure Monitor vám pomůže rychle posoudit, prozkoumat a vyřešit zjištěné problémy.

Informace o tom, jak povolit Azure Monitor pro kontejnery, najdete v tématu připojení [Azure monitor kontejnerů](container-insights-onboard.md).

Azure Monitor poskytuje zobrazení více clusterů, které zobrazuje stav všech monitorovaných clusterů Kubernetes se systémem Linux a Windows Server 2019 nasazených ve skupinách prostředků ve vašich předplatných. Zobrazuje clustery zjištěné ve všech prostředích, která nejsou monitorovaná řešením. Stav clusteru můžete okamžitě pochopit a z tohoto místa můžete přejít k podrobnostem na stránce s výkonem uzlů a kontroléru nebo přejít k zobrazení grafů výkonu pro cluster. U clusterů AKS, které byly zjištěny a identifikovány jako nemonitorované, můžete kdykoli povolit monitorování.

Hlavní rozdíly v monitorování clusteru Windows serveru s Azure Monitor pro kontejnery v porovnání s clusterem se systémem Linux jsou popsány [zde](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) v článku Přehled.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Zobrazení více clusterů z Azure Monitor

Pokud chcete zobrazit stav všech nasazených Kubernetes clusterů, vyberte **monitor** v levém podokně Azure Portal. V části **přehledy** vyberte **kontejnery**.

![Příklad řídicího panelu pro více clusterů Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

Můžete určit rozsah výsledků prezentovaných v mřížce a zobrazit tak clustery, které jsou:

* Clustery **Azure** – AKS a AKS-Engine hostované ve službě Azure Kubernetes
* **Azure Stack (Preview)** – AKS-Engine clustery hostované v Azure Stack
* **Mimo Azure (Preview)** – Kubernetes clustery jsou hostované místně.
* **Vše** – zobrazení všech clusterů Kubernetes hostovaných v Azure, Azure Stack a místních prostředích, která jsou zaregistrovaná Azure monitor pro kontejnery

Pokud chcete zobrazit clustery z konkrétního prostředí, vyberte ji v části **prostředí** v levém horním rohu stránky.

![Příklad selektoru v prostředí](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Na kartě **monitorované clustery** se dozvíte následující:

- Počet clusterů v kritickém nebo nesprávném stavu, v jakém jsou v pořádku nebo nikoli hlášení (označované jako neznámý stav).
- Zda jsou všechna nasazení [modulu Azure Kubernetes Engine (AKS-Engine)](https://github.com/Azure/aks-engine) v dobrém stavu.
- Kolik uzlů a systémových prostředí je nasazeno na cluster.
- Kolik místa na disku je k dispozici a pokud dojde k potížím s kapacitou.

K dispozici jsou tyto stavy:

* **V pořádku**: pro virtuální počítač se nezjistily žádné problémy a funguje podle potřeby.
* **Kritické**: zjistili jsme jeden nebo několik kritických problémů, které je potřeba vyřešit tak, aby se normální provozní stav obnovil podle očekávání.
* **Upozornění**: zjistili jsme jeden nebo víc problémů, které je potřeba vyřešit, nebo se může stát, že by se mohlo stát kritickým stavem.
* **Neznámé**: Pokud se službě nepovedlo připojit k uzlu nebo pod, stav se změní na neznámý stav.
* **Nenalezeno**: pracovní prostor, skupinu prostředků nebo předplatné obsahující pracovní prostor pro toto řešení bylo odstraněno.
* **Neautorizováno**: uživatel nemá požadovaná oprávnění ke čtení dat v pracovním prostoru.
* **Chyba**: při pokusu o čtení dat z pracovního prostoru došlo k chybě.
* Nesprávně **nakonfigurované**: Azure monitor pro kontejnery se v zadaném pracovním prostoru správně nenakonfigurovaly.
* **Žádná data**: za posledních 30 minut se v pracovním prostoru nenahlásila žádná data.

Služba Health (stav) počítá celkový stav clusteru jako *nejhorší ze* tří stavů s jednou výjimkou. Pokud některý ze tří stavů není znám, celkový stav clusteru zobrazuje **Neznámý**.

Následující tabulka uvádí rozpis výpočtu, který řídí stav pro monitorovaný cluster v zobrazení více clusterů.

| Monitorovaný cluster |Status |Dostupnost |
|-------|-------|-----------------|
|**Uživatel pod**| | |
| |V pořádku |100 % |
| |Upozornění |90 – 99% |
| |Kritické |<90% |
| |Neznámý |Pokud není uvedeno za posledních 30 minut |
|**Systém pod**| | |
| |V pořádku |100 % |
| |Upozornění |– |
| |Kritické |<100% |
| |Neznámý |Pokud není uvedeno za posledních 30 minut |
|**Node** | | |
| |V pořádku |>85% |
| |Upozornění |60 – 84% |
| |Kritické |<60% |
| |Neznámý |Pokud není uvedeno za posledních 30 minut |

V seznamu clusterů můžete přejít na stránku **clusteru** tak, že vyberete název clusteru. Pak přejdete na stránku výkon **uzlů** , a to tak, že vyberete souhrn uzlů ve sloupci **uzly** tohoto konkrétního clusteru. Případně můžete přejít k podrobnostem na stránce s výkonem **řadičů** , a to výběrem souhrnu sloupce **uživatelské lusky** nebo **Systémová lusky** .

## <a name="view-performance-directly-from-a-cluster"></a>Zobrazení výkonu přímo z clusteru

Přístup k Azure monitor pro kontejnery je k dispozici přímo z clusteru AKS výběrem možnosti  >  **cluster** Insights v levém podokně nebo při výběru clusteru ze zobrazení více clusterů. Informace o clusteru jsou rozdělené do čtyř perspektiv:

- Cluster
- Uzly
- Kontrolery
- Containers

>[!NOTE]
>Prostředí popsané ve zbývající části tohoto článku platí i pro zobrazení výkonu a stavu clusterů Kubernetes hostovaných v Azure Stack nebo jiném prostředí, když je vybraný ze zobrazení více clusterů.

Otevře se výchozí stránka a zobrazí se čtyři grafy výkonu řádků, které zobrazují klíčové metriky výkonu vašeho clusteru.

![Příklady grafů výkonu na kartě cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Grafy výkonu zobrazují čtyři metriky výkonu:

- **&nbsp; Využití % procesoru uzlem**: agregovaná perspektiva využití procesoru pro celý cluster. Pokud chcete filtrovat výsledky pro časový rozsah, vyberte v selektoru percentilu nad grafem hodnotu **AVG**, **min**, **50**, **devadesát**, **95.** nebo **Max** . Filtry lze použít buď jednotlivě, nebo v kombinaci.
- **&nbsp; Využití % paměti uzlu**: agregovaná perspektiva využití paměti pro celý cluster. Pokud chcete filtrovat výsledky pro časový rozsah, vyberte v selektoru percentilu nad grafem hodnotu **AVG**, **min**, **50**, **devadesát**, **95.** nebo **Max** . Filtry lze použít buď jednotlivě, nebo v kombinaci.
- **Počet uzlů**: počet uzlů a stav z Kubernetes. Stavy clusterů, které jsou reprezentovány, jsou celkem, připravené a nejsou připravené. Je možné je filtrovat individuálně nebo kombinovat v selektoru nad grafem.
- **Počet aktivních pod**: počet pod a stav z Kubernetes. Stavy, které jsou reprezentovány, jsou celkem, čeká, spuštěno, neznámý, úspěch nebo selhání. Je možné je filtrovat individuálně nebo kombinovat v selektoru nad grafem.

Pomocí šipek vlevo a vpravo můžete cyklicky přepínat mezi jednotlivými datovými body v grafu. Pomocí šipek nahoru a dolů můžete cyklicky procházet řádky percentilu. Vyberte ikonu připnutí v pravém horním rohu libovolného grafu, abyste mohli vybraný graf připnout na poslední prohlížený řídicí panel Azure. Z řídicího panelu můžete změnit velikost grafu nebo změnit jeho umístění. Výběr grafu z řídicího panelu vás přesměruje na Azure Monitor pro kontejnery a načte správný rozsah a zobrazení.

Azure Monitor for Containers také podporuje [Průzkumníka metrik](../platform/metrics-getting-started.md)Azure monitor, kde můžete vytvářet vlastní diagramy, sladit a prozkoumat trendy a připnout je na řídicí panely. Z Průzkumníka metrik můžete také použít kritéria, která jste nastavili k vizualizaci metrik, jako základ pro [pravidlo upozornění založené na metrikách](../platform/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Zobrazit metriky kontejneru v Průzkumníkovi metrik

V Průzkumníku metrik můžete zobrazit agregované metriky využití uzlů a pod Azure Monitor pro kontejnery. Následující tabulka shrnuje podrobnosti, které vám pomůžou pochopit, jak používat grafy metrik k vizualizaci metrik kontejnerů.

|Obor názvů | Metrika | Popis |
|----------|--------|-------------|
| Insights. Container/Nodes | |
| | cpuUsageMillicores | Agregované měření využití procesoru napříč clusterem. Jedná se o jádro procesoru rozdělené na 1000 jednotek (lisovny = 1000). Slouží k určení použití jader v kontejneru, kde mnoho aplikací může používat jeden Core.|
| | cpuUsagePercentage | Celkové průměrné využití procesoru měřené v procentech v rámci clusteru.|
| | memoryRssBytes | Využitá paměť RSS kontejneru v bajtech|
| | memoryRssPercentage | Paměť RSS kontejneru využitá v procentech|
| | memoryWorkingSetBytes | Využité paměti pracovní sady kontejneru.|
| | memoryWorkingSetPercentage | Paměť pracovní sady kontejneru využitá v procentech |
| | nodesCount | Počet uzlů z Kubernetes.|
| přehledy. kontejner/lusky | |
| | PodCount | Počet pod z Kubernetes.|

Můžete [rozdělit](../platform/metrics-charts.md#apply-splitting) metriku tak, aby se zobrazila podle dimenze, a vizualizovat, jak vzájemně porovnávají různé segmenty. Pro uzel můžete graf rozdělit podle dimenze *hostitele* . Z pod pod můžete segmentovat podle následujících dimenzí:

* Controller
* Obor názvů Kubernetes
* Uzel
* Fáze

## <a name="analyze-nodes-controllers-and-container-health"></a>Analýza stavů uzlů, řadičů a kontejnerů

Po přepnutí na karty **uzly**, **řadiče** a **kontejnery** se automaticky zobrazí podokno vlastností na pravé straně stránky. Zobrazuje vlastnosti vybrané položky, včetně popisků, které jste definovali pro uspořádání objektů Kubernetes. Když je vybrán uzel Linux, v části **kapacita místního disku** se zobrazí také dostupné místo na disku a procento využité pro každý disk prezentovaný uzlu. Vyberte **>>** odkaz v podokně pro zobrazení nebo skrytí podokna.

Při rozbalení objektů v hierarchii se podokno Vlastnosti aktualizuje podle vybraného objektu. Z podokna můžete také zobrazit protokoly kontejnerů Kubernetes (stdout/stderr), události a pod metrikou výběrem odkazu **Zobrazit živá data (Preview)** v horní části podokna. Další informace o konfiguraci potřebné k udělení a řízení přístupu pro zobrazení těchto dat najdete v tématu [Nastavení živých dat (Preview)](container-insights-livedata-setup.md). Při kontrole prostředků clusteru můžete tato data zobrazit z kontejneru v reálném čase. Další informace o této funkci najdete v tématu [jak zobrazit Kubernetes protokoly, události a metriky pod v reálném čase](container-insights-livedata-overview.md). Pokud chcete zobrazit data protokolu Kubernetes uložená ve vašem pracovním prostoru na základě předdefinovaných prohledávání protokolu, vyberte v rozevíracím seznamu **Zobrazit v analýze** možnost **Zobrazit protokoly kontejnerů** . Další informace o tomto tématu najdete v tématu [Analýza dat v protokolech hledání](container-insights-log-search.md#search-logs-to-analyze-data).

Pomocí možnosti **+ Přidat filtr** v horní části stránky můžete filtrovat výsledky pro zobrazení podle **služby**, **uzlu**, **oboru názvů** nebo **fondu uzlů**. Po výběru rozsahu filtru vyberte jednu z hodnot zobrazených v poli **Vybrat hodnoty** . Po nakonfigurování filtru se použije globálně při prohlížení jakékoli perspektivy clusteru AKS. Vzorec podporuje pouze rovnítko. K dalšímu zúžení výsledků můžete přidat další filtry nad první z nich. Pokud například zadáte Filter by **Node**, můžete pro druhý filtr vybrat pouze **službu** nebo **obor názvů** .

Zadání filtru na jedné kartě bude nadále použito, pokud vyberete jiný. Po výběru symbolu **x** vedle zadaného filtru se odstraní.

Přepněte na kartu **uzly** a hierarchie řádků se řídí objektovým modelem Kubernetes, který začíná uzlem v clusteru. Rozbalením uzlu zobrazíte jednu nebo více lusků spuštěných v uzlu. Pokud je více než jeden kontejner seskupen do seznamu pod, zobrazí se jako poslední řádek v hierarchii. Můžete také zobrazit, kolik úloh, které se nepoužívají, na hostiteli, pokud má hostitel tlak procesoru nebo paměti.

![Příklad hierarchie uzlu Kubernetes v zobrazení výkonu](./media/container-insights-analyze/containers-nodes-view.png)

Kontejnery Windows serveru, na kterých běží operační systém Windows Server 2019, se zobrazují po všech uzlech založených na systému Linux v seznamu. Když rozbalíte uzel systému Windows Server, můžete zobrazit jednu nebo více lusků a kontejnerů, které jsou spuštěny na uzlu. Po výběru uzlu se v podokně Vlastnosti zobrazí informace o verzi.

![Ukázková hierarchie uzlů s uvedenými uzly Windows serveru](./media/container-insights-analyze/nodes-view-windows.png)

Azure Container Instances virtuálních uzlů, které spouštějí operační systém Linux, se zobrazí za posledním uzlem clusteru AKS v seznamu. Když rozbalíte Container Instances virtuální uzel, můžete zobrazit jednu nebo více Container Instancesch lusků a kontejnerů, které jsou spuštěny na uzlu. Metriky nejsou shromažďovány a hlášeny pro uzly, pouze pro lusky.

![Ukázková hierarchie uzlů se Container Instances uvedenými](./media/container-insights-analyze/nodes-view-aci.png)

Z rozbaleného uzlu můžete procházet hierarchii pod nebo kontejnerem, který běží na uzlu, k zobrazení dat výkonu filtrovaných pro daný kontroler. Vyberte hodnotu pod sloupcem **kontroléru** pro konkrétní uzel.

![Snímek obrazovky s přechodem z uzlu na kontroler v zobrazení výkonu](./media/container-insights-analyze/drill-down-node-controller.png)

V horní části stránky vyberte řadiče nebo kontejnery, abyste si mohli prohlédnout stav a využití prostředků u těchto objektů. Pokud chcete zkontrolovat využití paměti, vyberte v rozevíracím seznamu **metrika** možnost **paměť RSS** nebo **pracovní sada paměti**. **Technologie RSS paměti** je podporována pouze pro Kubernetes verze 1,8 a novější. V opačném případě zobrazíte hodnoty pro **minimum &nbsp; %** jako *NaN &nbsp; %*, což je hodnota číselného datového typu, která představuje nedefinovanou nebo nereprezentovanou hodnotu.

![Zobrazení výkonu uzlů kontejnerů](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Pracovní sada paměti** zobrazuje jak rezidentní paměť, tak virtuální paměť (mezipaměť) a je celkový počet aplikací, které aplikace používá. **RSS paměti** zobrazuje pouze hlavní paměť (což není nic, ale rezidentní paměť jinými slovy). Tato metrika zobrazuje skutečnou kapacitu dostupné paměti. Jaký je rozdíl mezi rezidentní pamětí a virtuální pamětí?

- Rezidentní paměť nebo hlavní paměť je skutečná velikost paměti počítače, která je k dispozici pro uzly clusteru.

- Virtuální paměť je rezervované místo na pevném disku (mezipaměť) používané operačním systémem k výměně dat z paměti na disk v případě, že dojde k přetížení paměti, a pak ji v případě potřeby načtěte zpátky do paměti.

Ve výchozím nastavení jsou data o výkonu založena na posledních šesti hodinách, ale můžete změnit okno pomocí možnosti **TimeRange** vlevo nahoře. Výsledky můžete filtrovat také v časovém rozsahu tak, že v selektoru percentilu vyberete **min**, **AVG**, **50**, **devadesát**, **95.** a **Max** .

![Percentil – výběr pro filtrování dat](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Když najedete myší na pruhový graf ve sloupci **trend** , na každém řádku se zobrazí využití CPU nebo paměti v závislosti na tom, která metrika se vybrala, a to během vzorkovacího období 15 minut. Když vyberete graf trendu pomocí klávesnice, použijte klávesu ALT + PAGE UP nebo klávesu Alt + Page Down a procházejte jednotlivé panely samostatně. Budou se vám zobrazovat stejné podrobnosti, jako kdybyste najeďte na panel.

![Příklad přechodu ukazatele myši na pruhový graf trendu](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

V dalším příkladu se pro první uzel v seznamu *AKS-nodepool1-*, hodnota pro **kontejnery** je 9. Tato hodnota je souhrnem celkového počtu nasazených kontejnerů.

![Souhrn kontejnerů – příklad pro uzel](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Tyto informace vám pomůžou rychle zjistit, jestli máte v clusteru správnou rovnováhu mezi uzly.

Informace, které se zobrazí po zobrazení karty **uzly** , jsou popsány v následující tabulce.

| Sloupec | Popis |
|--------|-------------|
| Název | Název hostitele |
| Status | Kubernetes zobrazení stavu uzlu. |
| Minimální &nbsp; %, prům .% &nbsp; , 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, max.&nbsp;%  | Průměrné procento uzlů na základě percentilu během vybrané doby trvání. |
| Min, AVG, 50, 90, 95., Max | Průměrná hodnota uzlů na základě hodnoty percentilu v době zvolené doby trvání. Průměrná hodnota se měří od nastaveného limitu CPU/paměti pro uzel. V případě lusků a kontejnerů je to průměrná hodnota uvedená v hostiteli. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, kdy byl uzel spuštěn nebo byl restartován. |
| Controller | Pouze pro kontejnery a lusky. Zobrazuje, ve kterém řadiči se nachází. Ne všechny lusky jsou v kontroleru, takže se může zobrazit **N/a**. |
| Trend min &nbsp; %, AVG &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, Max&nbsp;% | Trend pruhového grafu představuje procento metriky průměrného percentilu řadiče. |

Po rozbalení uzlu s názvem **jiný proces** si můžete všimnout zatížení. Představuje nekontejnerové procesy, které běží na vašem uzlu, a zahrnuje:

* Samostatné spravované nebo spravované procesy Kubernetes bez kontejnerů

* Běhové procesy kontejneru

* Kubelet

* Systémové procesy spuštěné v uzlu

* Jiné úlohy bez Kubernetes spuštěné v hardwaru nebo na virtuálním počítači s uzlem

Počítá se podle: *celkové využití z CAdvisor*  -  *využití z kontejneru procesu*.

V selektoru vyberte možnost **řadiče**.

![Vybrat zobrazení řadičů](./media/container-insights-analyze/containers-controllers-tab.png)

Tady můžete zobrazit stav výkonu pro řadiče a Container Instances řadiče virtuálních uzlů nebo nepřipojené k řadiči virtuální uzel.

![\<Zobrazení výkonu názvů> řadičů](./media/container-insights-analyze/containers-controllers-view.png)

Hierarchie řádků začíná na řadiči. Když rozbalíte kontroler, zobrazíte jednu nebo více lusků. Rozbalte uzel pod a poslední řádek zobrazí kontejner seskupený do pole pod. Z rozbaleného kontroleru můžete přejít k podrobnostem uzlu, na kterém je spuštěný, aby se zobrazila data výkonu filtrovaná pro tento uzel. Container Instances lusky nepřipojené k řadiči se v seznamu zobrazí jako poslední.

![Příklad hierarchie řadičů s uvedením Container Instances lusky](./media/container-insights-analyze/controllers-view-aci.png)

Vyberte hodnotu ve sloupci **uzel** pro konkrétní kontroler.

![Příklad přechodu k podrobnostem z kontroleru na uzel v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení řadičů, jsou popsány v následující tabulce.

| Sloupec | Popis |
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav souhrnu kontejnerů po dokončení jeho spuštění se stavem, jako je například *OK*, *ukončeno*, *Chyba*, *Zastaveno* nebo *pozastaveno*. Pokud je kontejner spuštěný, ale stav buď nebyl správně zobrazen nebo nebyl vyzvednut agentem a nereagoval na více než 30 minut, je stav *Neznámý*. Další podrobnosti o ikoně stavu jsou uvedeny v následující tabulce.|
| Minimální &nbsp; %, prům .% &nbsp; , 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, max.&nbsp;%| Souhrnný průměr průměrného procenta každé entity pro vybranou metriku a percentil |
| Min, AVG, 50, 90, 95., Max  | Souhrn průměrného výkonu procesoru v millicore nebo paměti v kontejneru pro vybraný percentil. Průměrná hodnota se měří od limitu CPU nebo paměti nastaveného pro objekt pod. |
| Containers | Celkový počet kontejnerů pro řadič nebo pod. |
| Opětovné | Souhrn počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Uzel | Pouze pro kontejnery a lusky. Zobrazuje, ve kterém řadiči se nachází. |
| Trend min &nbsp; %, AVG &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, Max&nbsp;% | Trend pruhového grafu představuje metriku průměrné percentilu řadiče. |

Ikony v poli Stav označují online stav kontejnerů.

| Ikona | Status |
|--------|-------------|
| ![Ikona stavu spuštění připraveno](./media/container-insights-analyze/containers-ready-icon.png) | Spuštěno (připraveno)|
| ![Ikona stavu čekání nebo pozastavení](./media/container-insights-analyze/containers-waiting-icon.png) | Čekání nebo pozastavení|
| ![Ikona posledního hlášeného běžícího stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední zpráva byla spuštěna, ale nereagovala po dobu delší než 30 minut.|
| ![Ikona úspěšného stavu](./media/container-insights-analyze/containers-green-icon.png) | Stav se úspěšně zastavil nebo se nepovedlo zastavit.|

Ikona stavu zobrazuje počet na základě toho, co poskytuje. Zobrazuje nejhorší dva stavy a při najetí myší na stav zobrazuje souhrnný stav ze všech lusků v kontejneru. Pokud není připravený stav, zobrazí se hodnota stav **(0)**.

V selektoru vyberte **kontejnery**.

![Vybrat zobrazení kontejnerů](./media/container-insights-analyze/containers-containers-tab.png)

Tady si můžete prohlédnout stav výkonu pro Azure Kubernetes a kontejnery Azure Container Instances.

![\<Zobrazení výkonu názvů> kontejnerů](./media/container-insights-analyze/containers-containers-view.png)

Z kontejneru můžete přejít k podrobnostem nebo uzlu, abyste zobrazili data o výkonu filtrovaná pro daný objekt. Vyberte hodnotu pod sloupcem **pod** nebo **uzlu** pro konkrétní kontejner.

![Příklad přechodu k podrobnostem z uzlu na kontejnery v zobrazení výkonu](./media/container-insights-analyze/drill-down-controller-node.png)

Informace, které se zobrazí při zobrazení kontejnerů, jsou popsány v následující tabulce.

| Sloupec | Popis |
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav kontejnerů, pokud existují. Další podrobnosti o ikoně stavu jsou uvedeny v následující tabulce.|
| Minimální &nbsp; %, prům .% &nbsp; , 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, max.&nbsp;% | Souhrn průměrného procenta jednotlivých entit pro vybranou metriku a percentil. |
| Min, AVG, 50, 90, 95., Max | Souhrn průměrného výkonu procesoru v millicore nebo paměti v kontejneru pro vybraný percentil. Průměrná hodnota se měří od limitu CPU nebo paměti nastaveného pro objekt pod. |
| Nulu | Kontejner, ve kterém se nachází pod.|
| Uzel |  Uzel, ve kterém se nachází kontejner. |
| Opětovné | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, kdy byl kontejner spuštěn nebo restartován. |
| Trend min &nbsp; %, AVG &nbsp; %, 50 &nbsp; %, 90 &nbsp; %, 95. &nbsp; %, Max&nbsp;% | Trend pruhového grafu představuje procento metriky průměrného percentilu v kontejneru. |

Ikony v poli Stav označují stav online lusků, jak je popsáno v následující tabulce.

| Ikona | Status |
|--------|-------------|
| ![Ikona stavu spuštění připraveno](./media/container-insights-analyze/containers-ready-icon.png) | Spuštěno (připraveno)|
| ![Ikona stavu čekání nebo pozastavení](./media/container-insights-analyze/containers-waiting-icon.png) | Čekání nebo pozastavení|
| ![Ikona posledního hlášeného běžícího stavu](./media/container-insights-analyze/containers-grey-icon.png) | Poslední hlášení běželo, ale nereagovalo víc než 30 minut.|
| ![Ikona stavu ukončení](./media/container-insights-analyze/containers-terminated-icon.png) | Stav se úspěšně zastavil nebo se nepovedlo zastavit.|
| ![Ikona stavu selhání](./media/container-insights-analyze/containers-failed-icon.png) | Stav selhání |

## <a name="monitor-and-visualize-network-configurations"></a>Monitorování a vizualizace síťových konfigurací
Správce zásad sítě Azure zahrnuje informativní metriky Prometheus, které vám umožní monitorovat a lépe pochopit síťové konfigurace. Poskytuje integrované vizualizace v Azure Portal nebo Grafana Labs. Podrobnosti najdete v tématu [monitorování a vizualizace konfigurace sítě pomocí Azure npm](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm).


## <a name="workbooks"></a>Workbooks

Sešity kombinují text, dotazy protokolů, metriky a parametry do propracovaných interaktivních sestav, které umožňují analyzovat výkon clusteru. Popis sešitů dostupných pro Azure Monitor kontejnerů najdete [v tématu sešity v Azure monitor for Containers](container-insights-reports.md) .


## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [vytvoření výstrah výkonu pomocí Azure monitor pro kontejnery](./container-insights-log-alerts.md) , kde se dozvíte, jak vytvořit výstrahy s vysokým využitím procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.

- V [příkladech dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) si můžete prohlédnout předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizaci nebo analýze clusterů.

- Seznamte se s [monitorováním stavu clusteru](./container-insights-overview.md) , abyste se seznámili se zobrazením stavu clusteru Kubernetes.