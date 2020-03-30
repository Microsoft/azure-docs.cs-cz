---
title: Dotazujte se na data v Azure Monitoru pomocí Azure Data Exploreru (Preview)
description: V tomto tématu se dotazujte na data v Azure Monitoru vytvořením proxy azure data exploreru pro dotazy napříč produkty pomocí přehledů aplikací a analýzy protokolů.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560418"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování na data v Azure Monitoru pomocí Azure Data Exploreru (Preview)

Cluster proxy Azure Data Explorer (ADX Proxy) je entita, která umožňuje provádět dotazy napříč produkty mezi Průzkumníkem dat Azure, [Přehledy aplikací (AI)](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) ve službě [Azure Monitor.](/azure/azure-monitor/) Pracovní prostory Azure Monitor Log Analytics nebo aplikace Application Insights můžete mapovat jako proxy clustery. Potom můžete dotaz proxy clusteru pomocí nástrojů Azure Data Explorer a odkazovat na něj v křížovém clusteru dotazu. Článek ukazuje, jak se připojit ke clusteru proxy, přidat proxy cluster do webového uživatelského rozhraní Aplikace Azure Data Explorer a spouštět dotazy na aplikace AI nebo pracovní prostory LA z Průzkumníka dat Azure.

Tok proxy aplikace Azure Data Explorer: 

![Tok proxy serveru ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Proxy aDX je v režimu náhledu. [Chcete-li](#connect-to-the-proxy) povolit funkci proxy serveru ADX pro vaše clustery, připojte se k serveru proxy. S případnými dotazy se obraťte na tým [ADXProxy.](mailto:adxproxy@microsoft.com)

## <a name="connect-to-the-proxy"></a>Připojení k proxy serveru

1. Před připojením k clusteru Log Analytics nebo Application Insights ověřte nativní cluster Průzkumníka dat Azure (například cluster *nápovědy).*

    ![Nativní cluster ADX](media/adx-proxy/web-ui-help-cluster.png)

1. V unovém rozhraní Průzkumníka dat Azure (https://dataexplorer.azure.com/clusters)vyberte Přidat **cluster**.

1. V okně **Přidat cluster** přidejte adresu URL do clusteru LA nebo AI. 
    
    * Pro LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Pro umělou ai:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Vyberte **Přidat**.

    ![Přidání clusteru](media/adx-proxy/add-cluster.png)

    Pokud přidáte připojení k více než jednomu clusteru proxy, zadejte každému jiný název. Jinak budou mít všichni stejný název v levém podokně.

1. Po navázání připojení se cluster LA nebo AI zobrazí v levém podokně s nativním clusterem ADX. 

    ![Clustery Log Analytics a Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Spouštění dotazů

Dotazy můžete spouštět pomocí klientských nástrojů, které podporují dotazy Kusto, například: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * Název databáze by měl mít stejný název jako prostředek zadaný v clusteru proxy. Názvy rozlišují malá a velká písmena.
> * V dotazech napříč clustery se ujistěte, že je správné pojmenování aplikací Application Insights a pracovních prostorů Log Analytics.
>     * Pokud názvy obsahují speciální znaky, jsou nahrazeny kódováním adres URL v názvu proxy clusteru. 
>     * Pokud názvy obsahují znaky, které nesplňují [pravidla názvu identifikátoru KQL](/azure/kusto/query/schema-entities/entity-names), jsou nahrazeny znakem pomlčky. **-**

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Přímý dotaz z clusteru proxy LA nebo AI ADX

Spouštějte dotazy v clusteru LA nebo AI. Ověřte, zda je cluster vybrán v levém podokně. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Pracovní prostor LA dotazu](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Křížový dotaz clusteru proxy LA nebo AI ADX a nativního clusteru ADX 

Při spuštění dotazů mezi clustery z proxy serveru ověřte, zda je nativní cluster ADX vybraný v levém podokně. Následující příklady ukazují kombinaci tabulek clusteru ADX (pomocí) `union`s pracovním prostorem LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Křížový dotaz z proxy serveru Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Použití [ `join` operátoru](/azure/kusto/query/joinoperator), namísto sjednocení, může vyžadovat [`hint`](/azure/kusto/query/joinoperator#join-hints) spuštění v nativním clusteru Azure Data Explorer (a ne na proxy serveru). 

## <a name="additional-syntax-examples"></a>Další příklady syntaxe

Následující možnosti syntaxe jsou k dispozici při volání clusterů Application Insights (AI) nebo Log Analytics (LA):

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje pouze definovaný prostředek v tomto předplatném **(doporučeno pro dotazy mezi clustery)** |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, který obsahuje všechny aplikace nebo pracovní prostory v předplatném a jsou členy této skupiny prostředků    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, který obsahuje pouze definovaný prostředek v tomto předplatném      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Další kroky

[Zápis dotazů](write-queries.md)
