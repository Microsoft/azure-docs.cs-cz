---
title: Dotazování mezi prostředky Azure Průzkumník dat pomocí Azure Monitor
description: Pomocí Azure Monitor můžete provádět dotazy mezi produkty mezi Azure Průzkumník dat, Log Analytics pracovní prostory a klasické Application Insights aplikace v Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 1a35b80ceec12b378a01555f42b7a0500b8f6229
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060448"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Dotazování mezi prostředky Azure Průzkumník dat pomocí Azure Monitor
Azure Monitor podporuje dotazy mezi službami mezi Azure Průzkumník dat, [Application Insights](/azure/azure-monitor/app/app-insights-overview)a [Log Analytics](/azure/azure-monitor/platform/data-platform-logs). Pak můžete dotazovat cluster Azure Průzkumník dat pomocí nástrojů pro Log Analytics nebo Application Insights a odkazovat na něj v dotazu mezi službami. V tomto článku se dozvíte, jak vytvořit dotaz na více služeb.

Následující diagram znázorňuje Azure Monitor tok mezi službami:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagram znázorňující tok dotazů mezi uživatelem, Azure Monitor, proxy serverem a Průzkumník dat Azure.":::

>[!NOTE]
> Dotaz na Azure Monitor mezi službami je ve verzi Public Preview. Kontaktujte [tým služby](mailto:ADXProxy@microsoft.com) s případnými dotazy.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Křížové dotazování prostředků Log Analytics nebo Application Insights a Azure Průzkumník dat

Dotazy na více zdrojů můžete spouštět pomocí klientských nástrojů, které podporují dotazy Kusto. Mezi tyto nástroje patří například Log Analytics webové uživatelské rozhraní, sešity, PowerShell a REST API.

Zadejte identifikátor pro cluster Azure Průzkumník dat v dotazu v rámci `adx` vzoru následovaný názvem databáze a tabulkou.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Snímek obrazovky, který ukazuje příklad dotazu mezi službami.":::

> [!NOTE]
>* V názvech databází se rozlišují velká a malá písmena.
>* Dotaz mezi prostředky jako výstraha není podporován.

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Kombinování tabulek clusteru Azure Průzkumník dat s pracovním prostorem Log Analytics

Pomocí `union` příkazu můžete kombinovat tabulky clusteru s Log Analytics pracovním prostorem.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Snímek obrazovky, který zobrazuje příklad dotazu mezi službami pomocí příkazu Union.":::

> [!Tip]
> Je povolený zkrácený formát: *název_clusteru* / *vlastnost InitialCatalog*. Například `adx('help/Samples')` je přeložen na `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Připojení dat z clusteru Azure Průzkumník dat v jednom tenantovi s prostředkem Azure Monitor v jiném

Mezi službami nejsou podporované dotazy mezi klienty. Jste přihlášení k jednomu klientovi pro spuštění dotazu, který pokrývá oba prostředky.

Pokud je prostředek Azure Průzkumník dat v Tenantovi a a Log Analytics pracovní prostor je v Tenantovi B, použijte jednu z následujících metod:

*  Azure Průzkumník dat umožňuje přidávat role pro objekty zabezpečení v různých klientech. Přidejte své ID uživatele v Tenantovi B jako autorizovaného uživatele v clusteru Azure Průzkumník dat. Ověřte, jestli vlastnost [TrustedExternalTenant](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster) v clusteru Azure Průzkumník dat obsahuje tenanta b. Spusťte křížový dotaz, který je plně v tenantovi b.
*  Pomocí [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) můžete promítnout prostředek Azure monitor do tenanta A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Připojení k clusterům Azure Průzkumník dat z různých tenantů

Průzkumník Kusto se automaticky přihlásí k tenantovi, ke kterému uživatelský účet původně patří. Chcete-li získat přístup k prostředkům v jiných klientech se stejným uživatelským účtem, je nutné explicitně zadat `TenantId` připojovací řetězec:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Další kroky
* [Zápis dotazů](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Dotazování dat v Azure Monitor pomocí Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Provádění dotazů protokolu pro více prostředků v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
