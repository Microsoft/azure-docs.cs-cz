---
title: Zobrazení metrik v reálném čase pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje zobrazení metrik v reálném čase bez použití kubectl s Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 81d7210778fd6b5d75fb4b4fa8e066d2e015174f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85338023"
---
# <a name="how-to-view-metrics-in-real-time"></a>Jak zobrazit metriky v reálném čase

Funkce Azure Monitor for Containers Live data (Preview) umožňuje vizualizovat metriky o stavu uzlu a pod v clusteru v reálném čase. Emuluje přímý přístup k `kubectl top nodes` `kubectl get pods –all-namespaces` `kubectl get nodes` příkazům, a pro volání, analýzu a vizualizaci dat v grafech výkonu, které jsou součástí tohoto přehledu.

Tento článek poskytuje podrobný přehled a pomůže vám pochopit, jak tuto funkci používat.

>[!NOTE]
>AKS clustery, které jsou povolené jako [soukromé clustery](https://azure.microsoft.com/updates/aks-private-cluster/) , s touto funkcí se nepodporují. Tato funkce spoléhá přímo na rozhraní Kubernetes API prostřednictvím proxy server z prohlížeče. Když zapnete zabezpečení sítě, zablokujete tím, že rozhraní Kubernetes API z tohoto proxy serveru znemožní tento provoz.

Nápovědu k nastavení nebo řešení potíží s funkcí živá data (Preview) najdete v naší [příručce k instalaci](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Jak to funguje

Funkce Live data (Preview) přímo přistupuje k rozhraní Kubernetes API a další informace o modelu ověřování najdete [tady](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Tato funkce provádí operaci cyklického dotazování proti koncovým bodům metriky (včetně `/api/v1/nodes` , `/apis/metrics.k8s.io/v1beta1/nodes` a `/api/v1/pods` ), což je ve výchozím nastavení každých pět sekund. Tato data se uloží do mezipaměti v prohlížeči a naplní se čtyřmi grafy výkonu, které jsou součástí Azure Monitor pro kontejnery na kartě **cluster** , a to výběrem možnosti **Přejít na aktivní (Preview)**. Každé následné dotazování je rozdělené do více než pět minut okna vizualizace.

![Možnost přejít do živého zobrazení v clusteru](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Interval dotazování se konfiguruje v rozevíracím seznamu **nastavit interval** , který umožňuje nastavit dotazování pro nová data každých 1, 5, 15 a 30 sekund.

![Interval cyklického dotazování na živý seznam](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Doporučujeme nastavit interval dotazování na jednu sekundu a vyřešit problém po krátkou dobu. Tyto požadavky mohou mít vliv na dostupnost a omezování rozhraní Kubernetes API v clusteru. Následně znovu nakonfigurujte na delší interval dotazování.

>[!IMPORTANT]
>Během operace této funkce nejsou trvale uloženy žádná data. Všechny informace zachycené během této relace jsou okamžitě odstraněny při zavření prohlížeče nebo při opuštění funkce. Data zůstávají pouze pro vizualizaci v pěti minutách okna. Všechny metriky starší než pět minut jsou také trvale odstraněny.

Tyto grafy nejde připnout k poslednímu řídicímu panelu Azure, který jste prohlíželi v živém režimu.

## <a name="metrics-captured"></a>Zaznamenané metriky

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Využití CPU v uzlu%/využití paměti uzlu%

Tyto dva grafy výkonu se mapují na ekvivalent vyvolání `kubectl top nodes` a zachytí výsledky **procesoru%** a **paměti%** sloupců do příslušného grafu.

![Příklady výsledků Kubectl hlavních uzlů](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Graf procento využití CPU uzlů](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Graf procento využití paměti uzlu](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Výpočty percentilu budou fungovat ve větších clusterech, aby lépe identifikovaly uzly izolované ve vašem clusteru. Například pro pochopení, zda jsou uzly využívány pro účely horizontálního navýšení kapacity. Využití **minimální** agregace vám umožní zjistit, které uzly mají v clusteru nízké využití. Pro další zkoumání můžete vybrat kartu **uzly** a seřadit mřížku podle využití procesoru nebo paměti.

To vám také pomůže pochopit, které uzly jsou vloženy do jejich omezení a zda může být vyžadováno horizontální navýšení kapacity. Použití agregací **Max** i **P95** vám může pomáhat zjistit, jestli v clusteru existují uzly s vysokým využitím prostředků. Pro další šetření byste znovu přešli na kartu **uzly** .

### <a name="node-count"></a>Počet uzlů

Tento graf výkonu se mapuje na ekvivalent vyvolání `kubectl get nodes` a mapování sloupce **stav** na graf seskupený podle typů stavu.

![Příklady výsledků Kubectl Get Nodes](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Graf počtu uzlů](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Uzly jsou hlášeny buď ve stavu **připraveno** , nebo **není připraveno** . Jsou počítány (a je vytvořen celkový počet) a výsledky těchto dvou agregací jsou grafy.
Například pro pochopení, zda uzly spadají do stavu selhání. Díky agregaci, která **není připravena** , můžete rychle zobrazit počet uzlů v clusteru, které jsou aktuálně ve stavu **Nepřipraveno** .

### <a name="active-pod-count"></a>Počet aktivních pod

Tento graf výkonu se mapuje na ekvivalent vyvolání `kubectl get pods –all-namespaces` a mapuje sloupec status ( **stav** ) graf seskupený podle typů stavu.

![Příklady výsledků Kubectl Get v luskech](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Graf počtu uzlů pod](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Názvy stavů, které jsou interpretovány `kubectl` v grafu, nemusí přesně odpovídat.

## <a name="next-steps"></a>Další kroky

Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vytváření výstrah, vizualizací nebo provádění dalších analýz vašich clusterů.
