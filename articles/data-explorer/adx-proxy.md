---
title: Dotazování dat ve službě Azure Monitor, pomocí Průzkumníka dat služby Azure (Preview)
description: V tomto tématu dotazování na data ve službě Azure Monitor tak, že vytvoříte proxy služby Průzkumník dat Azure pro smíšený produkt dotazy pomocí Application Insights a Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800208"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování dat ve službě Azure Monitor, pomocí Průzkumníka dat služby Azure (Preview)

Cluster proxy Průzkumníka služby Azure Data (ADX proxy serveru) je entita, která umožňuje provádět dotazy napříč produktu mezi Průzkumník dat Azure, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), a [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) v [Azure Monitor](/azure/azure-monitor/) služby. Jako cluster s proxy můžete namapovat pracovní prostory Log Analytics monitorování Azure nebo aplikace Application Insights. Můžete dotazovat proxy serveru clusteru pomocí nástroje Průzkumník dat Azure a na něj odkazovat v dotazu různé clusteru. Tento článek ukazuje, jak se připojit k proxy serveru clusteru, přidání proxy serveru clusteru do webového uživatelského rozhraní pro Průzkumník dat Azure a spustí dotazy na vaše aplikace AI nebo LA pracovní prostory z Průzkumníka služby Azure Data.

Tento tok proxy Průzkumníka služby Azure Data: 

![Tok ADX proxy](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> ADX Proxy je v režimu náhledu. Chcete-li tuto funkci povolit, obraťte se [ADXProxy](mailto:adxproxy@microsoft.com) týmu.

## <a name="connect-to-the-proxy"></a>Připojení k proxy serveru

1. Ověření clusteru nativní Průzkumník dat Azure (například *pomáhají* clusteru) se zobrazí v nabídce vlevo, než se připojit ke svému clusteru Log Analytics nebo Application Insights.

    ![Nativní clusteru ADX](media/adx-proxy/web-ui-help-cluster.png)

1. V uživatelském rozhraní Průzkumník dat Azure (https://dataexplorer.azure.com/clusters) vyberte **clusteru přidat**.

1. V **clusteru přidat** okno:

    * Přidejte adresu URL do clusteru LA nebo AI. Příklad: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Vyberte **Přidat**.

    ![Přidání clusteru](media/adx-proxy/add-cluster.png)

    Pokud chcete přidat připojení k více než jeden cluster proxy, zadejte každý jiný název. V opačném případě budou všechny mít stejný název v levém podokně.

1. Po připojení se naváže, LA nebo AI clusteru se zobrazí v levém podokně se nativní ADX clusteru. 

    ![Clustery s log Analytics a Průzkumník dat Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Spouštění dotazů

Kusto Průzkumníku ADX webové Průzkumníku Jupyter Kqlmagic nebo rozhraní REST API můžete použít k dotazování clusterů proxy serveru. 

> [!TIP]
> * Název databáze musí mít stejný název jako prostředek určený v proxy serveru clusteru. Názvy jsou malá a velká písmena.
> * V clusteru dotazy napříč, ujistěte se, že [názvů aplikací a pracovních prostorů](#application-insights-app-and-log-analytics-workspace-names) je správná.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Dotaz pro cluster nativní Průzkumník dat Azure 

Spouštění dotazů v clusteru Průzkumník dat Azure (například *StormEvents* tabulku v *pomáhají* clusteru). Při spuštění dotazu, zkontrolujte, zda cluster nativní Průzkumník dat Azure vybrán v levém podokně.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Dotaz na tabulku StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Dotaz u vašeho clusteru LA nebo AI

Při spouštění dotazů v clusteru LA nebo AL ověřte, že je vybraná LA nebo AI clusteru v levém podokně. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Pracovního prostoru LA dotazu](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>LA nebo AI cluster z proxy serveru ADX dotazovat  

Při spouštění dotazů v clusteru LA nebo AI z proxy serveru, ověřte, že je vybraný váš cluster nativní ADX v levém podokně. Následující příklad ukazuje dotaz pracovního prostoru LA používají nativní cluster ADX

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Dotaz z Průzkumníka služby Azure Data proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Různé dotazu LA nebo AI clusteru a clusteru ADX od ADX proxy 

Když spustíte dotazy napříč clusteru z proxy serveru, ověřte, že je vybraný váš cluster nativní ADX v levém podokně. Následující příklady ukazují kombinování ADX clusteru tabulky (pomocí `union`) k pracovnímu prostoru LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Různé dotazu z proxy serveru Průzkumník dat Azure](media/adx-proxy/cross-query-adx-proxy.png)

Použití [ `join` operátor](/azure/kusto/query/joinoperator), namísto sjednocení, můžou vyžadovat nápovědu pro spuštění v clusteru nativní Průzkumník dat Azure (a ne na proxy serveru). 

## <a name="additional-syntax-examples"></a>Příklady dalších syntaxe

Při volání metody clustery Application Insights (AI) nebo Log Analytics (LA) jsou k dispozici následující možnosti syntaxe:

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje pouze definovaných prostředků v tomto předplatném (**doporučené pro různé dotazy clusteru**) |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, který obsahuje všechny aplikace a pracovní prostory v rámci předplatného a jsou členy této skupiny prostředků    |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, který obsahuje pouze definovaných prostředků v tomto předplatném      |    cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Aplikace Application Insights a názvy pracovních prostorů Log Analytics

* Pokud názvy obsahují speciální znaky, že nahrazuje kódování v názvu clusteru proxy adres URL. 
* Pokud se názvy obsahují znaky, které nesplňují [KQL identifikátor názvu pravidla](/azure/kusto/query/schema-entities/entity-names), jsou nahrazeny čárka **-** znak.

## <a name="next-steps"></a>Další postup

[Zápis dotazů](write-queries.md)