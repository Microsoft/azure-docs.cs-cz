---
title: Dotazování mezi prostředky Azure Průzkumník dat pomocí Azure Monitor
description: Pomocí Azure Monitor můžete provádět dotazy mezi produkty Azure Průzkumník dat, Log Analytics pracovní prostory a klasické Application Insights aplikace v Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552642"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Dotazování mezi prostředky Azure Průzkumník dat pomocí Azure Monitor
Azure Monitor podporuje dotazy mezi službami mezi Azure Průzkumník dat, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Pak můžete dotazovat cluster Azure Průzkumník dat pomocí nástrojů pro Log Analytics nebo Application Insights a pak na něj odkazovat v dotazu mezi jednotlivými službami. V tomto článku se dozvíte, jak vytvořit dotaz mezi jednotlivými službami.

Tok Azure Monitor mezi službami: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="tok Azure monitor a azure Průzkumník dat mezi službami.":::

>[!NOTE]
>* Azure Monitor proxy je v privátní verzi Preview – vyžaduje se AllowListing.
>* Kontaktujte [tým služby](mailto:ADXProxy@microsoft.com) s případnými dotazy.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Vzájemné dotazování Log Analytics nebo prostředků Application Insights a Azure Průzkumník dat

Dotazy na různé prostředky můžete spustit pomocí klientských nástrojů, které podporují dotazy Kusto, například: Log Analytics webové uživatelské rozhraní, sešity, PowerShell, REST API a další.

* Zadejte identifikátor pro cluster Azure Průzkumník dat v dotazu ve vzoru "ADX" následovaný názvem databáze a tabulkou.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Příklad dotazu mezi službami.":::

> [!NOTE]
>* V názvech databází se rozlišují velká a malá písmena.
>* Dotaz na různé prostředky jako výstrahu není podporován.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Kombinování tabulek Azure Průzkumník dat clusterů (pomocí sjednocení a spojení) s pracovním prostorem LA

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Příklad dotazu mezi službami a sjednocením.":::

>[!Tip]
>* Je povolený zkrácený formát – název_clusteru/vlastnost InitialCatalog. Například ADX (Help/Samples) se převede na ADX (Help. kusto. Windows. NET/Samples).
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Připojení dat z clusteru Azure Průzkumník dat v jednom tenantovi s prostředkem Azure Monitor v jiném

Mezi službami nejsou podporované dotazy mezi klienty. Jste přihlášení k jednomu klientovi pro spuštění dotazu zahrnujícího oba prostředky.

Pokud je prostředek Azure Průzkumník dat v Tenantovi a a Log Analytics pracovní prostor je v Tenantovi B, použijte jednu z následujících dvou metod:

*  Azure Průzkumník dat umožňuje přidávat role pro objekty zabezpečení v různých klientech. Přidejte své ID uživatele v Tenantovi B jako autorizovaného uživatele v clusteru Azure Průzkumník dat. Ověřte, že vlastnost *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* v clusteru Azure Průzkumník dat obsahuje tenanta ' B '. Spuštění křížového dotazu je plně v Tenantovi B.
*  Pomocí [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) můžete promítnout prostředek Azure monitor do tenanta "A".
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Připojení k clusterům Azure Průzkumník dat z různých tenantů

Průzkumník Kusto vás automaticky přihlásí k tenantovi, ke kterému uživatelský účet původně patří. Chcete-li získat přístup k prostředkům v jiných klientech se stejným uživatelským účtem, je `tenantId` nutné explicitně zadat v připojovacím řetězci: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Další kroky
* [Zápis dotazů](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Provádění dotazů protokolu pro více prostředků v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






