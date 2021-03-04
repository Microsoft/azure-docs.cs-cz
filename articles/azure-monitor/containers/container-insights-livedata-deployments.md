---
title: Zobrazit nasazení v kontejneru Insights (Preview) | Microsoft Docs
description: Tento článek popisuje zobrazení Kubernetes nasazení v reálném čase bez použití kubectl ve službě Container Insights.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: c6eaac209234f7352395502c6761312cf258108f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713911"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Postup zobrazení nasazení (Preview) v reálném čase

Díky službě Container Insights funkce Zobrazit nasazení (Preview) emuluje přímý přístup k objektům Kubernetes nasazení v reálném čase tím, že zpřístupňuje `kubeclt get deployments` příkazy a `kubectl describe deployment {your deployment}` .

>[!NOTE]
>AKS clustery, které jsou povolené jako [soukromé clustery](https://azure.microsoft.com/updates/aks-private-cluster/) , s touto funkcí se nepodporují. Tato funkce spoléhá na přímý přístup z prohlížeče k rozhraní Kubernetes API přes proxy server. Povolením blokování rozhraní Kubernetes API z tohoto proxy serveru v zabezpečení sítě se tento provoz zablokuje.

Další informace najdete v dokumentaci k Kubernetes o [nasazeních](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="how-it-works"></a>Jak to funguje

Funkce Live data (Preview) přímo přistupuje k rozhraní Kubernetes API a další informace o modelu ověřování najdete [tady](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

Funkce nasazení (Preview) provádí jednorázové (obnovitelné) zatížení proti koncovému bodu nasazení `/apis/apps/v1/deployments` . Umožňuje vybrat dané nasazení a načíst podrobné informace o tomto konkrétním nasazení v rámci koncového bodu nasazení `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Po výběru možnosti **aktualizovat** v levém horním rohu stránky se aktualizuje seznam nasazení. To simuluje opětovné spuštění `kubectl` příkazu.

>[!IMPORTANT]
>Během operace této funkce nejsou trvale uloženy žádná data. Všechny informace zaznamenané během relace se odstraní, když zavřete prohlížeč nebo z něj opustíte.

>[!NOTE]
>Data živého data (Preview) z konzoly nemůžete připnout na řídicí panel Azure.

## <a name="deployments-describe"></a>Nasazení popisují

Chcete-li zobrazit podrobné informace o nasazení, což je ekvivalent `kubectl describe deployment` , proveďte následující kroky.

1. V Azure Portal přejděte do skupiny prostředků clusteru AKS a vyberte svůj prostředek AKS.

2. Na řídicím panelu clusteru AKS v části **monitorování** na levé straně vyberte **přehledy**.

3. Vyberte kartu **nasazení (Preview)** .

    ![Zobrazení nasazení v Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Zobrazení obsahuje seznam všech běžících nasazení společně s oborem názvů a dalšími podrobnými informacemi, které emuluje spuštění příkazu `kubectl get deployments –all-namespaces` . Výsledky můžete seřadit výběrem některého z těchto sloupců.

![Podrobnosti podokna vlastností nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Po výběru nasazení ze seznamu se automaticky zobrazí podokno vlastností na pravé straně stránky. Zobrazuje informace související s vybraným nasazením, které byste měli zobrazit při spuštění příkazu `kubectl describe deployment {deploymentName}` . Možná jste si všimli, že v popisech chybí nějaké informace. Zejména **Šablona** chybí. Výběr **nezpracované** karty vám umožní přejít na podrobné informace o neanalyzovaných analýzách.

![Nezpracované Podrobnosti podokna Vlastnosti nasazení](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Při revizi podrobností nasazení můžete zobrazit protokoly kontejnerů a události v reálném čase. Zaškrtněte podokno **Zobrazit konzolu Live** a živá data (Preview) se zobrazí pod datovou mřížkou nasazení, kde můžete data v reálném protokolu zobrazit v souvislém datovém proudu. Pokud se v indikátoru stavu načítání zobrazuje zelený symbol zaškrtnutí, který je na pravé straně podokna, znamená to, že se data dají načíst a zahájí streamování do konzoly.

Můžete také filtrovat podle oboru názvů nebo událostí na úrovni clusteru. Další informace o zobrazení dat v reálném čase v konzole nástroje najdete v tématu [zobrazení živých dat (Preview) pomocí služby Container Insights](container-insights-livedata-overview.md).

![Nasazení zobrazují živá data v konzole nástroje.](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Další kroky

- Pokud chcete pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty clusteru AKS, přečtěte si téma [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vytváření výstrah, vizualizací nebo provádění dalších analýz vašich clusterů.
