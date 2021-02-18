---
title: Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)
description: Použijte Azure Průzkumník dat k provádění dotazů mezi produkty Azure Průzkumník dat, Log Analyticsmi pracovními prostory a klasickými Application Insights aplikacemi v Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a31ef69d84f64e4bcaa46adac26a29d2cc367351
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611428"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat (Preview)

Azure Průzkumník dat podporuje dotazy mezi službami mezi Azure Průzkumník dat, [Application Insights (AI)](../app/app-insights-overview.md)a [Log Analytics (La)](./data-platform-logs.md). Potom můžete do pracovního prostoru Log Analytics/Application Insights zadat dotaz pomocí nástrojů Azure Průzkumník dat a odkazovat na něj v dotazu mezi jednotlivými službami. V tomto článku se dozvíte, jak vytvořit dotaz mezi služby a jak přidat pracovní prostor Log Analytics/Application Insights do webového uživatelského rozhraní služby Azure Průzkumník dat.

Tok dotazů služby Azure Průzkumník dat mezi službami: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="tok proxy serveru Azure Data Explorer.":::

> [!NOTE]
> * Možnost dotazování dat Azure Monitor z Azure Průzkumník dat, a to buď přímo z klientských nástrojů Azure Průzkumník dat, nebo nepřímo spuštěním dotazu na cluster Průzkumník dat Azure je v režimu náhledu.
>* Kontaktujte tým [dotazů na vzájemné služby](mailto:adxproxy@microsoft.com) s případnými dotazy.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Přidání pracovního prostoru Log Analytics/Application Insights do Azure Průzkumník dat klientských nástrojů

1. Než se připojíte ke svému Log Analytics nebo Application Insights clusteru, ověřte, že je v levé nabídce zobrazený nativní cluster Azure Průzkumník dat (například cluster *help* ).

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Nativní cluster Azure Průzkumník dat.":::

 V uživatelském rozhraní Průzkumník dat Azure ( https://dataexplorer.azure.com/clusters) Vyberte **Přidat cluster**.

2. V okně **Přidat cluster** přidejte adresu URL clusteru La nebo AI.

    * Pro LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Pro AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Vyberte **Přidat**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Přidejte cluster.":::
 
>[!NOTE]
>Pokud přidáte připojení k více než jednomu pracovnímu prostoru Log Analytics nebo Application Insights, zadejte jiný název. Jinak budou mít všichni stejný název v levém podokně.

 Po navázání připojení se váš Log Analytics nebo Application Insights pracovní prostor zobrazí v levém podokně s vaším nativním clusterem Azure Průzkumník dat.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Clustery Log Analytics a Azure Průzkumník dat.":::
 
> [!NOTE]
> Počet Azure Monitor pracovních prostorů, které se dají mapovat, je omezený na 100.

## <a name="create-queries-using-azure-monitor-data"></a>Vytváření dotazů pomocí Azure Monitorch dat

Dotazy můžete spouštět pomocí klientských nástrojů, které podporují dotazy Kusto, například: Kusto Explorer, Azure Průzkumník dat Web UI, Jupyter Kqlmagic, flow, PowerQuery, PowerShell, Lens, REST API.

> [!NOTE]
> Možnost dotazování mezi službami se používá jenom pro načtení dat. Další informace najdete v tématu věnovaném [podpoře funkcí](#function-supportability).

> [!TIP]
> * Název databáze by měl mít stejný název jako prostředek zadaný v dotazu vzájemné služby. V názvech se rozlišují malá a velká písmena.
> * V dotazech mezi clustery se ujistěte, že je správně Naming Application Insights aplikace a pracovní prostory Log Analytics.
> * Pokud názvy obsahují speciální znaky, budou nahrazeny kódováním adresy URL v dotazu vzájemné služby.
> * Pokud názvy obsahují znaky, které nevyhovují [pravidlům názvů identifikátorů KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), jsou nahrazeny pomlčkou **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Přímé dotazování na Log Analytics nebo Application Insights pracovních prostorů z Azure Průzkumník dat klientských nástrojů

Spouštějte dotazy ve Log Analytics nebo Application Insights pracovních prostorech. Ověřte, že je v levém podokně vybraný pracovní prostor.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Dotaz Log Analytics pracovní prostor.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Vzájemné dotazování Log Analytics nebo Application Insights a nativního clusteru Azure Průzkumník dat

Když spouštíte dotazy meziclusterové služby, ověřte, že je v levém podokně vybraný nativní cluster Azure Průzkumník dat. Následující příklady ukazují kombinaci tabulek clusteru Azure Průzkumník dat [pomocí sjednocení](/azure/data-explorer/kusto/query/unionoperator) s Log Analytics pracovním prostorem.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Dotaz na vzájemnou službu z Průzkumník dat Azure":::

Použití [ `join` operátoru](/azure/data-explorer/kusto/query/joinoperator)namísto sjednocení může vyžadovat, aby se [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) spustilo v nativním clusteru Azure Průzkumník dat.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Připojení dat z clusteru Azure Průzkumník dat v jednom tenantovi s prostředkem Azure Monitor v jiném

Mezi službami nejsou podporované dotazy mezi klienty. Jste přihlášení k jednomu klientovi pro spuštění dotazu zahrnujícího oba prostředky.

Pokud je prostředek Azure Průzkumník dat v Tenantovi a a Log Analytics pracovní prostor je v Tenantovi B, použijte jednu z následujících dvou metod:

1. Azure Průzkumník dat umožňuje přidávat role pro objekty zabezpečení v různých klientech. Přidejte své ID uživatele v Tenantovi B jako autorizovaného uživatele v clusteru Azure Průzkumník dat. Ověřte, že vlastnost *[' TrustedExternalTenant '](/powershell/module/az.kusto/update-azkustocluster)* v clusteru Azure Průzkumník dat obsahuje tenanta ' B '. Spuštění křížového dotazu je plně v Tenantovi B.

2. Pomocí [Lighthouse](../../lighthouse/index.yml) můžete promítnout prostředek Azure monitor do tenanta "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Připojení k clusterům Azure Průzkumník dat z různých tenantů

Průzkumník Kusto vás automaticky přihlásí k tenantovi, ke kterému uživatelský účet původně patří. Chcete-li získat přístup k prostředkům v jiných klientech se stejným uživatelským účtem, je `tenantId` nutné explicitně zadat v připojovacím řetězci: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Podpora funkcí

Dotazy služby Azure Průzkumník dat mezi službami podporují funkce pro Application Insights i Log Analytics.
Tato možnost umožňuje, aby dotazy mezi clustery odkazovaly na Azure Monitor tabulkové funkce přímo.
Dotaz na různé služby podporuje následující příkazy:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Následující obrázek znázorňuje příklad dotazování tabulkové funkce z webového uživatelského rozhraní Azure Průzkumník dat.
Chcete-li použít funkci, spusťte název v okně dotazu.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Dotazování tabulkové funkce z Průzkumník dat webového uživatelského rozhraní Azure":::

## <a name="additional-syntax-examples"></a>Další příklady syntaxe

Při volání Log Analytics nebo Application Insightsch clusterů jsou k dispozici následující možnosti syntaxe:

|Popis syntaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databáze v rámci clusteru, který obsahuje jenom definovaný prostředek v tomto předplatném (**doporučuje se pro dotazování mezi clustery**). |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Cluster, který obsahuje všechny aplikace a pracovní prostory v tomto předplatném    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Cluster, který obsahuje všechny aplikace/pracovní prostory v rámci předplatného a jsou členy této skupiny prostředků    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Cluster, který obsahuje jenom definovaný prostředek v tomto předplatném      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [struktuře dat Log Analytics pracovních prostorech a Application Insights](data-platform-logs.md).
- Naučte se [zapisovat dotazy do Azure Průzkumník dat](/azure/data-explorer/write-queries).