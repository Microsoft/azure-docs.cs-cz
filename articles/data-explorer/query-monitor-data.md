---
title: Dotazování dat v Azure Monitor s využitím Azure Průzkumník dat (Preview)
description: V tomto tématu se můžete dotazovat na data v Azure Monitor vytvořením proxy serveru Azure Průzkumník dat pro dotazy na více produktů pomocí Application Insights a Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560418"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)

Cluster proxy serveru Azure Průzkumník dat (proxy server ADX) je entita, která umožňuje provádět dotazy na více produktů mezi Azure Průzkumník dat, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs) ve službě [Azure monitor](/azure/azure-monitor/) . Můžete namapovat Azure Monitor pracovní prostory Log Analytics nebo Application Insights aplikace jako clustery proxy. Pak můžete zadat dotaz na proxy cluster pomocí nástrojů Azure Průzkumník dat a odkazovat na něj v dotazu mezi clustery. Tento článek ukazuje, jak se připojit ke clusteru proxy, jak přidat proxy cluster do Azure Průzkumník dat webové uživatelské rozhraní a spouštět dotazy na aplikace AI nebo v pracovních prostorech LA z Azure Průzkumník dat.

Tok proxy serveru Azure Průzkumník dat: 

![Tok proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Požadavky

> [!NOTE]
> Proxy server ADX je v režimu náhledu. [Připojte se k proxy serveru](#connect-to-the-proxy) , aby bylo možné povolit funkci proxy serveru ADX pro vaše clustery. Kontaktujte tým [ADXProxy](mailto:adxproxy@microsoft.com) s případnými dotazy.

## <a name="connect-to-the-proxy"></a>Připojit k proxy

1. Než se připojíte ke svému Log Analytics nebo Application Insights clusteru, ověřte, že je v levé nabídce zobrazený nativní cluster Azure Průzkumník dat (například cluster *help* ).

    ![Nativní cluster ADX](media/adx-proxy/web-ui-help-cluster.png)

1. V uživatelském rozhraní Azure Průzkumník dat (https://dataexplorer.azure.com/clusters)vyberte **Přidat cluster**.

1. V okně **Přidat cluster** přidejte adresu URL do clusteru La nebo AI. 
    
    * Pro LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Pro AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Vyberte **Přidat**.

    ![Přidání clusteru](media/adx-proxy/add-cluster.png)

    Pokud přidáte připojení k více než jednomu proxy serveru, zadejte jiný název. Jinak budou mít všichni stejný název v levém podokně.

1. Po navázání připojení se cluster pro LA nebo AI zobrazí v levém podokně s vaším nativním clusterem ADX. 

    ![Clustery Log Analytics a Azure Průzkumník dat](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Spouštění dotazů

Dotazy můžete spouštět pomocí klientských nástrojů, které podporují dotazy Kusto, například: Kusto Explorer, ADX web UI, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, Jarvis, Lens REST API.

> [!TIP]
> * Název databáze by měl mít stejný název jako prostředek zadaný v proxy clusteru. V názvech se rozlišují malá a velká písmena.
> * V dotazech mezi clustery se ujistěte, že je správně Naming Application Insights aplikace a pracovní prostory Log Analytics.
>     * Pokud názvy obsahují speciální znaky, budou nahrazeny kódováním adresy URL v názvu clusteru proxy serveru. 
>     * Pokud názvy obsahují znaky, které nevyhovují [pravidlům názvů identifikátorů KQL](/azure/kusto/query/schema-entities/entity-names), jsou nahrazeny pomlčkou **-** znakem.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Přímý dotaz z clusteru ADX serveru pro LA nebo AI

Spusťte dotazy v clusteru LA nebo AI. Ověřte, že je váš cluster vybraný v levém podokně. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Pracovní prostor dotaz LA](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Dotazování clusteru s proxy LA nebo AI ADX a nativním clusterem ADX 

Při spouštění meziclusterových dotazů z proxy serveru ověřte, že je v levém podokně vybraný cluster ADX Native. Následující příklady ukazují kombinaci tabulek clusteru ADX (pomocí `union`) s pracovním prostorem LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![vzájemný dotaz z proxy Azure Průzkumník dat](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Použití [operátoru`join`](/azure/kusto/query/joinoperator)namísto sjednocení může vyžadovat, aby se [`hint`](/azure/kusto/query/joinoperator#join-hints) spouštěla v nativním clusteru Azure Průzkumník dat (a ne na proxy serveru). 

## <a name="additional-syntax-examples"></a>Další příklady syntaxe

K dispozici jsou následující možnosti syntaxe při volání Application Insights (AI) nebo Log Analytics (LA) clusterů:

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje jenom definovaný prostředek v tomto předplatném (**doporučuje se pro dotazování mezi clustery**). |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, který obsahuje všechny aplikace/pracovní prostory v rámci předplatného a jsou členy této skupiny prostředků    |   cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, který obsahuje jenom definovaný prostředek v tomto předplatném      |    cluster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Další kroky

[Zápis dotazů](write-queries.md)
