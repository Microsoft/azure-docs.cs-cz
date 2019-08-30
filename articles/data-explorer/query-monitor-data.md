---
title: Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)
description: V tomto tématu se můžete dotazovat na data v Azure Monitor vytvořením proxy serveru Azure Průzkumník dat pro dotazy na více produktů pomocí Application Insights a Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172693"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)

Cluster proxy serveru Azure Průzkumník dat (proxy server ADX) je entita, která umožňuje provádět dotazy na více produktů mezi Azure Průzkumník dat, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) ve službě [Azure monitor](/azure/azure-monitor/) . Můžete namapovat Azure Monitor pracovní prostory Log Analytics nebo Application Insights aplikace jako proxy cluster. Pak můžete zadat dotaz na proxy cluster pomocí nástrojů Azure Průzkumník dat a odkazovat na něj v dotazu mezi clustery. Tento článek ukazuje, jak se připojit ke clusteru proxy, jak přidat proxy cluster do Azure Průzkumník dat webové uživatelské rozhraní a spouštět dotazy na aplikace AI nebo v pracovních prostorech LA z Azure Průzkumník dat.

Tok proxy serveru Azure Průzkumník dat: 

![Tok proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Proxy server ADX je v režimu náhledu. Pokud chcete tuto funkci povolit, obraťte se na tým [ADXProxy](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Připojit k proxy

1. Než se připojíte ke svému Log Analytics nebo Application Insights clusteru, ověřte, že je v levé nabídce zobrazený nativní cluster Azure Průzkumník dat (například cluster *help* ).

    ![Nativní cluster ADX](media/adx-proxy/web-ui-help-cluster.png)

1. V uživatelském rozhraní Průzkumník dat Azure (https://dataexplorer.azure.com/clusters) vyberte **Přidat cluster**.

1. V okně **Přidat cluster** :

    * Přidejte adresu URL do clusteru LA nebo AI. Příklad: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Vyberte **Přidat**.

    ![Přidání clusteru](media/adx-proxy/add-cluster.png)

    Pokud přidáte připojení k více než jednomu proxy serveru, zadejte jiný název. Jinak budou mít všichni stejný název v levém podokně.

1. Po navázání připojení se cluster pro LA nebo AI zobrazí v levém podokně s vaším nativním clusterem ADX. 

    ![Clustery Log Analytics a Azure Průzkumník dat](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Spouštění dotazů

K dotazování clusterů proxy můžete použít Kusto Explorer, ADX web Explorer, Jupyter Kqlmagic nebo REST API. 

> [!TIP]
> * Název databáze by měl mít stejný název jako prostředek zadaný v proxy clusteru. V názvech se rozlišují malá a velká písmena.
> * V dotazech mezi clustery se ujistěte, že je správně Naming Application Insights aplikace a pracovní prostory Log Analytics.
>     * Pokud názvy obsahují speciální znaky, budou nahrazeny kódováním adresy URL v názvu clusteru proxy serveru. 
>     * Pokud názvy obsahují znaky, které nevyhovují [pravidlům názvů identifikátorů KQL](/azure/kusto/query/schema-entities/entity-names), jsou nahrazeny **-** pomlčkou.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Dotaz na nativní cluster Azure Průzkumník dat 

Spouštění dotazů v clusteru Azure Průzkumník dat (například tabulka *StormEvents* v clusteru s rutinou *help* ). Při spuštění dotazu ověřte, že je v levém podokně vybraný nativní cluster Azure Průzkumník dat.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Tabulka StormEvents dotazu](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Dotaz na váš cluster pro LA nebo AI

Když spustíte dotazy v clusteru LA nebo AL, ověřte, že je v levém podokně vybraný cluster LA nebo AI. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Pracovní prostor dotaz LA](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Dotazování clusteru LA nebo AI z proxy serveru ADX  

Když spustíte dotazy z proxy serveru v clusteru LA nebo AI, ověřte, že je v levém podokně vybraný cluster ADX Native. Následující příklad demonstruje dotaz na pracovní prostor LA pomocí nativního clusteru ADX.

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Dotaz z proxy serveru Azure Průzkumník dat](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Mezi dotazem clusteru LA nebo AI a clusterem ADX z proxy serveru ADX 

Při spouštění meziclusterových dotazů z proxy serveru ověřte, že je v levém podokně vybraný cluster ADX Native. Následující příklady ukazují kombinaci tabulek clusteru ADX (using `union`) s pracovním prostorem La.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Vzájemný dotaz z proxy serveru Azure Průzkumník dat](media/adx-proxy/cross-query-adx-proxy.png)

Použití operátoru místo sjednocení může vyžadovat nápovědu pro jeho spuštění v nativním clusteru Azure Průzkumník dat (a ne na proxy serveru). [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>Další příklady syntaxe

K dispozici jsou následující možnosti syntaxe při volání Application Insights (AI) nebo Log Analytics (LA) clusterů:

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje jenom definovaný prostředek v tomto předplatném (**doporučuje se pro dotazování mezi clustery**). |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, který obsahuje všechny aplikace/pracovní prostory v rámci předplatného a jsou členy této skupiny prostředků    |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, který obsahuje jenom definovaný prostředek v tomto předplatném      |    cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Další postup

[Zápis dotazů](write-queries.md)