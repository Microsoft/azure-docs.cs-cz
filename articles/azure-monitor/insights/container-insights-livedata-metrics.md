---
title: Zobrazení metrik v reálném čase pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje zobrazení metrikv reálném čase bez použití kubectl s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216591"
---
# <a name="how-to-view-metrics-in-real-time"></a>Jak zobrazit metriky v reálném čase

Azure Monitor pro kontejnery Live Data (preview) funkce umožňuje vizualizovat metriky o uzlu a stavu podu v clusteru v reálném čase. Emuluje přímý přístup `kubectl top nodes`k `kubectl get pods –all-namespaces`, `kubectl get nodes` a příkazy pro volání, analýzu a vizualizaci dat v grafech výkonu, které jsou součástí tohoto insight. 

Tento článek obsahuje podrobný přehled a pomůže vám pochopit, jak tuto funkci používat.  

>[!NOTE]
>Clustery AKS povolené jako [privátní clustery](https://azure.microsoft.com/updates/aks-private-cluster/) nejsou touto funkcí podporovány. Tato funkce závisí na přímém přístupu k rozhraní API Kubernetes prostřednictvím proxy serveru z vašeho prohlížeče. Povolení zabezpečení sítě blokovat Rozhraní API Kubernetes z tohoto proxy serveru bude blokovat tento provoz. 

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure China. V současné době není k dispozici v Azure US Government.

Nápovědu k nastavení nebo řešení potíží s funkcí Živá data (preview) naleznete v našem [průvodci nastavením](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Jak to funguje 

Funkce Živá data (preview) přímo přistupuje k rozhraní API Kubernetes a další informace o modelu ověřování naleznete [zde](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Tato funkce provádí operaci dotazování proti koncové `/api/v1/nodes` `/apis/metrics.k8s.io/v1beta1/nodes`body `/api/v1/pods`metriky (včetně , , a ), což je ve výchozím nastavení každých pět sekund. Tato data se uložených v mezipaměti ve vašem prohlížeči a zmapované ve čtyřech grafech výkonu zahrnuty v Azure Monitor pro kontejnery na kartě **Cluster** u výběru **Přejít live (náhled)**. Každá následující anketa je zmapována do postupného pětiminutového vizualizačního okna. 

![Možnost Přejít na živo v zobrazení clusteru](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Interval dotazování je konfigurován z rozevíracího souboru **Nastavit interval,** který umožňuje nastavit dotazování pro nová data každých 1, 5, 15 a 30 sekund. 

![Přejít na aktivní rozevírací interval dotazování](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Doporučujeme nastavit interval dotazování na jednu sekundu při řešení problému na krátkou dobu. Tyto požadavky mohou mít vliv na dostupnost a omezení rozhraní API Kubernetes ve vašem clusteru. Poté překonfigurujte na delší interval dotazování. 

>[!IMPORTANT]
>Během provozu této funkce nejsou trvale uložena žádná data. Všechny informace zachycené během této relace budou okamžitě odstraněny, když zavřete prohlížeč nebo přejdete mimo tuto funkci. Data zůstávají přítomna pouze pro vizualizaci uvnitř pětiminutového okna; všechny metriky starší než pět minut jsou také trvale odstraněny.

Tyto grafy nelze připnout na poslední řídicí panel Azure, který jste si prohlíželi v živém režimu.

## <a name="metrics-captured"></a>Zachycené metriky

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Využití procesoru uzlu % / Využití paměti uzlu % 

Tyto dva grafy výkonu mapovat na `kubectl top nodes` ekvivalent vyvolání a zachycení výsledků **CPU%** a **MEMORY%** sloupce do příslušného grafu. 

![Kubectl horní uzly příklad výsledků](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Graf procent uzly využití procesoru](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Graf procenta využití paměti uzlu](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Výpočty percentilu budou fungovat ve větších clusterech, které pomohou identifikovat odlehlé uzly ve vašem clusteru. Například pochopit, pokud uzly jsou nedostatečně využívány pro účely škálování. S využitím **min** agregace můžete vidět, které uzly mají nízké využití v clusteru. Chcete-li dále zkoumat, vyberte kartu **Uzly** a seřaďte mřížku podle využití procesoru nebo paměti.

To také pomáhá pochopit, které uzly jsou posunuty na jejich limity a pokud může být požadováno horizontální navýšení kapacity. Využití agregace **Max** a **P95** vám může pomoci zjistit, zda jsou v clusteru uzly s vysokým využitím prostředků. Pro další šetření byste znovu přepnout na kartu **Uzly.**

### <a name="node-count"></a>Počet uzlů

Tento graf výkonu mapuje na ekvivalent `kubectl get nodes` vyvolání a mapování sloupce **STATUS** na graf seskupený podle typů stavu.

![Kubectl získat uzly příklad výsledků](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Graf počtu uzlů](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Uzly jsou hlášeny buď ve stavu **Připraveno** nebo **Není připraven.** Počítají se (a vytvoří se celkový počet) a výsledky těchto dvou agregací jsou zmapovány.
Například pochopit, pokud vaše uzly spadají do selhání stavy. S využitím agregace **Není připravena** můžete rychle zobrazit počet uzlů v clusteru aktuálně ve stavu **Není připraven.**

### <a name="active-pod-count"></a>Počet aktivních podů

Tento graf výkonu mapuje na ekvivalent `kubectl get pods –all-namespaces` vyvolání a mapuje sloupec **STAV** grafu seskupený podle typů stavu.

![Kubectl získat lusky příklad výsledků](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Graf počtu uzlů podů](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Názvy stavu, jak `kubectl` je interpretováno, se nemusí přesně shodovat v grafu. 

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit předdefinované dotazy a příklady pro vytvoření výstrah, vizualizací nebo provedení další analýzy clusterů, zobrazte [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.
