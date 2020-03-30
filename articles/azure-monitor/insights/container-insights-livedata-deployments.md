---
title: Zobrazení Azure Monitor pro nasazení kontejnerů (preview) | Dokumenty společnosti Microsoft
description: Tento článek popisuje zobrazení v reálném čase kubernetes nasazení bez použití kubectl v Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404778"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Jak zobrazit nasazení (náhled) v reálném čase

Pomocí Azure Monitor pro kontejnery, zobrazení nasazení (preview) funkce emuluje přímý přístup k objektům `kubeclt get deployments` `kubectl describe deployment {your deployment}` nasazení Kubernetes v reálném čase tím, že vystaví a příkazy. 

>[!NOTE]
>Clustery AKS povolené jako [privátní clustery](https://azure.microsoft.com/updates/aks-private-cluster/) nejsou touto funkcí podporovány. Tato funkce závisí na přímém přístupu k rozhraní API Kubernetes prostřednictvím proxy serveru z vašeho prohlížeče. Povolení zabezpečení sítě blokovat Rozhraní API Kubernetes z tohoto proxy serveru bude blokovat tento provoz. 

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure China. V současné době není k dispozici v Azure US Government.

Další informace najdete v dokumentaci k ubernetes o [nasazení .](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 

## <a name="how-it-works"></a>Jak to funguje

Funkce Živá data (preview) přímo přistupuje k rozhraní API Kubernetes a další informace o modelu ověřování naleznete [zde](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funkce Nasazení (preview) provádí jednorázové (aktualizovatelné) zatížení koncového `/apis/apps/v1/deployments`bodu nasazení . Umožňuje vybrat dané nasazení a načíst podrobnosti popisu pro toto `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`konkrétní nasazení proti koncovému bodu nasazení . 

Výběrem **možnosti Aktualizovat** v levém horním rohu stránky se seznam nasazení aktualizuje. To simuluje `kubectl` opětovné spuštění příkazu. 

>[!IMPORTANT]
>Během provozu této funkce nejsou trvale uložena žádná data. Všechny informace zachycené během relace budou odstraněny, když zavřete prohlížeč nebo od něj odejdete.  

>[!NOTE]
>Data živých dat (Preview) nelze připnout z konzoly na řídicí panel Azure.

## <a name="deployments-describe"></a>Nasazení popisují

Chcete-li zobrazit podrobnosti describe pro `kubectl describe deployment`nasazení, které jsou ekvivalentní , proveďte následující kroky.

1. Na webu Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **Monitorování** na levé straně zvolte **Přehledy**. 

3. Vyberte kartu **Nasazení (náhled).**

    ![Zobrazení nasazení na webu Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Zobrazení zobrazuje seznam všech spuštěných nasazení spolu s oborem názvů a dalšími podrobnými `kubectl get deployments –all-namespaces`informacemi, které emulují spuštění příkazu . Výsledky můžete seřadit výběrem libovolného sloupce. 

![Podrobnosti podokna vlastností nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Když vyberete nasazení ze seznamu, podokno vlastností se automaticky zobrazí na pravé straně stránky. Zobrazuje informace týkající se vybraného nasazení, které byste `kubectl describe deployment {deploymentName}`viděli, pokud byste příkaz spustili . Možná jste si všimli, že v informacích o popisu chybí některé podrobnosti. Nejvíce pozoruhodně **šablona** chybí. Výběrem karty **Nezpracovaná** umožňuje přejít na podrobnosti o neanalyzovaném popisu.  

![Nezpracovaná data podokna vlastností nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Při kontrole podrobností o nasazení můžete zobrazit protokoly kontejnerů a události v reálném čase. Vyberte **zobrazit živou konzolu** a podokno konzoly Živá data (náhled) se zobrazí pod datovou mřížkou nasazení, kde můžete zobrazit data živého protokolu v nepřetržitém datovém proudu. Pokud indikátor stavu načítání zobrazuje zelenou značku zaškrtnutí, která je zcela vpravo od podokna, znamená to, že data lze načíst a začne streamovat do konzole.

Můžete také filtrovat podle oborů názvů nebo událostí na úrovni clusteru. Další informace o zobrazení dat v reálném čase v konzole najdete v [tématu Zobrazení živých dat (preview) pomocí Azure Monitor pro kontejnery](container-insights-livedata-overview.md). 

![Nasazení zobrazení živých dat v konzole](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Další kroky

- Informace o používání Služby Azure Monitor a monitorování dalších aspektů clusteru AKS najdete v [tématu Zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Chcete-li zobrazit předdefinované dotazy a příklady pro vytvoření výstrah, vizualizací nebo provedení další analýzy clusterů, zobrazte [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.
