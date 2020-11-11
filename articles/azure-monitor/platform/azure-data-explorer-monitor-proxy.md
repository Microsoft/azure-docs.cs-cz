---
title: Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)
description: Použijte Azure Průzkumník dat k provádění dotazů mezi produkty Azure Průzkumník dat, Log Analyticsmi pracovními prostory a klasickými Application Insights aplikacemi v Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 9b434c426264fcfee0dfe663a7d1b21a354badec
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491252"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)
Cluster Azure Průzkumník dat proxy umožňuje provádět dotazy na více produktů mezi službou Azure Průzkumník dat, Log Analyticsmi pracovními prostory a klasickými Application Insights aplikacemi v Azure Monitor. Můžete namapovat Log Analytics pracovní prostory v Azure Monitor nebo v klasických Application Insights aplikacích jako proxy clustery. Pak můžete zadat dotaz na proxy cluster pomocí nástrojů Azure Průzkumník dat a odkazovat na něj v dotazu mezi clustery. V tomto článku se dozvíte, jak se připojit ke clusteru proxy, jak přidat proxy cluster do Azure Průzkumník dat webové uživatelské rozhraní a spouštět dotazy v Log Analyticsch pracovních prostorech nebo klasických Application Insights aplikacích z Azure Průzkumník dat.

Následující diagram znázorňuje tok proxy serveru služby Azure Průzkumník dat:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Tok proxy serveru Azure Data Explorer.":::


> [!NOTE]
> Proxy server Azure Průzkumník dat je ve verzi Public Preview. [Připojte se k proxy serveru](#connect-to-the-proxy) , abyste mohli povolit funkci proxy serveru pro vaše clustery. 

## <a name="connect-to-the-proxy"></a>Připojit k proxy
Pokud chcete připojit Log Analytics pracovní prostor nebo klasickou Application Insightsovou aplikaci, otevřete[webové uživatelské rozhraní služby Azure Průzkumník dat](https://dataexplorer.azure.com/clusters). Než se připojíte ke svému Log Analytics nebo Application Insights clusteru, ověřte, že je v levé nabídce zobrazený nativní cluster Azure Průzkumník dat (například cluster *help* ).

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Nativní cluster Azure Průzkumník dat.":::

Klikněte na **Přidat cluster** a pak přidejte adresu URL Log Analytics nebo Application Insights clusteru v jednom z následujících formátů. 
    
* Pro Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Pro Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Klikněte na tlačítko **Přidat** a vytvořte připojení.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Přidejte cluster.":::
 
> [!NOTE]
> Pokud přidáte připojení k více než jednomu proxy serveru, zadejte jiný název. Jinak budou mít všichni stejný název v levém podokně.

Po navázání připojení se váš Log Analytics nebo cluster Application Insights zobrazí v levém podokně s vaším nativním clusterem služby Azure Průzkumník dat. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Clustery Log Analytics a Azure Průzkumník dat.":::
 
> [!NOTE]
> Počet Azure Monitor pracovních prostorů, které se dají mapovat, je omezený na 100.

## <a name="create-queries-using-azure-monitor-data"></a>Vytváření dotazů pomocí Azure Monitorch dat

Dotazy můžete spouštět pomocí klientských nástrojů, které podporují dotazy Kusto, například: Kusto Explorer, Azure Průzkumník dat Web UI, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, Jarvis, Lens a REST API.

> [!NOTE]
> Funkce proxy serveru Azure Průzkumník dat se používá jenom pro načtení dat. Další informace najdete v tématu věnovaném [podpoře funkcí](#function-supportability).

> [!TIP]
> * Název databáze by měl mít stejný název jako prostředek zadaný v proxy clusteru. V názvech se rozlišují malá a velká písmena.
> * V dotazech mezi clustery se ujistěte, že je správně Naming Application Insights aplikace a pracovní prostory Log Analytics.
>     * Pokud názvy obsahují speciální znaky, budou nahrazeny kódováním adresy URL v názvu clusteru proxy serveru. 
>     * Pokud názvy obsahují znaky, které nevyhovují [pravidlům názvů identifikátorů KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), jsou nahrazeny pomlčkou **-** .

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Přímý dotaz z vašeho Log Analytics nebo Application Insights clusteru proxy

Spouštění dotazů v clusteru Log Analytics nebo Application Insights. Ověřte, že je váš cluster vybraný v levém podokně. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Dotaz Log Analytics pracovní prostor.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Dotazování Log Analytics nebo proxy clusteru Application Insights a nativního clusteru Azure Průzkumník dat

Při spouštění meziclusterových dotazů z proxy serveru ověřte, že je v levém podokně vybraný nativní cluster Azure Průzkumník dat. Následující příklady ukazují kombinaci tabulek služby Azure Průzkumník dat cluster pomocí operátoru [Union](/azure/data-explorer/kusto/query/unionoperator) s pracovním prostorem Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Použití [ `join` operátoru](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)místo sjednocení může vyžadovat [nápovědu](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor#join-hints) pro jeho spuštění v nativním clusteru Azure Průzkumník dat (a ne na proxy serveru). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Připojení dat z clusteru Azure Průzkumník dat v jednom tenantovi s prostředkem Azure Monitor v jiném

Proxy Azure Průzkumník dat nepodporuje dotazy pro více tenantů. Jste přihlášení k jednomu klientovi pro spuštění dotazu zahrnujícího oba prostředky.

Pokud je prostředek Azure Průzkumník dat v Tenantovi a a Log Analytics pracovní prostor je v Tenantovi B, použijte jednu z následujících dvou metod:

- Azure Průzkumník dat umožňuje přidávat role pro objekty zabezpečení v různých klientech. Přidejte své ID uživatele v Tenantovi B jako autorizovaného uživatele v clusteru Azure Průzkumník dat. Ověřte, že vlastnost *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* v clusteru Azure Průzkumník dat obsahuje tenanta ' B '. Spuštění křížového dotazu je plně v Tenantovi B.

- Pomocí [Lighthouse](/azure/lighthouse/) můžete promítnout prostředek Azure monitor do tenanta "A".

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Připojení k clusterům Azure Průzkumník dat z různých tenantů

Průzkumník Kusto vás automaticky přihlásí k tenantovi, ke kterému uživatelský účet původně patří. Chcete-li získat přístup k prostředkům v jiných klientech se stejným uživatelským účtem, je `tenantId` nutné explicitně zadat v připojovacím řetězci: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Podpora funkcí

Cluster Azure Průzkumník dat proxy podporuje funkce pro Log Analytics i Application Insights. Tato možnost umožňuje, aby dotazy mezi clustery odkazovaly na Azure Monitor tabulkové funkce přímo.

Proxy podporuje následující příkazy:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Následující obrázek znázorňuje příklad dotazování tabulkové funkce z webového uživatelského rozhraní Azure Průzkumník dat. Chcete-li použít funkci, spusťte název v okně dotazu.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Dotazování tabulkové funkce z Průzkumník dat webového uživatelského rozhraní Azure":::
 
> [!NOTE]
> Azure Monitor podporuje jenom tabulkové funkce, které nepodporují parametry.

## <a name="additional-syntax-examples"></a>Další příklady syntaxe

Při volání Log Analytics nebo Application Insightsch clusterů jsou k dispozici následující možnosti syntaxe:

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje jenom definovaný prostředek v tomto předplatném ( **doporučuje se pro dotazování mezi clustery** ). |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Cluster, který obsahuje všechny aplikace/pracovní prostory v rámci předplatného a jsou členy této skupiny prostředků    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Cluster, který obsahuje jenom definovaný prostředek v tomto předplatném      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [struktuře dat Log Analytics pracovních prostorech a Application Insights](data-platform-logs.md).
- Naučte se [zapisovat dotazy do Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/write-queries).