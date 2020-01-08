---
title: Zobrazit Azure Monitor pro nasazení kontejnerů (Preview) | Microsoft Docs
description: Tento článek popisuje zobrazení Kubernetes nasazení v reálném čase bez použití kubectl v Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404778"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Postup zobrazení nasazení (Preview) v reálném čase

U Azure Monitor pro kontejnery, funkce Zobrazit nasazení (Preview) emuluje přímý přístup k objektům Kubernetes nasazení v reálném čase tím, že vystavuje příkazy `kubeclt get deployments` a `kubectl describe deployment {your deployment}`. 

>[!NOTE]
>AKS clustery, které jsou povolené jako [soukromé clustery](https://azure.microsoft.com/updates/aks-private-cluster/) , s touto funkcí se nepodporují. Tato funkce spoléhá přímo na rozhraní Kubernetes API prostřednictvím proxy server z prohlížeče. Když zapnete zabezpečení sítě, zablokujete tím, že rozhraní Kubernetes API z tohoto proxy serveru znemožní tento provoz. 

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure Čína. V tuto chvíli není dostupná ve službě Azure USA pro státní správu.

Další informace najdete v dokumentaci k Kubernetes o [nasazeních](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Jak to funguje

Funkce Live data (Preview) přímo přistupuje k rozhraní Kubernetes API a další informace o modelu ověřování najdete [tady](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funkce nasazení (Preview) provádí jednorázové (obnovitelné) načtení do koncového bodu nasazení `/apis/apps/v1/deployments`. Umožňuje vybrat dané nasazení a načíst podrobné informace o tomto konkrétním nasazení proti koncovému bodu nasazení `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`. 

Po výběru možnosti **aktualizovat** v levém horním rohu stránky se aktualizuje seznam nasazení. To simuluje opětovné spuštění `kubectl`ho příkazu. 

>[!IMPORTANT]
>Během operace této funkce nejsou trvale uloženy žádná data. Všechny informace zaznamenané během relace se odstraní, když zavřete prohlížeč nebo z něj opustíte.  

>[!NOTE]
>Data živého data (Preview) z konzoly nemůžete připnout na řídicí panel Azure.

## <a name="deployments-describe"></a>Nasazení popisují

Chcete-li zobrazit podrobné informace o nasazení, což je ekvivalentem `kubectl describe deployment`, proveďte následující kroky.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **monitorování** na levé straně vyberte **přehledy**. 

3. Vyberte kartu **nasazení (Preview)** .

    ![Zobrazení nasazení v Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Zobrazení obsahuje seznam všech běžících nasazení společně s oborem názvů a dalšími podrobnými informacemi, které emuluje spuštění příkazu `kubectl get deployments –all-namespaces`. Výsledky můžete seřadit výběrem některého z těchto sloupců. 

![Podrobnosti podokna vlastností nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Po výběru nasazení ze seznamu se automaticky zobrazí podokno vlastností na pravé straně stránky. Zobrazuje informace související s vybraným nasazením, které byste měli zobrazit, pokud jste spustili příkaz `kubectl describe deployment {deploymentName}`. Možná jste si všimli, že v popisech chybí nějaké informace. Zejména **Šablona** chybí. Výběr **nezpracované** karty vám umožní přejít na podrobné informace o neanalyzovaných analýzách.  

![Nezpracované Podrobnosti podokna Vlastnosti nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Při revizi podrobností nasazení můžete zobrazit protokoly kontejnerů a události v reálném čase. Zaškrtněte podokno **Zobrazit konzolu Live** a živá data (Preview) se zobrazí pod datovou mřížkou nasazení, kde můžete data v reálném protokolu zobrazit v souvislém datovém proudu. Pokud se v indikátoru stavu načítání zobrazuje zelený symbol zaškrtnutí, který je na pravé straně podokna, znamená to, že se data dají načíst a zahájí streamování do konzoly.

Můžete také filtrovat podle oboru názvů nebo událostí na úrovni clusteru. Další informace o zobrazení dat v reálném čase v konzole nástroje najdete v tématu [zobrazení živých dat (Preview) s Azure monitor pro kontejnery](container-insights-livedata-overview.md). 

![Nasazení zobrazují živá data v konzole nástroje.](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Další kroky

- Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vytváření výstrah, vizualizací nebo provádění dalších analýz vašich clusterů.
